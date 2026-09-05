---
title: INTR_FAST and selwakeup
date: '2007-11-25T13:22:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
- taskqueue
- zaptel
---

I stumbled over this problem a couple of years ago, ignored it, was punished for my carelessness, came out with hackerish solution and now I know "The Right Way" to solve this issue. So, the headache starts when you're trying to wakeup userland application polling on a descriptor from INTR_FAST IRQ handler (for 7.X and later that would be interrupt filter). INTR_FAST/interrupt filter  routines are usually used for timing-critical tasks and run in IRQ dispatcher context, so no operations that may cause context switch are allowed in this code. Unfortunately [selwakeup(9)](http://www.freebsd.org/cgi/man.cgi?query=selwakeup&apropos=0&sektion=0&manpath=FreeBSD+7-current&format=html) tries to acquire sellock that leads to a possible context switch and leaves us in a total mess. My solution was handmade kernel thread that has been running through list of channels and performed all dirty work, not the cleanest and easiest to maintain code. "Zaptel-bsd take 2" utilizes [taskqueue(9)](http://www.freebsd.org/cgi/man.cgi?query=taskqueue&apropos=0&sektion=0&manpath=FreeBSD+7-current&format=html) interface to work around IRQ handler limitations. There is handler routine:

```
static void 
handle_selwakeup(void *context, int pending) 
{
    struct selinfo *sel = context;
    selwakeup(sel);
}
```

then selinfo/task structures for every channel:

```
...
    /* thingy for select stuff */
    struct selinfo  sel;    
    struct task     selwakup_task;
...
```

which should be initialized during channel allocation:

```
...
    TASK_INIT(&chan->selwakup_task, 0, 
        handle_selwakeup, &chan->sel);
...
```

and used anytime we'd like to call selwakeup:

```
...
    taskqueue_enqueue_fast(taskqueue_fast, 
        &timer->selwakup_task);
...
```
