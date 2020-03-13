### 一些记录

statefulset为它所管理的pod提供<strong>序号</strong>和<strong>唯一性保证</strong>， provides guarantees about the ordering and uniqueness of these Pods.

如何提供序号？

如何提供唯一性保证？

选来创建一个statefulset， 使用官网提供的[yaml](https://kubernetes.io/zh/docs/tutorials/stateful-application/basic-stateful-set/#%e5%88%9b%e5%bb%ba-statefulset)文件例子

<img width="600" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/31@2x.png">

可以看到，有statefulset创建出来的pod的name被进行了编号，0，1

再查看下创建出的pvc，也被进行了编号

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/32@2x.png">

查看源码，发现podname和pvcname遵循以下规则

<img width="600" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/30@2x.png">