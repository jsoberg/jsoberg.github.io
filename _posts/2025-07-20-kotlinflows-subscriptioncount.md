---
title: "Automatic Resource Management with Kotlin Flows"
excerpt: "How to automatically manage resources with a Kotlin SharedFlow."
date: 2025-07-20 12:00:00 -0004
tags:
  - Android
  - Coroutines
  - Kotlin
  - Kotlin Flows
  - Reactive Programming
  - Software Development
---

<figure class="align-center">
  <img src="/assets/images/posts/2025-07-20-kotlinflows-subscriptioncount/simon-kadula-8gr6bObQLOI-unsplash.jpg" alt="Automatic resource management with Kotlin SharedFlow">
  <figcaption>Photo by <a href="https://unsplash.com/@simonkadula?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Simon Kadula</a> on <a href="https://unsplash.com/photos/a-factory-filled-with-lots-of-orange-machines-8gr6bObQLOI?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a></figcaption>
</figure>

In nearly every codebase that I've worked on since switching to [Kotlin Flows](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/) for reactive programming, I've run into the same situation: I need A shared flow of data that is backed by some resources (e.g. network requests), and those resources only need to be managed while there are active subscribers to the flow.

In this article, we'll discuss how to use the lesser-known [`subscriptionCount`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/subscription-count.html) API available in [`MutableSharedFlow`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/) to automatically manage resources for this situation.

{% include styled_div.html %}

# Problem

Let's reiterate the problem that we need to solve, with a bit more detail:

> I have a flow of data that one or more subscribers will observe at any given time. This flow is backed by some resources that only need to be active while subscribers are present. The resources should be cleaned up once subscribers are no longer present.

In an Android application, this issue often appears when maintaining a cache that is only relevant in a subsection of the app. To minimize resource usage (and the chance of memory leaks), we don't want to maintain this cache for the entire app lifecycle, potentially letting it grow indefinitely; we only want the cache to be active while it's in use.

## Example

For a more specific example, let's consider a media collection app. In this app, we may have different categories of media (i.e. books, movies, TV, music) that all use different datasets and utilize separate network APIs for pulling the latest state.

In this example, we want to pull the latest data when the user enters each category (e.g. pulling and storing movies *only* when the user enters the movies category). We also want this data to be cached, because the user will likely be scrolling through movies and entering sub-screens that can use the data we've already pulled from the network. This will speed up the loading of each sub-screen, while also minimizing network usage.

