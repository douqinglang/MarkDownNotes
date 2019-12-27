# Kali系统学习笔记
## Kali基于Debian系统
### Kali使用中文简体乱码解决
```bash
确定locales已经安装，用"apt-get install locales"命令；之后可用”locale -a”查看当前系统支持的字符集。
1. 在命令行输入”dpkg-reconfigure locales”。进入图形化界面之后，（空格是选择，Tab是切换，*是选中），选中en_US.UTF-8和zh_CN.UTF-8，确定后，将en_US.UTF-8选为默认。
2. 安装中文字体，”apt-get install xfonts-intl-chinese “和” apt-get install ttf-wqy-microhei”，这时发现网页不乱码，系统也不乱码。
3. 重启 。
```
## 安装ssh
ssh链接可以远程管理linux设备，默认端口是22，安装好系统默认是不开启的，需要修改配置文件
### 1.修改/etc/ssh/sshd_config配置文件
```bash
vim /etc/ssh/sshd_config
```
1.1 找到#PasswordAuthentication yes  把#的注释去掉
```bash
PasswordAuthentication yes
```
1.2 将PermitRootLogin without-password修改为：
```bash
PermitRootLogin yes
```
### 2: 然后启动ssh服务，这时候先不要链接，因为链接不上
```bash
/etc/init.d/ssh start
```
### 3: 然后设置开机自动启动
```bash
update-rc.d ssh enable
```
### 4: 次重启系统并且使用xshell在链接
```bash
reboot
```
## Kali常用命令
```bash
1.系统

uname -a查看内核/操作系统/CPU信息
head -n 1 /etc/issue查看操作系统版本
cat /proc/cpuinfo查看CPU信息
hostname查看计算机名
lspci -tv列出所有PCI设备
lsusb -tv列出所有USB设备
lsmod列出加载的内核模块
env查看环境变量
2.资源

free -m查看内存使用量和交换区使用量
df -h查看各分区使用情况
du -sh <目录名>查看指定目录的大小
grep MemTotal /proc/meminfo查看内存总量
grep MemFree /proc/meminfo查看空闲内存量
uptime查看系统运行时间、用户数、负载
cat /proc/loadavg查看系统负载
3.磁盘和分区

mount | column -t查看挂接的分区状态
fdisk -l查看所有分区
swapon -s查看所有交换分区
hdparm -i /dev/hda查看磁盘参数(仅适用于IDE设备)
dmesg | grep IDE查看启动时IDE设备检测状况
4.网络

ifconfig查看所有网络接口的属性
iptables -L查看防火墙设置
route -n查看路由表
netstat -lntp查看所有监听端口
netstat -antp查看所有已经建立的连接
netstat -s查看网络统计信息
5.进程

ps -ef查看所有进程
top实时显示进程状态
6.用户

w查看活动用户
id <用户名>查看指定用户信息
last查看用户登录日志
cut -d: -f1 /etc/passwd查看系统所有用户
cut -d: -f1 /etc/group查看系统所有组\
crontab -l查看当前用户的计划任务
7.服务

chkconfig –list列出所有系统服务
chkconfig –list | grep on列出所有启动的系统服务
```
