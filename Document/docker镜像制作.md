参考来源：https://docs.docker.com/engine/reference/builder/#env
docker镜像用于运行容器，其Dockrfile文件不仅仅用于创建镜像，还可以用查看镜像使用方法。

#### 创建docker image的方式：
-   从docker hub下载；
-   修改已有的docker image，使用docker commit 生成一个镜像；
-   使用Dockerfile创建镜像。注：有时国外镜像源无法下载，可通过在GitHub中创建Dockerfile，然后使docker hub根据Dockerfile创建镜像。即docker hub可以帮助我们下载一下无法下载的镜像。

以下使用Dockerfile文件创建镜像，创建命令：docker build -t xxx:vv path/to/Dockerfile
Dockerfile编写方式：
-   FROM
表示基础镜像，制作镜像第一次指令，其他命令在此命令基础之上执行；Dockerfile文件必须包含的。
    example:
    ```
    FROM ubuntu:16.04
    ```
    
-   MAINTAINER
用于标注镜像创建作者信息
    example:
    ```
    FROM ubuntu:16.04
    MAINTAINER lang
    ```
-   USER
用于设置容器使用的用户。如：使用root用户，以下的指令均以root身份运行
    example：
    ```
    FROM ubuntu:16.04
    MAINTAINER lang
    USER root
    ```

-   RUN
用于运行命令，例如：镜像里安装openssh-server。一个RUN命令按顺序执行，用&&隔开，换行前用\。可以有多个RUN，run安装顺序执行。
有两种形式：RUN <command>（此种是以shell形式运行，默认情况为 /bin/sh -c）;另一种是以RUN ["executable", "param1", "param2"]（执行形式）。
一般多以第一种方式运行。
    example:
    ```
    FROM ubuntu:16.04
    MAINTAINER lang
    USER root
    RUN apt-get update && apt-get install -y openssh-server && \
        apt-get install -y sudo 
    RUN mkdir test
    ```

-   EXPOSE
用于设置镜像里开放的端口号，在镜像运行时容器监听的端口，默认tcp。但需要在运行时映射才有效。如打开8888,8989端口
    example：
    ```
    FROM ubuntu:16.04
    MAINTAINER lang
    USER root
    RUN apt-get update && apt-get install -y openssh-server && \
        apt-get install -y sudo 
    RUN mkdir test
    EXPOSE [8888, 8989/UDP]
    ```
    
-   ENV
用于在镜像中设置环境变量，可被docker run -e XXX取代。
    example:
    ```
    ENV myName John Doe
    ENV myDog Rex The Dog
    ENV myCat fluffy
    ```
-   ADD
用于将文本或其他文件添加到镜像中，文件可以来源网上，或本地文件。如果是压缩文件，复制过程自动解压。

-   COPY
用于将文本或其他文件添加到镜像中。如将当地FILE文件复制到镜像/opt/中。
    example：
    ```
    COPY ./FILE /opt/
    ```

-   VOLUME
用于设置开放挂载的路径
    example：
    ```
    FROM ubuntu
    RUN mkdir /myvol
    RUN echo "hello world" > /myvol/greeting
    VOLUME /myvol
    ```

-   WORKDIR
用于设置工作目录，即以上命令所在的地方运行，也可指容器运行后，进入容器的初始路径。

-   CMD
用于设置容器运行的命令，多个CMD只有最后一个有效。可被容器运行设置的命令替代。
有三种方式：
    -   CMD ["executable","param1","param2"]，exec形式
    -   CMD ["param1","param2"]，提供给ENTRYPOINT 的默认参数
    -   CMD command param1 param2， shell形式

-   ENTRYPOINT
用于设置容器运行后执行的命令

注：
-   shell形式与exec形式区别
    shell运行命令可使用环境参数，exec不能使用环境参数，自己能使用自己的参数。

-   编写镜像Dockerfile文件，创建镜像后，可能出现运行后立刻退出的情况。
    ```
    ENTRYPOINT /usr/sbin/td-agent -c /etc/td-agent/td-agent.conf && /bin/bash
    ```

实例：
来源https://docs.docker.com/engine/examples/running_ssh_service/#build-an-eg_sshd-image
```
FROM ubuntu:16.04

RUN apt-get update && apt-get install -y openssh-server
RUN mkdir /var/run/sshd
RUN echo 'root:screencast' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
```
