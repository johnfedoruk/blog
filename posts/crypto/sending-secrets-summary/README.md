# Summary | Sending Secrets with OpenSSL

## Recap: Sending an Encrypted File

Let's send an encrypted version of `cat_video.mp4` to our friend Bob. We check his website, and we find his public RSA key. We save his public RSA key as `bob.pem`.

Note that I will be going through extra steps to use base64 here; this makes the commands a little more complex and does increase the file size. I like base64 because it make it easy to copy and paste output, but you should stick with binary for larger files. Please read earlier posts to see commands for working directly with binary output.

### Generate AES Key

We want the AES key to have 32 bits of entropy, since we're using AES 256\. We're going to save it to the file `aeskey.txt`

```bash
openssl rand -base64 32 > aeskey.txt
```

### Generate RSA Key Pair

We should have already generated our RSA key pair and have the public key published somewhere visible. We hope Bob already has our public key downloaded on his machine. Nevertheless, let's generate a 4096 bit RSA new key pair.

```bash
openssl genrsa 4096 > priv.pem
```

```bash
openssl rsa -in priv.pem -outform PEM -pubout > pub.pem
```

### Encrypting the File with AES

```bash
openssl enc -e -aes-256-cbc -in cat_video.mp4 -kfile aeskey.txt | base64 > cat_video.mp4.aes256cbc.base64
```

### Encrypting AES Key with RSA

```bash
openssl rsautl -encrypt -in aeskey.txt -inkey bob.pem -pubin | base64 > aeskey.txt.rsa.base64
```

### Creating a Digital Signature

```bash
openssl dgst -sha512 -sign priv.pem aeskey.txt | base64 > aeskey.txt.sha512.signature.base64
```

### Sending the Files

Now we just need to send the files. He needs the `aeskey.txt.rsa.base64`, `aeskey.txt.sha512.signature.base64`, and `cat_video.mp4.aes256cbc.base64` files from us, so we can just zip it. He also needs to download a copy of our public key and he needs his own private key on hand.

```bash
zip bob.zip aeskey.txt.rsa.base64 aeskey.txt.sha512.signature.base64 cat_video.mp4.aes256cbc.base64
```

## Recap: Sending an Encrypted File

Our friend Bob just sent us the following files: `cat_video.mp4.aes256cbc.base64`, `aeskey.txt.rsa.base64`, and `aeskey.txt.sha512.signature.base64`. We want to watch this secret cat video, but we also want to be sure it's not the _Silicon Valley_ Season Finally that Eve wants us to watch. We've got to decrpyt it and verify Bob as the sender. We know it's encrypted using our public key `pub.pem`, and we have a copy of Bob's public key `bob.pem`.

### Decrypting AES Key with RSA

```bash
openssl rsautl -decrypt -in <( cat aeskey.txt.rsa.base64 | base64 -d ) -inkey priv.pem > aeskey.txt
```

### Verifying the Digital Signature

```bash
openssl dgst -sha512 -verify bob.pem -signature <( cat aeskey.txt.sha512.signature.base64 | base64 -d ) aeskey.txt
```

Expect to see _Verified OK._

### Decrypting the File with AES

```bash
openssl enc -d -aes-256-cbc -in <( cat cat_video.mp4.aes256cbc.base64 | base64 -d) -kfile aeskey.txt > cat_video.mp4
```

Whoo! Now watch `cat_video.mp4` and relax!

## Summary

We want to send a file `cat_video.mp4` to a friend. We can generate the RSA key pair `pub.pem` and `priv.pem` and the AES key `aeskey.txt`. We can encrypt `cat_video.mp4` using the cipher AES 256 in CBC mode to get the file `cat_video.mp4.aes256cbc` or we can decrypt `cat_video.mp4.aes256cbc` to get back `cat_video.mp4`. We know how to encrypt the AES key using someone's RSA public key to generate `aeskey.txt.rsa`, our how to use our own RSA private key to decrypt `aeskey.txt.rsa` (encrypted with our key) to get back the plaintext `aeskey.txt`. Finally, we know how to create a signature using our private key and the plaintext message `aeskey.txt`, and we know how to verify a signature using someone else's private key and the decrypted `aeskey.txt.rsa`. We know a lot!

#### Back: [Generating a Digital Signature](../../../posts/crypto/sending-secrets-with-openssl-digital-signatures)

## Jump to

*   [Introduction](../../../posts/crypto/sending-secrets-with-openssl-getting-started)
*   [Key Generation](../../../posts/crypto/sending-secrets-with-openssl-key-generation)
*   [AES Encryption and Decryption](../../../posts/crypto/sending-secrets-with-openssl-aes-encryption-and-decryption)
*   [RSA Encryption and Decryption](../../../posts/crypto/sending-secrets-with-openssl-rsa-encryption-and-decryption)
*   [Generating a Digital Signature](../../../posts/crypto/sending-secrets-with-openssl-digital-signatures)
*   [Putting It All Together](../../../posts/crypto/sending-secrets-with-openssl-putting-it-all-together)
