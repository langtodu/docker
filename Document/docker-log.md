docker 默认将日志发送到容器的标准输出设备（STDOUT）和标准错误设备（STDERR），STDOUT 和 STDERR 实际上就是容器的控制台终端。
但Docker 支持多种日志机制帮助用户从运行的容器中提取日志信息。这些机制被称作 logging driver。
如：默认的json-file，还有其他syslog、fluentd等，可参考https://docs.docker.com/engine/admin/logging/overview/#supported-logging-drivers

容器启动时可以通过 --log-driver 指定使用的 logging driver。
如果要设置 Docker 默认的 logging driver，需要修改 Docker daemon 的启动脚本，指定 --log-driver 参数，如：
进入/etc/docker/daemon.json添加
```
ExecStart=/usr/bin/dockerd -H fd:// --log-driver=syslog 
```
### 使用Filebeat-ELK作为日志的存储及可视化
 Filebeat 收集 Docker 容器日志，利用的是 Docker 默认的 logging driver json-file.
-   安装elasticsearch、kibana
-   安装Filebeat，选择版本安装，可参考https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation.html
-   配置 Filebeat
    -   编辑/etc/filebeat/filebeat.yml，如果文件内容为空，可将filebeat.full.yml复制到filebeat.yml
    ```
    - input_type: log
      paths:
        - /var/log/*.log
        - /var/lib/docker/containers/*/*.log
    ```
    -   详细教程参考：https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-index-pattern.html
-   使用logstash
    -   安装logstash
    ```
    $ wget -c https://artifacts.elastic.co/downloads/logstash/logstash-5.6.4.deb
    $ dpkg -i logstash-5.6.4.deb
    ```
    -   配置Filebeat——logstash部分
    -   配置logstash

### 使用Fluentd-ELK作为日志的存储及可视化
