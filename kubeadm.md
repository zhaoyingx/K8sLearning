### 在阿里云上利用kubeadm搭建一个集群

生产环境，可以使用阿里云托管的Kubernetes集群——ACK，现在我想使用kubeadm搭建一个master单点的集群，不是ha的

整个过程需要以下几个步骤

1.	创建VPC、子网、安全组、密钥对等

2.	购买ECS

3.	登录到ECS，安装如docker、kubeadm。kubelet等

### VPC、子网（交换机）、安全组

设置可以参考以下图片

<img width="400" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/7@2x.png">

<img width="400" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/8@2x.png">

<img width="400" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/9@2x.png">

<img width="400" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/10@2x.png">

#### 购买虚拟机

选择spot竞价实例，规格就2c4g即可，购买3台，一台作为master节点，两台作为node节点

镜像我选了CentOS7.7，试了好几种机型，才遇到我所选择的可用区提供spot的

<img width="800" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/11@2x.png">

实例创建完成

<img width="600" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/12@2x.png">

### 登录到ECS

chmod 600 xxx.pem

执行`ssh -i kp-xx.pem root@x.x.x.x`, 登录成功

#### 安装docker

可以参考这个官方[链接](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

1.	安装docker

参考[链接](https://docs.docker.com/install/linux/docker-ce/centos/)，选择Install using the repository

执行`sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2`


开始了

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/13@2x.png">

安装完成后：

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/14@2x.png">

执行`sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo`

完成后

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/15@2x.png">

执行`sudo yum install docker-ce docker-ce-cli containerd.io`

开始了

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/16@2x.png">

安装完成后

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/17@2x.png">

执行`docker version`

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/18@2x.png">

启动docker, `sudo systemctl start docker`

Verify that Docker Engine - Community is installed correctly by running the hello-world image. `sudo docker run hello-world`

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/19@2x.png">

OK ,docker安装完成了

#### 安装kubeadm、kubelet、kubectl

这里需要，kubeadm把需要的各组件都使用容器方式安装运行，但是kubelet是直接安装在宿主机上的