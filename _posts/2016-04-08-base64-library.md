---
layout: post
title:  python base64 library
date:   2016-04-08 15:00:00 +0800
categories: articles
---

The base64, base32, and base16 encodings convert 8 bit bytes to values with 6,
5, or 4 bits of useful data per byte, allowing non-ASCII bytes to be encoded as
ASCII characters for transmission over protocols that require plain ASCII, such
as SMTP. The base values correspond to the length of the alphabet used in each
encoding. There are also URL-safe variations of the original encodings that use
slightly different results.


{% highlight python %}

import base64
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
    signature = base64.b64encode(signature)
    assert verify(public_key, text, base64.b64decode(signature)) == True

{% endhighlight %}

this example show you how to make a digital signature of the text you want to send,
the problem is how to send the digital signature through the network, errors will
occur if you don't deal with it. a way is to use the base64 python lib to help you
deal this problem.

## reference

[PyMOTW](https://pymotw.com/2/base64/)
