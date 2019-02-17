# RSA | Sending Secrets with OpenSSL

## RSA Encryption

In [Key Generation](../../../posts/crypto/sending-secrets-with-openssl-key-generation) we generated our `pub.pem` and `priv.pem` key pair. Now we want to be able to use public key for encryption. Let's get started.

RSA encryption requires that the message we're encrypting not exceed the key size. This, along with performance, are among the most important reasons why we use AES to encrypt files, and RSA to encrypt the AES keys. Since we generated the AES key `aeskey.txt`, and used that to encrypt the file `cat_video.mp4` in [AES Encryption and Decryption](../../../posts/crypto/sending-secrets-with-openssl-aes-encryption-and-decryption), we should continue forward with encrypting the small file `aeskey.txt`.

Encryption with RSA has the following command format

```bash
openssl rsautl -encrypt -in [plaintext file] -inkey [pub key] -pubin > [output file]
```

Or to output in base64

```bash
openssl rsautl -encrypt -in [plaintext file] -inkey [pub key] -pubin | base64 > [output file]
```

So to encrypt `aeskey.txt` to the binary file `aeskey.txt.rsa`

```bash
openssl rsautl -encrypt -in aeskey.txt -inkey pub.pem -pubin > aeskey.txt.rsa
```

Or to encrypt `aeskey.txt` to the base64 file `aeskey.txt.rsa.base64`

```bash
openssl rsautl -encrypt -in aeskey.txt -inkey pub.pem -pubin | base64 > aeskey.txt.rsa.base64
```

## RSA Decryption

Now that we have encrypted a small file in RSA using our public key pub.pem, let's decrypt it using our private key priv.pem.

Decryption or a binary file occurs as follows

```bash
openssl rsautl -decrypt -in [ciphertext file] -inkey [priv key] > [output file]
```

Or to decrypt a base64 file, we must first decode it in a named pipe and pass the returned file descriptor to the -in flag

```bash
openssl rsautl -decrypt -in <( cat [ciphertext file] | base64 -d ) -inkey [priv key] > [output file]
```

So to decrypt `aeskey.txt.rsa` using `priv.pem`

```bash
openssl rsautl -decrypt -in aeskey.txt.rsa -inkey priv.pem > aeskey.txt
```

Alternatively, to decrypt `aeskey.txt.rsa.base64` using `priv.pem`

```bash
openssl rsautl -decrypt -in <( cat aeskey.txt.rsa.base64 | base64 -d ) -inkey priv.pem >\r\n aeskey.txt
```

#### Next: [Generating a Digital Signature](../../../posts/crypto/sending-secrets-signatures)

#### Back: [AES Encryption and Decryption](../../../posts/crypto/sending-secrets-aes-crypto)

## Jump to

* [Introduction](../../../posts/crypto/sending-secrets-getting-started)
* [Key Generation](../../../posts/crypto/sending-secrets-key-generation)
* [AES Encryption and Decryption](../../../posts/crypto/sending-secrets-aes-crypto)
* [RSA Encryption and Decryption](../../../posts/crypto/sending-secrets-rsa-crypto)
* [Generating a Digital Signature](../../../posts/crypto/sending-secrets-signatures)
* [Putting It All Together](../../../posts/crypto/sending-secrets-summary)
