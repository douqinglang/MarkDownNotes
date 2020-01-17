## centos 常用命令
查看硬盘和分区-- # df -h
解决bash: ip: command not found问题: # yum -y install initscripts
解决bash: netstat: command not found问题: # yum install -y net-tools 
### yum安装完软件后的清理安装包和header
通常, yum下载的header和软件包都存储在cache中, 不会自动删除. 
yum clean all -y
1. 清楚软件包(/var/cache/yum)
yum clean packages
2. 清楚hearders(/var/cache/yum)
yum clean headers
3. 清楚旧的headers(/var/cache/yum)
yum clean oldheaders
4. 清除软件包和就的headers(/var/cache/yum)
yum clean, yum clean all (=yum clean packages; yum clean oldheaders)
## linux 执行脚本的三种方式 sh, source, exec
### sh方式
使用 sh script.sh执行脚本时, 当前shell是父进程, 会生成一个子进程的shell, 在子进程shell中执行脚本. 脚本执行完毕后, 退出子shell, 回到父shell.
./script.sh 和 sh script.sh等效
- 例如 
使用loop.sh循环打印当前进程号
```shell
$ vi loop.sh
#!/bin/sh
while [ 1 = 1 ]; do 
    echo $$
    sleep 1
done
```
查看当前父shell进程号
```shell
$ echo $$
2454
```
执行loop.sh脚本后, 打印子进程shell进程号 2700
```shell
$ sh loop.sh
2700
2700
```
### source方式
source script.sh, 在当前上下文中执行脚本, 不会产生新的进程. 脚本执行完毕放回当前shell
. script.sh 等价于 source script.sh 故也叫 点命令
### sh和source方式对上下文的影响
- 例如, jump.sh例子
1. 执行如下操作：1）跳到/，2）打印当前工作目录，3）打印Hello
```shell
#!/bin/sh
cd /
pwd
echo Hello
```
2. 通过sh执行脚本, 修改的上下文不会影响当前shell. jump.sh退出以后, 工作目录保持不变.
```shell
$ pwd
/home/riversec
$ ./jump.sh 
/
Hello
$ pwd
/home/riversec
```
3. source执行脚本, 修改的上下文会影响当前shell. jump.sh 退出以后, 当前工作目录变成 ```/```
```shell
$ pwd
/home/riversec
$ source jump.sh 
/
Hello
$ pwd
/
```
### exec方式
使用 exec command方式 会用command进程替换当前shell进程, 并保持pid保持不变. 执行完毕后, 直接退出, 不退回之前的shell环境
```shell
exec echo $$
2364
Connection to 192.168.2.123 closed.
```
## linux 日志系统
/var/log/meaasge    	#所有日志
/var/log/secure 		#系统登录日志
/var/log/cron 			#定时任务日志
/var/log/maillog		#邮件日志
/var/log/boot.log 		#系统启动日志

## 日志分类
auth 					#pam产生的日志
authpriv				#ssh,ftp等登录服务的验证信息
cron					#时间任务相关
kern					#内核
lpr						#打印
mail 					#邮件
mark(syslog)-rsyslog	#服务内部信息,时间标示
news					#新闻组
user 					#用户程序产生的相关信息
uucp					#unix to unix copy, unix主机之间相关的通讯
local 1~700 			#自定义的日志设备

## 日志级别
debug 					#有调试信息的, 日志信息最多
info 					#一般信息的日志, 最常用
notice 					#最具有重要性的普通条件信息
warning					#警告级别
err 					#错误级别, 组织某个功能或者模块不能正常工作的信息
crit 					#严重级别, 组织整个系统或者整个软件不能正常工作的信息
alert 					#需要立即修改的信息
emerg 					#内核崩溃等严重信息
none 					#什么都不记录

