---
layout: post
title: Linux下程序如何检测自己是否正在被调试
date: 2013-08-03 17:00:0
category: 技术
tags: [Linux, GDB, Programming]
---
{% include JB/setup %}

在Windows下，程序可以用以下API函数检测当前进程是否正在被调试：

    int debugger_present;
    HANDLE process = GetCurrentProcess();
    CheckRemoteDebuggerPresent(process, &debugger_present);

但是在Linux下如何实现呢？

<!--more-->
Linux下同样有检测当前进程是否正在被调试的需求。我最终从以下三种方法中选取了自认为比较适合的一种。

####方法一

    int has_debugger()
    {
        int debugger;
        if (ptrace(PTRACE_TRACEME, 0, NULL, NULL) == 0) {
            // return 0 means success, so no debugger is attached
            debugger = 0;
        } else {
            debugger = 1;
        }
        return debugger;
    }
    
被调试的程序会在依附调试器的时候调用ptrace(PTRACE_TRACEME, 0, NULL, NULL)（顾名思义就是请求trace自己）；而这个函数只能被调用一次，后续的调用均会失败，返回非零值。上述方法就是利用了这一点。显式地调用一次该函数，如果成功（返回0），则说明之前没调用过，说明没有调试器依附；反之，若失败，说明之前已经调用过，进程正处于调试中。

该方法很不错，可惜存在一个问题，而且恰好在我的程序中出现了。如果程序P在执行上述代码后调用了另外一个进程（如使用了一个Shell命令），则在调用成功后会收到SIGTRAP信号；由于此时P已经请求过TRACEME，SIGTRAP将不被忽略，而是被默认处理。Linux下对SIGTRAP的默认处理是什么呢？好吧，简单地停止执行。于是我们的程序P就“假死”了。这显然是不应该发生的。我没有使用这个方法。

####方法二

    static int debugger_present = -1;
    static void sigtrap_handler(int signum)
    {
        debugger_present = 0;
        signal(SIGTRAP, SIG_IGN);
    }
    
    int has_debugger()
    {
        if (-1 == debugger_present) {
            debugger_present = 1;
            signal(SIGTRAP, sigtrap_handler);
            raise(SIGTRAP);
        }
        
        return debugger_present;
    }
    
这个方法让程序自己注册了一个SIGTRAP处理函数（sigtrap\_handler），并显式地引发一个SIGTRAP信号。如果当前程序正在被调试，调试器（gdb）会拦截SIGTRAP，我们的处理函数不被执行，于是debugger\_present保持为1；如果当前程序没有被调试，则我们的处理函数将被调用，于是在其中debugger_present被置为0。这个方法有一点副作用，即：当前程序被调试时，gdb会意外收到一个SIGTRAP（我们显式引发的那个），这会让调试者大吃一惊（“我明明没有设置断点啊，这到底是怎么回事？”）！由于我希望神不知鬼不觉地检测调试器，因此也没有采用这个方法。

####方法三

    int has_debugger() 
    {
        char buff1[24], buff2[16];
        FILE* f;

        snprintf(buff1, 24, "/proc/%d/status", getppid());
        f = fopen(buff1, "r");
        // the first line in status is name
        fgets(buff2, 16, f);
        fclose(f);

        int has_gdb = (strstr(buff2, "gdb") || strstr(buff2, "ltrace") || strstr(buff2, "strace"));
        if (has_gdb == 0) {
            printf("no debugger is attached\n");
        } else {
            printf("debugger attached!\n");
        }
        return has_gdb;
    }
    
这个方法简单直接。首先获得当前程序父进程的id（getppid），然后借此读取父进程的状态，获得其名称。
在Linux下，调试器一般是gdb，即使不用gdb，也要用ltrace或strace这些trace工具。因此，我们通过搜索当前程序的
父进程名称中含不含有上述这些字符串，就可以判断是否是被调试器启动的。这个方法对于采用其他调试器的情况可能会失效。
但考虑到gdb的广泛性，失效的可能性很小。

我最终采取了第三种方法。
