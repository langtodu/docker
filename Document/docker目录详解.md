##### 初始安装docker
-   /var/lib/docker,用于存储镜像与容器数据，其初始目录结构如下：
```
├── builder
│   └── fscache.db
├── containerd
│   └── daemon
│       ├── io.containerd.content.v1.content
│       │   └── ingest
│       ├── io.containerd.metadata.v1.bolt
│       │   └── meta.db
│       ├── io.containerd.runtime.v1.linux
│       ├── io.containerd.snapshotter.v1.btrfs
│       └── io.containerd.snapshotter.v1.overlayfs
│           └── snapshots
├── containers
├── image
│   └── overlay2
│       ├── distribution
│       ├── imagedb
│       │   ├── content
│       │   │   └── sha256
│       │   └── metadata
│       │       └── sha256
│       ├── layerdb
│       └── repositories.json
├── network
│   └── files
│       └── local-kv.db
├── overlay2
│   └── l
├── plugins
│   ├── storage
│   │   └── blobs
│   │       └── tmp
│   └── tmp
├── runtimes
├── swarm
├── tmp
├── trust
└── volumes
    └── metadata.db
```

-   /var/run/docker
```
/var/run/docker
├── containerd
│   ├── containerd.toml
│   ├── daemon
│   │   └── io.containerd.runtime.v1.linux
│   ├── docker-containerd-debug.sock
│   ├── docker-containerd.pid
│   └── docker-containerd.sock
├── libnetwork
│   └── a2f56a95f7c5a30c2e9d9df2357134f91ae3f42ef07ee690fb2dee141f4d06ec.sock
├── metrics.sock
├── plugins
└── swarm
```

##### pull 镜像后的目录结构
```
ubuntu@node-1:~$ docker pull ubuntu:16.04
16.04: Pulling from library/ubuntu
1be7f2b886e8: Pull complete
6fbc4a21b806: Pull complete
c71a6f8e1378: Pull complete
4be3072e5a37: Pull complete
06c6d2f59700: Pull complete
Digest: sha256:e27e9d7f7f28d67aa9e2d7540bdc2b33254b452ee8e60f388875e5b7d9b2b696
Status: Downloaded newer image for ubuntu:16.04

ubuntu@node-1:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              16.04               0458a4468cbc        5 weeks ago         112MB

注：以上在每台host运行出现过程即结果一致。
```

