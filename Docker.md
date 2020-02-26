`docker search centos`  查找镜像

`docker pull centos` 拉取镜像

`docker run -it centos:latest /bin/bash` 进入容器

<img width="300" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/2@2x.png">

可以看到，在被隔离的容器内，bash的PID进程号是1，看不到宿主机上的其它进程

在宿主机上执行ps

<img width="300" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/3@2x.png">