## python3的编译安装

##下载安装包,并解压
cd /opt
wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tgz
tar -zxvf Python-3.6.6.tgz

###安装编译需要的依赖包
yum -y install gcc openssl-devel zlib* libffi-devel

##编译安装，进入到python包进行编译安装
cd /opt/Python-3.6.6
./configure --prefix=/usr/local/python3
make&&make install

##启动，软连接快速启动
ln -s /usr/local/python3/bin/python3 /usr/bin/python3
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