## 查看linux端口占用情况
netstat -tunlp 			#用于显示tcp，udp的端口和进程等相关情况
	-t 					#(tcp)仅显示tcp相关选项
	-u 					#(udp)仅显示udp相关选项
	-n 					#拒绝显示别名, 能显示数字的全部转化为数字
	-l 					#仅列出有在 Listen(监听)的服务状态
	-p 					#显示建立相关连接的程序名
## 查找隐藏文件或隐藏文件
ls -al
mv a.txt .a.txt  // .开头的都是隐藏的
ls -lh		//按照mb显示文件大小

## 查看文件 ls
-a 显示所有文件及目录 (ls内定将文件名或目录名称开头为"."的视为隐藏档，不会列出)
-l 除文件名称外，亦将文件型态、权限、拥有者、文件大小等资讯详细列出
-r 将文件以相反次序显示(原定依英文字母次序)
-t 将文件依建立时间之先后次序列出
-A 同 -a ，但不列出 "." (目前目录) 及 ".." (父目录)
-h 按照mb显示文件大小

## 备份centos系统
```bash
tar -cvpf /home/xfce-vnc-2.tar --directory=/ --exclude=/dev --exclude=/home/xfce-vnc-2.tar --exclude=/media --exclude=/mkimage-yum.sh --exclude=/mnt --exclude=/proc --exclude=/sys --exclude=/tmp /
```

tar 程序命令
-cvpfz tar命令参数 -> 创建档案文件,保持权限”(保留所有东西原来的权限),使用gzip来减小文件尺寸
/home/xfce-vnc-2.tar.gz 创建的备存文件所在
--exclude 备份时必须排除在外的目录,有些目录和文件是无用的，例如 /proc /lost+found /sys /media /mnt /proc /sys, xfce-vnc-2.tar.gz将要生成的 mkimage-yum.sh是不需要的文件

