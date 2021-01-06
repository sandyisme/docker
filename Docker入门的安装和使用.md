## docker的安装

### 设置docker仓库

- 安装所需要的包

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

- 设置稳定的仓库

```shell
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    
# 或者换成阿里云
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

- 安装Docker Engine-Community

```shell
sudo yum -y install docker-ce docker-ce-cli containerd.io
```

- GPG密钥

```shell
060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35
```

- 列出并排序存储库中可用的版本，按版本号（从高到低）对结果进行排序

```shell
yum list docker-ce --showduplicates | sort -r
```

- 指定安装的docker版本

```shell
sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io

# 例如docker-ce-18.09.6
sudo yum install docker-ce-18.09.6 docker-ce-cli-18.09.6 containerd.io
```

- 启动docker

```shell
sudo systemctl start docker
```

- 测试docker安装是否正确

```shell
sudo docker run hello-world
```

## docker的卸载

- docker的卸载

```shell
# 卸载安装的旧版本的docker以及相关的依赖项
sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine

# 或者docker engine-community的卸载
sudo yum remove docker-ce docker-ce-cli containerd.io
```

- 删除docker的所有图像，容器和卷的配置文件

```shell
sudo rm -rf /var/lib/docker
```

## docker 镜像加速器的添加

- [获取阿里云的个人加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

- 添加加速器配置并重启服务生效

```shell
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://1b6ys59u.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

- 检查加速器是否生效

```shell
docker info
```

- 登录registry实例

```shell
docker login --username=18895302620 registry.cn-hangzhou.aliyuncs.com
```

## docker运行web程序

- 拉取镜像

```shell
docker pull training/webapp
```

- docker后台启动容器（将容器内部使用的网络端口映射）

```shell
docker run -d -P training/webapp python app.py

# 创建容器时命名
docker run -d -P --name sandy training/webapp python app.py


#容器内部的端口5000 本地主机的端口32768
0.0.0.0:32768->5000/tc
```

- 查看web应用容器是否正在运行

```shell
docker ps
```

- 设置网络端口映射端口

```shell
docker run -d -p 5000:5000 training/webapp python app.py
```

- 查看程序的日志

```shell
docker logs -f 【ID或者名字】
```

- 查看程序的进程

```shell
docker top 【ID或者名字】
```

## docker仓库管理

- 登录

```shell
docker login
```

- 退出

```shell
docker logout
```

- 将镜像推送到docker hub（docker hub 用户名为sandysaidi）

```shell
docker push 【docker hub用户名】/ubuntu:15.10
```

- 查看是否推送成功

```shell
docker search 【docker hub用户名】/ubuntu:15.10
```

## 使用dockerfile构建镜像

- 新建文件夹

```shell
mkdir Dockerfile
```

- 新建文件下创建Dockerfile构建文件

```shell
cd Dockerfile

vi Dockerfile

# 以 && 符号连接命令，这样执行后，减少创建层镜像

FROM 【定制的镜像都是基于FROM的镜像，例如ubuntu】
RUN 【用于执行后面跟着的命令行命令，1）RUN <命令行命令>	2）RUN ["可执行文件", "参数1", "参数2"]】

# 改变复制到容器内文件的拥有者和属组;源文件或者源目录,通配符规则要满足 Go 的 filepath.Match 规则;容器内的指定路径，该路径不用事先建好，路径不存在的话，会自动创建
COPY [--chown=<user>:<group>] <源路径1>...  <目标路径>
COPY [--chown=<user>:<group>] ["<源路径1>",...  "<目标路径>"]
# ADD 指令和 COPY 的使用格式一致（同样需求下，官方推荐使用 COPY）。功能也类似。ADD 的优点：在执行 <源文件> 为 tar 压缩文件的话，压缩格式为 gzip, bzip2 以及 xz 的情况下，会自动复制并解压到 <目标路径>。ADD 的缺点：在不解压的前提下，无法复制 tar 压缩文件。会令镜像构建缓存失效，从而可能会令镜像构建变得比较缓慢。具体是否使用，可以根据是否需要自动解压来决定。
ADD [--chown=<user>:<group>] <源路径1>...  <目标路径>
ADD [--chown=<user>:<group>] ["<源路径1>",...  "<目标路径>"]
# 类似于 RUN 指令，用于运行程序，但二者运行的时间点不同,CMD 在docker run 时运行。RUN 是在 docker build。
CMD <shell 命令> 
CMD ["<可执行文件或命令>","<param1>","<param2>",...] 
CMD ["<param1>","<param2>",...] 
# 类似于 CMD 指令，但其不会被 docker run 的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序。如果运行 docker run 时使用了 --entrypoint 选项，此选项的参数可当作要运行的程序覆盖 ENTRYPOINT 指令指定的程序。在执行 docker run 的时候可以指定 ENTRYPOINT 运行所需的参数。注意：如果 Dockerfile 中如果存在多个 ENTRYPOINT 指令，仅最后一个生效。可以搭配 CMD 命令使用：一般是变参才会使用 CMD ，这里的 CMD 等于是在给 ENTRYPOINT 传参。
ENTRYPOINT ["<executeable>","<param1>","<param2>",...]
# 设置环境变量，定义了环境变量，那么在后续的指令中，就可以使用这个环境变量
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>...
# 构建参数，与 ENV 作用一至。不过作用域不一样。ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效，构建好的镜像内不存在此环境变量。
构建命令 docker build 中可以用 --build-arg <参数名>=<值> 来覆盖
ARG <参数名>[=<默认值>]
# 定义匿名数据卷。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。避免重要的数据，因容器重启而丢失，这是非常致命的。避免容器不断变大。在启动容器 docker run 的时候，我们可以通过 -v 参数修改挂载点。
VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>
# 仅仅只是声明端口。帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射。在运行时使用随机端口映射时，也就是 docker run -P 时，会自动随机映射 EXPOSE 的端口。
EXPOSE <端口1> [<端口2>...]
# 指定工作目录。用 WORKDIR 指定的工作目录，会在构建镜像的每一层中都存在。（WORKDIR 指定的工作目录，必须是提前创建好的）docker build 构建镜像过程中的，每一个 RUN 命令都是新建的一层。只有通过 WORKDIR 创建的目录才会一直存在。
WORKDIR <工作目录路径>
# 用于指定执行后续命令的用户和用户组，这边只是切换后续命令执行的用户（用户和用户组必须提前已经存在）。
USER <用户名>[:<用户组>]
# 用于指定某个程序或者指令来监控 docker 容器服务的运行状态。
HEALTHCHECK [选项] CMD <命令>：设置检查容器健康状况的命令
HEALTHCHECK NONE：如果基础镜像有健康检查指令，使用这行可以屏蔽掉其健康检查指令
HEALTHCHECK [选项] CMD <命令> : 这边 CMD 后面跟随的命令使用，可以参考 CMD 的用法。
# 用于延迟构建命令的执行。简单的说，就是 Dockerfile 里用 ONBUILD 指定的命令，在本次构建镜像的过程中不会执行（假设镜像为 test-build）。当有新的 Dockerfile 使用了之前构建的镜像 FROM test-build ，这是执行新镜像的 Dockerfile 构建时候，会执行 test-build 的 Dockerfile 里的 ONBUILD 指定的命令。
ONBUILD <其它指令>
```

- 开始构建镜像

```shell
# .代表本次执行的上下文路径,上下文路径放无用的文件，会一起打包发送给docker引擎，文件过多会造成过程缓慢

docker build -t 【镜像名】:【tag】 .
```
