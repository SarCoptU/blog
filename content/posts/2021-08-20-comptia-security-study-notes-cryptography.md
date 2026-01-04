---
title: 'CompTIA Security + Study Notes: Cryptography'
author: SarCoptU
date: 2021-08-20T09:41:27+00:00

categories:
 - IT/Cybersec
tags:
 - ComTIA Security+
 - cryptography
 - cybersecurity
---
**Cryptography** is as old as the desire of humans to communicate secretly. A famous example is that of the so-called Caesar cypher, named after Julius Caesar. He used to encrypt his correspondence by shifting the letters of the alphabet to the right or left an agreed-upon number of spaces. 

<div class="wp-block-image is-style-default">
 <figure class="aligncenter size-full"><img data-recalc-dims="1" loading="lazy" decoding="async" width="480" height="480" src="/uploads/2021/09/480px-CipherDisk2000.jpg" alt="Cypher disc" class="wp-image-269" sizes="auto, (max-width: 480px) 100vw, 480px" /><figcaption>Cypher disc</figcaption></figure>
</div>

<p class="has-text-align-center" style="font-size:12px">
 Image Credit: Cypher disc By Hubert Berberich (HubiB) &#8211; Own work, Public Domain, <a rel="noreferrer noopener" href="https://commons.wikimedia.org/w/index.php" target="_blank">https://commons.wikimedia.org/w/index.php
</p>

Another example is the Enigma machine used during World War II by the germans for secret military communications. The device used an electromechanical rotor system to scramble the letters of the alphabet.

<div class="wp-block-image is-style-default">
 <figure class="aligncenter size-full"><img data-recalc-dims="1" loading="lazy" decoding="async" width="440" height="480" src="/uploads/2021/09/440px-Enigma_crittografia_-_Museo_scienza_e_tecnologia_Milano.jpg" alt="Enigma machine" class="wp-image-270" sizes="auto, (max-width: 440px) 100vw, 440px" /><figcaption>Enigma machine</figcaption></figure>
</div>

<p class="has-text-align-center" style="font-size:12px">
 Image credit: Enygma Machine By Alessandro Nassiri &#8211; Museo della Scienza e della Tecnologia &#8220;Leonardo da Vinci&#8221;, CC BY-SA 4.0, <a href="https://commons.wikimedia.org/w/index.php" target="_blank" rel="noreferrer noopener">https://commons.wikimedia.org/w/index.php
</p>

Cryptography is a vast domain of study, and this blogpost will only contain some definitions and basic understanding of the field, mainly in the context of CompTIA Security + certification.

## Definitions {.wp-block-heading}

The computer can read-only **binary**, meaning a long list of 0s and 1s. The context that we give to the computer helps to give meaning to the 0s and 1s, like numbers, text, photo, video or computer code, all referred to as **data**.

**Plaintext** represents the data that computers and humans can read. **Cyphertext** is any data that is made unreadable. The process of transforming data from plaintext to ciphertext is called **encryption**. The reverse process is **decryption**. 

An **algorithm** or **cypher** is a mathematical formula used to encrypt and decrypt data using a variable known as a **key**. If we take the Caesar cypher as an example, the algorithm is the character substitution, and the key is the actual number of spaces the alphabet is shifted by.

The cypher might process a single bit of data at a time, a technique called a **stream** cypher or a block of data, a **block** cypher. 

## Keys and how to use them {.wp-block-heading}

Keys are fundamental for algorithm implementations. The longer and more complex the key, the more secure the algorithm is, i.e. a key based on a password. Even if malicious users know the algorithm used, they cannot decrypt any information without the cryptographic key. 

If the same key is used to encrypt and decrypt the data, the system **uses a symmetric**, **secret key**, **private key** or **shared secret** cryptography. For example, imagine a safe deposit box: if someone is trying to leave some secure information for you, she needs the key to the box. Likewise, as the receiver, you will need the same key to get access to the information.

<div class="wp-block-image is-style-default">
 <figure class="aligncenter size-full"><img data-recalc-dims="1" loading="lazy" decoding="async" width="640" height="359" src="/uploads/2021/09/640px-Symmetric_encryption.png" alt="Symmetric cryptography graphic" class="wp-image-272" sizes="auto, (max-width: 640px) 100vw, 640px" /><figcaption>Symmetric cryptography</figcaption></figure>
</div>

<p class="has-text-align-center" style="font-size:12px">
 Symmetric cryptography By Munkhzaya Ganbold &#8211; Own work, CC BY-SA 4.0, <a href="https://commons.wikimedia.org/w/index.php" target="_blank" rel="noreferrer noopener">https://commons.wikimedia.org/w/index.php
</p>

If different keys, usually **public** key for encryption and **private** for decryption, are used, the system uses asymmetric or public-key cryptography. Let&#8217;s take a postbox as an example here: anyone with your postbox (public key) location can leave secure mail in the box, but only the receiver who has the key to the postbox (private key) has access to the mail.

