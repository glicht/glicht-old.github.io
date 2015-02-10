---
layout: post
title: "Strace an Existing Multi-threaded Process"
modified:
categories:
excerpt:
tags: [Linux]
image:
  feature:
date: 2015-02-10T05:42:13-05:00
---

I use strace from time to time for debugging and analysis on Linux. It is a powerful tool that can easily help understand what a process is doing. Many times you would want to connect strace to an existing process. This is done easily with the `-p <pid>` option. If you need to strace a multi-threaded process then there is the `-f` option, but this only connects strace to new threads and not to existing ones. If you need to strace an existing multi-threaded process (such as a java server) then you need to pass each thread id to strace using the `-p` option.

Linux provides an easy way to find the thread ids for a process by running the following command:

    ls /proc/<pid>/task/

Another way to get the thread ids is via `ps`:

    ps -L -p <pid> -o lwp=

And here is a one liner that starts strace to monitor an existing process and all its threads:

    strace -f -o strace.out `ls /proc/<pid>/task/ | xargs -n 1 echo -n " -p"`

Hope this helps out. I find this one liner quite useful at times.
