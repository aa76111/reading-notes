# Docker

[TOC]



gradle init --type java-library

**Lmctfy**, **LibContainer**, **PodMan**

### 5个基本概念

* Docker Daemon: 守护进程, 运行在宿主机上, 用户通过 Docker Client客户端执行Docker命令与Docker Daemon交互
* Docker Client: Docker命令行工具, 是用户使用Docker的主要方式
* Docker Image: 镜像. 相当于root文件系统
* Docker Container: 容器就是镜像的一个实例
* Docker Repository: 代码的控制中心, 作用就是保存镜像

```shell
安装Docker
//	确认系统版本
uname -a 
//	更新yum
yum update
//	使用aliyun镜像
[sudo] yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
//	安装docker
yum install -y docker-ce
//	启动docker
systemctl start docker
//	如果没有权限, 则需修改权限
su root
visudo

//	查看docker是否开启(主要查看 client 和 server 是否都启用)
docker version
//	设置docker开机自启动
systemctl enable docker
```

![1623492073092](C:\Users\QC\AppData\Roaming\Typora\typora-user-images\1623492073092.png)

```
sudo vim /etc/docker/daemon.json

{
        "registry-mirrors": ["https://hub-mirror.c.163.com"]
} 
```



### Docker Client

#### run命令

通过镜像创建并启动容器

=create命令+start命令

* -i (标准输入)
* -t (终端或模拟终端)
* -d: 守护进程的方式后台运行

#### Exit命令

从交互的模式中退出

#### ps命令

查看容器的状态

#### start命令

container_id唯一

#### stop命令

container_id唯一

#### exec命令

docker exec -i -t  ID /bin/bash

exit退出时, 容器不会退出

#### attach命令

不推荐使用, 进入容器内部 Exit退出时, 容器也中止 

#### rm命令

删除容器(一个或多个)

`docker rm -f $(docker ps -a -q)`: 删除所有 

#### kill命令

pkill -9

#### restart

`docker retart ID/name`

##### pause: 暂停

`docker pause ID/Name`

#### Unpause: 恢复

`docker unpause ID/Name`

#### top

查看容器中运行的进程信息

#### 容器的状态

* Created
* Up(运行中)
* pause
* Exited

#### Logs

#### inspect

### 镜像管理

```
docker search httpd;
docker pull image_name
```

#### 镜像导出

* docker export
* docker save

#### 镜像导入

* docker import
* docker load

#### 删除镜像

* docker rmi image_id [-f]
  * 强制删除需要注意
* docker rmi image_name
* docker rmi image_name:target
* `docker rmi $(docker images -q)`: 删除所有镜像

![1623550089351](C:\Users\QC\AppData\Roaming\Typora\typora-user-images\1623550089351.png)

对于 image_id相同的, 根据name:target 来删除

#### 镜像标签

`docker tag image_id image_name:tag`

#### 镜像提交

`docker push`

* 镜像推送到DokcerHub

  ```
  docker login
  docker tag image_id aa76111/image_name:tag
  docker push aa76111/image_name:tag
  
  其他docker拉取镜像
  docker pull aa76111/image_name:tag
  ```

#### 镜像创建

* `docker commit`
* `docker build 和 Dockerfile`

#### diff命令 

检查容器的文件系统变动

#### stats命令

实时监控显示容器的资源使用情况

#### 镜像结构

千层饼结构: `联合挂载技术`

* 对内: 多个目录挂载到同一个目录上(甚至也可以对应不同的文件系统)
* 对外: 显示这些目录的整合 (联合) 形态
* 对应Docker的 AUFS: another union file system

#### 镜像构建

​	docker run -d -p 80:80 azkanban/custom_nginx:v1

### 私有仓库

`安全` ,`高速`, `运维方便`, `仓库除了镜像管理, 还可以集成管理系统`

* Docker-registry 私有仓库组件
* VMware 公司: Harbor组件

##### 环境搭建

1. 主机规划
   * CentOS7 10.0.0.12 私有仓库 Docker-CE Docker-registry组件
   * CentOS7 10.0.0.13 客户端 Docker-CE组件
2. 搜索镜像
   * docker search repository
3. 拉取镜像
   * docker pull repository
   * `docker pull IP:PORT/image_name:image_tag`
4. 启动容器
   * `docker run -d -v /registory:/home/docker-registry -p 5000:5000 --restart=always --privileged=true --name registry registry:latest`
   * -d: 后台运行
   * -v: 卷
   * -p: 打开网络端口
   * --restart=always: 容器挂掉后, 自动重启
   * --privileged=true: 打开安全模块, 赋予权限
5. 验证步骤
   * http://192.168.1.06:5000/v2/_catalog

#### Harbor

启动harbor: `docker-compose up -d`





#### 外部访问容器

* 宿主机访问容器
  * -P 内容端口随机映射到主机的高端口(65535以上的)
  * -p: 指定端口映射, 主机端口:容器端口
* 宿主机随机端口映射容器所有端口
* 宿主机随机端口映射容器指定端口
* 宿主机随机端口映射容器指定IP和指定端口
* 宿主机指定端口映射容器指定端口

docker network create -d bridge network_名称 



docker port container_id: 查看容器开启的端口

##### 容器互联

![1624030905711](C:\Users\QC\AppData\Roaming\Typora\typora-user-images\1624030905711.png)

```
netstat -lntup

查看系统随机端口
sysctl -a | grep port | grep range

查看 iptables
// 使用 docker run -d -p 方式启动的容器,自动添加一条NAT规则
docker网络的底层原理就是通过 iptables实现的
iptables -t nat -L

docker port container_id

docker run -d -p host_ip:host_端口:con_端口

docker netword ls

运行容器指定network
docker run -itd -p 127.0.0.1:8091:80 --name nginx-01 --network custom-net my-nginx
```

* 网络四种模式
  * 桥接模式
  * host模式
  * none模式
  * container模式

![1624075030091](C:\Users\QC\AppData\Roaming\Typora\typora-user-images\1624075030091.png)