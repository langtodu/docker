### 创建私有仓库

-   在/etc/docker/registry/ 创建并编辑config.yml文件，参考 yml/config_registry.yml

以本地仓库为例：
-   使用命令运行registry
```
$ docker run -d --name registry -p 5000:5000 -v /opt/registry:/var/lib/registry --restart always registry：2
```
    -   上传image到本地仓库，例如
    ```
    $ docker tag scrapyd:latest 10.0.2.15:4000/myscrapyd:latest
    $ docker push 10.0.2.15:4000/myscrapyd
    ```
-   查看本地仓库镜像
    -   修改/etc/docker/daemon.json文件，添加：
    ```
    {
        "insecure-registries": ["ip:port"] 
    }
    ```
    -   使用浏览器查询：ip:port/v2/_catalog，注：默认显示100条信息，也可以使用以下命令查询n条记录，如200条，详情可参考：https://docs.docker.com/registry/spec/api/#introduction
    ```
    $ curl http://ip:port/v2/catalog?n=200
    ```

-   下载镜像
    -   使用命令
    ```
    $ docker pull 10.0.2.15:4000/myscrapyd
    ```
    -   或修改配置，将私有仓库地址作为默认
    