<div class="wp-block-image is-style-default">
 <figure class="aligncenter size-full"><img data-recalc-dims="1" loading="lazy" decoding="async" width="640" height="406" src="/uploads/2021/09/640px-Orange_blue_public_key_cryptography_en.svg_.png" alt="Asymmetric cryptography graphic" class="wp-image-274" sizes="auto, (max-width: 640px) 100vw, 640px" /><figcaption>Asymmetric cryptography</figcaption></figure>
</div>

<p class="has-text-align-center" style="font-size:12px">
 Image Credit: Asymmetric cryptography By Bananenfalter &#8211; Own work, CC0, <a rel="noreferrer noopener" href="https://commons.wikimedia.org/w/index.php" target="_blank">https://commons.wikimedia.org/w/index.php
</p>

## How does this all work? {.wp-block-heading}

To better understand the cryptographic process, we need to define a few more terms:

**Confidentiality** refers to the assurance that other parties have not read the message. This is usually achieved by encryption. 

**Integrity** refers to the assurance that the message has not been changed during transport. The sender creates a &#8220;digital fingerprint&#8221; of the message or a **hash** that verifies the message has not been modified. The hashing process is irreversible and one-way, meaning the initial message cannot be recreated from the resulting hash. The receiver gets the message and the hash and processes the message through the same hashing algorithm, and compares the two hashes. If they are the same, integrity is assured.

**Authentication** is the process of proving the identity of a user to get access to confidential data. It usually involves a password (something a user knows) but might involve some extra factors like ID cards, security tokens (something a user has), or biometric identifiers (something a user is) in a process called **multifactor authentication**. 

Asymmetric cryptography is used for **digital** **signatures**, which implies that for the receiver to be sure the document comes from the named sender, the sender will sign the document with his private key. The receiver can check the signature with the sender&#8217;s public key. With this proof, the sender cannot deny she sent the message, and the receiver cannot deny she received the data (**nonrepudiation**).<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="406" src="/uploads/2021/09/digital-signature.png" alt="Digital signature" class="wp-image-275" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>Digital signature</figcaption></figure> 

<p class="has-text-align-center" style="font-size:12px">
 Image Credit: Digital signature <a rel="noreferrer noopener" href="https://i.stack.imgur.com/Q0a0u.png" target="_blank">https://i.stack.imgur.com/Q0a0u.png</a>
</p>

The implementations of asymmetric cryptography tend to be quite computationally intensive, and, in the case of symmetric cryptography, the problem is the **key exchange**, or how do you get the secret key to the intended receiver without interception by bad actors. 

So modern systems need to balance usability and privacy and use a combination of symmetric and asymmetric cryptography topped up by hashing and digital certificates.

This blog post is just an introduction to the introduction into the cryptography field, a very complex and mathematically intensive area of cybersecurity. This blog post attempted to make some of the definitions of cryptography easier to grasp and help out while preparing for CompTIA Security + certification.

## References {.wp-block-heading}

<ul class="wp-block-list">
 <li>
 Weiss, M. (2021). CompTIA Security+ SY0-601 exam cram. Hoboken, Nj: Pearson.
 </li>
 <li>
 Mike Meyers’ CompTIA Security+ Certification Guide, Third Edition (Exam SY0-601), 3rd Edition. (n.d.). Mcgraw-Hill.
 </li>
 <li>
 Wm Arthur Conklin, White, G., Cothren Cothren, L Roger Davis and Williams, D. (2021). CompTIA Security+ All-in-One Exam Guide, Sixth Edition (Exam SY0-601) /. New York, N.Y.: Mcgraw-Hill Education.
 </li>
 <li>
 https://en.wikipedia.org/wiki/Enigma_machine
 </li>
 <li>
 Image by <a href="https://www.flickr.com/photos/110751683@N02/13334048894/in/photolist-mjhubJ-4uwyQL-2MShu-5qXF77-fdsWEz-ibyAjN-82yEZ1-fEtokK-CJhVy-8Tqysh-8Tnr1n-8TqwwL-fEKF4m-fEKF3W-fEKF3J-fEt6aR-fEt6bg-fEt6bi-fEKF2J-fEt6aK-cXXqXy-8Tnk1g-9fFxN-8Tqqum-4usG5p-cvNwF3-iuSuaX-nzUL62-8TnkqM-7xBBNQ-4Ubsk3-bCWVn-7MHKyR-cFhFbE-4DWodH-dxdmTX-io1t3T-7sNKjK-7ULkET-dpu5tk-jG3PyA-fLiRtR-2bQLa-2bQKu-omBrsw-ncPteb-2bQUq-nLZ4io-fw1RkU-nzLKiB">Yuri Samoilov/Flickr</a>
 </li>
</ul>

‌

‌

‌
