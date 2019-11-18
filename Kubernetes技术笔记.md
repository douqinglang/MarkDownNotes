# Kubernetes 技术笔记
Kubernetes 简称 k8s 或者是kube
使用Kubernetes API 对象描述集群的预期状态(desired state)
Kubernetes系统中 Kubernetes是持久化的实体. Kubernetes使用这些实体表示这些整个集群的状态.

## 命令行工具:kubectl
kubernetes组件:
1. Master组件: 集群控制. 全局调度,检测和响应集群事件
Pod: 为其组成的容器提供两种共享资源: 网络和存储
网络: 每一个Pod都分配唯一的ip地址. Pod中每个容器都共享网络 命名空间, 包括ip地址和网络端口. Pod内的容器使用localhost相互通信, 当Pod中的容器和Pod外的实体通信时, 必须配置如何使用网络资源(如:端口)
存储: 可以指定一组 共享存储Volume. 所有Pod中的容器都可以访问共享Volume, 从而这些容器共享数据. 如果需要启动其中的一个容器, 则卷还允许Pod中的持久数据存活.
## Kubernetes对象约束(Spec)和状态(Status)
每个Kubernetes对象包含两个字段, 对象Spec 和 对象Status
## pod对象
pod 是Kubernetes项目中的最小编排单位, 容器就是pod对象的一个字段. 凡是调度, 网络, 存储和安全相关的属性, 基本上都是pod级别. 
### pod中常用的字段含义及用法
#### NodeSelector
供用户将Pod和Node进行绑定的字段, 
用法示例:
```	yaml
apiVersion:v1
kind: pod
...
spec:
	nodeSelector:
		disktype: ssd
```

这样的配置, 意味着这个Pod只能运行在携带了"disktype: ssd" 标签(label)的节点上, 否则就是调度失败
#### NodeName
如果pod的NodeName字段被赋值, Kubernetes会默认这个pod已经是被调度过, 调度的结果就是此字段赋值的节点名字. 这个字段有调度器负责设置, 用户设置后就可以"骗过"Kubernetes的调度器.
一般这个做法会在测试中用到
#### HostAliases
定义了hosts文件(比如: /etc/hosts)里的内容
用法示例:
```yaml
apiVersion: v1
kind: pod
...
spec:
	hostAliases:
	- ip: "10.1.2.3"
	hostname:
	- "foo.remote"
	- "bar.remote"
...等
```

这个pod的yaml文件中, 设置到一组ip和hostname数据. 这样的pod启动后, /etc/hosts文件如下所示
```
cat /etc/hosts
# Kubernetes-managed host file
127.0.0.1 localhost
...
10.244.135.10 hostaliases-pod
10.1.2.3 foo.remote
10.1.2.3 bar.remote
```

最下面的两行, 就是通过pod的yaml的HostAliases字段的pod设置. 在Kubernetes中, 如果需要设置hosts文件中的内容, 一定要通过此方法. 如果直接修改了hosts文件, 在pod被删除重建后, kubulet会自动覆盖被修改的内容.
凡是和容器的Linux namespace相关的属性, 一定是Pod级别的. Pod在设计就是让里面的容器尽可能多的共享Linux Namespace, 仅保留必要的隔离和限制能力. 
#### Containers
InitContainers 和 Container 都是属于Pod对容器的定义, 内容也完全相同, 只是InitContainers的生命周期会先于所有的Containers, 并严格按照定义顺序执行.
Kubernetes项目中对container的定义和docker中的定义差不多. Image(镜像), Command(启动命令), workingDir(容器的工作目录), Ports(容器要开放的端口), volumeMounts(容器要挂载的Volume)都是构成Containers的主要字段.
#### ImagePullPolicy字段
定义拉取镜像的策略. 和Container是一个级别. 默认: Always, 即每次创建Pod都重新拉取镜像, 当容器的镜像类似ngnix或ngnix:latest这样的名字时, ImagePullPolicy也会被认为是Always.如果值为Never或者是IfNotPresent, 意味着Pod创建时永远不会拉取镜像或只有宿主机上不存在这个镜像时才会拉取

#### Lifecycle
Container Lifecycle Hooks, 容器状态发生变化时触发一系列"钩子"
例如:
```yaml
apiVersion: v1
kind: pod
metadata:
	name: lifecycle-demo
spec:
	container:
	- name: lifecycle-demo-container
	image: ngnix
	lifecycle:
		postStart:
			exec: 
				command: ["/bin/bash", "-c", "echo Hello from the postStart handler > /usr/share/message"]
		preStop:
			exec:
				command: ["/usr/sbin/ngnix", "-s", "quit"]
```

spec.container.lifecycle.postStart: 容器启动后, 立刻执行一个操作. 虽然是docker容器ENTRYPOINT执行之后, 但是并不是严格按照顺序, 有可能postStart启动时, ENTRYPOINT有可能没有结束
spec.container.lifecycle.preStop: 在容器被杀死之前, preStop操作是同步的, 会阻塞当前容器杀死的流程
### Pod的生命周期
Pod生命周期的变化主要体现在Pod API对象的Status字段, 是除Metadata, Spec之外重要字段. pod.status.phase就代表Pod的当前状态, 
1. Pending: Pod已经将yaml文件提交给了Kubernetes, API对象已经被创建并保存在etcd中. 有可能Pod中的有些容器因为有种原因不能被顺利创建, 如调度失败
2. Running: 这个状态, Pod已经成功调度跟一个具体节点绑定, 包含的容器已经创建成功, 并且至少有一个正在运行中
3. Succeeded: Pod中的所有容器都已经成功运行完毕, 并已经退出. 在运行一次性任务中比较常见
4. Faild: Pod中至少有一个容器以不正常的状态（非 0 的返回码）退出。需要debug这个容器应用, 查看 Pod 的 Events 和日志。
5. Unknown: 异常状态，意味着 Pod 的状态不能持续地被 kubelet 汇报给 kube-apiserver，这很有可能是主从节点（Master 和 Kubelet）间的通信出现了问题。
## Deployment对象
实现了Kubernetes项目的功能: Pod的"水平扩展/收缩", 此阿勇"滚动更新"(rolling update), 依赖API对象ReplicaSet
### ReplicaSet yaml文件
主要由副本数目定义和一个Pod模板组成. 也是Deployment的子集. 而Deployment控制器操作的是ReplicaSet对象, 不是Pod对象
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-set
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
```



## Kubernetes常用命令
### 1.查看安装Kubernetes系统日志
journalctl -l -u kubelet
### 2. 查看Deployment状态信息
```yaml
$ kubectl get deployments
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3 
```

1. DESIRED: 用户期望的Pod的个数 = spec.replicas的值
2. CURRENT: 当前处于Running状态的Pod的个数
3. UP-TO-DATE: 处于最新版本的Pod的个数. Pod的Spec部分和Deployment里的Pod模板定义一样
4. AVAILABLE: 可用的Pod个数, 既是Running, 又是最新版本并处于Ready状态的Pod个数

### 3 实时查看Deployment对象状态变化
```yaml
$ kubectl rollout status deployment/nginx-deployment
Waiting for rollout to finish: 2 out of 3 new replicas have been updated...
deployment.apps/nginx-deployment successfully rolled out
```
### 4 带有命名空间Kubernetes 命令
kubectl describe pods 名称 -n 命名空间
### 5
### 6
### 7
### 8
### 9