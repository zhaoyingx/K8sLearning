### 自动伸缩过程

#### 如何获取Pod度量，即CPU使用率/QPS等数据

有运行在每个节点的kubelet上的cAdvisor的agent采集

#### 如何计算所需Pod副本数

所有Pod度量求和，再除以HPA配置的目标度量值，向上取整；如果配置了多个度量目标，则取计算出的最大值

cAdvisor负责采集，Heapster聚合所采集的全部数据，Autoscaler计算所需副本数，HPA控制更新replicas

Audoscaler扩容速率：单次扩容的副本数；两次扩容的时间间隔

修改一个已有HPA的度量值：可以直接Kubectl edit修改，Autoscaler会检测到度量发生变化；也可以删除旧的HPA，重新apply一个

#### 基于CPU伸缩，基于内存伸缩

基于CPU伸缩，deployment为什么要熬设置cpu resource request值？Autoscaler需要对比Pod的实际CPU使用与CPU requests

#### 自定义度量

哪些度量适合用到自动伸缩？为什么内存不是一个适合的度量：增加副本数，度量值呈线性或者接近线性下降，即可作为度量

### 区分横向伸缩和纵向伸缩

目前还不支持改变正在运行的Pod的resource request和limit？ 

###集群节点自动伸缩 Cluster Autoscaler

<img width="600" src="https://github.com/zhaoyingx/K8sLearning/blob/master/images/1@2x.png">