Proper [memory management](https://developer.android.com/topic/performance/memory) is critical in Android applications, to reduce active memory/CPU usage and the chance of memory leaks. We don't want this cache to grow indefinitely, and we don't want to manage the movie resources for the entire app lifecycle. We also don't want to manage the resources for books, TV, music, and other categories while the user is only looking at movies, since that data isn't necessary in the movies section of the app.

{% include styled_div.html %}

# Solution

Let's list the requirements we need to fulfill to solve this problem:

1. When the first subscriber starts observing the Flow, we need to begin managing the necessary resources.
1. When subsequent subscribers begin observing the Flow, we don't need to change anything - we just need to continue managing the resources.
1. When all subscribers have stopped observing the Flow, we need to cleanup the resources.
1. If another subscriber comes in after cleanup, begin managing the resources again.

## `MutableSharedFlow`'s `subscriptionCount`

As hinted earlier, the available [`subscriptionCount`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/subscription-count.html) field on `MutableSharedFlow` can be used to solve our problem.

`subscriptionCount` is a [`StateFlow`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/) that will emit the number of active subscriptions that a `MutableSharedFlow` has. `subscriptionCount`'s emitted count will increment when new subscriptions are made (e.g. by calling [`collect`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/collect.html)), and decrement when an active subscription is disposed (e.g. when the `CoroutineScope` that a subscription was launched in is canceled). I've added some test code on [GitHub](https://github.com/jsoberg/manged-resources-flow-example/blob/main/src/test/kotlin/com/soberg/example/SubscriptionCountTest.kt) that runs through this process directly on `subscriptionCount` as an example.

The Kotlin docs for [`subscriptionCount`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/subscription-count.html) provide a great starting point for building a solution to our problem:
```kotlin
sharedFlow.subscriptionCount
    .map { count -> count > 0 } // map count into active/inactive flag
    .distinctUntilChanged() // only react to true<->false changes
    .onEach { isActive -> // configure an action
        if (isActive) onActive() else onInactive()
    }
    .launchIn(scope) // launch it
```

In this example, `onActive()` is a lambda that will start necessary resources (e.g. starting up any network requests that back a cache) while `onInactive()` will cleanup those resources (e.g. cancel active network requests, and/or clear the cache from memory). `map` is used to transform from the subscription count to whether or not there are active subscribers (a subscription count above 0), and then `distinctUntilChanged()` insures the flow only emits when going from active to inactive (or vice versa). This prevents calling `onActive()` multiple times as the subscription count continues to change with multiple subscribers.

## Building a Reusable Pattern

We can make a few small modifications to create a more generally reusable pattern. I've built a simple example out on [GitHub](https://github.com/jsoberg/manged-resources-flow-example), named [`ManagedResourcesFlow`](https://github.com/jsoberg/manged-resources-flow-example/blob/main/src/main/kotlin/com/soberg/example/ManagedResourcesFlow.kt):
```kotlin
/** @return A Flow that can be used to reliably manage the resources controlled by [onActive] and [onInactive].
 *  When subscribers are present, [onActive] will be called to initialize any managed resources.
 *  When subscribers are no longer present, [onInactive] will be called to cleanup any managed resources. */
class ManagedResourcesFlow(
    /** [CoroutineScope] that will be used to observe resource management changes. */
    scope: CoroutineScope,
    /** Callback to begin managing resources when subscribers are present. */
    private val onActive: suspend () -> Unit,
    /** Callback to cleanup managed resources when subscribers are no longer present. */
    private val onInactive: suspend () -> Unit,
) {
    private val internalMonitoringFlow = MutableSharedFlow<Unit>()

    init {
        internalMonitoringFlow.subscriptionCount
            .map { count -> count > 0 }
            .distinctUntilChanged()
            // Drop the first inactive emission - we can assume resources are inactive by default,
            // and therefore calling onInactive() is unnecessary.
            .drop(1)
            .onEach { isActive ->
                if (isActive) onActive() else onInactive()
            }
            .launchIn(scope)
    }

    /** @return A Flow that will control reliable management of resources defined in the constructor.
     * This Flow will **always** emit once, and only once. */
    operator fun invoke(): Flow<Unit> = internalMonitoringFlow
        // The output of this Flow is not important - we just need to make sure that it emits once
        // (and only once) so that downstream subscribers will be able to flatMap to what they need.
        .onStart { emit(Unit) }
        .distinctUntilChanged()
}
```

The constructor accepts the `onActive` and `onInactive` lambdas, for starting and stopping resources as the managed flow is active or not (respectively). Additionally, a `CoroutineScope` is provided for launching the resource management flow itself.

The `init` block collects a flow similar to the Kotlin docs-defined [`subscriptionCount`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/subscription-count.html) flow, with one small modification. `drop` is used to ignore the first emission, which means `onInactive()` won't be called unnecessarily (since the first emission will always be `false`, as no active subscribers are present on start).

The resource management flow itself is launched in the constructor-provided `CoroutineScope`. This launched resource management flow should outlive any subscriptions that are made, so this scope should likely be more global than what the subscribers will be using. Using an Android application example, this scope might be an application lifecycle tied scope, where subscriptions are made using lower-level [lifecycle-aware scopes (e.g. `ViewModelScope`)](https://developer.android.com/topic/libraries/architecture/coroutines).

Now, `ManagedResourcesFlow` can be used to manage whatever specific resources are needed for the situation. Note that the `invoke()` function returns a `Flow<Unit>` that **emits once, and only once**. Under the hood. This is chaining off of the internal `MutableSharedFlow` (so that `subscriptionCount` will emit appropriately), and then just emitting a single `Unit`. This is effectively just being used to make sure active subscriptions are counted for resource management, and the `Unit` emission can be ignored - [`flatMapLatest`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/flat-map-latest.html) will be used to connect to the Flow that should actually be emitting to subscribers.

## Example

I've written some example unit tests on [GitHub](https://github.com/jsoberg/manged-resources-flow-example/blob/main/src/test/kotlin/com/soberg/example/ManagedResourcesFlowTest.kt) that confirm the listed requirements are fulfilled. 

Building on the example of a media collection app, here's a simplified example of how `ManagedResourcesFlow` could be utilized to manage a network-driven cache for the movies section of the app:
```kotlin
class MovieCache @Inject constructor(
    private val appScope: CoroutineScope,
    private val observeNetworkChanges: Flow<Map<Id, MovieInformation>>,
) {
    private val activeJobLock = Mutex()
    private var activeJob: Job? = null

    private val internalCacheFlow =
        MutableStateFlow<MutableMap<Id, MovieInformation>>(mutableMapOf())

    private val managedResourcesFlow = ManagedResourcesFlow(
        scope = appScope,
        onActive = ::activateCache,
        onInactive = ::disposeCache,
    )

    private suspend fun activateCache() {
        // Having active subscribers means we'll want to observe network changes.
        val job = observeNetworkChanges.onEach { networkInfo ->
            internalCacheFlow.update { cache -> (cache + networkInfo).toMutableMap() }
        }.launchIn(appScope)
        activeJobLock.withLock { activeJob = job }
    }

    private suspend fun disposeCache() {
        // When no active subscribers are present, we can cancel network observation and clear
        // the cache to reduce memory usage.
        activeJobLock.withLock {
            activeJob?.cancel()
            activeJob = null
        }
        internalCacheFlow.update { mutableMapOf() }
    }

    // Can be called for local cache updates for subscribers.
    fun cacheChange(id: Id, movie: MovieInformation) {
        internalCacheFlow.update { cache -> cache.apply { put(id, movie) } }
    }

    // Subscribers will receive all updates from the `internalCacheFlow`, which is being managed here.
    fun observeCache(): Flow<Map<Id, MovieInformation>> =
        managedResourcesFlow().flatMapLatest { internalCacheFlow.map { it.toMap() } }
}
```

Internally, `MovieCache` instantiates a `ManagedResourcesFlow` to manage activation and disposal of the movie cache resources. For this example, those resources are network and local changes that get added to an in-memory cache (in the form of a `Map`).

When subscribers become active by collecting `observeCache()`, network observation is started. When subscribers are no longer present, the network observer is disposed and the in-memory cache is cleared, insuring resources are cleaned up when not in use. This adds some assurance that excess resources won't be wasted when `MovieCache` lives within a larger DI scope (e.g. `Singleton`).

{% include styled_div.html %}

# Final Thoughts

I've outlined a method for managing resources automatically based on active subscription to a `SharedFlow`, using the [`subscriptionCount`](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/subscription-count.html) API. 

Most examples discussed are available on my [GitHub](https://github.com/jsoberg/manged-resources-flow-example). If you have any questions not answered in this article, feel free to add them to the discussions forum [here](https://github.com/jsoberg/manged-resources-flow-example/discussions)!