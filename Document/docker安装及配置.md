-   docker安装
参考：[官网安装](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/)

[docker安装](http://202.117.219.110/wangluhui/greenchili/blob/master/documents/%E9%83%A8%E7%BD%B2%E6%95%99%E7%A8%8B.md)

-   docker配置
参考：
https://docs.docker.com/engine/admin/systemd/
https://docs.docker.com/engine/reference/commandline/dockerd/#run-multiple-daemons

    -   配置日志驱动
        docker默认日志驱动为json-file，改为fluentd
        方法一：此种方法过于复杂，不建议使用
        ```
        # 关闭docker
        $ service docker stop
        # 修改日志驱动
        $ sudo dockerd --log-driver fluentd
        # 启动docker
        $ 
        ```
        另外一种方法：在/etc/docker/创建daemon.json文件，并编辑：
        daemon.json：
        ```
        {
          "log-driver": "fluentd",
          "log-opts": {
            "fluentd-address": "192.168.99.109:24224"
          }
        }
        ```
        然后执行：
        ```
        # 重启docker服务
        $ sudo service docker start
        # 查看
        $ docker info | grep Logging
        ```
        注：其他日志驱动修改可参考：https://docs.docker.com/engine/admin/logging/overview/
        
    -   修改docker网络ip，即docker0 ip。默认172.17.0.1/16，修改为10.10.0.1/16
        方法一：
        ```
        # 关闭docker
        $ service docker stop
        # 修改日志驱动
        $ sudo dockerd --bip 10.0.0.0/16
        # 启动docker
        $ 
        ```
        方法二：
        daemon.json：
        ```
        {
          "bip": "10.10.0.1/16" 
        }
        ```
        
    -   docker代理，在下载一些国外镜像时可使用。
        -   在/etc/systemd/system/创建docker.service.d文件夹
        ```
        $ sudo mkdir -p /etc/systemd/system/docker.service.d
        ```
        -   在/etc/systemd/system/docker.service.d/文件夹内创建http-proxy.conf，并编辑
        ```
        $ sudo touch /etc/systemd/system/docker.service.d/http-proxy.conf
        # cat <<EOF >>/etc/systemd/system/docker.service.d/http-proxy.conf
        > [Service]
        > Environment="HTTP_PROXY=http://ip:port/" "NO_PROXY=localhost,127.0.0.1,docker-registry.somecorporation.com[私有仓库]"
        > EOF
        ```
        -   重启
        ```
        $ sudo systemctl daemon-reload
        $ sudo systemctl restart docker
        ```
    
-   其他配置
```
{
	"authorization-plugins": [],
	"data-root": "",
	"dns": [],
	"dns-opts": [],
	"dns-search": [],
	"exec-opts": [],
	"exec-root": "",
	"experimental": false,
	"storage-driver": "",
	"storage-opts": [],
	"labels": [],
	"live-restore": true,
	"log-driver": "",
	"log-opts": {},
	"mtu": 0,
	"pidfile": "",
	"cluster-store": "",
	"cluster-store-opts": {},
	"cluster-advertise": "",
	"max-concurrent-downloads": 3,
	"max-concurrent-uploads": 5,
	"default-shm-size": "64M",
	"shutdown-timeout": 15,
	"debug": true,
	"hosts": [],
	"log-level": "",
	"tls": true,
	"tlsverify": true,
	"tlscacert": "",
	"tlscert": "",
	"tlskey": "",
	"swarm-default-advertise-addr": "",
	"api-cors-header": "",
	"selinux-enabled": false,
	"userns-remap": "",
	"group": "",
	"cgroup-parent": "",
	"default-ulimits": {},
	"init": false,
	"init-path": "/usr/libexec/docker-init",
	"ipv6": false,
	"iptables": false,
	"ip-forward": false,
	"ip-masq": false,
	"userland-proxy": false,
	"userland-proxy-path": "/usr/libexec/docker-proxy",
	"ip": "0.0.0.0",
	"bridge": "",
	"bip": "",
	"fixed-cidr": "",
	"fixed-cidr-v6": "",
	"default-gateway": "",
	"default-gateway-v6": "",
	"icc": false,
	"raw-logs": false,
	"allow-nondistributable-artifacts": [],
	"registry-mirrors": [],
	"seccomp-profile": "",
	"insecure-registries": [],
	"disable-legacy-registry": false,
	"no-new-privileges": false,
	"default-runtime": "runc",
	"oom-score-adjust": -500,
	"runtimes": {
		"cc-runtime": {
			"path": "/usr/bin/cc-runtime"
		},
		"custom": {
			"path": "/usr/local/bin/my-runc-replacement",
			"runtimeArgs": [
				"--debug"
			]
		}
	}
}
```
        
        
