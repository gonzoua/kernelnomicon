---
title: MFC tracking tool filters
date: '2016-10-29T15:20:21'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

I've just added extended filters to http://mfc.kernelnomicon.org. Now filter can be either committer, or path, or both. Path is indicated by adding @ character to the beginning, so `gonzo` means all commits by gonzo, `@sys/arm` means all commits to sys/arm directory, and `gonzo@sys/arm` means all commits by gonzo to sys/arm directory. If there are more then one expression separated by space the result will be all filters OR-ed together: `@sys/arm  @boot/efi` means all commits to sys/arm plus all commits to boot/efi directory.
