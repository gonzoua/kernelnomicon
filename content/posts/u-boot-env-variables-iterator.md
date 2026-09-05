---
title: U-Boot env variables iterator
date: '2012-11-26T15:26:49'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Ran into it recently and decided to post here just in case someone will have this problem too. Do not define both CONFIG_LOADADDR and loadaddr in CONFIG_EXTRA_ENV_SETTINGS. Otherwise environment variables enumeration API(API_ENV_ENUM) will loop forever.
