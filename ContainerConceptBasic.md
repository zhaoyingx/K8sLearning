### 一些容器相关的基本概念记录

#### Docker

##### 实现原理

<strong>隔离</strong> 利用Linux的Namespace，Docker为进程提供了一个隔离出来的空间，使得被隔离的进程看不到容器外部的其它进程。在容器空间内，进程有了一个新的容器内的PID，但对于宿主机来说，该进程的PID还是其宿主机上的真实PID。并且除了隔离出PID Namespace，还有User、Network等Namespace。Docker为进程指定了一组Namespace参数，使得被隔离的进程只能看到限定的资源。所以，容器是一种特殊的进程。

<strong>限制</strong> 容器内进程虽然被隔离，但对于宿主机来说，它还是跟宿主机上的其它进程共享资源。而Linux Cgroups可以限定进程能使用的资源上限。

<strong>挂载根目录</strong> 启用Mount Namespace后，需要挂载容器进程的根目录（如果不重新挂载，那么容器进程的根目录内容跟宿主机是一样的）。这个挂载在根目录上用来为容器进程提供执行环境的文件系统，叫rootfs（根文件系统）。一个常见的rootfs，包括以下目录：bin dev etc home lib lib64 mnt opt proc root run sbin sys tmp usr var，和宿主机类似。

#### Kubernetes

##### Kubernetes架构

Master节点+Node节点，分别对应控制节点和计算节点。计算节点上最重要的组建是<strong>Kubelet</strong>, Kubelet负责同容器运行时（例如Docker）交互，这个交互依赖CRI，即Container Runtime Interface。所以，K8s并不关心使用Docker还是其它容器运行时，只要所使用的容器运行时能够运行标准的容器镜像，就可以通过实现CRI接口接入到Kubernetes。

Kubernetes从一个宏观的视角定义了各容器之间的交互关系，利用Pod划分容器。

总体来说，Kubernetes的对象分为编排对象和服务对象，编排对象例如Replicaset，Cronjob等，服务对象例如Service，Secret，HPA等。

Kubernetes的核心思想，即按照用户意愿和系统规则来自动处理各容器之间的关系，这就是所谓的”容器编排能力“。

##### Kubernetes各组件作用

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/0%402x.png">

有上图可以看出，API Server和其它组件通信基本都是有那些其它组件发起。为了保证高可用，Master中<strong>etcd和API Server组件都包含多个实例</strong>运行在多个服务器上——Kubernetes的分布式特性。

但是Scheduler和Controller Manager在特定时间内<strong>只能有一个实例运行</strong>。

Master中各组件可以作为pod来运行。

<strong>etcd介绍</strong>

1.	key-value 分布式存储
2.	防止API Server重启时数据丢失
3.	etcd唯一的客户端就是API Server（但一般是多个实例）
4.	如何保证多个etcd实例的一致性？这里了解下一致性算法原理
5.	etcd实例数量一定要是基数

<strong>API Server介绍</strong>

kubectl apply -f xx.yaml通过发送http post请求到API Server，API Server将对象存储到etcd。客户端发送请求到API Server后，API Server需要对客户端进行验证。

<strong>调度器介绍</strong>

1.	默认的调度算法：查找可用节点，从可用节点中找出最有节点
2.	查找可用节点的方式：通过一个预测函数
3.	pod可以不适用默认的调度器default-scheduler，可以通过设置schedulerName指定一个自己的调度器

<strong>控制器介绍</strong>

<strong>Kubelet介绍</strong>