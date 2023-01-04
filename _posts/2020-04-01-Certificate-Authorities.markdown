---
layout: post
title: "Certificate Authorities"
author: "Kevin Chang"
date: "2020-04-01 17:10:00"
excerpt_separator: <!--more-->
---

As part of the process to ensure secure communication over the public Internet, certificate authorities (CA) were created to certify and legitimize the ownership of public keys by certain entities.<!--more--> As the reader may or may not know, secure Internet traffic is produced via asymmetric encryption through the Transport Layer Security (TLS) protocol. For more information on asymmetric encryption, the reader may refer to this [blog post]({% post_url 2020-04-01-Encryption %}) on encryption. Since asymmetric encryption relies on an entity having a public key and a private key, there must be a method to verify that a certain public key is indeed owned by a certain entity. For more information on TLS, the reader may refer to this [blog post]({% post_url 2020-04-02-SSL-TLS %}) on SSL/TLS. This is where CAs come into the picture.

### Certificate Authorities (CA)

CAs are entities that issue digital certificates which certify the ownership of a public key by the named entity on the certificate. For example, an SSL certificate is a digital certificate that certifies a web server’s ownership of a particular public key which is used in the SSL/TLS protocol.

Suppose that a client browser wants to establish an SSL/TLS connection with a particular web server. The client must first verify that the web server is legitimate and not a third party attempting a man-in-the-middle attack before engaging in any communication. This verification process is through the inspection of the web server’s SSL certificate. In order for the client to trust the certificate, the certificate must be issued from a CA that the client trusts, it must contain the ownership details (person, organization, domain name) of the web server, and it must not have expired.

### Digital Certificate Creation

Disregarding the paperwork and identity verification process handled by the CA,

1. the interested entity that owns the web server will send a **Certificate Signing Request (CSR)** containing its public key and ownership details to the certificate authority.
2. After the CA does its due diligence to verify the identity of the requesting entity, the CA will encrypt the received contents using its own private key.

Encryption using the private key to encrypt the contents at this point is tantamount to putting a digital signature on the certificate because anyone with the CA’s public key can easily verify that the contents were encrypted using the matching private key which only the CA possesses; i.e. the certificate now contains the CA’s stamp of approval.

With this digitally signed digital certificate from the CA, the web server can prove its identity to respective clients and respective clients can distinguish between the legitimate web server and fake web servers trying to act like the legitimate one.

To add a bit more complexity into the picture, CAs can create a number of **intermediate certificate authorities (ICAs)** which can be used to issue end-entity certificates such as the SSL certificate previously explained. As we can therefore see, nested ICAs can create a trust hierarchy starting from the root CA. There also exists self-signed certificates in which the web server acts as its own CA. In this way, clients that want to interact with the web server must first add the created CA to their list of trusted CAs. This is commonly done during testing to expedite development.

---

|References|
|---|
|[Wikipedia](https://en.wikipedia.org/wiki/Certificate_authority)|
|[GlobalSign](https://www.globalsign.com/en/ssl-information-center/what-are-certification-authorities-trust-hierarchies)|
|[DNSimple](https://support.dnsimple.com/articles/what-is-certificate-authority/)|
|[CheapSSLSecurity](https://cheapsslsecurity.com/blog/what-is-a-certificate-authority-ca/)|
|[AboutSSL](https://aboutssl.org/certificate-authority/)|
|[YouTube](https://www.youtube.com/watch?v=T4Df5_cojAs)|
