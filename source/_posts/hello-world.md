---
title: YICHUANBAIYUE.IO：专门为国内中小型团队而生的云原生服务
---
# 致力打造下一代自主独立的云原生服务套件（待实现）
计算机技术的发展一定离不开技术爱好者的支持，现在国内开发者面临资源紧、急功近利、国外封锁等问题，不能很好的提供计算机兴趣者发展的一个空间，所以，这个开源项目旨在为国内中小型团队提供上云的便利，刺激国内计算机技术和互联网产业的发展
## MJC：连接器平台
使用webhook进行触发，只需要一个公网IP或者域名，就可以多端同时订阅多类的推送消息
## by2(Automaven): 一键安装对口的Maven包
自动化管理maven包，不用执着于pom，一命令自动安装各大依赖兼容的插件版本
```shell
by2 install qcloud-cos-sdk
```
## SelvOps(SKE)：下一代k8s自动化驱动装置
- 节点间自动同步镜像，无需从外网拉取，解决离线集群无法拉镜像问题的不二优选
- 与CI/CD主流流水线无缝结合，自动化部署服务
- 兼容一切helm图表，直接从宿主机层安装功能增强插件
```shell
sevctl install repo/some-plugin
```
## Linabell：更加方便的服务发现
```python
import LinabellSVC from yichuanbaiyue.linabell

@LinabellSVC("service","/")
def hello_world(a):
    return a+1
```
```python
import LbClient from yichuanbaiyue.linabeell
b = LbClient("service/").invoke(3)
```
## Twelve：自然语言进行检索的模型工具仓库
根据自然语言需求，从模型仓库中搜索最符合模型，并运行机器学习流水线，可以评估模型算力，并将推理安排到边缘设备或者专用推理节点
## yanyan：机器学习镜像管理工具
提供模型、数据集、运行环境容器化打包构建装置，兼容所有dockerfile语法
```Yanyanfile
FROM pytorch latest #微调环境
PULL MODEL qwen2:7b as model #拉取千问7b作为基础模型
ADD DATASET yanyanexample/helloworld dataset/helloworld #添加hf的dataset到指定文件夹
DO FINE-TUNING --config=finetuning.yaml #按照finetuning.yaml配置文件进行微调
FROM pytorch:latest as inference #推理环境
INSTALL MODEL --from=model #安装微调中微调后的模型
SERVE --openai 8080 #以openai API范式对外8080暴露推理api
```
