##### docker volume针对容器存储，可分为本地存储、跨主机存储。本地存储即挂载本地某一文件到对应容器，存在问题：
##### 一旦该主机宕机，存储的数据将无法使用，如MySQL容器，故需要一些其他主机作为存储后端，即为跨主机存储

-   docker支持的存储后端
    参考来源：https://docs.docker.com/engine/extend/legacy_plugins/#volume-plugins
    有：Microsoft Azure File Storage、cinder等，支持的插件有：
    Azure File Storage plugin
    REX-Ray plugin：该插件支持多个平台：VirtualBox, EC2, Google Compute Engine, OpenStack, and EMC；
    
-   使用rex-ray插件，openstack作为后端
    详细信息参考：https://github.com/thecodeteam/rexray
    -   安装rex-ray
        ```
        curl -sSL https://dl.bintray.com/emccode/rexray/install | sh -
        ```
    -   配置rex-ray，在/etc/rexray/创建并编辑config.yml
        如何创建参考：http://rexrayconfig.codedellemc.com/
        ```buildoutcfg

```
        