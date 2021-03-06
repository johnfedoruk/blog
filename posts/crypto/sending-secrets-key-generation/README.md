# Key Generation | Sending Secrets with OpenSSL

## AES Key Generation

There's tons of ways to generate random keys. Using bits from a series of coin flips, executing cat on `/dev/random` or `/dev/urandom`, or even making one up (probably not a good idea, actually). But since we're using OpenSSL for all this crypto, let's just stick to that.

OpenSSL provides a simple utility for generating keys. AES 256 has a 2<sup>256</sup> bit keyspace, which correlates to a 32 byte key, so we're going to want to produce a key with 32 bytes of entropy. We can output this key in base64, hex, or binary. I personally don't like binary, as it makes sharing and visual inspection of the files difficult. Base64 stores more information per character, so let's use that.

To generate the key, we're going to use the command

```bash
openssl rand -[base64 | hex] [bytes] > [output file]
```

So, knowing we want 32 bytes in base64, run

```bash
openssl rand -base64 32 > aeskey.txt
```

Great! We've just generated our AES key and saved it into the file `aeskey.txt`

## RSA Key Pair Generation

RSA is an asymmetric encryption cipher. This means that it requires a public key for encryption and a private key for decryption. These keys must not be arbitrary in the context to each other, thus requiring the public key be derived from the private key. We'll begin by generating a private key, and then using it to extract a public key. We're going to generate a 4096bit key, though NIST suggests that 2048bit RSA should be secure until 2030.

To generate a private key, use the following command

```bash
openssl genrsa [keysize] > [output file]
```

Generating the 4096bit public key in PEM format. Note that we won't be encrypting the key.

```bash
openssl genrsa 4096 > priv.pem
```

To export the public key from the private key, use

```bash
openssl rsa -in [private key] -outform PEM -pubout > [output file]
```

So to generate a public key from the `priv.pem` key

```bash
openssl rsa -in priv.pem -outform PEM -pubout > pub.pem
```

Okay, now we've got our key pair: `priv.pem` and `pub.pem`

#### Next: [AES Encryption and Decryption](../../../posts/crypto/sending-secrets-aes-crypto)

#### Back: [Introduction](../../../posts/crypto/sending-secrets-getting-started)

## Jump to

* [Introduction](../../../posts/crypto/sending-secrets-getting-started)
* [Key Generation](../../../posts/crypto/sending-secrets-key-generation)
* [AES Encryption and Decryption](../../../posts/crypto/sending-secrets-aes-crypto)
* [RSA Encryption and Decryption](../../../posts/crypto/sending-secrets-rsa-crypto)
* [Generating a Digital Signature](../../../posts/crypto/sending-secrets-signatures)
* [Putting It All Together](../../../posts/crypto/sending-secrets-summary)

