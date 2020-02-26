`docker search centos`  查找镜像

`docker pull centos` 拉取镜像

`docker run -it centos:latest /bin/bash` 进入容器

<img width="400" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/2@2x.png">

可以看到，在被隔离的容器内，bash的PID进程号是1，看不到宿主机上的其它进程

在宿主机上执行ps

<img width="400" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/3@2x.png">

在容器中执行一些安装，然后提交 `docker commit -m "local test" -a "y" 593ccd6fd61b zhaoying/centos:v0`

<img width="400" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/4@2x.png">

<img width="400" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/5@2x.png">

Dockerfile Command

<strong>FROM</strong> imagename

<strong>COPY</strong> 

<strong>RUN</strong>

<strong>EXPOSE</strong>

<strong>CMD</strong>

<strong>LABEL</strong>

执行`docker run -p 8888:5000 --name localwebapp zhaoying/localtestwebapp`

访问`http://localhost:8888/`

登录 `docker login`

退出 `docker logout`

<img width="400" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/6@2x.png">

上传到dockerhub `docker push zhaoying/localtestwebapp`

