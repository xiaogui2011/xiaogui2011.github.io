---
layout: post
title: shellErrHandle
slug: shellerrhandle
author: ymkNK
date: 2024-05-26 23:32:06 +0800
categories: [开发, linux]
tags: [linux, shell]
img: 1.jpg
---


## 概述

[如何在shell中处理异常](https://blog.csdn.net/jazywoo123/article/details/47747763)

### 立即捕获

[Automatic exit from Bash shell script on error \[duplicate\]](https://stackoverflow.com/questions/2870992/automatic-exit-from-bash-shell-script-on-error)

> set -e

任何后续的脚本，一旦存在错误， 会立马退出

```shell
#!/bin/bash
set -e
# Any subsequent(*) commands which fail will cause the shell script to exit immediately
```

### || 或的逻辑

[Is there a TRY CATCH command in Bash](https://stackoverflow.com/questions/22009364/is-there-a-try-catch-command-in-bash)

> There is no try/catch in bash; however, one can achieve similar behavior using `&&` or `||`.
>
> `command 1 || command 2`
>
> if command1 fails then command2 runs as follows
>
> * `正是我需要的, 前边执行失败， 才会执行后边的命令` > Similarly, using `&&`, command2 will run `if command1 is successful`

[Exception handling in shell scripting?](https://stackoverflow.com/questions/6961389/exception-handling-in-shell-scripting)

```shell
a_command || fallback_command
```

> In this example, you want to run fallback\_command if a\_command fails (returns a non-zero value):
>
> * `|| 失败才会执行 fallback_command`

**sed 如果匹配失败， 不会报错**

### 有些异常不是异常

[mac上遇到的错误sed command a expects followed by text](https://www.jianshu.com/p/87a57a12d5e6)

> gsed替换sed

### 放弃捕获

[Throw away standard output and error](https://unix.stackexchange.com/questions/205788/throw-away-standard-output-and-error)

> `{ ps -p "$proc" | fgrep "$proc";} >/dev/null 2>&1` 效果，只输出echo内容

### 停止进程的错误理解

[Linux后台进程管理以及ctrl+z（挂起）、ctrl+c（中断）、ctrl+\（退出）和ctrl+d（EOF）的区别](https://www.cnblogs.com/jiangzhaowei/p/8971265.html)

> 前台进程的终止： ctrl+c, 后台并没有终止
>
> ctrl+c强行中断当前程序的执行。
>
> ctrl+z将任务中断,但是此任务并没有结束,他仍然在进程中，只是放到后台并维持挂起的状态。
>
> * 如需其在后台继续运行，需用“bg 进程号”使其继续运行；再用"fg 进程号"可将后台进程前台化。
>
> ctrl+\表示退出。
复制成功
