### 关于DaemonSet的一些记录

DaemonSet保证符合要求的节点运行一个pod副本，节点增加时自动部署pod，节点删除时自动删除pod。 当删除DaemonSet时，会一并删除由它所创建的pod。

DaemonSet的pod模板的restartpolicy必须为always（或者不指定，默认是always）

仅在某些节点上运行DaemonSet： 需要指定nodeSelector： .spec.template.spec.nodeSelector

也可以指定.spec.template.spec.affinity。

如果不指定，则会在所有节点上创建pod

更新daemonset