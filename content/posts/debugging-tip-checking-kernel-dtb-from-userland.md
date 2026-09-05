---
title: 'Debugging tip: checking kernel DTB from userland'
date: '2016-12-01T17:23:08'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

You can get source for current kernel FDT blob by running following command

```
sysctl -b hw.fdt.dtb | dtc -I dtb -O dts
```

Edit 27/10/2017: added -O option, looks like it's required in newer dtc
