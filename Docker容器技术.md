# Docker容器技术
## 1.组件
### 1.1.Docker客户端和服务端
提供了一个命令行工具docker和一整套restfulAPI
### 1.2.Docker镜像
用户基于镜像运行自己的Docker容器.镜像是基于联合文件系统的一种层式结构, 由一系列指令一步一步构建出来的
### 1.3.Docker注册中心
Docker用Registry来保存镜像. Registry有公共和私有两种. Docker公司运营公共的Registry叫做Docker Hub, 用户可以在Docker Hub注册账号分享并保存自己的镜像(下载镜像慢, 可以自己构建私有的Registry)
### 1.4.Docker容器
容器基于镜像启动, 一旦容器启动完成, 就可以登录到容器中安装需要的软件或服务
容器: 一个镜像格式; 一系列标准操作; 一个执行环境
## 2. docker命令
1. docker特权模式, 端口映射 运行容器 --name设置容器名称 --privileged使用特权模式 端口映射主机端口:容器端口 镜像名称 /usr/sbin/init->使用systemctl
-p指定要映射的端口，一个指定端口上只可以绑定一个容器
-P将容器内部开放的网络端口随机映射到宿主机的一个端口上
docker run -di --name=centos7-xfce-vnc-demo --privileged=true -p 45901:5901 centos:latest /usr/sbin/init
- -di 后台守护式进程启动 容器启动后以后台方式运行, 并执行 /usr/sbin/init 脚本
docker run -di --name=centos-xfce-vnc-dhl centos-xfce-vnc:2.0 /bin/bash
docker run -it --name=xfce-vnc-0828 xfce-vnc-0828:2.1 /usr/local/bin/bash
- -it 前台启动, 容器启动后立刻进入容器运行 /usr/local/bin/bash shell脚本

2. docker进入容器
docker exec -it 43e575457614(或者容器名称) /bin/bash

3. docker 登录私有仓库
docker login 192.168.3.152  ->用户名douhl, 密码Yian2019

4. docker 将正在运行的容器做成镜像 docker commit 容器id/名称 仓库名称/镜像名称:版本号
docker commit 2a8a60d8d7c3 centos7-xfce-vnc:1.0()

5. docker 删除镜像 docker rmi 镜像id
docker rmi aa8163a7522d
6. 删除停止容器
docker rm -f 容器id/容器名

7. docker 更改镜像名称-> docker tag 镜像id/名称 新镜像名:版本号
docker tag aa8163a7522d centos7-xfce-vnc:1.1

8. docker 将镜像名和私有仓库引用
docker tag SOURCE_IMAGE[:TAG] 192.168.3.152/private_test/IMAGE[:TAG]

9. docker 将带私有仓库的引用镜像传到私有仓库
docker push 192.168.3.152/private_test/IMAGE[:TAG]

10. docker查看容器端口映射 输出: 5901/tcp -> 0.0.0.0:45901
docker port container_ID 
11. 查看所有运行中的容器
docker container list
docker ps
12. 查看已经停止的容器
docker container list -all
13. Docker导入本地镜像
cat 本地镜像名.tar.gz | docker import - 导入镜像名:版本号
14. 从容器中创建一个镜像
docker commit [options] containerID [repository[:tag]]
options说明:
-a: 提交镜像的作者
-c: 使用Dockerfile指令创建镜像
-m: 提交时的说明
-p: 在提交时将容器暂停
15. 在docker镜像仓库中保存(下载)镜像到本地
docker save -o 本地要保存的镜像名.tar 远程镜像名(imageId)  # -o：指定保存的镜像的名字
15. 
