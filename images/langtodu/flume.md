Dockerfile
```
FROM java:openjdk-8-jdk
MAINTAINER langtodu

ENV FLUME_VERSION=1.8.0
ENV FLUME_HOME /opt/flume
ENV FLUME_AGENT=agent

WORKDIR /opt/
ADD ./apache-flume-1.8.0-bin.tar.gz .
RUN mv apache-flume-1.8.0-bin ./flume && \
    cd flume/conf/ && cp flume-conf.properties.template ./flume.conf && cd /opt/

VOLUME [ "/opt/flume/conf" ]

ADD ./start.sh /start.sh

ENTRYPOINT [ "/start.sh" ]
```

start.sh
```
#! /bin/bash

/opt/flume/bin/flume-ng agent --conf conf --conf-file /opt/flume/conf/flume.conf --name $FLUME_AGENT -Dflume.root.logger=debug,console
```

创建命令：
```
docker build -t flume:v1.8.0 .
```

example:
```
 docker run -d --name flume -e FLUME_AGENT=a1 -v ~/flume/conf/flume.conf:/opt/flume/conf/flume.conf -v /var/log:/var/log flume:v1.8.0
```

-   update——v1.8.2
```
此版本增加了向fluentd传递数据的插件
```
-   update——v1.8.3

参考来源：https://github.com/lucidfrontier45/ElasticsearchSink2
```
此版本增加了向elasticsearch传递数据的插件
注：elasticsearch version=2.4.6 高版本无法使用
```