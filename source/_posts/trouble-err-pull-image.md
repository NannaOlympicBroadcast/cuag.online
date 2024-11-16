---
title: 【经验】解决容器镜像拉不下来的问题
date: 2024-11-16 17:58:50
tags:
---
# 先拉后推法
- 在具有代理的桌面端拉取镜像
- 开通一个个人版TCR容器镜像服务
前往[腾讯云容器镜像服务控制台](https://console.cloud.tencent.com/tcr)开通一个镜像
- 按照[操作提示](https://cloud.tencent.com/document/product/1141/63910)进行登陆和推送
- 在compose文件或者k8s资源文件里面将地址改为ccr.ccs.tencentyun.com/YOURNAMESPACE/YOURIMAGE:TAG
# 流水线白嫖法
- 登陆[e.coding.net](https://e.coding.net)
- 在「项目」中新建一个项目
- 进入项目主页，新建一个代码仓库，使用README.md进行初始化
- 进入代码仓库页面，点击➕，创建一个名为Dockerfile的文件
- 然后上面写上 
```Dockerfile
FROM <YOURIMAGE>:<TAG>
```
- 切换到「持续集成」->「构建计划」，新建一个构建计划，模板选择「docker镜像推送」
- 修改代码仓库代码源为coding，并且选中你的仓库，将docker镜像名称改为你想拉取的镜像名称，选择推送的制品库，如果没有就新建，并且设置为**公开**。
- 取消勾选「创建后立即触发构建」
- 回到你的构建计划，点击后看到小齿轮图标，点进去
- 节点池选择中国香港，并保存，再切换到「触发规则」，取消勾选「代码更新时自动触发」和「合并请求的时候触发」
- 点击保存以后，运行构建，弹出确认界面，将DOCKER_IMAGE_NAME改为你自己的想要的镜像名称，并且将DOCKER_IMAGE_VERSION改为latest
- 构建运行成功后，切换到侧边「制品管理」->制品仓库，点击右上「操作指引」，然后选择「拉取」，输入镜像名称、版本输入latest，复制命令即可拉取
# 容器镜像服务法
参见本页面左上角服务->容器镜像服务->详情

