---
layout: post
title:  digital signature using pycrypto
date:   2016-04-07 09:06:00 +0800
categories: articles
---

pycrypto is a good python encryption and decryption lib.

use the digital signature you can safely convey script or message to your client.
the pycrypto lib already contains the digital signature sign and verify method.

this tutorial contains:

- manage the key
- sign the text
- verify the text

### manage the key

you can use the exportKey to export the public and private key:

{% highlight python %}

from Crypto.Hash import SHA256
from Crypto.PublicKey import RSA
from Crypto.Signature import PKCS1_v1_5
from Crypto import Random


random_generator = Random.new().read

key = RSA.generate(1024, random_generator)

print 'can sign: ', key.can_sign()
print 'has private: ', key.has_private()
print 'can encrypt: ', key.can_encrypt()

print 'public key: ', key.publickey().exportKey('PEM')
print 'private key: ', key.exportKey('PEM')

{% endhighlight %}

### sign and verify a message

- import the key
- create the signer or verifier
- sign or verify

{% highlight python %}

def sign(private_key, text):
  """sign the text with private key"""
  key = RSA.importKey(private_key)
  _hash = SHA256.new(text)
  signer = PKCS1_v1_5.new(key)
  return signer.sign(_hash)


def verify(public_key, text, signature):
  """verify the signature"""
  key = RSA.importKey(public_key)
  _hash = SHA256.new(text)
  verifier = PKCS1_v1_5.new(key)
  return verifier.verify(_hash, signature)


if __name__ == '__main__':
  text = 'this message send from bob.'
  public_key = key.publickey().exportKey('PEM')
  private_key = key.exportKey('PEM')
  signature = sign(private_key, text)
  assert verify(public_key, text, signature) == True

{% endhighlight %}

## reference

[pycrypto tutorial](http://www.laurentluce.com/posts/python-and-cryptography-with-pycrypto/)
[pycrypto doc](https://www.dlitz.net/software/pycrypto/api/current/Crypto-module.html)
