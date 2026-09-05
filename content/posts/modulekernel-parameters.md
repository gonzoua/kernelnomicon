---
title: Module/kernel parameters
date: '2007-11-20T20:58:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
- kernel
- module parameters
- tunables
---

Sometimes it's desirable to pass some arguments to module to customize its behavior, e.g.: to set/unset verbosity level of debug output, set operation mode etc... Linux modules can get this information from insmod utility, but kldload is not capable of doing such kind of things. What a pity. But don't get desperate - tunables to the rescue!

Just like an ordinary command shell (bash, csh, sh) kernel has its own environment, the set of <name, value> pairs. You can get, set, test, unset these variables using **getenv**, **setenv**, **testenv**, **unsetenv** functions in the kernel and [**kenv(2)**](http://www.freebsd.org/cgi/man.cgi?query=kenv&sektion=2&apropos=0&manpath=FreeBSD+7-current) syscall or [**kenv(1)**](http://www.freebsd.org/cgi/man.cgi?query=kenv&sektion=1&apropos=0&manpath=FreeBSD+7-current) command in userland. So if you want to set verbosity level for module, you would do something like this:

```
    # kenv zaptel.debug=1
    # kldload ./zaptel.ko
```

and then in module initialization routine:

```
    static int debug = 0; /* Hush-hush */
    ...
    char * value = getenv("zaptel.debug");
    if (value) {
        debug = strtol(value, NULL, 10);
        freeenv(value);
    }
```

 

Too much code for such a simple task, don't you think? Yeah, like for every common task there are useful macroses defined in kernel headers, you should just find them. [Heavy coffeinated kernel hackers](http://www.userfriendly.org/cartoons/archives/00sep/uf002235.gif) knew most of them. In this particular case neat code would look something like that (for statically initialized variable):

```
    static int debug = 0; /* Hush-hush */
    TUNABLE_INT("zaptel.debug", &debug);
```

or, if debug is the member of structure or local variable:

```
    sc->debug = 0;
    TUNABLE_INT_FETCH("zaptel.debug", &sc->debug);
```

TUNABLE_XXX macroses exist for INT, LONG, ULONG and STR types. TUNABLE_STR unlike the others requires third parameter - maximum size of the string. For a dynamic value retrieval every type has TUNABLE_XXX_FETCH macro defined. Nota bene: if there is no environent variable set with requested name, the value of acceptor variable remains untouched.
