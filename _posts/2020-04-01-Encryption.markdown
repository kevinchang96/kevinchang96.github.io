---
layout: post
title: "Encryption"
author: "Kevin Chang"
date: "2020-04-01 13:10:00"
excerpt_separator: <!--more-->
---

Encryption is the process of transforming data--that anyone can understand--into a seemingly random, incomprehensible collections of symbols. In this way, only certain people--people with access to a “secret key”--can decrypt the jumbled message and understand the original meaning of the data.<!--more--> As a result, encryption is used for the transportation of sensitive information so that only intended parties may have access to the information. 

### The Jargon

Any piece of text or data can be encrypted. The original, unencrypted text is commonly referred to as **plaintext**. On the other hand, the encrypted text is known as **ciphertext**. This name is derived from a term called a **cipher**, which is essentially the encryption algorithm used to encode and decode text; so the cipher encrypts text into ciphertext. The last piece is the encryption/decryption key, otherwise known as the **secret key**. The secret key is a generated string of characters that the cipher uses to produce a unique ciphertext. This string helps the algorithm transform the text to appear random and undecipherable.

In short, one may imagine the cipher as a black box that takes in plaintext and a secret key. With these two pieces, the cipher will generate a unique ciphertext by encrypting the input plaintext using the secret key. Inversely, one may decrypt the ciphertext using the ciper and the secret key via the black box cipher. Therefore in order to decrypt an encrypted message, one must have both the cipher and the secret key. As a result, it is oftentimes time-consuming and computationally intensive to decrypt an encrypted message without being in possession of both the secret key and the cipher.

### Types of Encryption

Encryption can be divided into two categories: **symmetric** and **asymmetric**. The category names refer to the process in which the encryption key is used. In symmetric encryption, also known as **secret key encryption**, there is only one secret key shared among all authorized parties. In other words, all parties must use the single secret key to encrypt and decrypt messages. As a result of the simplicity of this method, symmetric encryption is faster than asymmetric encryption. However, trouble may arise if an unauthorized third party comes into possession of the secret key. With symmetric encryption, there exists a chicken-and-the-egg problem wherein one chooses to use encryption in order to keep the transfer of data private among intended individuals. However in order for other parties to decrypt the encrypted messages, they must first possess the secret key. So how does one safely distribute the secret key to certain individuals without worrying about third party interference? The answer to this problem lies with asymmetric encryption.

Unlike symmetric encryption, asymmetric encryption relies on two keys. Each individual will have their own **public key** and **private key**; this is why asymmetric encryption is also known as **public key encryption**. Though the keys are different, they are logically linked such that a message encrypted using the public key can only be decrypted by the matching private key. Moreover as the names suggest, the public key, though owned by the individual, can be shared among all individuals. In the same vein, the private key should be kept private. The private nature of the private key is instrumental to the success of this encryption scheme, as we will later see.

Furthermore, secure communication between parties can be accomplished with the exchange of the public keys of the parties involved. A message intended for a particular party is first encrypted using their public key. In this way, the only way for the message to be decrypted is via the matching private key. This is why it is essential to keep the private key private. Once the intended party receives the ciphertext, the ciphertext can be decrypted with the appropriate private key. Any third party that happens to intercept the encrypted message has no way to decrypt the message without the matching private key, unless through brute-force methods which are time-consuming and unreliable. So we see that the added complexity of asymmetric encryption brings added security where symmetric encryption lacked, however the extra steps that must be made cause this encryption scheme to perform slower than its symmetric counterpart.

If security and the safe transport of sensitive or private information is important, then some form of encryption is likely used. The most commonly used symmetric encryption cipher is the **Advanced Encryption Standard (AES)** while **Rivest-Shamir-Adleman (RSA)** is the most commonly used asymmetric encryption cipher. Not only do these ciphers protect data in-transit (over the Internet), but there is nothing stopping individuals from encrypting data at-rest (stored) as long as the secret key is stored in a safe secure area.

---

|References|
|---|
|[Wikipedia](https://en.wikipedia.org/wiki/Encryption)|
|[SearchSecurity TechTarget](https://searchsecurity.techtarget.com/definition/encryption)|
|[CloudFare](https://www.cloudflare.com/learning/ssl/what-is-encryption/)|
|[Norton](https://us.norton.com/internetsecurity-privacy-what-is-encryption.html)|
|[DigitalGuardian](https://digitalguardian.com/blog/what-data-encryption)|
