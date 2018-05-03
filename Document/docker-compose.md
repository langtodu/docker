#### 语法简介（以yml为例）

docker compose version可分为version1、version2、version3 三大类，一般常用的version3。目前为止，version3已经有了v3.6，可在
docker engine 18.02.0+使用。以下介绍是以version3为基础。

-   单个service简介
```
version: "3.1"            #声明compose版本
services:               #部署的services列表
  redis:                #第一个service，名为redis
    image: redis:alpine #该服务所使用镜像 
    ports:              #开放的端口号
      - "6379::6379"    #前面6379为宿主机端口，后面为容器开放端口
    networks:           #使用的网络
      - frontend
networks:               #配置网络
  frontend:
```
-   container_name

设置容器名称，否则使用默认的随机名字
``` 
version: "3.1" 
services:
  nginx:             
    image: nginx:stable
    
    container_name: nginx_container
```
-   deploy

用于设置的部署服务，如设置容器副本数，自启方式，资源限制等，仅用于version3
```
version: '3'
services:
  redis:
    image: redis:alpine
    deploy:
      replicas: 6              #设置6个副本
      update_config:           #设置容器更新升级
        parallelism: 2         
        delay: 10s             
        order：stop-first      
      restart_policy:          #设置容器重启策略
        condition: on-failure  
      resources:               #设置资源限制
        limits:                #设上限
          cpus: '0.001'        #宿主机可用cpu的1/1000
          memory: 50M          
        reservations:          #设下限
          cpus: '0.0001'
          memory: 20M
```
-   labels

用于为container或service添加标签，lables位置不同
```
#仅为service添加标签，该服务下的容器不具有该标签
version: "3"
services:
  web:
    image: web
    deploy:
      labels:
        com.example.description: "This label will appear on the web service"
#为容器添加标签
version: "3"
services:
  web:
    image: web
    labels:
      com.example.description: "This label will appear on all containers for the web service"
```
-   mode

有两种模式：global、replicated。replicated是dokcer swarm默认的方式，用于设置容器的副本数；global表示每个swarm node运行一个该容器。
```
version: '3'
services:
  worker:
    image: dockersamples/examplevotingapp_worker
    deploy:
      mode: global
*****************************************************
version: '3'
services:
  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - frontend
      - backend
    deploy:
      mode: replicated
      replicas: 6
```
-   placement

指定容器运行的约束条件，包括位置、偏好
```
version: '3.3'
services:
  db:
    image: postgres
    deploy:
      placement:
        constraints:  # 容器运行在manager节点，且节点系统为ubuntu 14.04
          - node.role == manager
          - engine.labels.operatingsystem == ubuntu 14.04
        preferences:  # 
          - spread: node.labels.zone
```
-   restart_policy

设置当容器退出时，容器启动。
```
version: "3"
services:
  redis:
    image: redis:alpine
    deploy:
      restart_policy:
        condition: on-failure  # 有3 种：none、on-failure、any（默认）
        delay: 5s              # 每次重启的等待时间，默认：0
        max_attempts: 3        # 重启的最大次数，默认：never give up
        window: 120s           # 判断是否重启成功的等待时间
```
-   update_config

设置service如何更新，有parallelism、failure_action（更新失败后，continue、rollback、pause(默认)）、
monitor（每个任务更新后监视失败的持续时间(ns|us|ms|s|m|h)(默认为0)）、max_failure_ratio（在更新期间容忍失败率）等
```
version: '3.4'
services:
  vote:
    image: dockersamples/examplevotingapp_vote:before
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2       # 每次更新容器的个数
        delay: 10s           # 容器更新的时间
        order: stop-first    # 更新过程运行的顺序：stop-first（默认），先停止就得task，再运行新的task；start-first，新的task先启动
```

####  **以下命令不支持 docker stack deploy**

-   depends_on

