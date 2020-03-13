### 在阿里云上利用kubeadm搭建一个集群

生产环境，可以使用阿里云托管的Kubernetes集群——ACK，现在我想使用kubeadm搭建一个master单点的集群，不是ha的

整个过程需要以下几个步骤

1.	创建VPC、子网、安全组、密钥对等

2.	购买ECS

3.	登录到ECS，安装如docker、kubeadm。kubelet等

### 前期准备

#### VPC、子网（交换机）、安全组

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

#### 登录到ECS

chmod 600 xxx.pem

执行`ssh -i kp-xx.pem root@x.x.x.x`, 登录成功

### 安装

可以参考这个官方[链接](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

#### 安装docker

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

这里需要注意，kubeadm把需要的各组件都使用容器方式安装运行，但是kubelet是直接安装在宿主机上的. Kubernetes官方[文档](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)提供了安装方式，如下图所示

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/20@2x.png">

在执行`yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes`时，如果购买的虚拟机是大陆地区的，可能拉不到镜像安装失败

安装完成

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/21@2x.png">

在另外机器上，重复以上安装操作

### 集群搭建

在master节点上，执行`kubeadm init`, 之后会做一系列检查

`
W0305 17:49:28.444727    2038 validation.go:28] Cannot validate kube-proxy config - no validator is available
W0305 17:49:28.444813    2038 validation.go:28] Cannot validate kubelet config - no validator is available
[init] Using Kubernetes version: v1.17.3
[preflight] Running pre-flight checks
	[WARNING Service-Docker]: docker service is not enabled, please run 'systemctl enable docker.service'
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [izt4ncitbq8nvz1nis6u3sz kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 10.10.190.99]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [izt4ncitbq8nvz1nis6u3sz localhost] and IPs [10.10.190.99 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [izt4ncitbq8nvz1nis6u3sz localhost] and IPs [10.10.190.99 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
W0305 17:49:59.628098    2038 manifests.go:214] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[control-plane] Creating static Pod manifest for "kube-scheduler"
W0305 17:49:59.629138    2038 manifests.go:214] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 21.002628 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.17" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node izt4ncitbq8nvz1nis6u3sz as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node izt4ncitbq8nvz1nis6u3sz as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: yi3u77.7te5j2fyu17bavh2
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy
Your Kubernetes control-plane has initialized successfully!
To start using your cluster, you need to run the following as a regular user:
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/
Then you can join any number of worker nodes by running the following on each as root:`


<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/22@2x.png">

master节点已经ready了，执行`kubectl config view` , 可以查看集群配置信息

接下来将node节点加入进来，执行'kubeadm join 10.10.190.99:6443 --token yi3u77.7te5j2ddddfxxxxxxv \
    --discovery-token-ca-cert-hash sha256:04a4f28a219xxxxxxxxxxxxxxxxxxxx46db0226d08a096a2f295d7320'


在master节点上执行`kubectl get nodes`

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/23@2x.png">

可以看到节点已经加进来了， 不过为啥是NotReady ?

用`kubectl describe node izt4ncitbq8ndd` 查看， 发现一个报错<strong> KubeletNotReady runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized</strong>

这个[issue](https://github.com/kubernetes/kubeadm/issues/1031)提到了同样的问题，并且给出了解决方法：

在master节点上执行`kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.ym`

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/24@2x.png">

可以了，至于原因我没有细看，先解决问题再说，现在各节点已经是Ready状态了

至此，一个集群已经搭建完成，注意这个集群不是高可用的，所以只能用于实验环境

### 后续

进入到/etc/kubernetes中

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/25@2x.png">