-   /var/lib/docker
```
├── builder
│   └── fscache.db
├── containerd
│   └── daemon
│       ├── io.containerd.content.v1.content
│       │   └── ingest
│       ├── io.containerd.metadata.v1.bolt
│       │   └── meta.db
│       ├── io.containerd.runtime.v1.linux
│       ├── io.containerd.snapshotter.v1.btrfs
│       └── io.containerd.snapshotter.v1.overlayfs
│           └── snapshots
├── containers
├── image
│   └── overlay2
│       ├── distribution ## 不同主机具有相同结构
│       │   ├── diffid-by-digest ##镜像层在云端的sha ID
│       │   │   └── sha256
│       │   │       ├── 06c6d2f5970057aef3aef6da60f0fde280db1c077f0cd88ca33ec1a70a9c7b58 #[sha256:6f4ce6b888495c7c9bd4a0ac124b039d986a3b18250fa873d11d13b42f6a79f4]
│       │   │       ├── 1be7f2b886e89a58e59c4e685fcc5905a26ddef3201f290b96f1eff7d778e122 #[sha256:ff986b10a018b48074e6d3a68b39aad8ccc002cdad912d4148c0f92b3729323e]
│       │   │       ├── 4be3072e5a37392e32f632bb234c0b461ff5675ab7e362afad6359fbd36884af #[sha256:92914665e7f61f8f19b56bf7983a2b3758cb617bef498b37adb80899e8b86e32]
│       │   │       ├── 6fbc4a21b806838b63b774b338c6ad19d696a9e655f50b4e358cc4006c3baa79 #[sha256:9c7183e0ea88b265d83708dfe5b9189c4e12f9a1d8c3e5bce7f286417653f9b7]
│       │   │       └── c71a6f8e13782fed125f2247931c3eb20cc0e6428a5d79edb546f1f1405f0e49 #[sha256:c98ef191df4b42c3fd5155d23385e75ee59707c6a448dfc6c8e4e9c005a3df11]
│       │   └── v2metadata-by-diffid 
│       │       └── sha256
│       │           ├── 6f4ce6b888495c7c9bd4a0ac124b039d986a3b18250fa873d11d13b42f6a79f4 #[{"Digest":"sha256:06c6d2f5970057aef3aef6da60f0fde280db1c077f0cd88ca33ec1a70a9c7b58","SourceRepository":"docker.io/library/ubuntu","HMAC":""}]
│       │           ├── 92914665e7f61f8f19b56bf7983a2b3758cb617bef498b37adb80899e8b86e32 #[{"Digest":"sha256:4be3072e5a37392e32f632bb234c0b461ff5675ab7e362afad6359fbd36884af","SourceRepository":"docker.io/library/ubuntu","HMAC":""}]
│       │           ├── 9c7183e0ea88b265d83708dfe5b9189c4e12f9a1d8c3e5bce7f286417653f9b7 #[{"Digest":"sha256:6fbc4a21b806838b63b774b338c6ad19d696a9e655f50b4e358cc4006c3baa79","SourceRepository":"docker.io/library/ubuntu","HMAC":""}]
│       │           ├── c98ef191df4b42c3fd5155d23385e75ee59707c6a448dfc6c8e4e9c005a3df11 #[{"Digest":"sha256:c71a6f8e13782fed125f2247931c3eb20cc0e6428a5d79edb546f1f1405f0e49","SourceRepository":"docker.io/library/ubuntu","HMAC":""}]
│       │           └── ff986b10a018b48074e6d3a68b39aad8ccc002cdad912d4148c0f92b3729323e #[{"Digest":"sha256:1be7f2b886e89a58e59c4e685fcc5905a26ddef3201f290b96f1eff7d778e122","SourceRepository":"docker.io/library/ubuntu","HMAC":""}]
│       ├── imagedb ## 不同主机具有相同结构
│       │   ├── content ##镜像的sha ID
│       │   │   └── sha256
│       │   │       └── 0458a4468cbceea0c304de953305b059803f67693bad463dcbe7cce2c91ba670 #内容json结构，ubuntu镜像的配置信息，包含镜像层栈的sha ID
│       │   └── metadata
│       ├── layerdb ## 不同主机具有相同结构，但不同的内容
│       │   ├── sha256
│       │   │   ├── 5a3e35538f7f2e2727c8ac92f08c30002b9e8a77737de0dab91244344d59f69b
│       │   │   │   ├── cache-id #[a2cbaf47a1a0a57c322595a60a29378174d7058f61621e551cd03cc52b11e2d2]## 镜像层在本地的sha ID，对应后面的overlay2/
│       │   │   │   ├── diff #[sha256:9c7183e0ea88b265d83708dfe5b9189c4e12f9a1d8c3e5bce7f286417653f9b7]
│       │   │   │   ├── parent #[sha256:ff986b10a018b48074e6d3a68b39aad8ccc002cdad912d4148c0f92b3729323e]
│       │   │   │   ├── size  #[745]
│       │   │   │   └── tar-split.json.gz
│       │   │   ├── 77e6ddba346d8ad1e436256f6373dede5af4002006981b7d4116c561c759cefa
│       │   │   │   ├── cache-id #[e5cfe05f37f266ae0f5b49ca785b8ab1f5d1c8d185d3198427853f838b8b9677]
│       │   │   │   ├── diff #[sha256:6f4ce6b888495c7c9bd4a0ac124b039d986a3b18250fa873d11d13b42f6a79f4]
│       │   │   │   ├── parent #[sha256:8db758ab2fdb54da0aec53aeac876934337e6170f5a8c8872b3d4171e3d465b7]
│       │   │   │   ├── size
│       │   │   │   └── tar-split.json.gz
│       │   │   ├── 8db758ab2fdb54da0aec53aeac876934337e6170f5a8c8872b3d4171e3d465b7
│       │   │   │   ├── cache-id #[79db6f57327bd36e4a51a2e993f90719581f49ff7fadc2fd1986d145180d5ea8]
│       │   │   │   ├── diff #[sha256:92914665e7f61f8f19b56bf7983a2b3758cb617bef498b37adb80899e8b86e32]
│       │   │   │   ├── parent #[sha256:a7fc6b405fe8ef71edfa6163d1dc9f1cb1df426049eefaa7d388e9df21a061ad]
│       │   │   │   ├── size 
│       │   │   │   └── tar-split.json.gz
│       │   │   ├── a7fc6b405fe8ef71edfa6163d1dc9f1cb1df426049eefaa7d388e9df21a061ad
│       │   │   │   ├── cache-id #[6ac3329caa5de7da4922928b8fe2a090f974267018fed54cdb3cead62f6a1b22]
│       │   │   │   ├── diff #[sha256:c98ef191df4b42c3fd5155d23385e75ee59707c6a448dfc6c8e4e9c005a3df11]
│       │   │   │   ├── parent #[sha256:5a3e35538f7f2e2727c8ac92f08c30002b9e8a77737de0dab91244344d59f69b]
│       │   │   │   ├── size
│       │   │   │   └── tar-split.json.gz
│       │   │   └── ff986b10a018b48074e6d3a68b39aad8ccc002cdad912d4148c0f92b3729323e
│       │   │       ├── cache-id #[59404304a96bd7ff147551f572dc700cf339d6391aff2b96db5ca6db8cd9a5aa]
│       │   │       ├── diff #[sha256:ff986b10a018b48074e6d3a68b39aad8ccc002cdad912d4148c0f92b3729323e]
│       │   │       ├── size 
│       │   │       └── tar-split.json.gz
│       │   └── tmp
│       └── repositories.json
├── network
│   └── files
│       └── local-kv.db
├── overlay2 ## 不同主机具有不同结构
│   ├── 59404304a96bd7ff147551f572dc700cf339d6391aff2b96db5ca6db8cd9a5aa
│   │   ├── diff
│   │   │   ├── bin
│   │   │   ├── boot
│   │   │   ├── dev
│   │   │   ├── etc
│   │   │   ├── home
│   │   │   ├── lib
│   │   │   ├── lib64
│   │   │   ├── media
│   │   │   ├── mnt
│   │   │   ├── opt
│   │   │   ├── proc
│   │   │   ├── root
│   │   │   ├── run
│   │   │   ├── sbin
│   │   │   ├── srv
│   │   │   ├── sys
│   │   │   ├── tmp
│   │   │   ├── usr
│   │   │   └── var
│   │   └── link
│   ├── 6ac3329caa5de7da4922928b8fe2a090f974267018fed54cdb3cead62f6a1b22
│   │   ├── diff
│   │   │   └── var
│   │   ├── link
│   │   ├── lower
│   │   └── work
│   ├── 79db6f57327bd36e4a51a2e993f90719581f49ff7fadc2fd1986d145180d5ea8
│   │   ├── diff
│   │   │   └── etc
│   │   ├── link
│   │   ├── lower
│   │   └── work
│   ├── a2cbaf47a1a0a57c322595a60a29378174d7058f61621e551cd03cc52b11e2d2
│   │   ├── diff
│   │   │   ├── etc
│   │   │   ├── sbin
│   │   │   ├── usr
│   │   │   └── var
│   │   ├── link
│   │   ├── lower
│   │   └── work
│   ├── e5cfe05f37f266ae0f5b49ca785b8ab1f5d1c8d185d3198427853f838b8b9677
│   │   ├── diff
│   │   │   └── run
│   │   ├── link
│   │   ├── lower
│   │   └── work
│   └── l
│       ├── BOFQ5BNEZBJVH57CEHOGKEEJ6P -> ../a2cbaf47a1a0a57c322595a60a29378174d7058f61621e551cd03cc52b11e2d2/diff
│       ├── OVEUUZ26SQWKSLGR4W7XXUUUWR -> ../e5cfe05f37f266ae0f5b49ca785b8ab1f5d1c8d185d3198427853f838b8b9677/diff
│       ├── TRTVBX6EA7PPD5WAAQOMGSTNMA -> ../79db6f57327bd36e4a51a2e993f90719581f49ff7fadc2fd1986d145180d5ea8/diff
│       ├── V3QWZOJOQAZPX6MB2GUUPUOISQ -> ../6ac3329caa5de7da4922928b8fe2a090f974267018fed54cdb3cead62f6a1b22/diff
│       └── YCH5BPLPUKQCP6YDJIJQDBRI7S -> ../59404304a96bd7ff147551f572dc700cf339d6391aff2b96db5ca6db8cd9a5aa/diff
├── plugins
│   ├── storage
│   │   └── blobs
│   │       └── tmp
│   └── tmp
├── runtimes
├── swarm
├── tmp
├── trust
└── volumes
    └── metadata.db
```
```
目录结构无变化
```

