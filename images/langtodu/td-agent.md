此镜像包含的input有：http-pull等，output镜像包含elasticsearch、kafka等。
其他组件安装可使用td-agent-gem install XXX，其他安装方式安装的插件不一定可用。
```
FROM ubuntu:16.04
MAINTAINER langtodu
USER root

#RUN set -xe

RUN rm /etc/apt/sources.list && touch /etc/apt/sources.list
ADD ./sources.list /etc/apt/sources.list

RUN  apt-get update && apt-get install -y ruby && apt-get install -y sudo &&  apt-get install -y curl && \
     apt-get install -y build-essential ruby-dev libffi-dev libsystemd-dev && \
     curl -L https://toolbelt.treasuredata.com/sh/install-ubuntu-xenial-td-agent3.sh | sh && \
     /etc/init.d/td-agent start && \
     gem sources -a https://ruby.taobao.org/ && gem sources –r https://rubygems.org/ && gem sources –u && \
     td-agent-gem install fluent-plugin-http-pull && \
     td-agent-gem install fluent-plugin-mongo

WORKDIR /
#VOLUME /etc/td-agent/

EXPOSE 24224 5140

#ENV

#COPY ./start.sh /
#RUN chmod +x start.sh
#CMD ["/start.sh"]

ENTRYPOINT /usr/sbin/td-agent -c /etc/td-agent/td-agent.conf && /bin/bash
#CMD /bin/bash /start.sh
#CMD ["/bin/bash", "-D"]
```
-   使用方式
```
$ docker run -d --name td-fluentd -p 24224:24224 -v path/to/td-agent.conf:/etc/td-agent/td-agent.conf langtodu/td-agent:v1.0 
```