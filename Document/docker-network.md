docker网络按照部署方式大致可分为单机网络、集群网络：本地网络主要指容器通过宿主机连接外部网络，集群网络主要指相关联容器跨主机通讯以及
不同服务之间的网络隔离。docker的网络支持插件的形式，即使用的网络模式可自由配置，一般默认网络模式：**host**、**overly**、**macvlan**、**none**

-   bridge

docker默认的网络模式，属于本地网络，一般将应用部署在一个容器的情况下使用该模式。一个bridge网络，也称为docker_gwbridge。在我们使用该模式运行容器时，
dokcer bridge driver自动为容器与宿主机之间创建。在同一bridge网络下，容器之间可以相互通信，不同的bridge下容器之间无法建立连接。在集群环境中，要使
不同docker daemon hosts的容器通过bridge相互连接，需要层次上进行路由管理，或使用overly网络模式。

-   host

该网络模式下，容器与宿主机共同使用相同的网络空间。

-   overly

docker集群网络，dokcer swarm默认的网络模式。一个overly网络，也称之为ingress。在该网络模式下，同一service下的多个容器可以实现跨主机通信。

-   macvlan
-   none

参考：https://docs.docker.com/network/#network-drivers