### 管理

-   安装，参考：http://rancher.com/docs/rancher/v1.6/en/installing-rancher/installing-server/
```
$ docker run -d --name rancher --restart=unless-stopped -p 8080:8080 rancher/server
```

rancher可以使用不同的编排工具，但无法直接使用命令创建运行容器，只能通过创建yml文件编排。

### 基于rancher的kubernetes部署

由于kubernetes默认设置的原因，致使部署kubernetes无法启动kubernetes-dashboard，
所以需要进行修改，修改kubernetes默认镜像来源。
参考来源：
https://www.cnrancher.com/rancher-k8s-accelerate-installation-document/
https://www.cnrancher.com/kubernetes-installation/
https://www.kubernetes.org.cn/2955.html

-   前提准备
    -   关闭主机swap
    ```
    使用命令关闭swap，但本地主机重启后无效
    $ sudo swapoff -a
    使用命令查看是否关闭
    $ free -m
    若swap为0，则关闭成功。
    永久关闭——编辑/etc/fstab，注释掉swap一行
    ```
-   版本对应，即docker版本与rancher版本，本次实验rancher：stable，
    docker-17.06.0-ce，rancher/ks：v1.7.7-rancher1

-   运行rancher
-   进入rancher UI界面，——系统管理——系统设置
    在添加应用商店一览添加如下信息
    名称：library
    地址：https://git.oschina.net/rancher/rancher-catalog.git
    分类：k8s-cn，据说已不再维护，可使用k8s-v1.6-release
    来源：http://blog.csdn.net/csdn_duomaomao/article/details/78068178
    然后保存。

-   查看kubernetes环境模板是否发生改变
    若无，可进行如下修改：
    Private Registry for Add-Ons and Pod Infra Container Image一览填写:
    registry.cn-hangzhou.aliyuncs.com
    Pod Infra Container Image一览填写:
    rancher-cn/pause-amd64:3.0

-   重新添加kubetnetes环境，添加主机
-   等待一段时间，dashboard可使用。


