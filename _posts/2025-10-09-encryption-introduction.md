---
title: "An Introduction to Encryption"
excerpt: "A beginner-friendly guide explaining modern encryption techniques."
date: 2025-10-09 18:00:00 -0004
tags:
  - Cybersecurity
  - Cryptography
  - Encryption
  - Online Privacy
  - TOALC
---

<figure class="align-center">
  <img src="/assets/images/posts/2025-10-09-encryption-introduction/jason-dent-p89q19F4EL4-unsplash.jpg" alt="Introduction to Encryption">
  <figcaption>Photo by <a href="https://unsplash.com/@jdent?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Jason Dent</a> on <a href="https://unsplash.com/photos/gray-steel-door-with-silver-door-knob-p89q19F4EL4?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a></figcaption>
</figure>

<center><small><i> This article was written as part of a course that I'm teaching for <a href="https://www.theoalc.org/">The Older Adult Learning Community (TOALC)</a>. If you enjoy this content and would like to support the endeavor of lifelong learning, please consider <a href="https://www.theoalc.org/donate">donating</a>. </i></small></center>

{% include styled_div.html %}

Without knowing it, nearly every interaction that you have on the Internet relies on encryption. From using a banking app to sending a private message to a friend, encryption serves as the digital lockbox that secures your information from malicious actors who would otherwise use that information for personal gain.

This article will introduce the basics of encryption, including why it's important and how it's used to secure communication. The aim of this article is to provide an overview of encryption at a high-level, so we won’t explore the technical or mathematical aspects of modern encryption in depth.

{% include styled_div.html %}

# What is Encryption?

Encryption is the process of taking readable information (often referred to as **plaintext**) and obscuring it using various techniques that make it unintelligible (often referred to as **ciphertext**). This ensures that if the encrypted information is intercepted by someone other than the intended recipient, the information is useless without the key.

The only way to read encrypted information is to decrypt it into its original plaintext form with the proper key. With strong enough encryption, including the modern techniques we'll discuss in this article, it can be nearly impossible to decrypt ciphertext without that key.

## The Importance of Encryption

When you visit a website from your home computer or smartphone, the data that you're sending can move through countless different servers before it reaches its ultimate destination. The path that the information will travel on its journey is typically unknown to you, and will likely change over time and depending on network conditions. Likewise, a response will travel its own path back to you which may not be the same.

