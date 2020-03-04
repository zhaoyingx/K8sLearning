### PV & PVC

PV描述一个持久化存储卷，即一个具体的宿主机上的存储路径

PVC描述的是Pod所期望使用的存储化的属性，比如存储大小，可读写权限等

创建完的PVC如果要使用，必须和某一个PV进行绑定。 绑定需要检查两个条件：1. spec字段，如pvc指定的存储大小，pv必须满足； 2. pv和pvc的storageClassName必须一致

在创建Pod时，如果Pod中定义的pvc找不到pv进行绑定（bound），那么Pod就会启动失败。当pv被创建出来后，PersistentVolumeController会检查到已经有可用的pv，就会将这个非bound的pvc与pv进行绑定。

绑定时会将pv对象的名字填在pvc的spec.volumeName上，这样就完成了绑定，通过访问pvc，就一定会找到pv路径。 所以pvc相当于一个“接口”

容器的Volume就是宿主机上的一个路径与容器里一个路径进行绑定

持久化：容器重启、或者节点下线，数据都不会丢失。-远程存储

持久化宿主机目录的过程：分两个阶段：attach & mount

attach：Instanceid与Diskid相关联， attach需要调用公有云的API接口

mount：将磁盘挂载到宿主机特定的挂载点上

完成mount后，路径就是一个持久化的目录了，数据会存储在远程的磁盘上

如果持久化类型是文件存储，那么不需要attach过程，即不需要将存储设备挂载到宿主机上

AttachDetachController检查pod对应的pv的宿主机的挂载情况，决定是否要执行attach、detach。 AttachDetachController是kubeController的一部分，它运行在master上。

mount控制器VolumeManagerReconciler是kubelet的一部分，运行在node节点上

### StorageClass

作用：创建pv模板

#### Local Persistent Volume

首先在集群中配置好存储设备，即给虚拟机额外挂载一个磁盘

StorageClass volumeBindingMode=WaitForFirstConsumer含义，Pod被调度时执行绑定