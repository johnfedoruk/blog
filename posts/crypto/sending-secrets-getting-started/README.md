# Getting Started | Sending Secrets with OpenSSL

## What is OpenSSL

OpenSSL is an open source cryptographic library developed for secure socket layer (SSL)/transport layer security (TLS). OpenSSL provides a command line tool suite used as the foundation for encryption in many applications. We're going to be taking advantage of OpenSSL's random number generator, it's AES cipher, it's RSA cipher, and its code signing functionality.

## Platforms

It's available on all platforms (Windows, Mac, and Linux), though this series of posts will be working specifically on Linux. OpenSSL should come preinstalled on all modern *NIX systems (Linux and Mac), though it shouldn't be too difficult to install in Windows (remember to set the Environment Path to the binary executables to get it to run in the CMD window). The basic set of OpenSSL commands should all be the same, though this series will explore using named pipes and other platform specific commands and operators for increased productivity. Please be creative and imagine better ways to do things than are described here.

## Sending Secrets

Putting on a trenchcoat, meeting someone in the park, and exchanging encryption keys is a great way to establish a secure line of communications. However, we sometimes want to be able to open encrypted channels without wanting to first travel the globe to get the encryption keys.

Our goal is going to be to be able to send an encrypted file with someone we've never previously shared any secret keys with. We'll start by generating an AES key and then we'll use that key to encrypt a file. Now we've got an encrypted file and an encryption key. The file is safe to send, but we need to securely send the key.

We check the recipient's website and find his public RSA key. We use this public key to encrypt the AES key. We then want to convince the recipient that he's receiving this file from us and not an impersonator, and that the file hasn't been modified.

We can create a digital signature to identify ourselves. We do this by creating a digest hash from the AES key and, using that and our own private RSA key, create a digital signature. We must also ensure that our recipient knows how to find our public key; it should be visible on our blog, website, or some sort of public site. Our recipient can then use our public key to verify the digital signature was generated by us, identifying us as the real sender with the added benefit of validating the AES encryption key against tampering.

We can then send off the encrypted file and the encrypted AES encryption key with our digital signature. The recipient should then be able to use their private key to decrypt the AES key, use our public key and the digital signature to validate the AES key was encrypted by us, then use the AES key to decrypt the file. We have successfully sent a secret message!

#### Next: [Key Generation](/posts/sending-secrets-with-openssl-key-generation)

## Jump to

* [Introduction](/posts/sending-secrets-with-openssl-getting-started)
* [Key Generation](/posts/sending-secrets-with-openssl-key-generation)
* [AES Encryption and Decryption](/posts/sending-secrets-with-openssl-aes-encryption-and-decryption)
* [RSA Encryption and Decryption](/posts/sending-secrets-with-openssl-rsa-encryption-and-decryption)
* [Generating a Digital Signature](/posts/sending-secrets-with-openssl-digital-signatures)
* [Putting It All Together](/posts/sending-secrets-with-openssl-putting-it-all-together)