用于设置容器启动顺序， **在version3 的docker stack deploy中将被忽略**
```
version: '3'
services:
  web:              # 先启动db、redis，等到db、redis启动后再启动web
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```
-   entrypoint

覆盖原来入口，与dockerfile中的entrypoint类似
```
entrypoint: /code/entrypoint.sh
或
entrypoint:
    - php
    - -d
    - zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20100525/xdebug.so
    - -d
    - memory_limit=-1
    - vendor/bin/phpunit
```
-   env_file

从一个文件中为容器添加环境变量。
```
env_file: .env
或
env_file:
  - ./common.env
  - ./apps/web.env
  - /opt/secrets.env
#每个文件内容以 VAR=VAL 的形式，如
# web.env
RACK_ENV=development
```
-   environment

为容器添加环境变量
```
environment:
  RACK_ENV: development
  SHOW: 'true'
  SESSION_SECRET:
或
environment:
  - RACK_ENV=development
  - SHOW=true
  - SESSION_SECRET
```
-   expose

暴露端口号，与宿主机无关；只能被有关连接的服务连接，只能指定内部端口号
```
expose:
 - "3000"
 - "8000"
```
-   external_links

链接到 docker-compose.yml 外部的容器，甚至 并非 Compose 项目文件管理的容器。参数格式跟 links 类似
```
external_links:
 - redis_1
 - project_db_1:mysql
 - project_db_1:postgresql
```
-   extra_hosts

添加hostname映射,向/etc/hosts 文件中添加一些记录
```
extra_hosts:
 - "somehost:162.242.195.82"
 - "otherhost:50.31.209.229"
```
-   healthcheck

健康检查，判断service中的容器是否"health", start_period仅支持v3.4及以上版本
```
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 1m30s
  timeout: 10s
  retries: 3
  start_period: 40s  # 
```
-   labels

为容器添加标签
```
labels:
  com.example.description: "Accounting webapp"
  com.example.department: "Finance"
  com.example.label-with-empty-value: ""
或
labels:
  - "com.example.description=Accounting webapp"
  - "com.example.department=Finance"
  - "com.example.label-with-empty-value"
```
-   links

将一个容器与其他service连接
```
web:
  links:
   - db
   - db:database
   - redis
```
-   logging

配置容器的日志驱动，与--log-driver类似
```
logging:
  driver: syslog
  options:
    syslog-address: "tcp://192.168.0.42:123"
或
services:
  some-service:
    image: some-service
    logging:
      driver: "json-file"
      options:
        max-size: "200k"
        max-file: "10"
```
-   ports

暴露端口，形式：host-port：container-port
```
ports:
 - "3000"           # 随机映射到主机的一个端口
 - "3000-3005"
 - "8000:8000"      # 将主机的8000端口映射到容器的8000
 - "9090-9091:8080-8081"
 - "49100:22"
 - "127.0.0.1:8001:8001"
 - "127.0.0.1:5000-5010:5000-5010"
 - "6060:6060/udp"
```
-   secrets

为service设置访问权限
```
version: "3.1"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    secrets:
      - my_secret
      - my_other_secret
secrets:
  my_secret:
    file: ./my_secret.txt
  my_other_secret:
    external: true
或
version: "3.1"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    secrets:
      - source: my_secret
        target: redis_secret
        uid: '103'
        gid: '103'
        mode: 0440
secrets:
  my_secret:
    file: ./my_secret.txt
  my_other_secret:
    external: true
```
-   volumes

绑定一个host path或其他的volumes到容器
```
version: "3.2"
services:
  web:
    image: nginx:alpine
    volumes:
      - type: volume
        source: mydata
        target: /data
        volume:
          nocopy: true
      - type: bind
        source: ./static
        target: /opt/app/static

  db:
    image: postgres:latest
    volumes:
      - "/var/run/postgres/postgres.sock:/var/run/postgres/postgres.sock"
      - "dbdata:/var/lib/postgresql/data"

volumes:
  mydata:
  dbdata:
```

