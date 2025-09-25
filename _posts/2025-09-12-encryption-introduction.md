---
title: "Introduction to Encryption"
excerpt: "A high level overview of encryption, how it's used and why it's important"
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

This article is part of a series where we'll learn about Encryption, Authentication, and Passkeys. Today's article will provide a brief introduction to encryption, including why it's important and how it's used every day to protect your critical information.

# What is Encryption?

Encryption is the process of taking readable information (often referred to as *plaintext*) and obscuring it using various techniques that make it unreadable (often referred to as *ciphertext*). This insures that if the encrypted information is intercepted by someone other than the intended recipients, which is an inevitability on the Internet, the information will be useless to them without the proper key.

The only way to read encrypted information is to decrypt it into its original plaintext form with the proper key. With a strong enough encryption technique, including the modern techniques noted in this article, it's all but impossible to decrypt the information without the key.

## The Importance of Encryption

## Encryption's Historical Context

The study and usage of encryption has been around for far longer than the Internet and the modern digital computer. 

# Types of Encryption

There are many techniques that can be used to encrypt information, but all of these techniques fall under two major categories - symmetric, and asymmetric.

## Symmetric Key Encryption

When an encryption algorithm uses a single key for both encryption (converting to ciphertext) and decryption (converting back to plaintext), it's referred to as a _symmetric_ encryption algorithm. This means that both the sender and the receiver will be using a single key when communicating.

## Asymmetric Key Encryption

Encryption algorithms that use separate keys for encrypting and decrypting information are referred to as _asymmetric_ encryption algorithms. Unlike with symmetric encryption (such as the Caesar cipher noted earlier), there are two separate keys required to communicate. 

Asymmetric encryption is the backbone of secure communication in a zero-trust environment such as the Internet, as it allows you to securely communicate without having to share any information that would allow third parties to intercept and read that information. This is distinct from symmetric encryption, where one has to share a single key to perform any secure communication - since we can never rely on a secure method of transport

## Combined Usage of Symmetric and Asymmetric Encryption

Asymmetric encryption is critical for the secure exchange of information, but it comes with a cost. Due to the high mathematical complexity, encrypting and decrypting information with an asymmetric algorithm (such as RSA) is incredibly slow in comparison to an symmetric algorithm (such as AES), even for a modern computer.

# Resources

- [Cisco - What is Encryption? (cisco.com)](https://www.cisco.com/site/us/en/learn/topics/security/what-is-encryption.html)
- [EFF - What Should I Know About Encryption? (eff.org)](https://ssd.eff.org/module/what-should-i-know-about-encryption)
- [IBM - A Brief History of Cryptography](https://www.ibm.com/think/topics/cryptography-history)
- [Trenton Systems - Symmetric vs. Asymmetric Encryption](https://www.trentonsystems.com/en-us/resource-hub/blog/symmetric-vs-asymmetric-encryption)