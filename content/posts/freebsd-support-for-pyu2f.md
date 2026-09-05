---
title: FreeBSD support for pyu2f
date: '2019-09-22T13:39:41'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

After long hiatus (because $JOB) I'm trying to find some time to spend on FreeBSD-related projects, looking for small ones that can be done over weekend or a bit more. One of the ideas came from Ed Maste's twitter: implement FreeBSD support for pyu2f. Since I already spent some time working on FreeBSD U2F support for Chromium it felt like a good small project.

The challenging part of the project was not U2F/HID but interfacing ioctl with Python, something I have never done before. It wasn't super complex and I learned about Python's ctype module. 

Even more challenging though was to find a code to verify the implementation. Turned out there was no script to run end-to-end test. The closest I managed to find was [this code](https://xpra.org/trac/attachment/ticket/1789/u2f_auth.py) in Xpra project. I used it as a base to write following test that registers app and then signs a pseudo challenge. It only verifies the interface part and doesn't care about the actual signatures/keys:

```
import os
from pyu2f import u2f
from pyu2f import model

ORIGIN = 'https://kernelnomicon.org'
APP_ID = 'wordpress'
REGISTRATION_DAT = 'registration.dat'

device = u2f.GetLocalU2FInterface(ORIGIN)

# Try to register new app or read saved registration data if it exists
if os.path.exists(REGISTRATION_DAT):
    with open(REGISTRATION_DAT, 'rb') as f:
        rd = f.read()
else:
    r = device.Register(APP_ID, b'ABCD', [])
    rd = r.registration_data
    with open(REGISTRATION_DAT, 'wb+') as f:
        f.write(rd)

# extract public key, key handle length, and key handle
pubkey = bytes(rd[1:66])
# this is for Python3, use ord(rd[66]) for Python2
khl = rd[66]
key_handle = bytes(rd[67:67 + khl])

# Try to authenticate
key = model.RegisteredKey(key_handle)
response = device.Authenticate(APP_ID, b'012345678', [key])
print (response.signature_data)
print (response.client_data)
```

The final result is in [my fork of pyu2f repo](https://github.com/gonzoua/pyu2f/tree/freebsd), on **freebsd** branch.
