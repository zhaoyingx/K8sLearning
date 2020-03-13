### 关于Kubernetes apiversion的记录

在yaml文件中，可以发现关于apiVersion字段有时候是v1，有时候是apps/v1， 那么创建不同资源该使用哪种apiVersion该如何确定呢

查看当前集群可用的apiVersion，使用命令`kc api-versions`, 输出如下

<img width="300" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/33@2x.png">

看起来主要分两大类：<strong>v1</strong> 以及 <strong>xxgroup/xxversion</strong>

在<strong>xxgroup/xxversion</strong>中，又分为 <strong>group/v1</strong>, <strong>group/v1beta</strong>, <strong>group/v1alpha</strong>

其中beta和alpha都是属于测试版

那么v1和xxgroup/v1 该怎么使用呢， 需要参考Kubernetes官方提供的API文档， 链接： https://kubernetes.io/zh/docs/reference/

查看1.17： https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/

在该文档中，分别对workload、service、config & storage、 metadata、 cluster的apiVersion进行了说明

例如workload的apiVersion，包含如下：

<img width="300" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/34@2x.png">

查看具体的apiVersion使用，如Daemonset

<img width="300" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/35@2x.png">

<img width="300" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/36@2x.png">

所以什么样的kind该使用哪个apiVersion，查阅Kubernetes对应版本的文档即可