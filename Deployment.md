运行中的rs升级 - 更改镜像Tag

获取rs如下：

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/26@2x.png">

`kc edit deployment mysql-1577770266`

修改镜像的Tag，改成‘8.0.19’

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/27@2x.png">

`kc get pods --watch`

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/28@2x.png">

旧的pod被删除

`kc get pod mysql-1577770266-668bf9dd78-6rxck -o yaml`， 可以看到，镜像Tag已经变了

<img width="500" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/29@2x.png">