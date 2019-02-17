# AES | Sending Secrets with OpenSSL

## AES Encryption

We've got our new key `aeskey.txt` that we generated using OpenSSL. Now we want to  encrypt the file `cat_video.mp4`. We're going to use AES 256 for this, and we're going to use the CBC cipher mode. You can use a different cipher mode, but CBC is the most widely used.

Encrypting a file with is done with the following command pattern

```bash
openssl enc -e -[cipher] -in [plaintext file] -kfile [key file] > [out file]
```

Note that we may want to save the file in base64, so we'd pipe the output into the `base64` utility before saving the file

```bash
openssl enc -e -[cipher] -in [plaintext file] -kfile [key file] | base64 > [out file]
```

Now, in order to encrypt our file `cat_video.mp4` using AES 256 in CBC mode, using our password in `aeskey.txt`, we'd run the following

```bash
openssl enc -e -aes-256-cbc -in cat_video.mp4 -kfile aeskey.txt > cat_video.mp4.aes256cbc
```

Or to save it as base64

```bash
openssl enc -e -aes-256-cbc -in cat_video.mp4 -kfile aeskey.txt | base64 > cat_video.mp4.aes256cbc.base64
```

## AES Decryption

Okay, so we've encrypted a file `cat_video.mp4` as the binary file cat_video.mp4.aes256cbc or as the base64 file `cat_video.mp4.aes.256cbc.base64`. How can it be decrypted?

Decrypting the binary version requires the following command

```bash
openssl enc -d -[cipher] -in [ciphertext file] -kfile [key file] > [out file]
```

And decrypting a base64 file requires us first create a binary file. We're going to use a one-liner that creates a temporary file descriptor holding the binary version of the file using a named pipe. (Note that on Mac, you must use -D as the decode flag for base64).

```bash
openssl enc -d -[cipher] -in <( cat [ciphertext file] | base64 -d ) -kfile [key file] > [out file]
```

So to decrypt `cat_video.mp4.aes256cbc` using `aeskey.txt`

```bash
openssl enc -d -aes-256-cbc -in cat_video.mp4.aes256cbc -kfile aeskey.txt > cat_video.mp4
```

Or to decrypt `cat_video.mp4.aes256cbc.base64` using `aeskey.txt`

```bash
openssl enc -d -aes-256-cbc -in <( cat cat_video.mp4.aes256cbc.base64 | base64 -d) -kfile aeskey.txt > cat_video.mp4
```

#### Next: [RSA Encryption and Decryption](../../../posts/crypto/sending-secrets-with-openssl-rsa-encryption-and-decryption)

#### Back: [Key Generation](../../../posts/crypto/sending-secrets-with-openssl-key-generation)

## Jump to

*   [Introduction](../../../posts/crypto/sending-secrets-with-openssl-getting-started)
*   [Key Generation](../../../posts/crypto/sending-secrets-with-openssl-key-generation)
*   [AES Encryption and Decryption](../../../posts/crypto/sending-secrets-with-openssl-aes-encryption-and-decryption)
*   [RSA Encryption and Decryption](../../../posts/crypto/sending-secrets-with-openssl-rsa-encryption-and-decryption)
*   [Generating a Digital Signature](../../../posts/crypto/sending-secrets-with-openssl-digital-signatures)
*   [Putting It All Together](../../../posts/crypto/sending-secrets-with-openssl-putting-it-all-together)
