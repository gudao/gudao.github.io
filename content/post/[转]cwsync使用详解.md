---
title: "[转]cwsync使用详解"
date: 2012-09-19
draft: false
tags: [cwrsync]
categories: [服务器]
---

![](/Content/upload/Img20120907/qv2qdap0.dze.jpg) 1、cwsync 服务器和客户端软件下载地址[http://www.itefix.no/i2/download](http://www.itefix.no/i2/download)http://switch.dl.sourceforge.net/sourceforge/sereds



<table><tbody><tr><td valign="top">
提示：

在需要传同步文件出来的windows服务器上安装同步Rsync Server软件cwRsync_Server_2.0.10_Installer后，会在服务器上建一个管理员级别的SvcwRsync用户，可以修改密码但是我一般不修改，在此服务器上会增加一个RsyncServer服务，并且会设成自动启动。 
</td></tr></tbody></table>


2、把目录C:\Program Files\cwRsyncServer\bin填加到当前用户的path里。（这路径是看个人安装的路径不同而改变） 

3、编写Rsync Server配置文件rsyncd.conf的一般格式如下： 




<table><tbody><tr><td valign="top">
########################################### uid = 0 gid = 0   使用匿名传输时必须加入以上2行use chroot = falsestrict modes = false#hosts allow = 192.168.0.2        ←表示只允许某一个IP地址访问hosts allow = *                   ←表示允许所有地址访问log file = rsyncd.log        日志文件pid file = rsyncd.pid        进程文件secrets file = password   在当前目录下password目录存放的连接用户和密码yjm:123max connections = 100     最大连接数100人 

# Module definitions# Remember cygwin naming conventions : c:\work becomes /cygwin/c/work#[dbbackup]                      ←项目名称，随便取path = /cygdrive/f/rsync ←文件目录（此处路径代表f:/rsync目录)read only = false               ←只读（一般是false完全控制）list = noauth users = yjm               ←认证用户名transfer logging = yes                  

[test]path = /cygdrive/d/yingread only = truelist = noauth users = yjmtransfer logging = yes #####################################
</td></tr></tbody></table>


rsync客户端命令参数详解

-v, --verbose 详细模式输出

-q, --quiet 精简输出模式

-c, --checksum 打开校验开关，强制对文件传输进行校验

-a, --archive 归档模式，表示以递归方式传输文件，并保持所有文件属性，等于-rlptgoD

-r, --recursive 对子目录以递归模式处理

-R, --relative 使用相对路径信息

-b, --backup 创建备份，也就是对于目的已经存在有同样的文件名时，将老的文件重新命名为~filename。可以使用--suffix选项来指定不同的备份文件前缀。

--backup-dir 将备份文件(如~filename)存放在在目录下。

-suffix=SUFFIX 定义备份文件前缀

-u, --update 仅仅进行更新，也就是跳过所有已经存在于DST，并且文件时间晚于要备份的文件。(不覆盖更新的文件)

-l, --links 保留软链结

-L, --copy-links 想对待常规文件一样处理软链结

--copy-unsafe-links 仅仅拷贝指向SRC路径目录树以外的链结

--safe-links 忽略指向SRC路径目录树以外的链结

-H, --hard-links 保留硬链结

-p, --perms 保持文件权限

-o, --owner 保持文件属主信息

-g, --group 保持文件属组信息

-D, --devices 保持设备文件信息

-t, --times 保持文件时间信息

-S, --sparse 对稀疏文件进行特殊处理以节省DST的空间

-n, --dry-run现实哪些文件将被传输

-W, --whole-file 拷贝文件，不进行增量检测

-x, --one-file-system 不要跨越文件系统边界

-B, --block-size=SIZE 检验算法使用的块尺寸，默认是700字节

-e, --rsh=COMMAND 指定使用rsh、ssh方式进行数据同步

--rsync-path=PATH 指定远程服务器上的rsync命令所在路径信息

-C, --cvs-exclude 使用和CVS一样的方法自动忽略文件，用来排除那些不希望传输的文件

--existing 仅仅更新那些已经存在于DST的文件，而不备份那些新创建的文件

--delete 删除那些DST中SRC没有的文件

--delete-excluded 同样删除接收端那些被该选项指定排除的文件

--delete-after 传输结束以后再删除

--ignore-errors 及时出现IO错误也进行删除

--max-delete=NUM 最多删除NUM个文件

--partial 保留那些因故没有完全传输的文件，以是加快随后的再次传输

--force 强制删除目录，即使不为空

--numeric-ids 不将数字的用户和组ID匹配为用户名和组名

--timeout=TIME IP超时时间，单位为秒

-I, --ignore-times 不跳过那些有同样的时间和长度的文件

--size-only 当决定是否要备份文件时，仅仅察看文件大小而不考虑文件时间

--modify-window=NUM 决定文件是否时间相同时使用的时间戳窗口，默认为0

-T --temp-dir=DIR 在DIR中创建临时文件

--compare-dest=DIR 同样比较DIR中的文件来决定是否需要备份

-P 等同于 --partial

--progress 显示备份过程

-z, --compress 对备份的文件在传输时进行压缩处理

--exclude=PATTERN 指定排除不需要传输的文件模式

--include=PATTERN 指定不排除而需要传输的文件模式

--exclude-from=FILE 排除FILE中指定模式的文件

--include-from=FILE 不排除FILE指定模式匹配的文件

--version 打印版本信息

--address 绑定到特定的地址

--config=FILE 指定其他的配置文件，不使用默认的rsyncd.conf文件

--port=PORT 指定其他的rsync服务端口

--blocking-io 对远程shell使用阻塞IO

-stats 给出某些文件的传输状态

--progress 在传输时现实传输过程

--log-format=formAT 指定日志文件格式

--password-file=FILE 从FILE中得到密码

--bwlimit=KBPS 限制I/O带宽，KBytes per second

-h, --help 显示帮助信息

 

3.1设置服务器目录中的权限，在备份目录添加SvcwRsync用户的完全控制权限

 

4在客户端测试连接

telnet 192.168.1.200 873(默认是873端口) 

4.1 差异备份

rsync -avr 192.168.1.200::mydoc /cygdrive/e/test （表示匿名连接）

将192.168.1.200上zhmj01t目录同步到本机E:\test目录中

 

4.2设置备份计划


       4.2.1 创建自动备份脚本


Echo off


@rsync -avr 192.168.1.200::mydoc /cygdrive/e/test

:: 将起保存为sync.bat文件

 


       4.2.2添加计划任务


<pre>At  05:00  /every:M,T,W,Th,F,S,Su  e:\sync.bat</pre>

 

 

5、帮助

 

错误1: rsync: read error: Connection reset by peer (104)

rsync error: error in rsync protocol data stream (code 12) at io.c(794) [receive

r=3.0.2]

解决：很大可能是服务器端没有开启 rsync 服务。开启服务。

 

错误2：@ERROR: chdir failed

rsync error: error starting client-server protocol (code 5) at main.c(1495) [rec

eiver=3.0.2]

解决：服务器端同步目录没有权限，cwrsync默认用户是Svcwrsync。为同步目录添加用户Svcwrsync权限。

 

错误3：@ERROR: failed to open lock file

rsync error: error starting client-server protocol (code 5) at main.c(1495) [rec

eiver=3.0.2]

解决：配置文件 rsync.conf中添加 lock file = rsyncd.lock 即可解决。

Q：如何通过ssh进行rsync，而且无须输入密码？

A：可以通过以下几个步骤

1. 通过ssh-keygen在server A上建立SSH keys，不要指定密码，你会在~/.ssh下看到identity和identity.pub文件

2. 在server B上的home目录建立子目录.ssh

3. 将A的identity.pub拷贝到server B上

4. 将identity.pub加到~[user b]/.ssh/authorized_keys

5. 于是server A上的A用户，可通过下面命令以用户B ssh到server B上了

                e.g. ssh -l userB serverB  

这样就使server A上的用户A就可以ssh以用户B的身份无需密码登陆到server B上了。

 

Q：如何通过在不危害安全的情况下通过防火墙使用rsync?

A：解答如下：

这通常有两种情况，一种是服务器在防火墙内，一种是服务器在防火墙外。

无论哪种情况，通常还是使用ssh，这时最好新建一个备份用户，并且配置sshd仅允许这个用户通过RSA认证方式进入。

如果服务器在防火墙内，则最好限定客户端的IP地址，拒绝其它所有连接。

如果客户机在防火墙内，则可以简单允许防火墙打开TCP端口22的ssh外发连接就ok了。

 

Q：我能将更改过或者删除的文件也备份上来吗？

A：当然可以：

你可以使用如：rsync -other -options -backupdir = ./backup-2000-2-13 ...这样的命令来实现。

这样如果源文件:/path/to/some/file.c改变了，那么旧的文件就会被移到./backup-2000-2-13/path/to/some/file.c，

这里这个目录需要自己

手工建立起来

 

Q：我需要在防火墙上开放哪些端口以适应rsync？

A：视情况而定

rsync可以直接通过873端口的tcp连接传文件，也可以通过22端口的ssh来进行文件传递，但你也可以通过下列命令改变它的端口：

rsync --port 8730 otherhost:: 或者 rsync -e ssh -p 2002 otherhost:

 

Q：我如何通过rsync只复制目录结构，忽略掉文件呢？

A：rsync -av --include */ --exclude * source-dir dest-dir

 

Q：为什么我总会出现&#34;Read-only file system&#34;的错误呢？

A：看看是否忘了设&#34;read only = no&#34;了

 

Q：为什么我会出现@ERROR: invalid gid的错误呢？

A：rsync使用时默认是用uid=nobody;gid=nobody来运行的，如果你的系统不存在nobody组的话，就会出现这样的错误，可以试试gid = nogroup或者其它

 

Q：绑定端口873失败是怎么回事？

A：如果你不是以root权限运行这一守护进程的话，因为1024端口以下是特权端口，会出现这样的错误。你可以用--port参数来改变。

 

Q：为什么我认证失败？

A：从你的命令行看来：

你用的是：

&gt; bash$ rsync -a 144.16.251.213::test test

&gt; Password:

&gt; @ERROR: auth failed on module test

&gt; I dont understand this. Can somebody explain as to how to acomplish this.

&gt; All suggestions are welcome.

应该是没有以你的用户名登陆导致的问题，试试rsync -a max@144.16.251.213::test test
 
- - -
 