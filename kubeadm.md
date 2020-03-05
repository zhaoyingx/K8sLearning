### 在阿里云上利用kubeadm搭建一个集群

生产环境，可以使用阿里云托管的Kubernetes集群——ACK，现在我想使用kubeadm搭建一个master单点的集群，不是ha的

整个过程需要以下几个步骤

1.	创建VPC、子网、安全组、密钥对等

2.	购买ECS

3.	登录到ECS，安装如docker、kubeadm。kubelet等

### VPC、子网（交换机）、安全组

设置可以参考以下图片

<img width="600" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/7@2x.png">

<img width="600" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/8@2x.png">

<img width="600" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/9@2x.png">

<img width="600" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/10@2x.png">

#### 购买虚拟机

选择spot竞价实例，规格就2c4g即可，购买3台，一台作为master节点，两台作为node节点

镜像我选了CentOS7.7，试了好几种机型，才遇到我所选择的可用区提供spot的

<img width="800" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/11@2x.png">

实例创建完成

<img width="600" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/12@2x.png">