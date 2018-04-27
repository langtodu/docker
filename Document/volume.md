### 卷
#### 容器卷
以容器的形式创建卷，可供其他容器使用。卷容器秩序创建，无需运行; 其他容器使用时，只需将其挂载。创建volume时，默认的driver为local。

-   创建容器v1
```
$ docker volume create v1
```
-   查看v1信息
```
$ docker volume inspect v1 
[
    {
        "CreatedAt": "2017-11-17T11:32:03+08:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/v1/_data",
        "Name": "v1",
        "Options": {},
        "Scope": "local"
    }
]
```
-   删除v1
```
$ docker volume rm v1
```
-   创建一个大小100M，uid=1000的v2
```
$ docker volume create --driver local --opt type=tmps --opt device=tmpfs --opt o=size=100m,uid=1000 v2
```
-   创建一个容器挂载v2
```
$ docker run -d --volumes-from v2 --name db1 training/postgres
$ docker ins
```

-   安装驱动vieux/sshfs，并使用创建v3
    -   安装
    ```
    $ docker plugin install --grant-all-permissions vieux/sshfs
    ```
    -   创建v3，设为密码，但必须含有sshcmd
    ```
    $ docker volume create --driver vieux/sshfs -o sshcmd=test@localhost:/home/test -o password=testpasswd v3
    $ docker volume inspect v3
        [
            {
                "CreatedAt": "0001-01-01T00:00:00Z",
                "Driver": "vieux/sshfs:latest",
                "Labels": {},
                "Mountpoint": "/mnt/volumes/1c896a81df378682482f2abd43a05fe6",
                "Name": "v3",
                "Options": {
                    "password": "testpasswd",
                    "sshcmd": "test@localhost:/home/test"
                },
                "Scope": "local"
            }
        ]    
    ```