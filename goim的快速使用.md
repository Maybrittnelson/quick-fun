[TOC]

Table of Contents
=================

* [Per-requisites](#per-requisites)
  * [安装jdk并更新path环境变量](#安装jdk并更新path环境变量)
  * [搭建kafka集群案例](#搭建kafka集群案例)
  * [搭建golang环境](#搭建golang环境)
* [安装goim并启动](#安装goim并启动)
* [Demo启动并校验](#demo启动并校验)

## Per-requisites

### 安装jdk并更新path环境变量 

### 搭建kafka集群案例

1. [Download the code](http://kafka.apache.org/quickstart#quickstart_download)

2. [Get a quick-and-dirty single-node Zookeeper instance](http://kafka.apache.org/quickstart#quickstart_startserver)

3. 修改kafka默认启动参数
     ```shell
      [root@shaofeng ~]# vi /usr/local/kafka/bin/kafka-server-start.sh
      ...
      if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
          export KAFKA_HEAP_OPTS="-Xmx512M -Xms512M" #将原有1G变成512M
      fi
      ...
      ```

4. [Setting up a multi-broker cluster](http://kafka.apache.org/quickstart#quickstart_multibroker)

[Back to TOC](#table-of-contents)

### 搭建golang环境

1.下载源码(根据自己的系统下载对应的[安装包](http://golang.org/dl/))

```shell
$ cd /data/programfiles
$ wget -c --no-check-certificate https://storage.googleapis.com/golang/go1.11.2.linux-amd64.tar.gz
$ tar -xvf go1.11.2.linux-amd64.tar.gz -C /usr/local
```

2.配置GO环境变量 (这里我加在/etc/profile.d/golang.sh)

```shell
$ vi /etc/profile.d/golang.sh
# 将以下环境变量添加到profile最后面
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
export GOPATH=/data/apps/go
$ source /etc/profile
```
[Back to TOC](#table-of-contents)

### 安装goim并启动

1. 下载goim以及安装第三依赖

```shell
git clone https://github.com/Terry-Mao/goim.git
mkdir $GOPATH/src/github.com/Terry-Mao/goim
mv goim $GOPATH/src
#安装
go get -u github.com/kardianos/govendor
go install github.com/kardianos/govendor
#进入goim
cd $GOPATH/src/goim
#初始化
govendor init
#加入本地 vendor目录当中。
govendor add +external
#查看生成列表。
govendor list

govendor fetch github.com/thinkboy/log4go
govendor fetch github.com/gorilla/websocket
govendor fetch github.com/Shopify/sarama
govendor fetch github.com/wvanbergen/kafka/consumergroup
govendor fetch github.com/Terry-Mao/goconf
```

2.安装router、logic、comet、job模块(配置文件请依据实际机器环境配置)

```shell
$ cd $GOPATH/src/goim/router
$ go install
$ cp router-example.conf $GOPATH/bin/router.conf
$ cp router-log.xml $GOPATH/bin/
$ cd ../logic/
$ go install
$ cp logic-example.conf $GOPATH/bin/logic.conf
$ cp logic-log.xml $GOPATH/bin/
$ cd ../comet/
$ go install
$ cp comet-example.conf $GOPATH/bin/comet.conf
$ cp comet-log.xml $GOPATH/bin/
$ cd ../logic/job/
$ go install
$ cp job-example.conf $GOPATH/bin/job.conf
$ cp job-log.xml $GOPATH/bin/
```

3. 启动goim

```shell
$ cd /$GOPATH/bin
$ nohup $GOPATH/bin/router -c $GOPATH/bin/router.conf 2>&1 > /data/logs/goim/panic-router.log &
$ nohup $GOPATH/bin/logic -c $GOPATH/bin/logic.conf 2>&1 > /data/logs/goim/panic-logic.log &
$ nohup $GOPATH/bin/comet -c $GOPATH/bin/comet.conf 2>&1 > /data/logs/goim/panic-comet.log &
$ nohup $GOPATH/bin/job -c $GOPATH/bin/job.conf 2>&1 > /data/logs/goim/panic-job.log &
```

如果启动失败，默认配置可通过查看panic-xxx.log日志文件来排查各个模块问题.

[Back to TOC](#table-of-contents)

# Demo启动并校验

1. 安装并启动demo

```shell
[root@shaofeng bin]# cd $GOPATH/src/goim/examples/javascript
[root@shaofeng javascript]# go build main.go
[root@shaofeng javascript]# ./main
```

2. 验证demo

通过浏览器访问：http://host:1999/demo.html

是否有如下图片中的network：
<img src="https://github.com/Maybrittnelson/quick-fun/blob/master/pics/demo.jpg">

[Back to TOC](#table-of-contents)
