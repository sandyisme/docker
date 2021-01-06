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
