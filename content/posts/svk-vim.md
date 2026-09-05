---
title: svk & vim
date: '2009-04-10T16:45:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
- SVK
- vim
---

My SCM of choice for FreeBSD-related projects is SVK (it's much faster then subversion and provides offline access to repo history, easy branching/merging and so on). And my editor of choice is vim. And it turns out vim doesn't highlight svk commit files. What a shame. No bright colors for happy hacker who is about to commit clean and robust code (or break buildworld, whatever).  So I spent 20 minutes of tweaking svn.vim and produced [this](http://www.vim.org/scripts/script.php?script_id=2604). 

It's a syntax file for svk-commit* and two functions to place file under/out of SVK control (just like in perforce one can tweak commit file to select which files will be committed and which won't).
