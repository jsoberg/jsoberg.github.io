---
title: "Introduction to Encryption"
excerpt: "A high level overview of encryption; how it's used, and why it's important"
date: 2025-09-12 12:00:00 -0004
tags:
  - Cybersecurity
  - Cryptography
  - Encryption
  - TOALC
---

<figure class="align-center">
  <img src="/assets/images/posts/2025-09-12-encryption-introduction/jason-dent-p89q19F4EL4-unsplash.jpg" alt="Introduction to Encryption">
  <figcaption>Photo by <a href="https://unsplash.com/@jdent?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Jason Dent</a> on <a href="https://unsplash.com/photos/gray-steel-door-with-silver-door-knob-p89q19F4EL4?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a></figcaption>
</figure>


<center><small><i> This article was written as part of a course that I'm teaching for <a href="https://www.theoalc.org/">The Older Adult Learning Community (TOALC)</a>. If you enjoy this content and would like to support the endeavor of lifelong learning, please consider <a href="https://www.theoalc.org/donate">donating</a>. </i></small></center>

{% include styled_div.html %}

Today's article will provide a brief introduction to encryption, including why it's important and how it's used every day to protect your critical information. The aim of this article is to provide an overview of encryption at a high-level in order to gather a better understanding of its usage, and therefore the technical/mathematical side of applied encryption won't be discussed at any length.

# What is Encryption?

Encryption is the process of taking readable information (often referred to as *plaintext*) and obscuring it using various techniques that make it unreadable (often referred to as *ciphertext*). This insures that if the encrypted information is intercepted by someone other than the intended recipients, which is an inevitability on the Internet, the information will be useless to them without the proper key.

The only way to read encrypted information is to decrypt it into its original plaintext form with the proper key. With a strong enough encryption technique, including the modern techniques noted in this article, it's all but impossible to decrypt the information without the key.

## Encryption's Historical Context

The study and usage of encryption has been around for far longer than the Internet and the modern digital computer. 

## The Importance of Encryption Today

The Internet was built as an open network used to facilitate the transfer of information across vast distances, and to that measure it's succeeded greatly. Security and privacy, however, were not large considerations when the Internet as we know it was being formed.

When you visit a website from your home computer or smartphone, the information that you're sending can move through countless different servers and locations in-between. There's no real way of knowing where that information will go, and who could potentially intercept it before it reaches it's destination and starts the long journey back to you.

The free tool [IP2Location](https://www.ip2location.com/free/traceroute) can help us visualize an example of this process by attempting to trace and map out the servers that information is traveling through to reach it's ultimate destination. In this example, the sender is in the United States and the receiver is Google:

<figure class="align-center">
  <img src="/assets/images/posts/2025-09-12-encryption-introduction/us-traceroute.jpg" alt="Traceroute from U.S to Google">
  <figcaption>Traceroute from the United States to Google</figcaption>
</figure>

Accessing Google is likely a communication that you make many times every day. This (relatively) simple transfer of data travels back and forth across the United States several times, touching 8+ servers to finally reach Google. The results can expand when the source and destination are further away from one another, and will differ for many reasons (i.e lesser known destinations than Google, or day-to-day as servers come online and routes change). A separate example can be seen here, tracing a communication from Germany to the website [hardcover.app](https://hardcover.app/) (a great book-tracking social media app):

<figure class="align-center">
  <img src="/assets/images/posts/2025-09-12-encryption-introduction/de-traceroute.jpg" alt="Traceroute from Germany to Hardcover.app">
  <figcaption>Traceroute from Germany to Hardcover.app</figcaption>
</figure>

We can see this case expand to touching 9+ different servers, traveling across the globe many times before reaching its ultimately destination. The magic of the Internet is that all of this happens in fractions of a second, but it can also be troublesome to consider that whatever information you send is traveling through an innumerable number of hosts no matter what you're trying to access. 

The hosts that sit in-between you and your intended destination could very well be acting maliciously, and serve to gain something from reading the information that you're sending and receiving. It's well known that your Internet Service Provider (the first checkpoint in-between you and your destination) can and likely does collect whatever information about you that it can when your information moves through it ([ftc.gov](https://www.ftc.gov/news-events/news/press-releases/2021/10/ftc-staff-report-finds-many-internet-service-providers-collect-troves-personal-data-users-have-few)).

# Types of Encryption

There are many techniques that can be used to encrypt information, but all of these techniques fall under two major categories - symmetric, and asymmetric.

## Symmetric Key Encryption

When an encryption algorithm uses a single key for both encryption (converting to ciphertext) and decryption (converting back to plaintext), it's referred to as a _symmetric_ encryption algorithm. This means that both the sender and the receiver will be using a single key when communicating.

## Asymmetric Key Encryption

Encryption algorithms that use separate keys for encrypting and decrypting information are referred to as _asymmetric_ encryption algorithms. Unlike with symmetric encryption (such as the Caesar cipher noted earlier), there are two separate keys required to communicate. 

Asymmetric encryption is the backbone of secure communication in a zero-trust environment such as the Internet, as it allows you to securely communicate without having to share any information that would allow third parties to intercept and read that information. This is distinct from symmetric encryption, where one has to share a single key to perform any secure communication - since we can never rely on a secure method of transport

## Combining Symmetric and Asymmetric Encryption

Asymmetric encryption is critical for the secure exchange of information, but it comes with a cost. Due to the high mathematical complexity, encrypting and decrypting information with an asymmetric algorithm (such as RSA) is incredibly slow in comparison to an symmetric algorithm (such as AES), even for a modern computer. In rough terms, using an asymmetric algorithm like RSA to encrypt a small piece of information is **thousands** of times more computationally expensive than when using a symmetric algorithm like AES - You can run a basic comparison between RSA and AES yourself using the Python code provided [here](https://gist.github.com/jsoberg/4897bd5104847e7026107228c6a1f1c4).

Considering the complexity of asymmetric algorithms, most modern encryption uses a hybrid system. These systems use an asymmetric algorithm like RSA to encrypt and deliver a shared symmetric key to one another, and then use that symmetric key to communicate the bulk of the information with a symmetric algorithm like AES. This provides the best of both worlds - the ability to securely exchange keys in an insecure environment (using an asymmetric algorithm) and then quickly send and receive vast amounts of information (using a symmetric algorithm).

# Resources

- [Cisco - What is Encryption? (cisco.com)](https://www.cisco.com/site/us/en/learn/topics/security/what-is-encryption.html)
- [EFF - What Should I Know About Encryption? (eff.org)](https://ssd.eff.org/module/what-should-i-know-about-encryption)
- [IBM - A Brief History of Cryptography (ibm.com)](https://www.ibm.com/think/topics/cryptography-history)
- [Trenton Systems - Symmetric vs. Asymmetric Encryption (trentonsystems.com)](https://www.trentonsystems.com/en-us/resource-hub/blog/symmetric-vs-asymmetric-encryption)