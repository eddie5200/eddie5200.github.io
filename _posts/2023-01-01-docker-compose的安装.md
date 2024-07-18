---
layout:     post
title:      docker-compose
subtitle:   docker-compose安装docker
date:       2023-01-01
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
---

```
1.安装docker

2.安装docker-compose
    2.1 下载
         https://github.com/docker/compose/releases/download/v2.24.4/docker-compose-linux-x86_64
    
    2.2 安装
        mv docker-compose-linux-x86_64 /usr/local/bin/docker-compose
        chmod +x /usr/local/bin/docker-compose

    2.3 查看版本
        docker-compose --version



3. docker-compose常用命令命令 
    build	重新构建服务
    ps	列出容器
    up	创建和启动容器
    exec	在容器里面执行命令
    scale	指定一个服务容器启动数量
    top	显示容器进程
    logs	查看容器输出
    down	删除容器、网络、数据卷和镜像
    stop/start/restart	停止/启动/重启服务


4. docker-compose配置常用字段

    build	        指定 Dockerfile 文件名，要指定Dockerfile文件需要在build标签的子级标签中使用dockerfile标签指定
    dockerfile	    构建镜像上下文路径 dockerfile-nginx
    context	        可以是 dockerfile 的路径，或者是指向 git 仓库的 url 地址
    image	        指定镜像
    command	        执行命令，覆盖容器启动后默认执行的命令
    container_name	指定容器名称，由于容器名称是唯一的，如果指定自定义名称，则无法scale指定容器数量
    deploy	        指定部署和运行服务相关配置，只能在 Swarm 模式使用
    environment	    添加环境变量
    networks	    加入网络，引用顶级networks下条目
    network_mode	设置容器的网络模式，如 host，bridge，...
    ports	        暴露容器端口，与 -p 相同，但端口不能低于 60
    volumes	        挂载一个宿主机目录或命令卷到容器，命名卷要在顶级 volumes 定义卷名称
    volumes_from	从另一个服务或容器挂载卷，可选参数 :ro 和 :rw，仅版本 '2' 支持
    hostname	    容器主机名
    sysctls	        在容器内设置内核参数
    links	        连接到另外一个容器，- 服务名称[:服务别名]
    privileged	    用来给容器root权限，注意是不安全的，true | false
    restart	        设置重启策略，no，always，no-failure，unless-stoped                 
                        no，默认策略，在容器退出时不重启容器。         
                        on-failure，在容器非正常退出时（退出状态非0），才会重启容器。
                        on-failure:3，在容器非正常退出时重启容器，最多重启3次。
                        always，在容器退出时总是重启容器。
                        unless-stopped，在容器退出时总是重启容器，但是不考虑在 Docker 守护进程启动时就已经停止了的容器。
```