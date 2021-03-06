# 镜像

> 国内镜像地址 https://www.daocloud.io/mirror#accelerator-doc

独立的软件包 用来安装程序，容器是镜像运行的实例

**注意： 创建 了镜像不要在镜像中保存业务数据,应该放在宿主机上面**

## 镜像的命令

> 为了解决 docker 命令 sudo 授权问题可以把 docker 赋予权限添加到用户组

- 启动镜像 `server docker start`
- 停止镜像 `server docker stop`

* 重启镜像 `server docker restart`

- 获取镜像 `git pull NAME[:tag]`

* 查看所有镜像 `docker images`

* 用 tag 命令添加标签 `docker tag ubuntu:14.04 myubuntu:latest`

* 镜像重新起名 `docker tag OLDNAME NEWNAME`

- 查看详情镜像 `docker inspect NAME`

- 删除镜像 `docker rmi IMAG [IMAGE...](标签或ID)`

### 创建镜像

1. 基于已有的镜像进行创建
   `docker commit -m 'add a new file' -a 'kl' 4771f8bf30cc test:0.1`

   - -a 作者信息

   * -m 提交信息
   * -p 提交时暂停容器运行

2. 基于本地模板导入

3. 通过 dockerfile

### 存入和载入镜像

1. 存出镜像
2. 载入

### 上传镜像

`docker push NAME:[:TAG]`

## 数据管理

容器中的数据管理有

- 数据卷 : 容器内数据直接映射到本机环境
- 数据卷容器：使用特定容器维护数据卷

```
docker run -it -p 9000:3000 --name koatests --rm -v /Users/suhailong/su/NOTE/async:/demos --privileged koa-demo
```

# 端口映射与容器互联

## 端口映射实现访问容器

1. 从外部访问容器
   利用 `-P(大写)` 来实现，docker 会映射一个 49000~49900 范围的一个端口,

> 可以使用`docker ps` 查看端口映射

```
 docker run  -P -it koa-demo /bin/bash
```

2.  映射所有接口地址

> 可以使用多个 -p

```
   docker run  -p 8000:3000 -it koa-demo /bin/bash
```

3. 映射到指定地址的指定端口

```
   docker run  -p 127.128.9.0:8000:3000 -it koa-demo /bin/bash
```

4. 映射到指定地址的任意端口

```
   docker run  -p 127.128.9.0::3000 -it koa-demo /bin/bash
```

5. 查看映射关系

   docker port CONTAINER

## 互联机制

1. 自定义容器命名
   - `--name`

```
docker run -d -P --name=web koa-demo
```

# 容器

- 创建
  `docker run`

  - `-it 启动一个界面（bash）`
    - `exit 退出 bash`
  - `-d 后台运行`
  - `-v 数据卷`
    - `-v 主机目录：容器目录`

  * `--privileged 数据卷的读写最高权限`

  - `--net 网段`

  * `--name 容器命名`
  * `--ip 规定的IP地址`

```
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -v v1:/var/lib/mysql --privileged --name=node1 --net=net1 --ip 172.18.0.2 pxc
```

第一个初始化较为缓慢，必须等待第一个初始化完毕

```
docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456  -e CLUSTER_JOIN=node1 -v v2:/var/lib/mysql --privileged --name=node2 --net=net1 --ip 172.18.0.3 pxc
```

```
docker run -d -p 3308:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456  -e CLUSTER_JOIN=node1 -v v3:/var/lib/mysql --privileged --name=node3 --net=net1 --ip 172.18.0.4 pxc
```

```
docker run -d -p 3309:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456  -e CLUSTER_JOIN=node1 -v v4:/var/lib/mysql --privileged --name=node4 --net=net1 --ip 172.18.0.5 pxc
```

```
docker run -d -p 3310:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456  -e CLUSTER_JOIN=node1 -v v5:/var/lib/mysql --privileged --name=node5 --net=net1 --ip 172.18.0.6 pxc
```

- 查看

```
docker container ls --all 所有容器
```

# 网段设置

mysql 默认的网段是 172.17.0.X

如果创建则按顺序递增 172.17+n.0.X

```
docker network create NAME
```

```
eg: docker network create --subnet=172.18.0.0/24 net1
```

- 查看网段信息

```
docker network inspect NAME`
```

- 删除网段

```
  docker newwork rm net1
```

- 可以创建特定网段
- `--subnet`

# Docker 数据管理

- 创建数据卷

  - `docker volume create NAME`
    - 在创建容器的时候是 -v
    - `--name v1`

* 查看数据卷

  - `docker inspect NAME`

* 查看所有

  - docker volume ls

* 删除数据卷
  - `docker valume rm NAME`
