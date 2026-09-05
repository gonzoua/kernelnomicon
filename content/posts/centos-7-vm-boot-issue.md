---
title: CentOS 7 VM boot issue
date: '2020-01-10T22:21:01'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

If you're building OVA based on CentOS 7 and your VM is stuck during boot at "tsc: Refined TSC clocksource calibration: NNNN.MMM MHz" message do not despair! It means the kernel can not find the root device and just waits for it to pop up. Which is not going to happen. In my case, it was a lack of required drivers loaded boot-time. Following line in an OVA build script resolved the issue:

```
dracut --regenerate-all --force --add-drivers "sd_mod mptspi" 
```

This post is just a breadcrumb for people googling for the line from the boot log. Might save you some time (might save future me some time).
