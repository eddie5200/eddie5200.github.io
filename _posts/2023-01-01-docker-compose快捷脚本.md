---
layout:     post
title:      docker-compose快捷命令
subtitle:   docker-compose快捷命令
date:       2023-01-01
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - docker-compose
---

### 安装jq工具

```
    yum install -y jq
```

#### 列出镜像

```
 docker-compose config  --resolve-image-digests --format json|jq ".services.\"${service}\".image"
```

#### 列出服务

```
docker-compose config  --resolve-image-digests --format json|jq '.services[].container_name'
```


#### 列出端口

```
docker-compose config  --resolve-image-digests --format json|jq ".services.\"${service}\".ports[].published"
```


