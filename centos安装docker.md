# CentOS 安装docker 和 docker-compose

### 1.docker安装

**卸载老旧的版本（若未安装过可省略此步）：**

```
$ sudo apt-get remove docker docker-engine docker.io
```

**安装最新的docker：**

    $ curl -fsSL get.docker.com -o get-docker.sh
    
    $ sudo sh get-docker.sh
    （或者使用：
    $ curl -sSL https://get.docker.com/ | sh ）

**启动docker**

````
service docker start
````

**确认Docker成功最新的docker：**

```
$ sudo docker run hello-world
```

### 2.安装docker-compose

1.从github上下载docker-compose二进制文件安装

    下载最新版的docker-compose文件 
    $ sudo curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    添加可执行权限 
    $ sudo chmod +x /usr/local/bin/docker-compose
    测试安装结果 
    $ docker-compose --version 
    docker-compose version 1.16.1, build 1719ceb
    
2.pip安装

````
$ sudo pip install docker-compose
````

**#如出现以下情况**

**[root@izwz91quxhnlk9mqhy9p75z run]# docker-compose version**
*/usr/lib/python2.7/site-packages/requests/init.py:80: RequestsDependencyWarning: urllib3 (1.22) or chardet (2.2.1) doesn't match a supported version!
  RequestsDependencyWarning)
docker-compose version 1.23.1, build b02f130
docker-py version: 3.5.1
CPython version: 2.7.5
OpenSSL version: OpenSSL 1.0.1e-fips 11 Feb 2013*

**原因**：python库中urllib3 (1.22) or chardet (2.2.1) 的版本不兼容 

**解决如下：**

```
$ pip uninstall urllib3

$ pip uninstall chardet 

$ pip install requests
```

