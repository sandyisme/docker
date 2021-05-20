# docker hub push images
## 登录docker hub
```shell
doker login --username=***
```
## tag 
```shell
docker tag [原镜像名称] [docker hub用户名]/[镜像名称:tag]
```
## 上传镜像到hub
```shell
docker push [docker hub用户名]/[镜像名称:tag]
```
