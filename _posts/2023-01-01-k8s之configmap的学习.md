---
layout:     post
title:       k8s的configmap学习
subtitle:    k8s的configmap学习
date:       2023-01-01
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - configmap
---

#
**kubernetes实战篇之创建configmap**

ConfigMap是k8s的一个配置管理组件，可以将配置以key-value的形式传递，或者文件形式，通常用来保存不需要加密的配置信息，加密信息则需用到Secret，主要用来应对以下场景：

&ensp; &ensp; 1. 生成为容器内的环境变量；

&ensp; &ensp; 2. 设置容器启动命令的启动参数（需设置为环境变量）

&ensp; &ensp; 3. 以Volume的形式挂载为容器内部的文件或目录。



###
**一：configmap类型**

&ensp; &ensp; &ensp; &ensp; 1. data类型：传递数据

&ensp; &ensp; &ensp; &ensp; 2. binaryData类型：使用二进制传递数据

```
kubectl explain cm
   binaryData 使用二进制传递数据
   data       传递数据
注意：
   基于data的方式传递信息的话，他会在pod的容器内部生成一个单独的数据文件
 
数据的表现样式：
 普通数据：
 属性名称: 属性值
 文件数据：
 文件名称: |
 文件内容
 注意：
   | 是"多行键值"的标识符
```




###
**二：configmap创建**


&ensp; &ensp; 1. **--from-file:通过普通文件创建**
```
单个文件导入
kubectl create configmap cm-test1 --from-file=./test1.conf -n your-namespace

多个文件导入
kubectl create configmap cm-test1-test2 --from-file=./test1.conf --from-file=./test2.conf  -n your-namespace
```

&ensp; &ensp; 2. **--from-file:通过目录创建**
```
[root@node1 ~]# mkdir config
[root@node1 ~]# echo hello > config/hello.txt
[root@node1 ~]# echo world > config/world.txt
[root@node1 ~]# 
[root@node1 ~]# kubectl create cm test-configmap --from-file=/root/config -n your-namespace
```

&ensp; &ensp; 3. **--from-file:通过自定义数据源创建** 
```
 kubectl create configmap cm-data --from-file=test-cm-define=/etc/yum.repos.d/local.repo  -n configmap

```


&ensp; &ensp; 4. **--from-env-file='' 以环境变量专用文件的方式配置数据** 
```
kubectl create configmap cm-env --from-env-file=./test.env 
```


&ensp; &ensp; 5.  **--from-literal:通过从字符集创建**
```
 kubectl create cm test-configmap --from-literal=user=admin --from-literal=password=123456 -n your-namespace
 ```

 &ensp; &ensp; 6. **yaml:通过yaml文件,文件格式创建**
 
 ```
 ---
apiVersion: v1
data:
  customization.cnf: |   # customization.cnf是文件名
    [mysqld]
    datadir = /mariadb_data
    lower_case_table_names = 1
    sql-mode="NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"
    character-set-server=utf8
    [client]
    default-character-set=utf8
kind: ConfigMap
metadata:
  name: mysql-config
  namespace: configmap
```

 &ensp; &ensp; 4. yaml:通过yaml文件,是Key/value格式创建
 ```
apiVersion: v1
kind: ConfigMap
metadata:
  name: test-configmap
data:
  cache_host: localhost
 ```

 ###
**三：configmap合并**
```
mkdir nginx-conf && cd nginx-conf
# 主配置文件 
cat >myserver.conf<<'EOF'
server {
    listen 8080;
    server_name www.sswang.com;
    include /etc/nginx/conf.d/myserver-*.cfg;
    location / {
        root /usr/share/nginx/html;
    }
}
EOF

# 压缩配置片段文件
cat >myserver-gzip.conf<<'EOF'
gzip on;
gzip_comp_level 5;
gzip_proxied     expired no-cache no-store private auth;
gzip_types text/plain text/css application/xml text/javascript;
EOF

# 状态配置片段文件
cat >myserver-status.conf<<'EOF'
location /nginx-status {
    stub_status on;
    access_log off;
}
EOF
```

