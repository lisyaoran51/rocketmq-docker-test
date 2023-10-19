# rocketmq-docker-test

https://blog.clarence.tw/2019/09/10/docker-removing-containers-images-volumes-and-networks/

	docker pull apache/rocketmq:4.9.3
	docker network create rocketmq
	docker run -d --name rocketmq-test --network rocketmq -p 9876:9876 -v /Users/josh_sinrui/Documents/josh/sinrui/mount/rocketmq-test/logs:/root/logs -v /Users/josh_sinrui/Documents/josh/sinrui/mount/rocketmq-test/store:/root/store apache/rocketmq:4.9.3 sh mqnamesrv
	docker cp rocketmq-test:/home/rocketmq/rocketmq-4.9.3/conf /Users/josh_sinrui/Documents/josh/sinrui/mount/rocketmq-test/broker/broker-a/

	docker run -d --name rocketmq-broker-a --network rocketmq -p 10909:10909 -p 10911:10911 -v /Users/josh_sinrui/Documents/josh/sinrui/mount/rocketmq-test/broker/broker-a/logs:/root/logs -v /Users/josh_sinrui/Documents/josh/sinrui/mount/rocketmq-test/broker/broker-a/store:/root/store -v /Users/josh_sinrui/Documents/josh/sinrui/mount/rocketmq-test/broker/broker-a/conf:/home/rocketmq/rocketmq-4.9.3/conf apache/rocketmq:4.9.3 sh mqbroker -c /home/rocketmq/rocketmq-4.9.3/conf/broker.conf

	docker run -d --name rocketmq-console --network rocketmq -e "JAVA_OPTS=-Drocketmq.namesrv.addr=rocketmq-test:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false" -p 8000:8080 apacherocketmq/rocketmq-dashboard:latest


## broker.conf

```
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
#  limitations under the License.

# brokerClusterName = DefaultCluster
# brokerName = broker-a
# brokerId = 0
# deleteWhen = 04
# fileReservedTime = 48
# brokerRole = ASYNC_MASTER
# flushDiskType = ASYNC_FLUSH

# 所属集群名称如果节点较多可以配置多个
brokerClusterName = DefaultCluster
#broker名称 master和slave使用相同的名称 表明他们的主从关系
brokerName = broker-a
#0表示Master,大于0表示不同的slave
brokerId = 0
#表示几点做消息删除动作,默认是凌晨4点
deleteWhen = 04
#在磁盘上保留消息的时长,单位是小时
fileReservedTime = 48
#有三个值&#xff1a;SYNC_MASTER,ASYNC_MASTER,SLAVE 同步和异步表示Master和Slave之间同步数据的机制 
brokerRole = SYNC_MASTER
#刷盘策略,取值为&#xff1a;ASYNC_FLUSH,SYNC_FLUSH表示同步刷盘和异步刷盘 SYNC_FLUSH消息写入磁盘后才返回成功状态,ASYNC_FLUSH不需要 
flushDiskType = SYNC_FLUSH
# 设置broker节点所在服务器的ip地址&#xff08;**这个非常重要,主从模式下,从节点会根据主节点的brokerIP2来同步数据,如果不配置,主从无法同步,brokerIP1设置为自己外网能访问的ip,服务器双网卡情况下必须配置,比如阿里云这种,主节点需要配置ip1和ip2,从节点只需要配置ip1即可 
# ipconfig getifaddr en0
brokerIP1 = 192.168.21.2
#nameServer地址,分号分割
namesrvAddr=192.168.21.2:9876
#Broker 对外服务的监听端口,
listenPort = 10911
#是否允许Broker自动创建Topic
autoCreateTopicEnable = true
#是否允许 Broker 自动创建订阅组
autoCreateSubscriptionGroup = true
#linux开启epoll
useEpollNativeSelector = true

#数据存放的根目录
#storePathRootDir = /root/store/path
#commit log保存目录
#storePathCommitLog = /root/store/path/commitlog
#消费队列存储路径存储路径
#storePathConsumerQueue = /root/store/path/consumequeue

slaveReadEnable = true
```