##### 运行镜像后的目录结构

```
ubuntu@node-1:~$ docker$ docker run -it ubuntu:16.04  /bin/bash

root@node-1:/var/lib/docker# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
55dc948ba249        ubuntu:16.04        "/bin/bash"         About a minute ago   Up About a minute                       sad_fermat
```

-   /var/lib/docker
```
├── builder
│   └── fscache.db
├── containerd ##变化
│   └── daemon
│       ├── io.containerd.content.v1.content
│       │   └── ingest
│       ├── io.containerd.metadata.v1.bolt
│       │   └── meta.db
│       ├── io.containerd.runtime.v1.linux
│       │   └── moby
│       │       └── 55dc948ba24925f29294d6048183a261740681ac13607a857448f2d1dcf9b907
│       ├── io.containerd.snapshotter.v1.btrfs
│       └── io.containerd.snapshotter.v1.overlayfs
│           └── snapshots
├── containers ##变化
│   └── 55dc948ba24925f29294d6048183a261740681ac13607a857448f2d1dcf9b907
│       ├── 55dc948ba24925f29294d6048183a261740681ac13607a857448f2d1dcf9b907-json.log
│       ├── checkpoints
│       ├── config.v2.json
│       ├── hostconfig.json
│       ├── hostname
│       ├── hosts
│       ├── resolv.conf
│       ├── resolv.conf.hash
│       └── shm
├── image ##变化
│   └── overlay2 ##未变化
│       ├── distribution
│       │   ├── diffid-by-digest
│       │   │   └── sha256
│       │   │       ├── 06c6d2f5970057aef3aef6da60f0fde280db1c077f0cd88ca33ec1a70a9c7b58
│       │   │       ├── 1be7f2b886e89a58e59c4e685fcc5905a26ddef3201f290b96f1eff7d778e122
│       │   │       ├── 4be3072e5a37392e32f632bb234c0b461ff5675ab7e362afad6359fbd36884af
│       │   │       ├── 6fbc4a21b806838b63b774b338c6ad19d696a9e655f50b4e358cc4006c3baa79
│       │   │       └── c71a6f8e13782fed125f2247931c3eb20cc0e6428a5d79edb546f1f1405f0e49
│       │   └── v2metadata-by-diffid
│       │       └── sha256
│       │           ├── 6f4ce6b888495c7c9bd4a0ac124b039d986a3b18250fa873d11d13b42f6a79f4
│       │           ├── 92914665e7f61f8f19b56bf7983a2b3758cb617bef498b37adb80899e8b86e32
│       │           ├── 9c7183e0ea88b265d83708dfe5b9189c4e12f9a1d8c3e5bce7f286417653f9b7
│       │           ├── c98ef191df4b42c3fd5155d23385e75ee59707c6a448dfc6c8e4e9c005a3df11
│       │           └── ff986b10a018b48074e6d3a68b39aad8ccc002cdad912d4148c0f92b3729323e
│       ├── imagedb
│       │   ├── content
│       │   │   └── sha256
│       │   │       └── 0458a4468cbceea0c304de953305b059803f67693bad463dcbe7cce2c91ba670
│       │   └── metadata
│       │       └── sha256
│       ├── layerdb 
│       │   ├── mounts ##变化
│       │   │   └── 55dc948ba24925f29294d6048183a261740681ac13607a857448f2d1dcf9b907
│       │   │       ├── init-id
│       │   │       ├── mount-id
│       │   │       └── parent
│       │   ├── sha256
│       │   │   ├── 5a3e35538f7f2e2727c8ac92f08c30002b9e8a77737de0dab91244344d59f69b
│       │   │   │   ├── cache-id
│       │   │   │   ├── diff
│       │   │   │   ├── parent
│       │   │   │   ├── size
│       │   │   │   └── tar-split.json.gz
│       │   │   ├── 77e6ddba346d8ad1e436256f6373dede5af4002006981b7d4116c561c759cefa
│       │   │   │   ├── cache-id
│       │   │   │   ├── diff
│       │   │   │   ├── parent
│       │   │   │   ├── size
│       │   │   │   └── tar-split.json.gz
│       │   │   ├── 8db758ab2fdb54da0aec53aeac876934337e6170f5a8c8872b3d4171e3d465b7
│       │   │   │   ├── cache-id
│       │   │   │   ├── diff
│       │   │   │   ├── parent
│       │   │   │   ├── size
│       │   │   │   └── tar-split.json.gz
│       │   │   ├── a7fc6b405fe8ef71edfa6163d1dc9f1cb1df426049eefaa7d388e9df21a061ad
│       │   │   │   ├── cache-id
│       │   │   │   ├── diff
│       │   │   │   ├── parent
│       │   │   │   ├── size
│       │   │   │   └── tar-split.json.gz
│       │   │   └── ff986b10a018b48074e6d3a68b39aad8ccc002cdad912d4148c0f92b3729323e
│       │   │       ├── cache-id
│       │   │       ├── diff
│       │   │       ├── size
│       │   │       └── tar-split.json.gz
│       │   └── tmp
│       └── repositories.json
├── network
│   └── files
│       └── local-kv.db
├── overlay2 ##变化
│   ├── 16776b74a86655feed72901bdc8af114179b31ad3de76c3b04dd7a7c9fb7d6fe
│   │   ├── diff
│   │   ├── link
│   │   ├── lower
│   │   ├── merged
│   │   │   ├── bin
│   │   │   ├── boot
│   │   │   ├── dev
│   │   │   ├── etc
│   │   │   ├── home
│   │   │   ├── lib
│   │   │   ├── lib64
│   │   │   ├── media
│   │   │   ├── mnt
│   │   │   ├── opt
│   │   │   ├── proc
│   │   │   ├── root
│   │   │   ├── run
│   │   │   ├── sbin
│   │   │   ├── srv
│   │   │   ├── sys
│   │   │   ├── tmp
│   │   │   ├── usr
│   │   │   └── var
│   │   └── work
│   │       └── work
│   ├── 16776b74a86655feed72901bdc8af114179b31ad3de76c3b04dd7a7c9fb7d6fe-init
│   │   ├── diff
│   │   │   ├── dev
│   │   │   └── etc
│   │   ├── link
│   │   ├── lower
│   │   └── work
│   │       └── work
│   ├── 59404304a96bd7ff147551f572dc700cf339d6391aff2b96db5ca6db8cd9a5aa
│   │   ├── diff
│   │   │   ├── bin
│   │   │   ├── boot
│   │   │   ├── dev
│   │   │   ├── etc
│   │   │   ├── home
│   │   │   ├── lib
│   │   │   ├── lib64
│   │   │   ├── media
│   │   │   ├── mnt
│   │   │   ├── opt
│   │   │   ├── proc
│   │   │   ├── root
│   │   │   ├── run
│   │   │   ├── sbin
│   │   │   ├── srv
│   │   │   ├── sys
│   │   │   ├── tmp
│   │   │   ├── usr
│   │   │   └── var
│   │   └── link
│   ├── 6ac3329caa5de7da4922928b8fe2a090f974267018fed54cdb3cead62f6a1b22
│   │   ├── diff
│   │   │   └── var
│   │   ├── link
│   │   ├── lower
│   │   └── work
│   ├── 79db6f57327bd36e4a51a2e993f90719581f49ff7fadc2fd1986d145180d5ea8
│   │   ├── diff
│   │   │   └── etc
│   │   ├── link
│   │   ├── lower
│   │   └── work
│   ├── a2cbaf47a1a0a57c322595a60a29378174d7058f61621e551cd03cc52b11e2d2
│   │   ├── diff
│   │   │   ├── etc
│   │   │   ├── sbin
│   │   │   ├── usr
│   │   │   └── var
│   │   ├── link
│   │   ├── lower
│   │   └── work
│   ├── e5cfe05f37f266ae0f5b49ca785b8ab1f5d1c8d185d3198427853f838b8b9677
│   │   ├── diff
│   │   │   └── run
│   │   ├── link
│   │   ├── lower
│   │   └── work
│   └── l ##变化
│       ├── BOFQ5BNEZBJVH57CEHOGKEEJ6P -> ../a2cbaf47a1a0a57c322595a60a29378174d7058f61621e551cd03cc52b11e2d2/diff
│       ├── OVEUUZ26SQWKSLGR4W7XXUUUWR -> ../e5cfe05f37f266ae0f5b49ca785b8ab1f5d1c8d185d3198427853f838b8b9677/diff
│       ├── RNCOXCBMU7PXO77GFLADGNO5PG -> ../16776b74a86655feed72901bdc8af114179b31ad3de76c3b04dd7a7c9fb7d6fe-init/diff
│       ├── T5CWGRW6FQZFKVMUBZJ3DE62IA -> ../16776b74a86655feed72901bdc8af114179b31ad3de76c3b04dd7a7c9fb7d6fe/diff
│       ├── TRTVBX6EA7PPD5WAAQOMGSTNMA -> ../79db6f57327bd36e4a51a2e993f90719581f49ff7fadc2fd1986d145180d5ea8/diff
│       ├── V3QWZOJOQAZPX6MB2GUUPUOISQ -> ../6ac3329caa5de7da4922928b8fe2a090f974267018fed54cdb3cead62f6a1b22/diff
│       └── YCH5BPLPUKQCP6YDJIJQDBRI7S -> ../59404304a96bd7ff147551f572dc700cf339d6391aff2b96db5ca6db8cd9a5aa/diff
├── plugins
│   ├── storage
│   │   └── blobs
│   │       └── tmp
│   └── tmp
├── runtimes
├── swarm
├── tmp
├── trust
└── volumes
    └── metadata.db
```

-   /var/run/docker
```
├── containerd
│   ├── 55dc948ba24925f29294d6048183a261740681ac13607a857448f2d1dcf9b907
│   │   ├── init-stdin
│   │   └── init-stdout
│   ├── containerd.toml ##内容无变化
│   ├── daemon
│   │   └── io.containerd.runtime.v1.linux
│   │       └── moby
│   │           └── 55dc948ba24925f29294d6048183a261740681ac13607a857448f2d1dcf9b907
│   │               ├── config.json
│   │               ├── init.pid
│   │               ├── log.json
│   │               └── rootfs
│   ├── docker-containerd-debug.sock
│   ├── docker-containerd.pid
│   └── docker-containerd.sock
├── libnetwork
│   └── a2f56a95f7c5a30c2e9d9df2357134f91ae3f42ef07ee690fb2dee141f4d06ec.sock
├── metrics.sock
├── netns
│   └── dd871970e462
├── plugins
├── runtime-runc
│   └── moby
│       └── 55dc948ba24925f29294d6048183a261740681ac13607a857448f2d1dcf9b907
│           └── state.json
└── swarm
```