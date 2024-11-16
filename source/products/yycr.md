---
title: 容器镜像服务
date: 2024-11-16 16:06:37
---
# 容器镜像服务
## 容器镜像加速
- 方法1：Linux端配置容器镜像加速站点
```shell
sudo vim /etc/docker/daemon.json
```
在其中写入或者修改
```json
{  
  "registry-mirror": [  
    "https://yanyancr.cn"  
  ]  
}
```
然后运行
```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```
- 方法2：命令拉取加前缀
```shell
docker pull yanyancr.cn/<REPONAME>/<IMAGE>:<TAG>
docker tag yanyancr.cn/<REPONAME>/<IMAGE>:<TAG> <REPONAME>/<IMAGE>:<TAG>
```
## 容器镜像私服储存
请微信联系:vme50choukeqing1919