The free online tool [IP2Location](https://www.ip2location.com/free/traceroute) can help us visualize an example of this process by attempting to trace and map out the servers that information is traveling through to reach its ultimate destination. In this example, the sender is in the United States and the receiver is Google:

<figure class="align-center">
  <img src="/assets/images/posts/2025-10-09-encryption-introduction/us-traceroute.jpg" alt="Traceroute from U.S to Google (IP2Location)">
  <figcaption>Traceroute from the United States to Google (IP2Location)</figcaption>
</figure>

Accessing Google is likely a connection that your computer and smartphone make many times daily. This data transfer can travel back and forth across the United States several times, touching more than 8 servers to finally reach Google. The path can become even more complex when the source and destination are farther apart, and will differ for many reasons (i.e lesser known destinations than Google, or day-to-day as new servers come online and routes change). A separate example can be seen here, tracing a communication from Germany to the website [hardcover.app](https://hardcover.app/) (a great book-tracking social media app):

<figure class="align-center">
  <img src="/assets/images/posts/2025-10-09-encryption-introduction/de-traceroute.jpg" alt="Traceroute from Germany to Hardcover.app (IP2Location)">
  <figcaption>Traceroute from Germany to Hardcover.app (IP2Location)</figcaption>
</figure>

We can see this case expand to touching 9+ different servers, traveling across the globe many times before reaching the hardcover.app server. The magic of the Internet is that all of this happens in fractions of a second, but it can also be troublesome to consider that whatever information you send is traveling through these (unknown to you) servers on its journey. 

The hosts that sit in-between you and your intended destination could very well serve to gain something from reading the information that you're sending and receiving. It's well known that your Internet Service Provider (one of the first checkpoints in the journey from your computer to your destination) can and likely does collect as much information about you that it can to sell to the highest bidder ([ftc.gov](https://www.ftc.gov/news-events/news/press-releases/2021/10/ftc-staff-report-finds-many-internet-service-providers-collect-troves-personal-data-users-have-few)).

When your information is properly encrypted, unauthorized parties who intercept your data will only have access to it in an indecipherable form (ciphertext), effectively making it garbage without access to the encryption key. Proper encryption insures a high degree of safety and privacy in a zero-trust environment such as the Internet.

{% include styled_div.html %}

# Types of Encryption

There are many techniques that can be used to encrypt information, but all of these techniques fall under two major categories - symmetric, and asymmetric. You use both of these forms of encryption for nearly any communication that you make over the Internet.

## Symmetric Key Encryption

When an encryption algorithm uses a single key for both encryption (converting to ciphertext) and decryption (converting back to plaintext), it's referred to as a _symmetric_ encryption algorithm. This means that both the sender and the receiver will need the same key to communicate with one another.

### Symmetric Key Encryption Example

The earliest forms of symmetric encryption are known as **substitution ciphers**. The **Caesar cipher**, named after its usage of protecting Roman military communication during the reign of Julius Caesar ([caesarcipher.org](https://caesarcipher.org/learn/caesar-cipher-history)), is likely the simplest substitution cipher that we can use to form an example.

A Caesar cipher can be encrypted and decrypted by hand without a computer, a critically important factor when it was originally used in ~100 B.C. It works by shifting letters by a consistent number, a number which is effectively the encryption key. For the English alphabet, the key can be any number between 1 and 25 - the number of letters you can shift before you reach the original value again.

We can align each letter in the alphabet with an incremental number (i.e `A -> 1`, `B -> 2`, all the way to `Z -> 26`) to find its numerical value. We encrypt a message by incrementally shifting the letters in our message by that key (e.g. `A (1) + 2 -> C (3)` for a key of 2), and decrypt a message by decrementally shifting the letters with the same key (e.g. `C (3) - 2 -> A (1)` for a key of 2).

Let's consider an example using a key of 3. If we wanted to encrypt the message `HELLO WORLD` using a key of 3, we would incrementally shift each letter in our message by 3 (i.e `H (8) + 3 -> K (11)`, `E (5) + 3 -> H (8)`, etc). This results in the ciphertext `KHOOR ZRUOG`:

<figure class="align-center">
  <img src="/assets/images/posts/2025-10-09-encryption-introduction/caesar-cipher-shift-3.jpg" alt="Caesar Cipher example with key of 3">
  <figcaption>Caesar Cipher example with key of 3</figcaption>
</figure>

The receiver would then (knowing that the key was 3) shift the letters back (subtracting 3 from each letter) in order to get the original (plaintext) message of `HELLO WORLD`.

### Modern Symmetric Key Encryption

Modern computers would be able to crack the code to a Caesar cipher in fractions of a second. The Caesar cipher has a key space of only 25 potential keys, of which a computer could quickly exhaust all potential values to find the readable message from a ciphertext. 

Additionally, the Caesar cipher is vulnerable to what is known as a *frequency analysis attack* ([caesarcipher.org](https://caesarcipher.org/learn/why-caesar-cipher-is-not-secure-modern-cryptanalysis-methods#frequency-analysis-attack)), since any given letter (e.g. `A`) will *always* exist as the same letter in the equivalent ciphertext (e.g. `A (1) + 2 -> C (3)`, where every `A` in plaintext would be replaced by `C` in ciphertext). Any given language has an overall letter frequency appearance (e.g. in English, `E` appears more often than any other letter). Therefore, a message with only a few sentences could map the frequency of each letter of the ciphertext and derive the key from the known overall frequency map (e.g. if `G` appears most often in the ciphertext, and we can assume the message is in English, it's very likely that the key is 2: `G (7) - 2 -> E (5)`).

Modern encryption algorithms, such as **Advanced Encryption Standard (AES)** are much more mathematically complex than the Caesar cipher, and resolve the vulnerabilities described above. For one, AES is not vulnerable to a frequency analysis attack; the encryption method insures that a letter in a plaintext message will not always equate to the same letter in the equivalent AES ciphertext (e.g. a repeated `E` in a plaintext message could result in any number of different letters/characters after encryption.)

Importantly, AES also supports a key space of 2<sup>256</sup> possible keys (approximately 1.1579 * 10<sup>77</sup>). To put this number into perspective, there is a rough estimate that 7.5 x 10<sup>18</sup> grains of sand exist on Earth ([npr.org](https://www.npr.org/sections/krulwich/2012/09/17/161096233/which-is-greater-the-number-of-sand-grains-on-earth-or-stars-in-the-sky)). While searching the key space for Caesar cipher would take fractions of a second even on a decades-old personal computer, even the most powerful supercomputer that exists today would have to run constantly for many times longer than the universe itself has existed (100s of billions of years) in order to perform the same task with a 256-bit AES key ([talkcrypto.org](https://www.talkcrypto.org/blog/2019/04/08/all-you-need-to-know-about-2256/)).

Note that the AES algorithm, while being extremely mathematically complex, is completely open and can be implemented by anyone ([nist.gov](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.197-upd1.pdf)). Much like all the encryption methods used to secure your information, the strength of encryption is not on the secrecy of *how* the encryption takes place; the strength is instead derived from the secrecy and protection of the key(s) used.

## Asymmetric Key Encryption

Encryption algorithms that use separate keys for encrypting and decrypting information are known as _asymmetric_ encryption algorithms, also referred to as **public key cryptography**. Unlike with symmetric encryption (such as the Caesar cipher noted earlier), there are two separate keys required to communicate - a **public key**, and a **private key**. Only the private key needs to be kept secure in order to create a secure communication channel; the public key can safely be shared in *any* channel, regardless of whether that channel is secure or not. The public key can *only* be used to encrypt information, while the private key can *only* be used to decrypt information.

One of the most widely used asymmetric encryption algorithms is **RSA** (Rivest–Shamir–Adleman, named after the three computer scientists who devised the algorithm in 1977). RSA, along with all asymmetric encryption algorithms, are inherently much more computationally complex than symmetric encryption algorithms.

### Asymmetric Key Encryption Example

Unlike with symmetric encryption and the example of the Caesar cipher, there isn't an easily digestible example of asymmetric encryption; While the underlying fundamentals of asymmetric encryption are complex, the process at a high level is fairly straightforward.

Let's create an example with two people who would like to send a secure message, Alice and Bob. If Alice needs to send a secure message to Bob, she only needs Bob's public key. Bob can make his public key freely available to anyone on the Internet, and can send this public key to Alice over unsecure channels without security risk.

Once Alice has Bob's public key, she uses it with the asymmetric encryption algorithm to turn her plaintext message into an unreadable ciphertext. This ciphertext is now only decipherable by Bob, since Bob is the only one who has the private key necessary to decipher it. Anyone who obtained Bob's public key has no means of deciphering this message - even Alice herself.

<figure class="align-center">
  <img src="/assets/images/posts/2025-10-09-encryption-introduction/public-key-cryptography-diagram.jpg" alt="Example of using Asymmetric Encryption">
  <figcaption>Example of using Asymmetric Encryption</figcaption>
</figure>

We can break this example down further in physical terms. If Alice wanted to send Bob something securely through the mail and couldn't trust that couriers wouldn't open the package on the way, Alice and Bob might do the following:

1. Bob sends Alice an empty lockbox. Inside of the lockbox is an open lock (Bob's public key) that only Bob has the key to (Bob's private key).
2. The courier delivers the empty lockbox - even if they look inside, all they see is an open lock without a key.
3. Alice receives the lockbox in the mail, places her message in the box, and secures it closed with Bob's lock (Bob's public key). The lockbox can now only be opened by Bob himself, since he alone has the key.
4. The courier delivers the lockbox back to Bob. He can shake and rattle the lockbox, but has no key and therefore can't see what's inside. If we assume that the lock is nearly impenetrable (this can be assumed for public key cryptography), there's little to no chance that the courier can pick the lock.
5. Bob receives the lockbox, and uses his key (Bob's private key) to to unlock the box and read Alice's message.

While this example is cumbersome for delivering a physical package, it takes place multiple times in digital form nearly every time you connect to a website.

## Hybrid Encryption

Modern symmetric encryption algorithms like AES provide a secure way to send and receive information over the Internet. However, the security of the encryption relies entirely on the secrecy of the key, and that key needs to be shared with both parties in order to begin communication. 

As noted earlier, there's no reliable way to know where information will travel through when sent over the Internet. Additionally, there are more local security risks when using a shared/public wifi network that malicious actors could be sharing. If we consider symmetric encryption alone, this poses a Catch-22 - we need to share the key in order to begin secure communication, but we can't securely share that key without *already having a secure channel of communication*.

This is where asymmetric encryption (such as RSA) becomes critically important, as it allows for secure communication to take place in an entirely unsecure environment. As noted in the earlier example, a public key can be shared freely without degrading the security of the encryption. Asymmetric encryption, however, comes with a high cost. 

Due to the high mathematical complexity, encrypting and decrypting information with an asymmetric algorithm is *incredibly* slow when compared to a symmetric algorithm, even for a modern computer. In rough terms, using an asymmetric algorithm like RSA to encrypt a small piece of information is **thousands** of times more computationally expensive than when using a symmetric algorithm like AES. I've created a small comparison between RSA and AES in Python [here](https://gist.github.com/jsoberg/4897bd5104847e7026107228c6a1f1c4); On my personal computer with the linked example, the average time to encrypt a 256-bit key with RSA took roughly 7,000 times longer than the same information with AES.

Considering the complexity of asymmetric algorithms and the "initial key delivery" problem of symmetric algorithms, most modern encryption uses a hybrid system in order to create secure channels of communication. These hybrid systems use an asymmetric algorithm to exchange a shared symmetric key, and then use that key to communicate the bulk of the information with a symmetric algorithm. 

This provides the best of both worlds - the ability to securely exchange a key in an insecure environment (using an asymmetric algorithm like RSA) and then send and receive vast amounts of information quickly using that securely exchanged key (using a symmetric algorithm like AES).

{% include styled_div.html %}

# Conclusion

<figure class="align-center">
  <img src="/assets/images/posts/2025-10-09-encryption-introduction/sasun-bughdaryan-8admGA18lBs-unsplash.jpg" alt="Introduction to Encryption - Conclusion">
  <figcaption>Photo by <a href="https://unsplash.com/@sasun1990?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Sasun Bughdaryan</a> on <a href="https://unsplash.com/photos/black-and-silver-key-on-black-and-silver-laptop-computer-8admGA18lBs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a></figcaption>
</figure>

In this article, we've learned the basics of encryption, and the different techniques that are used to secure nearly every interaction made on the Internet. While the underlying math of modern encryption can be complex, the principles are essential to understand in an age where digital threats are always evolving.

{% include styled_div.html %}

# Resources

- [Cisco - What is Encryption? (cisco.com)](https://www.cisco.com/site/us/en/learn/topics/security/what-is-encryption.html)
- [EFF - What Should I Know About Encryption? (eff.org)](https://ssd.eff.org/module/what-should-i-know-about-encryption)
- [IBM - A Brief History of Cryptography (ibm.com)](https://www.ibm.com/think/topics/cryptography-history)
- [Trenton Systems - Symmetric vs. Asymmetric Encryption (trentonsystems.com)](https://www.trentonsystems.com/en-us/resource-hub/blog/symmetric-vs-asymmetric-encryption)
- [Cisco - RSA Algorithm in Cryptography (splunk.com)](https://www.splunk.com/en_us/blog/learn/rsa-algorithm-cryptography.html)