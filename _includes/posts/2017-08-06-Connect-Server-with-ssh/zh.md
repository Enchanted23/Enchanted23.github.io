## 1. 如何连接

最简单的 SSH 命令只需要指定用户名和主机名参数即可. 主机名可以是 IP 地址或者域名. 命令格式如下:

```
ssh user@hostname
```

SSH 默认连接到目标主机的 22 端口上,但是由于各种原因你可能需要连接到其他端口.

```
ssh -p 10022 user@hostname
```

如上命令就是通过添加参数 -p 指定端口号为 10022.

当然，如果你是使用的上传公钥的方式进行登录(这样其实是更安全的方式)，而且之前你的ssh key设置过密码，你可以通过做下面的工作让自己以后可以无密码登录：

```
 ssh-add -K ~/.ssh/[your-private-key] 
```

输入你的密码，之后你ssh登录的时候就不会让你输入密码了。

参考：[https://linux.cn/article-3858-1.html#3_487](https://link.jianshu.com/?t=https%3A%2F%2Flinux.cn%2Farticle-3858-1.html%233_487)

## 补充：倒腾环境

那连接了之后，你可能是root用户，也可能不是。

这个时候，假如服务器已经预先装了一些东西，然后你需要倒腾出一个你自己的项目需要的环境，该怎么做？

最好的方式是用**virtualenv**装一个虚拟环境，然后在里面下自己想要的依赖包。

###1. 了解服务器配置

首先，你需要了解一下你的服务器以及配置的环境：

1. **查看硬件/软件信息：**

   *查看内存大小*

   ```
   cat /proc/meminfo |grep MemTotal 
   ```

   *查看内核/操作系统/CPU信息*

   ```
   uname -a
   ```

   *查看计算机名*

   ```
   hostname 
   ```

   *查看CPU信息*

   ```
   cat /proc/cpuinfo
   ```

   *查看GPU使用情况*

   ```
   nvidia-smi
   ```

   ***比较全面的查看***

   ```
   top #后面还可以加命令，ctrl+C退出
   ```

   ***查看python的版本和路径***

   ```
   which python	#查看default的python2
   which python3	#查看default的python3
   whereis python	#查看ubuntu中所有安装的python路径
   ```

### 2. virtualenv

要非常全面的了解virtualenv的话，可以看[官方指南](https://virtualenv.pypa.io/en/stable/reference/#virtualenv-command)，这里我仅说说常见的命令。

**建立一个名叫ENV的虚拟环境：**

```
virtualenv --python=/usr/local/bin/python3 ENV
# 选择的解释器的路径为/usr/local/bin/python3,如何查看python路径见上面。
```

**一些可以选择的参数：**

```
virtualenv -h #可以通过这个命令来查看常见的参数
```

**如果你因为某些原因选择了`--no-setuptools` `--no-pip` `--no-wheel`，那么你可能需要再建好之后多做一些工作。**

*我实验室的服务器有时候需要连接网络通：*

```
wlt #连接网络通的命令
```

*如果你是root用户想更换镜像源可以参考：* [网址](http://wlt.ustc.edu.cn/cgi-bin/ip?url=http://www.cnblogs.com/dtiove/p/5917263.html&dip=101.37.225.65)

我连的那个服务器就慢的让人绝望。。。100kB/s

装好了想要的环境之后呢，就可以开始干活了。

## 2. 如何转移数据

### scp

相信各位VPSer在使用VPS时会经常在不同VPS间互相备份数据或者转移数据，大部分情况下VPS上都已经安装了Nginx或者类似的web server，直接将要传输的文件放到web server的目录，然后在目标机器上执行：wget [http://www.vpser.net/testfile.zip](https://link.jianshu.com/?t=http%3A%2F%2Fwww.vpser.net%2Ftestfile.zip) 就行了。当VPS上没有安装web server和ftp server的时候或感觉上面的方法比较麻烦，那么用scp命令就会排上用场。

**一、scp是什么?**
scp是secure copy的简写，用于在Linux下进行远程拷贝文件的命令，和它类似的命令有cp，不过cp只是在本机进行拷贝不能跨服务器，而且scp传输是加密的。可能会稍微影响一下速度。

**二、scp有什么用?**

1. 我们需要获得远程服务器上的某个文件，远程服务器既没有配置ftp服务器，没有开启web服务器，也没有做共享，无法通过常规途径获得文件时，只需要通过scp命令便可轻松的达到目的。
2. 我们需要将本机上的文件上传到远程服务器上，远程服务器没有开启ftp服务器或共享，无法通过常规途径上传是，只需要通过scp命令便可以轻松的达到目的。

**三、scp使用方法**

1. 获取远程服务器上的文件

   ```
    scp -P 2222 root@www.vpser.net:/root/lnmp0.4.tar.gz /hom  e/lnmp0.4.tar.gz
   ```

   端口大写P 为参数，2222 表示更改SSH端口后的端口，如果没有更改SSH端口可以不用添加该参数。 [root@www.vpser.net](https://link.jianshu.com/?t=mailto%3Aroot%40www.vpser.net) 表示使用root用户登录远程服务器[www.vpser.net](https://link.jianshu.com/?t=http%3A%2F%2Fwww.vpser.net%2F)，:/root/lnmp0.4.tar.gz 表示远程服务器上的文件，最后面的/home/lnmp0.4.tar.gz表示保存在本地上的路径和文件名。

2. 获取远程服务器上的目录

   ```
    scp -P 2222 -r root@www.vpser.net:/root/lnmp0.4/ /home/lnmp0.4/
   ```

   端口大写P 为参数，2222 表示更改SSH端口后的端口，如果没有更改SSH端口可以不用添加该参数。-r 参数表示递归复制(即复制该目录下面的文件和目录);[root@www.vpser.net](https://link.jianshu.com/?t=mailto%3Aroot%40www.vpser.net) 表示使用root用户登录远程服务器[www.vpser.net](https://link.jianshu.com/?t=http%3A%2F%2Fwww.vpser.net%2F)，:/root/lnmp0.4/ 表示远程服务器上的目录，最后面的/home/lnmp0.4/表示保存在本地上的路径。

3. 将本地文件上传到服务器上

   ```
    scp -P 2222 /home/lnmp0.4.tar.gz root@www.vpser.net:/root/lnmp0.4.tar.gz
   ```

   端口大写P 为参数，2222 表示更改SSH端口后的端口，如果没有更改SSH端口可以不用添加该参数。 /home/lnmp0.4.tar.gz表示本地上准备上传文件的路径和文件名。[root@www.vpser.net](https://link.jianshu.com/?t=mailto%3Aroot%40www.vpser.net) 表示使用root用户登录远程服务器[www.vpser.net](https://link.jianshu.com/?t=http%3A%2F%2Fwww.vpser.net%2F)，:/root/lnmp0.4.tar.gz 表示保存在远程服务器上目录和文件名。

4. 将本地目录上传到服务器上

   ```
    scp -P 2222 -r /home/lnmp0.4/ root@www.vpser.net:/root/lnmp0.4/
   ```

   端口大写P 为参数，2222 表示更改SSH端口后的端口，如果没有更改SSH端口可以不用添加该参数。-r 参数表示递归复制(即复制该目录下面的文件和目录);/home/lnmp0.4/表示准备要上传的目录，[root@www.vpser.net](https://link.jianshu.com/?t=mailto%3Aroot%40www.vpser.net) 表示使用root用户登录远程服务器[www.vpser.net](https://link.jianshu.com/?t=http%3A%2F%2Fwww.vpser.net%2F)，:/root/lnmp0.4/ 表示保存在远程服务器上的目录位置。

5. 可能有用的几个参数 :
   -v 和大多数 linux 命令中的 -v 意思一样 , 用来显示进度 . 可以用来查看连接 , 认证 , 或是配置错误 .

   -C 使能压缩选项 .

   -4 强行使用 IPV4 地址 .

   -6 强行使用 IPV6 地址 .

参考: [http://blog.sina.com.cn/s/blog_53b95aec0100hcjn.html](https://link.jianshu.com/?t=http%3A%2F%2Fblog.sina.com.cn%2Fs%2Fblog_53b95aec0100hcjn.html)

## 3. 如何编辑远程文件

### 1. vim

**常用命令: 见图**

![img](https://upload-images.jianshu.io/upload_images/7248047-cac4dd1cd5e677d1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

More：[http://www.jianshu.com/p/bcbe916f97e1](https://www.jianshu.com/p/bcbe916f97e1)

### 2. 利用sublime或者item的插件

*下面是我亲自配置的过程*

#### sublime的配置

方法一：安装rsub插件
装一个叫 rsub 的插件，直接在 install package里面搜就有

开启本地端口转发，rsub 默认的是 52698

```
ssh -R 52698:localhost:52698 user@remote_domain

```

或者在 .ssh/config 中加入

```
host remote_domain
  RemoteForward 52698 127.0.0.1:52698
  // 当然，如果你想对所有服务器端都起作用，可以把remote_domain设为 *

```

这里顺便提一句，如果在mac终端要显示隐藏的文件和文件夹用

```
ls -a
```

如果要新建一个文本文件，比如没有config文件要新建

```
touch config
```

登录服务器端，安装rmate，最简便的方法使用pip安装

```
pip install rmate
```

你也可以用比较复杂的方法，如果你没有装pip，当然pip是个好东西，没有装是不太可能的，但还是说一下，如果你对改这些配置文件不是很熟，那就用pip吧。

```
// 使用时输入如下命令即可
rmate filename
```

值得一提的是，sublime配合rsub、rmate这样只能在服务器上打开一个文件到本地修改，修改完之后会自动同步到服务器上，和用 vim 修改的唯一区别就是可以使用本地的编辑器了。功能有限，不能提供目录树修改，只是为习惯编辑器的同学提供另一种选择而已。

## 4. 让进程在后台可靠运行

我们经常会碰到这样的问题，用 telnet/ssh 登录了远程的 Linux 服务器，运行了一些耗时较长的任务， 结果却由于网络的不稳定导致任务中途失败。如何让命令提交后不受本地关闭终端窗口/网络断开连接的干扰呢？下面举了一些例子， 您可以针对不同的场景选择不同的方式来处理这个问题。

### 最简单的byobu

本文只介绍如何使用byobu让程序在服务器端跑，你可以关掉终端去喝杯咖啡或者睡个觉，然后回来继续工作。
需要做的事在连接上服务器端之后，直接输入byobu然后回车，然后运行程序，想离开时关闭终端，然后去干其它事情，等过了几个小时或者几天想看看跑的怎么样的时候，打开终端连上服务器，再输入byobu即可。
如果想退出byobu的模式，只需要按F6即可(在macOS中按 fn + F6）
（注：要有良好的写程序要写checkpoint的习惯，这样再配合byobu，程序跑的还算是很安全。）

当然这只是byobu的冰山一角，真正的用处可比这个大得多。
**byobu和tmux的详细介绍请看：**
[http://byobu.co/index.html](https://link.jianshu.com/?t=http%3A%2F%2Fbyobu.co%2Findex.html)
[https://github.com/tmux/tmux/wiki](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Ftmux%2Ftmux%2Fwiki)
[https://imtx.me/archives/1693.html](https://link.jianshu.com/?t=https%3A%2F%2Fimtx.me%2Farchives%2F1693.html)
[http://harttle.com/2015/11/06/tmux-startup.html](https://link.jianshu.com/?t=http%3A%2F%2Fharttle.com%2F2015%2F11%2F06%2Ftmux-startup.html)
tmux和byobu在很多方面功能是类似的，操作有所不同。

### nohup/setsid/&

场景：
如果只是临时有一个命令需要长时间运行，什么方法能最简便的保证它在后台稳定运行呢？

> hangup 名称的来由:
> 在 Unix 的早期版本中，每个终端都会通过 modem 和系统通讯。当用户 logout 时，modem 就会挂断（hang up）电话。 同理，当 modem 断开连接时，就会给终端发送 hangup 信号来通知其关闭所有子进程。

解决方法：
我们知道，当用户注销（logout）或者网络断开时，终端会收到 HUP（hangup）信号从而关闭其所有子进程。因此，我们的解决办法就有两种途径：要么让进程忽略 HUP 信号，要么让进程运行在新的会话里从而成为不属于此终端的子进程。

#### 1. nohup

nohup 无疑是我们首先想到的办法。顾名思义，nohup 的用途就是让提交的命令忽略 hangup 信号。让我们先来看一下 nohup 的帮助信息：

```
NOHUP(1)                    User Commands                    NOHUP(1)

NAME
   nohup - run a command immune to hangups, with output to a non-tty

SYNOPSIS
       nohup COMMAND [ARG]...
       nohup OPTION

DESCRIPTION
       Run COMMAND, ignoring hangup signals.

       --help display this help and exit

       --version
              output version information and exit

```

可见，nohup 的使用是十分方便的，只需在要处理的命令前加上 nohup 即可，标准输出和标准错误缺省会被重定向到 nohup.out 文件中。一般我们可在结尾加上"&"来将命令同时放入后台运行，也可用">filename 2>&1"来更改缺省的重定向文件名。

**nohup 示例**

```
[root@pvcent107 ~]# nohup ping www.ibm.com &
[1] 3059
nohup: appending output to `nohup.out'
[root@pvcent107 ~]# ps -ef |grep 3059
root      3059   984  0 21:06 pts/3    00:00:00 ping www.ibm.com
root      3067   984  0 21:06 pts/3    00:00:00 grep 3059
[root@pvcent107 ~]#

```

其中ps指令为进程监视工具，更多参考可以见：[http://itshine.blog.51cto.com/648476/645466](https://link.jianshu.com/?t=http%3A%2F%2Fitshine.blog.51cto.com%2F648476%2F645466)
[http://www.cnblogs.com/peida/archive/2012/12/19/2824418.html](https://link.jianshu.com/?t=http%3A%2F%2Fwww.cnblogs.com%2Fpeida%2Farchive%2F2012%2F12%2F19%2F2824418.html)
这里ps -ef |grep 3059起到的作用就是ps 与grep 常用组合用法，查找特定进程。

#### 2. setsid

nohup 无疑能通过忽略 HUP 信号来使我们的进程避免中途被中断，但如果我们换个角度思考，如果我们的进程不属于接受 HUP 信号的终端的子进程，那么自然也就不会受到 HUP 信号的影响了。setsid 就能帮助我们做到这一点。让我们先来看一下 setsid 的帮助信息：

```
SETSID(8)     Linux Programmer’s Manual     SETSID(8)
NAME 
        setsid - run a program in a new session
SYNOPSIS 
        setsid program [ arg ... ]
DESCRIPTION 
        setsid runs a program in a new session.

```

可见 setsid 的使用也是非常方便的，也只需在要处理的命令前加上 setsid 即可。
setsid 示例

```
[root@pvcent107 ~]# setsid ping www.ibm.com
[root@pvcent107 ~]# ps -ef |grep www.ibm.com
root 31094 **1** 0 07:28 ? 00:00:00 ping www.ibm.com
root 31102 29217 0 07:29 pts/4 00:00:00 grep www.ibm.com
[root@pvcent107 ~]#

```

值得注意的是，上例中我们的进程 ID(PID)为31094，而它的父 ID（PPID）为1（即为 init 进程 ID），并不是当前终端的进程 ID。请将此例与[nohup 例](https://link.jianshu.com/?t=https%3A%2F%2Fwww.ibm.com%2Fdeveloperworks%2Fcn%2Flinux%2Fl-cn-nohup%2F%23nohup)中的父 ID 做比较。

#### 3. &

这里还有一个关于 subshell 的小技巧。我们知道，将一个或多个命名包含在“()”中就能让这些命令在子 shell 中运行中，从而扩展出很多有趣的功能，我们现在要讨论的就是其中之一。
当我们将"&"也放入“()”内之后，我们就会发现所提交的作业并不在作业列表中，也就是说，是无法通过jobs来查看的。让我们来看看为什么这样就能躲过 HUP 信号的影响吧。
subshell 示例

```
[root@pvcent107 ~]# (ping www.ibm.com &)
[root@pvcent107 ~]# ps -ef |grep www.ibm.com
root     16270     1  0 14:13 pts/4    00:00:00 ping www.ibm.com
root     16278 15362  0 14:13 pts/4    00:00:00 grep www.ibm.com
[root@pvcent107 ~]#

```

从上例中可以看出，新提交的进程的父 ID（PPID）为1（init 进程的 PID），并不是当前终端的进程 ID。因此并不属于当前终端的子进程，从而也就不会受到当前终端的 HUP 信号的影响了。

### disown

场景：
我们已经知道，如果事先在命令前加上 nohup 或者 setsid 就可以避免 HUP 信号的影响。但是如果我们未加任何处理就已经提交了命令，该如何补救才能让它避免 HUP 信号的影响呢？
解决方法：
这时想加 nohup 或者 setsid 已经为时已晚，只能通过作业调度和 disown 来解决这个问题了。让我们来看一下 disown 的帮助信息：

```
disown [-ar] [-h] [jobspec ...]
    Without options, each jobspec is  removed  from  the  table  of
    active  jobs.   If  the -h option is given, each jobspec is not
    removed from the table, but is marked so  that  SIGHUP  is  not
    sent  to the job if the shell receives a SIGHUP.  If no jobspec
    is present, and neither the -a nor the -r option  is  supplied,
    the  current  job  is  used.  If no jobspec is supplied, the -a
    option means to remove or mark all jobs; the -r option  without
    a  jobspec  argument  restricts operation to running jobs.  The
    return value is 0 unless a jobspec does  not  specify  a  valid
    job.

```

可以看出，我们可以用如下方式来达成我们的目的:

1. 用disown -h jobspec来使某个作业忽略HUP信号。
2. 用disown -ah 来使所有的作业都忽略HUP信号。
3. 用disown -rh 来使正在运行的作业忽略HUP信号。

需要注意的是，当使用过 disown 之后，会将把目标作业从作业列表中移除，我们将不能再使用jobs来查看它，但是依然能够用ps -ef查找到它。
但是还有一个问题，这种方法的操作对象是作业，如果我们在运行命令时在结尾加了"&"来使它成为一个作业并在后台运行，那么就万事大吉了，我们可以通过jobs命令来得到所有作业的列表。但是如果并没有把当前命令作为作业来运行，如何才能得到它的作业号呢？

> 灵活运用 CTRL-z：
> 在我们的日常工作中，我们可以用 CTRL-z 来将当前进程挂起到后台暂停运行，执行一些别的操作，然后再用 fg 来将挂起的进程重新放回前台（也可用 bg 来将挂起的进程放在后台）继续运行。这样我们就可以在一个终端内灵活切换运行多个任务，这一点在调试代码时尤为有用。因为将代码编辑器挂起到后台再重新放回时，光标定位仍然停留在上次挂起时的位置，避免了重新定位的麻烦。

答案就是用 CTRL-z（按住Ctrl键的同时按住z键）了！
CTRL-z 的用途就是将当前进程挂起（Suspend），然后我们就可以用jobs命令来查询它的作业号，再用bg jobspec来将它放入后台并继续运行。需要注意的是，如果挂起会影响当前进程的运行结果，请慎用此方法。

**disown 示例1（如果提交命令时已经用“&”将命令放入后台运行，则可直接使用“disown”）**

```
[root@pvcent107 build]# cp -r testLargeFile largeFile &
[1] 4825
[root@pvcent107 build]# jobs
[1]+  Running                 cp -i -r testLargeFile largeFile &        
[root@pvcent107 build]# disown -h %1
[root@pvcent107 build]# ps -ef |grep largeFile
root      4825   968  1 09:46 pts/4    00:00:00 cp -i -r testLargeFile largeFile
root      4853   968  0 09:46 pts/4    00:00:00 grep largeFile
[root@pvcent107 build]# logout

```

**disown 示例2（如果提交命令时未使用“&”将命令放入后台运行，可使用 CTRL-z 和“bg”将其放入后台，再使用“disown”）**

```
[root@pvcent107 build]# cp -r testLargeFile largeFile2

[1]+  Stopped                 cp -i -r testLargeFile largeFile2
[root@pvcent107 build]# bg %1
[1]+ cp -i -r testLargeFile largeFile2 &
[root@pvcent107 build]# jobs
[1]+  Running                 cp -i -r testLargeFile largeFile2 &
[root@pvcent107 build]# disown -h %1
[root@pvcent107 build]# ps -ef |grep largeFile2
root      5790  5577  1 10:04 pts/3    00:00:00 cp -i -r testLargeFile largeFile2
root      5824  5577  0 10:05 pts/3    00:00:00 grep largeFile2
[root@pvcent107 build]#
```

### screen

场景：
我们已经知道了如何让进程免受 HUP 信号的影响，但是如果有大量这种命令需要在稳定的后台里运行，如何避免对每条命令都做这样的操作呢？
解决方法：
此时最方便的方法就是 screen 了。简单的说，screen 提供了 ANSI/VT100 的终端模拟器，使它能够在一个真实终端下运行多个全屏的伪终端。screen 的参数很多，具有很强大的功能，我们在此仅介绍其常用功能以及简要分析一下为什么使用 screen 能够避免 HUP 信号的影响。我们先看一下 screen 的帮助信息：

```
SCREEN(1)                        SCREEN(1)

NAME
       screen - screen manager with VT100/ANSI terminal emulation

SYNOPSIS
       screen [ -options ] [ cmd [ args ] ]
       screen -r [[pid.]tty[.host]]
       screen -r sessionowner/[[pid.]tty[.host]]

DESCRIPTION
       Screen  is  a  full-screen  window manager that multiplexes a physical
       terminal between several  processes  (typically  interactive  shells).
       Each  virtual  terminal provides the functions of a DEC VT100 terminal
       and, in addition, several control functions from the  ISO  6429  (ECMA
       48,  ANSI  X3.64)  and ISO 2022 standards (e.g. insert/delete line and
       support for multiple character sets).  There is a  scrollback  history
       buffer  for  each virtual terminal and a copy-and-paste mechanism that
       allows moving text regions between windows.

```

使用 screen 很方便，有以下几个常用选项：

1. 用screen -dmS session_name来建立一个处于断开模式下的会话（并指定其会话名）。
2. 用screen -list 来列出所有会话。
3. 用screen -r session_name来重新连接指定会话。
4. 用快捷键(CTRL+a) + d 来暂时断开当前会话。
5. 如果要杀死一个session，用 screen -S session_name -X quit来杀死会话。

screen 示例

```
[root@pvcent107 ~]# screen -dmS Urumchi
[root@pvcent107 ~]# screen -list
There is a screen on:
        12842.Urumchi   (Detached)
1 Socket in /tmp/screens/S-root.

[root@pvcent107 ~]# screen -r Urumchi

```

当我们用“-r”连接到 screen 会话后，我们就可以在这个伪终端里面为所欲为，再也不用担心 HUP 信号会对我们的进程造成影响，也不用给每个命令前都加上“nohup”或者“setsid”了。这是为什么呢？让我来看一下下面两个例子吧。

1. 未使用 screen 时新进程的进程树

   ```
    [root@pvcent107 ~]# ping www.google.com &
    [1] 9499
    [root@pvcent107 ~]# pstree -H 9499
    init─┬─Xvnc
         ├─acpid
         ├─atd
         ├─2*[sendmail] 
         ├─sshd─┬─sshd───bash───pstree
         │       └─sshd───bash───ping

   ```

   我们可以看出，未使用 screen 时我们所处的 bash 是 sshd 的子进程，当 ssh 断开连接时，HUP 信号自然会影响到它下面的所有子进程（包括我们新建立的 ping 进程）。

2. 使用了 screen 后新进程的进程树

   ```
    [root@pvcent107 ~]# screen -r Urumchi
    [root@pvcent107 ~]# ping www.ibm.com &
    [1] 9488
    [root@pvcent107 ~]# pstree -H 9488
    init─┬─Xvnc
         ├─acpid
         ├─atd
         ├─screen───bash───ping
         ├─2*[sendmail]

   ```

   而使用了 screen 后就不同了，此时 bash 是 screen 的子进程，而 screen 是 init（PID为1）的子进程。那么当 ssh 断开连接时，HUP 信号自然不会影响到 screen 下面的子进程了。

**screen补充：**
先来看看如何使用screen解决SIGHUP问题，比如现在我们要ftp传输一个大文件。如果按老的办法，SSH登录到系统，直接ftp命令开始传输，之后。。如果网络速度还可以，恭喜你，不用等太长时间了；如果网络不好，老老实实等着吧，只能传输完毕再断开SSH连接了。让我们使用screen来试试。
SSH登录到系统，在命令行键入screen。

```
[root@tivf18 root]# screen
```

在screen shell窗口中输入ftp命令，登录，开始传输。不愿意等了？OK，在窗口中键入C-a d：
**管理你的远程会话**

![img](https://upload-images.jianshu.io/upload_images/7248047-906c83d080f40f8e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/572)

然后退出SSH登录？随你怎样，只要别杀掉screen会话。

是不是很方便？更进一步，其实我们可以利用screen这种功能来管理你的远程会话，保存你所有的工作内容。你是不是每次登录到系统都要开很多窗口，然后每天都要重复打开关闭这些窗口？让screen来帮你“保存”吧，你只需要打开一个ssh窗口，创建需要的screen窗口，退出的时候C-a d“保存”你的工作，下次登录后直接screen -r <screen_pid>就可以了。

最好能给每个窗口起一个名字，这样好记些。使用C-a A给窗口起名字。使用C-a w可以看到这些窗口名字，可能名字出现的位置不同。

### 总结

现在几种方法已经介绍完毕，我们可以根据不同的场景来选择不同的方案。nohup/setsid 无疑是临时需要时最方便的方法，disown 能帮助我们来事后补救当前已经在运行了的作业，而 screen 则是在大批量操作时不二的选择了。
当然对于我来说，byobu完全够用了。

参考：
[https://www.ibm.com/developerworks/cn/linux/l-cn-nohup/](https://link.jianshu.com/?t=https%3A%2F%2Fwww.ibm.com%2Fdeveloperworks%2Fcn%2Flinux%2Fl-cn-nohup%2F)
[https://www.ibm.com/developerworks/cn/linux/l-cn-screen/](https://link.jianshu.com/?t=https%3A%2F%2Fwww.ibm.com%2Fdeveloperworks%2Fcn%2Flinux%2Fl-cn-screen%2F)
[http://harttle.com/2015/11/06/tmux-startup.html](https://link.jianshu.com/?t=http%3A%2F%2Fharttle.com%2F2015%2F11%2F06%2Ftmux-startup.html)

## 5. 如何远程执行命令

### 和本地一样的常用命令：

*以macOS为例*

#### 目录操作

| 命令名    | 功能描述       | 使用举例             |
| ------ | ---------- | ---------------- |
| mkdir  | 创建一个目录     | mkdir doorman    |
| rmdir  | 删除一个目录     | rmdir dirname    |
| mvdir  | 移动或重命名一个目录 | mvdir dir1 dir2  |
| cd     | 改变当前目录     | cd dirname       |
| pwd    | 显示当前目录的路径名 | pwd              |
| ls     | 显示当前目录的内容  | ls -la           |
| dircmp | 比较两个目录的内容  | dircmp dir1 dir2 |

#### 文件操作

| 命令名  | 功能描述        | 使用举例                      |
| ---- | ----------- | ------------------------- |
| cat  | 显示或连接文件     | cat filename              |
| pg   | 分页格式化显示文件内容 | pg filename               |
| more | 分屏显示文件内容    | more filename             |
| od   | 显示非文本文件的内容  | od -c filename            |
| cp   | 复制文件或目录     | cp file1 file2            |
| rm   | 删除文件或目录     | rm filename               |
| mv   | 改变文件名或所在目录  | mv file1 file2            |
| ln   | 联接文件        | ln -s file1 file2         |
| find | 使用匹配表达式查找文件 | find . -name "*.c" -print |
| file | 显示文件类型      | file filename             |
| open | 使用默认的程序打开文件 | open filename             |

#### 选择操作

| 命令名   | 功能描述            | 使用举例                         |
| ----- | --------------- | ---------------------------- |
| head  | 显示文件的最初几行       | head -20 filename            |
| tail  | 显示文件的最后几行       | tail -15 filename            |
| cut   | 显示文件每行中的某些域     | cut -f1,7 -d: /etc/passwd    |
| colrm | 从标准输入中删除若干列     | colrm 8 20 file2             |
| paste | 横向连接文件          | paste file1 file2            |
| diff  | 比较并显示两个文件的差异    | diff file1 file2             |
| sed   | 非交互方式流编辑器       | sed "s/red/green/g" filename |
| grep  | 在文件中按模式查找       | grep "^[a-zA-Z]" filename    |
| awk   | 在文件中查找并处理模式     | awk '{print $1 $1}' filename |
| sort  | 排序或归并文件         | sort -d -f -u file1          |
| uniq  | 去掉文件中的重复行       | uniq file1 file2             |
| comm  | 显示两有序文件的公共和非公共行 | comm file1 file2             |
| wc    | 统计文件的字符数、词数和行数  | wc filename                  |
| nl    | 给文件加上行号         | nl file1 >file2              |

#### 安全操作

| 命令名    | 功能描述        | 使用举例                    |
| ------ | ----------- | ----------------------- |
| passwd | 修改用户密码      | passwd                  |
| chmod  | 改变文件或目录的权限  | chmod ug+x filename     |
| umask  | 定义创建文件的权限掩码 | umask 027               |
| chown  | 改变文件或目录的属主  | chown newowner filename |
| chgrp  | 改变文件或目录的所属组 | chgrp staff filename    |
| xlock  | 给终端上锁       | xlock -remote           |

#### 编程操作

| 命令名   | 功能描述         | 使用举例                       |
| ----- | ------------ | -------------------------- |
| make  | 维护可执行程序的最新版本 | make                       |
| touch | 更新文件的访问和修改时间 | touch -m 05202400 filename |
| dbx   | 命令行界面调试工具    | dbx a.out                  |

#### 进程操作

| 命令名    | 功能描述        | 使用举例             |
| ------ | ----------- | ---------------- |
| ps     | 显示进程当前状态    | ps u             |
| kill   | 终止进程        | kill -9 30142    |
| nice   | 改变待执行命令的优先级 | nice cc -c *.c   |
| renice | 改变已运行进程的优先级 | renice +20 32768 |

#### 时间操作

| 命令名  | 功能描述         | 使用举例       |
| ---- | ------------ | ---------- |
| date | 显示系统的当前日期和时间 | date       |
| cal  | 显示日历         | cal 8 1996 |
| time | 统计程序的执行时间    | time a.out |

#### 网络与通信操作

| 命令名    | 功能描述              | 使用举例                        |
| ------ | ----------------- | --------------------------- |
| telnet | 远程登录              | telnet hpc.sp.net.edu.cn    |
| rlogin | 远程登录              | rlogin hostname -l username |
| rsh    | 在远程主机执行指定命令       | rsh f01n03 date             |
| ftp    | 在本地主机与远程主机之间传输文件  | ftp ftp.sp.net.edu.cn       |
| rcp    | 在本地主机与远程主机 之间复制文件 | rcp file1 host1:file2       |
| ping   | 给一个网络主机发送 回应请求    | ping hpc.sp.net.edu.cn      |
| mail   | 阅读和发送电子邮件         | mail                        |
| write  | 给另一用户发送报文         | write username pts/1        |
| mesg   | 允许或拒绝接收报文         | mesg n                      |

#### Korn Shell 命令

| 命令名     | 功能描述            | 使用举例            |
| ------- | --------------- | --------------- |
| history | 列出最近执行过的几条命令及编号 | history         |
| r       | 重复执行最近执行过的某条命令  | r -2            |
| alias   | 给某个命令定义别名       | alias del=rm -i |
| unalias | 取消对某个别名的定义      | unalias del     |

#### 其它命令

| 命令名    | 功能描述            | 使用举例         |
| ------ | --------------- | ------------ |
| uname  | 显示操作系统的有关信息     | uname -a     |
| clear  | 清除屏幕或窗口内容       | clear        |
| env    | 显示当前所有设置过的环境变量  | env          |
| who    | 列出当前登录的所有用户     | who          |
| whoami | 显示当前正进行操作的用户名   | whoami       |
| tty    | 显示终端或伪终端的名称     | tty          |
| stty   | 显示或重置控制键定义      | stty -a      |
| du     | 查询磁盘使用情况        | du -k subdir |
| df     | 显示文件系统的总空间和可用空间 | df /tmp      |
| w      | 显示当前系统活动的总信息    | w            |

参考：[http://www.jianshu.com/p/3291de46f3ff](https://www.jianshu.com/p/3291de46f3ff)

### 补充

#### 查看大小使用情况

##### 1. 查看文件大小

查看文件大小的命令：

```
$ls -l filename
```

会在终端输出：-rw-r–r– 1 root root 2147483648 Mar 5 09:39 filetemp0606 其中数字2147483648 就是文件filename的大小单位是字节B

```
$ls -l
```

会在终端输出当前文件夹下的所有文件的权限大小信息。
注意：如果filename是一个目录的话，执行以上命令会把filename目录下所有文件的大小信息在终端输出。

##### 2. 查看文件夹大小

查看文件夹的大小，也就是查看文件夹下所有文件的大小总和，例如查看文件夹Foldename的大小的命令：

```
$cd Foldename #进到Foldename 目录
$du -sh
```

会给出总大小的一个数字，如果目录下文件很大，要等一会。

##### 3. 查看当前文件系统各分区的大小

查看磁盘的使用情况命令：

```
$df -h
```

输出格式如下：
Filesystem Size Used Avail Use% Mounted on/dev/sda1 146G 30G 109G 22% /udev 426M 4.0K 426M 1% /devtmpfs 174M 784K 173M 1% /runnone 5.0M 0 5.0M 0% /run/locknone 435M 0 435M 0% /run/shm
查看分区磁盘的速度命令：

```
$hdparm -Tt /dev/sda1
```

在终端打印出如下信息：
/dev/sda1:Timing cached reads: 1770 MB in 2.00 seconds = 885.55 MB/secTiming buffered disk reads: 218 MB in 3.03 seconds = 71.92 MB/sec
不同时间速度会有一些波动。
参考：[http://blog.csdn.net/xiqingchun/article/details/42466267](https://link.jianshu.com/?t=http%3A%2F%2Fblog.csdn.net%2Fxiqingchun%2Farticle%2Fdetails%2F42466267)

#### 如何在Ubuntu上检查一个软件包是否安装

如果你正在管理Debian或者Ubuntu服务器，你也许会经常使用 **dpkg** 或者 **apt-get**命令 这两个命令用来安装、卸载和更新包。
让我们看下如何在基于DEB的系统下检查是否安装了一个包。

1. 要检查特定的包，比如firefox是否安装了，使用这个命令：

   ```
    $ dpkg -s firefox
   ```

2. 同样，你可以使用**dpkg-query** 命令。这个命令会有一个更好的输出，当然，你可以用通配符。

   ```
    $ dpkg-query -l firefox
   ```

3. 要列出你系统中安装的所有包，输入下面的命令：

   ```
    $ dpkg --get-selections
   ```

4. 你同样可以通过**grep**来过滤割到更精确的包。比如，我想要使用**dpkg**命令查看系统中安装的gcc包：

   ```
    $ dpkg --get-selections | grep gcc
   ```

5. 此外，你可以使用“**-L**”参数来找出包中文件的位置。

   ```
    $ dpkg -L gcc-4.8
   ```

## 6. 关于共享的远程访问