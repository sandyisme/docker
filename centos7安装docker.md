# 安装docker
- 更新软件包数据库
```shell
sudo yum check-update
```
- 添加官方docker存储库，下载最新版本的docker并进行安装
```shell
curl -fsSL https://get.docker.com/ | sh
```
- 启动docker
···shell
sudo systemctl start docker
```
- 查看docker的状态
```shell
sudo systemctl status docker
```
- docker的自启动开启
```shell
sudo systemctl enable docker
```
- 显示以下信息表示docker的自启服务启动完成
```shell
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
```
# 普通用户使用docker
- 用户添加到 docker 组
```shell
sudo usermod -aG docker $(whoami)
# 或
sudo usermod -aG docker username
```
# docker 中使用镜像
- 检查是否可以访问 docker hub 和从这个网站下载镜像
```shell
docker run hello-world
```
- 搜索 docker hub 上可用的镜像("OFFICIAL" 列（即官方）对应的镜像)
```shell
docker search centos
```
- 用pull命令,将你需要的镜像下载下来
```shell
docker search centos
```
- 用run命令,把下载的镜像运行起来变成容器
```shell
docker run centos
```
- 查看已下载到计算机的镜像
```shell
docker images
```
# 运行 docker 容器
- 以交互模式启动一个容器（/bin/bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell， /bin/sh同/bin/bash只是不显示用户和容器id）
```shell
# 推荐使用docker run -it centos /bin/bash
docker run -it centos /bin/bash
# 或
docker run -it centos /bin/sh
# 或
docker run -it centos
```
# 管理docker容器
- 查看活动中的容器
```shell
docker ps
```
- 查看所有容器（活动和非活动）
```shell
docker ps -a
```
- 查看最后创建的容器
```shell
docker ps -l
```
- 启动容器
```shell
docker start 【容器ID或容器名】
```
- 停止正在运行的容器
```shell
docker stop 【容器ID或容器名】
```
- 删除容器
```shell
docker rm 【容器ID或容器名】
```
# docker 镜像推送到docker hub并修改
- 登录docker hub
```shell
docker login -u  sandysaidi
```
- 输入账号密码后显示如下，表示登录成功
```shell
Login Succeeded
```
- 修改镜像名
```shell
docker centos sandysaidi/centos
```
- 将镜像推送到docker hub上
```shell
docker push sandysaidi/centos
```
- 修改docker hub的镜像
```shell
docker commit -m "【提交镜像的备注】" -a "【指定作者名】" sandysaidi/centos
```