/bin 二进制可执行命令
/dev 设备特殊文件
/etc 系统管理和配置文件
/etc/rc.d 启动的配置文件和脚本
/home 用户主目录的基点，比如用户user的主目录就是/home/user，可以用~user表示
/lib 标准程序设计库，又叫动态链接共享库，作用类似windows里的.dll文件
/sbin 超级管理命令，这里存放的是系统管理员使用的管理程序
/tmp 公共的临时文件存储点
/root 系统管理员的主目录
/mnt 系统提供这个目录是让用户临时挂载其他的文件系统
/lost+found这个目录平时是空的，系统非正常关机而留下“无家可归”的文件（windows下叫什么.chk）就在这里
/proc 虚拟的目录，是系统内存的映射。可直接访问这个目录来获取系统信息。
/var 某些大文件的溢出区，比方说各种服务的日志文件
/usr 最庞大的目录，要用到的应用程序和文件几乎都在这个目录
[Linux各目录及每个目录的详细介绍](https://www.cnblogs.com/zhuchenglin/p/8686924.html)

## tar,gzip命令详解
### tar [-cxtzjvfpPN] 文件与目录 ...
1. 参数：
-c ：建立一个压缩文件的参数指令(create 的意思)；
-x ：解开一个压缩文件的参数指令！
-t ：查看tarfile 里面的文件！
特别注意，在参数的下达中，c/x/t 仅能存在一个！不可同时存在！

-z ：是否同时具有gzip 的属性？亦即是否需要用gzip 压缩？
-j ：是否同时具有bzip2 的属性？亦即是否需要用bzip2 压缩？
-v ：压缩的过程中显示文件！这个常用，但不建议用在背景执行过程！
-f ：使用档名，请留意，在f 之后要立即接档名喔！不要再加参数！
2. 示例
```bash
[root@linux ~]# tar -cvf /tmp/etc.tar /etc <==仅打包，不压缩！
[root@linux ~]# tar -zcvf /tmp/etc.tar.gz /etc <==打包后，以gzip 压缩
[root@linux ~]# tar -jcvf /tmp/etc.tar.bz2 /etc <==打包后，以bzip2 压缩
```

参数f 之后的文件档名是自己取的，我们习惯上都用.tar 来作为辨识
z 参数，则以.tar.gz 或.tgz 来代表gzip 压缩过的tar file
j 参数，则以.tar.bz2 
### gzip, zcat 命令
```bash
[root@linux ~]# gzip [-cdt#] 档名
[root@linux ~]# zcat 档名.gz
```

-c ：将压缩的资料输出到萤幕上，可透过资料流重导向来处理；
-d ：解压缩的参数；
-t ：可以用来检验一个压缩档的一致性～看看文件有无错误；
-# ：压缩等级，-1 最快，但是压缩比最差、-9 最慢，但是压缩比最好！预设是-6 ～
```bash
##将/etc/man.config 複制到/tmp ，并且以gzip 压缩
[root@linux ~]# cd /tmp
[root@linux tmp]# cp /etc/man.config .
[root@linux tmp]# gzip man.config
##将范例一的文件内容读出来
[root@linux tmp]# zcat man.config.gz
##将范例一的文件解压缩
[root@linux tmp]# gzip -d man.config.gz
##将范例三解开的man.config 用最佳的压缩比压缩，并保留原本的文件
[root@linux tmp]# gzip -9 -c man.config > man.config.gz
gzip -9 man.config 直接压缩, 不保留源文件
```
### bzip2, bzcat 命令
```bash
[root@linux ~]# bzip2 [-cdz] 档名
[root@linux ~]# bzcat 档名.bz2
```

-c ：将压缩的过程产生的资料输出到萤幕上！
-d ：解压缩的参数
-z ：压缩的参数
-# ：与gzip 同样的，都是在计算压缩比的参数，-9 最佳，-1 最快！
用法和gzip类似
## scp命令的使用
### 将本机文件复制到远程服务器
```bash
scp /home/administrator/news.txt root@192.168.6.129:/etc/squid
```

/home/administrator/      本地文件的绝对路径
news.txt                  要复制到服务器上的本地文件
root                      通过root用户登录到远程服务器（也可以使用其他拥有同等权限的用户）
192.168.6.129             远程服务器的ip地址（也可以使用域名或机器名）
/etc/squid                将本地文件复制到位于远程服务器上的路径
### 将远程服务器文件复制到本机
```bash
scp remote@www.abc.com:/usr/local/sin.sh /home/administrator
```

remote                       通过remote用户登录到远程服务器（也可以使用其他拥有同等权限的用户）
www.abc.com              远程服务器的域名（当然也可以使用该服务器ip地址）
/usr/local/sin.sh           欲复制到本机的位于远程服务器上的文件
/home/administrator  将远程文件复制到本地的绝对路径

## centos 自启动服务或脚本
### 添加开机自启服务
docker 服务为例，设置如下两条命令即可
```bash
systemctl enable docker.service #设置docker服务为自启动服务 相当于我们的 chkconfig docker on
systemctl start docker.service #启动docker服务
```

### 添加开机自启脚本
centos7中增加脚本有两种常用的方法,helloworld.sh为例
```bash
#!/bin/bash
chkconfig: 2345 10 90 
description: myservice ....
echo "hello world"
```

#### 方法一
1. 赋予脚本可执行权限(/home/test.sh是你的脚本路径)
```bash
chmod +x /home/test.sh
```

2. 打开/etc/rc.d/rc.local文件，在末尾增加如下内容
```bash
echo "/home/test.sh" >> /etc/rc.d/rc.local
```

3. 在centos7中，/etc/rc.d/rc.local的权限被降低了，所以需要执行如下命令赋予其可执行权限
```bash
chmod +x /etc/rc.d/rc.local
```

#### 方法二
1. 将脚本移动到/etc/rc.d/init.d目录下
```bash
mv  /home/test.sh /etc/rc.d/init.d
```

2. 增加脚本的可执行权限
```bash
chmod +x  /etc/rc.d/init.d/test.sh
```

3. 添加脚本到开机自动启动项目中
```bash
cd /etc/rc.d/init.d
chkconfig --add test.sh
chkconfig test.sh on
```
## find命令查找文件或文件夹
### 精确查找
find / (查找范围) -name "查找名字" -type d  -- 查找文件夹
find / (查找范围) -name "查找名字"          -- 查找文件
### 模糊查找 "*关键字*", 忽略大小写 == -iname
	find / -name "*关键字*" -type d  -- 文件夹
	搜索最近七天内被访问过的所有文件
	find . -type f -atime -7
	搜索恰好在七天前被访问过的所有文件
	find . -type f -atime 7
	搜索超过七天内被访问过的所有文件
	find . -type f -atime +7
	搜索访问时间超过10分钟的所有文件
	find . -type f -amin +10
	找出比file.log修改时间更长的所有文件
	find . -type f -newer file.log
### 根据文件大小进行匹配
	find . -type f -size 文件大小单元
		文件大小单元：
		b —— 块（512字节）
		c —— 字节
		w —— 字（2字节）
		k —— 千字节
		M —— 兆字节
		G —— 吉字节
### find 命令详解
	不设置任何参数，则find命令将在当前目录下查找子目录与文件。并且将查找到的子目录和文件全部进行显示。
#### 语法
	find(选项)(参数)
##### 选项
		-amin<分钟>：查找在指定时间曾被存取过的文件或目录，单位以分钟计算；
		-anewer<参考文件或目录>：查找其存取时间较指定文件或目录的存取时间更接近现在的文件或目录；
		-atime<24小时数>：查找在指定时间曾被存取过的文件或目录，单位以24小时计算；
		-cmin<分钟>：查找在指定时间之时被更改过的文件或目录；
		-cnewer<参考文件或目录>查找其更改时间较指定文件或目录的更改时间更接近现在的文件或目录；
		-ctime<24小时数>：查找在指定时间之时被更改的文件或目录，单位以24小时计算；
		-daystart：从本日开始计算时间；
		-depth：从指定目录下最深层的子目录开始查找；
		-expty：寻找文件大小为0 Byte的文件，或目录下没有任何子目录或文件的空目录；
		-exec<执行指令>：假设find指令的回传值为True，就执行该指令；
		-false：将find指令的回传值皆设为False；
		-fls<列表文件>：此参数的效果和指定“-ls”参数类似，但会把结果保存为指定的列表文件；
		-follow：排除符号连接；
		-fprint<列表文件>：此参数的效果和指定“-print”参数类似，但会把结果保存成指定的列表文件；
		-fprint0<列表文件>：此参数的效果和指定“-print0”参数类似，但会把结果保存成指定的列表文件；
		-fprintf<列表文件><输出格式>：此参数的效果和指定“-printf”参数类似，但会把结果保存成指定的列表文件；
		-fstype<文件系统类型>：只寻找该文件系统类型下的文件或目录；
		-gid<群组识别码>：查找符合指定之群组识别码的文件或目录；
		-group<群组名称>：查找符合指定之群组名称的文件或目录；
		-help或——help：在线帮助；
		-ilname<范本样式>：此参数的效果和指定“-lname”参数类似，但忽略字符大小写的差别；
		-iname<范本样式>：此参数的效果和指定“-name”参数类似，但忽略字符大小写的差别；
		-inum<inode编号>：查找符合指定的inode编号的文件或目录；
		-ipath<范本样式>：此参数的效果和指定“-path”参数类似，但忽略字符大小写的差别；
		-iregex<范本样式>：此参数的效果和指定“-regexe”参数类似，但忽略字符大小写的差别；
		-links<连接数目>：查找符合指定的硬连接数目的文件或目录；
		-iname<范本样式>：指定字符串作为寻找符号连接的范本样式；
		-ls：假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出；
		-maxdepth<目录层级>：设置最大目录层级；
		-mindepth<目录层级>：设置最小目录层级；
		-mmin<分钟>：查找在指定时间曾被更改过的文件或目录，单位以分钟计算；
		-mount：此参数的效果和指定“-xdev”相同；
		-mtime<24小时数>：查找在指定时间曾被更改过的文件或目录，单位以24小时计算；
		-name<范本样式>：指定字符串作为寻找文件或目录的范本样式；
		-newer<参考文件或目录>：查找其更改时间较指定文件或目录的更改时间更接近现在的文件或目录；
		-nogroup：找出不属于本地主机群组识别码的文件或目录；
		-noleaf：不去考虑目录至少需拥有两个硬连接存在；
		-nouser：找出不属于本地主机用户识别码的文件或目录；
		-ok<执行指令>：此参数的效果和指定“-exec”类似，但在执行指令之前会先询问用户，若回答“y”或“Y”，则放弃执行命令；
		-path<范本样式>：指定字符串作为寻找目录的范本样式；
		-perm<权限数值>：查找符合指定的权限数值的文件或目录；
		-print：假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式为每列一个名称，每个名称前皆有“./”字符串；
		-print0：假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式为全部的名称皆在同一行；
		-printf<输出格式>：假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式可以自行指定；
		-prune：不寻找字符串作为寻找文件或目录的范本样式;
		-regex<范本样式>：指定字符串作为寻找文件或目录的范本样式；
		-size<文件大小>：查找符合指定的文件大小的文件；
		-true：将find指令的回传值皆设为True；
		-typ<文件类型>：只寻找符合指定的文件类型的文件；
		-uid<用户识别码>：查找符合指定的用户识别码的文件或目录；
		-used<日数>：查找文件或目录被更改之后在指定时间曾被存取过的文件或目录，单位以日计算；
		-user<拥有者名称>：查找符和指定的拥有者名称的文件或目录；
		-version或——version：显示版本信息；
		-xdev：将范围局限在先行的文件系统中；
##### 参数
-type 参数如下:
	f 普通文件
	l 符号连接
	d 目录
	c 字符设备
	b 块设备
	s 套接字
	p Fifo

## shell 脚本 - 安全无限制启动vncserver
```bash
#!/bin/bash
chkconfig: 2345 100 95
description: start vncserver :1 port 5901
file="/tmp/.X1-lock"
tmpDirectory="/tmp/"
directory="/tmp/.X11-unix/X1"
svcname="Xvnc"
if  ! ps -ef | grep $svcname | egrep -v grep >/dev/null
then
    if [ -d $tmpDirectory ]
    then
        echo "tmp文件夹不存在, 创建/tmp/文件夹中..."
        mkdir $tmpDirectory
        chmod 1777 $tmpDirectory
    if [ -d $directory ]
    then
        echo "$directory  is existed 删除$directory 文件夹"
        rm -rf $directory
    fi
    if [ -f $file ]
    then
        echo "$file is existed 删除$file 文件"
        rm -rf $file
    fi
    echo "$svcname is not started! 启动 vncserver :1"
   vncserver :1
    echo "执行 vncserver -list 查看vnc窗口号"
   vncserver -list
    echo "查看 ps命令执行结果"
    ps -ef | grep vnc
else
    echo "$svcname is started"
fi
```
## 查看linux系统使用的哪种桌面环境类型
```bash
// 1: 进入系统桌面后
env | grep DESKTOP_SESSION=
// 2: 进入系统桌面后
echo $DESKTOP_SESSION
// 3: 进入系统桌面后
echo $GDMSESSION
```

## 添加用户
```bash
// username 就是你想要添加的用户
useradd -m username
// 为新添加用户设置密码
passwd username //根据提示设置
// 创建的用户添加到sudo用户组 安装软件权限 特权模式权限等
usermod -a -G sudo username
//创建的用户的默认外壳改成bash
chsh -s /bin/bash username
```
## 安装软件时自动输入
```bash
echo '输入的文字'|sudo -S apt-get install 插件名-y
```