合并： 多个文件之间，属性名是文件名，属性值是文件内容，属性和属性彼此之间是通过空行来隔开
```
kubectl create configmap nginx-conf --from-file nginx-conf/myserver.conf \
--from-file=nginx-conf/myserver-status.conf --from-file=nginx-conf/myserver-gzip.conf
kubectl create configmap nginx-conf --from-file=nginx-conf/
```


###
**三：在Pod中使用configmap**


&ensp; &ensp;1. **通过环境变量的env方式，直接定义变量，传递给pod**
<br>
&ensp; &ensp; &ensp; &ensp;环境变量是容器启动时注入的，不会随CM更改而发生变化
```
cat >configmap-env-test.yml<<'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config
data:
  port: "10086"
  user: "www"
---
apiVersion: v1
kind: Pod
metadata:
  name: configmap-env-test
spec:
  containers:
  - name: nginx1
    image: 192.168.10.33:80/k8s/my_nginx:v1
    env:
    - name: NGINX_HOST   ### 环境变量
      value: "192.168.10.33"  ### 环境变量的值
EOF
```


&ensp; &ensp;2. **通过环境变量env的方式，configMapKeyRef，传递给pod**
```
cat >configmap-env-test.yml<<'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config
data:
  port: "10086"
  user: "www"
---
apiVersion: v1
kind: Pod
metadata:
  name: configmap-env-test
spec:
  containers:
  - name: nginx1
    image: 192.168.10.33:80/k8s/my_nginx:v1
    env:
    - name: NGINX_PORT
      valueFrom:
        configMapKeyRef:
          name: nginx-config  ### 引用的configmap名称
          key: port           ##### 引用的configmap的key
          optional: true
    - name: NGINX_USER
      valueFrom:
        configMapKeyRef:
          name: nginx-config
          key: user
          optional: false
EOF
```


&ensp; &ensp;3. **通过环境变量envFrom的方式，configMapRef，传递给pod**
```
cat >configmap-envfrom-test.yml<<'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config
data:
  NGINX_PORT: "10086"
  NGINX_USER: "www"
---
apiVersion: v1
kind: Pod
metadata:
  name: configmap-envfrom-test
spec:
  containers:
  - name: nginx1
    image: 192.168.10.33:80/k8s/my_nginx:v1
    envFrom:
    - configMapRef:
        name: nginx-config
EOF

[root@master1 ]# kubectl exec -it configmap-envfrom-test -- /bin/bash
root@configmap-envfrom-test:/# echo $NGINX_PORT $NGINX_USER
10086 www
```

&ensp; &ensp;4. **${var_name}参数变量，传递给pod**
```
cat >command-pod.yml<<'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: command-arg
data:
  time: "3600"
---
apiVersion: v1
kind: Pod
metadata:
  name: command-pod
spec:
  containers:
    - name: command-pod
      image: 192.168.10.33:80/k8s/busybox:latest
      command: [ "/bin/sh", "-c", "sleep ${SPECIAL_TIME}" ]
      env:
      - name: SPECIAL_TIME
        valueFrom:
          configMapKeyRef:
            name: command-arg
            key: time
  restartPolicy: Never
EOF
```


&ensp; &ensp;5. **数据卷，传递给pod**
```
cat > redis-configmap.yml<<'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: redis-configmap
data:
  special.name: redis-name
  redis.conf: |
    [daemon]
    daemon=true
---
apiVersion: v1
kind: Pod
metadata:
  name: conf-test-volume
spec:
  volumes:
    - name: redis-volume
      configMap:
        name: redis-configmap
  containers:
    - name: nginx
      image: 192.168.10.33:80/k8s/my_nginx:v1
      volumeMounts:
      - name: redis-volume
        mountPath: /redis_home/
EOF
```

参考文件：https://www.cnblogs.com/ygbh/p/17262329.html#_label3_1_4_1