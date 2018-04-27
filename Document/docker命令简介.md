##### docker命令大致分为两类，docker 客户端命令和docker daemon（新版本为dockerd）指令。

-   docker 客户端命令
客户端命令有可分为单个容器命令和容器栈命令，单个容器命令用于对一个容器进行操作，容器栈命令用于对一组容器操作。
更多命令解释查看：https://docs.docker.com/engine/reference/run/

    -   单个容器命令  
        用于查看、创建容器等，常用的包含：
        ```
        docker info    ## 查看docker信息，包含版本、日志驱动、镜像长裤地址等；
        docker run     ## 运行单个容器
        docker ps      ## 查看运行的容器
        docker cp      ## 在容器与本地之间进行文件复制
        docker exec    ## 可进入容器执行命令
        docker stop    ## 暂停容器运行
        docker rm      ## 删除容器
        docker login   ## 需要用户名即账号登录仓库，默认为公有仓库dockerhub
        docker push    ## 上传镜像，需要登陆后才可上传
        docker pull    ## 下载镜像
        docker network ## docker 网络管理
        docker volume  ## docker volume管理
        命令详细用法 可通过--help查看，如： docker run --help
        更过的docker 命令 可通过 docker --help查看
        ```
    -   容器栈命令
        用于查看创建一组容器，即docker swarm 命令
        ```
        docker swarm init    ## docker swarm 初始化
        docker node    ## 查看docker swarm 集群节点
        docker swarm join-token   ## 查看加入docker swarm集群所需要的token，分为worker、manager两个
        docker swarm join    ## 加入docker swarm 集群
        docker swarm leave   ## 离开docker swarm 集群
        docker stack         ## 管理容器栈
        docker stack deploy  ## 部署一个容器栈
        docker stack ls      ## 查看容器栈
        docker service       ## 管理服务
        指令的详细用法使用--help查看，如：docker swarm init --help
        更过的docker swarm命令 可通过 docker swarm --help查看
        ```
-   docker daemon
用于管理docker服务端，多用于修改docker配置，如修改docker日志驱动，或添加私有仓库等。新版本将docker daemon 用dockerd代替。
    
    更多命令解释可参考：
    - https://docs.docker.com/engine/reference/commandline/dockerd/
    - https://docs.docker.com/engine/admin/#configure-the-docker-daemon
    - http://blog.sina.com.cn/s/blog_13ea37b210102wwwb.html

    example：
    ```
    $ service docker stop     # 修改docker配置之前，需关闭docker 
    $ dockerd --log-driver syslog
    ```

实例参考：docker安装及配置，不建议使用。

