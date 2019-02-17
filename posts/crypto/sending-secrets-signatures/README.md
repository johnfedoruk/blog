# Signatures | Sending Secrets with OpenSSL

## Generating a Digital Signature

Okay, so we created an AES key and RSA key pair, we encrypted a file using AES, we encrypted the AES key using RSA, and now we need to create a digital signature using our private key to prove the AES key (and by extension, the encrypted file) originated from us.  OpenSSL provides us with this ability.

Creating the signature is done by creating a hash digest of the plaintext `aeskey.txt` file, and then basically using the private key `priv.pem` to encrypt the digest. The recipient decrypts `aeskey.txt.rsa` using their own private RSA key, then they use our public key to decrypt the signature. They compare the digest of the decrypted `aeskey.txt` with the decrypted signature - these values should match! That sounds like a lot of work, but the commands for doing this are relatively straight forward.

Creating a digital signature with a binary output

```bash
openssl dgst -sha512 -sign [priv key] [plaintext file] > [output file]
```

Creating a digital signature with base64 encoding

```bash
openssl dgst -sha512 -sign [priv key] [plaintext file] | base64 > [output file]
```

Using `priv.pem` and `aeskey.txt`

```bash
openssl dgst -sha512 -sign priv.pem aeskey.txt > aeskey.txt.sha512.signature
```

Using `priv.pem` and `aeskey.txt` with base64 output

```bash
openssl dgst -sha512 -sign priv.pem aeskey.txt | base64 > aeskey.txt.sha512.signature.base64
```

## Verifying a Digital Signature

Okay, we've been sent the digital signature file `aeskey.txt.sha512.signature` (binary) or `aeskey.txt.sha512.signature.base64` (base64) and an encrypted file which we decrypted using our private key to yield `aeskey.txt`. We know the sender's public key to be `pub.pem`. Now we need to ensure that they're the actual senders of this file.

Verifying a binary digital signature looks like this

```bash
openssl dgst -sha512 -verify [pub key] -signature [signature file] [plaintext file]
```

If the digital signature is in base64, it'll look like this instead

```bash
openssl dgst -sha512 -verify [pub key] -signature <( cat [signature file] | base64 -d ) [plaintext file]
```

Given the decrypted file `aeskey.txt` and our the sender's public key `pub.pem`, we'd verify a binary signature `aeskey.txt.sha512.signature` with

```bash
openssl dgst -sha512 -verify pub.pem -signature aeskey.txt.sha512.signature aeskey.txt
```

Or if the signature file `aeskey.txt.sha512.signature.base64` is base64, we'd verify with

```bash
openssl dgst -sha512 -verify pub.pem -signature <( cat aeskey.txt.sha512.signature.base64 | base64 -d ) aeskey.txt
```

Either way, we should expect the output to be _Verified OK_.

#### Next: [Putting It All Together](../../../posts/crypto/sending-secrets-summary)

#### Back: [RSA Encryption and Decryption](../../../posts/crypto/sending-secrets-rsa-crypto)

## Jump to

* [Introduction](../../../posts/crypto/sending-secrets-getting-started)
* [Key Generation](../../../posts/crypto/sending-secrets-key-generation)
* [AES Encryption and Decryption](../../../posts/crypto/sending-secrets-aes-crypto)
* [RSA Encryption and Decryption](../../../posts/crypto/sending-secrets-rsa-crypto)
* [Generating a Digital Signature](../../../posts/crypto/sending-secrets-signatures)
* [Putting It All Together](../../../posts/crypto/sending-secrets-summary)
