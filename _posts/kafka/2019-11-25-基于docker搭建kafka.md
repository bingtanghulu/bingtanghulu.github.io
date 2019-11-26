---
layout: post
title:  "docker 搭建kafaka"
date:   2019-11-25 21:06:48 +0800
categories: kafka docker
excerpt: 基于docker搭建kafka环境
---
#### 搭建步骤
#####1. 镜像下载 zk 和 kafka
```bash
docker pull wurstmeister/zookeeper
```
```bash
docker pull wurstmeister/kafka  
```
##### 2. 运行zk 和 kafka
```bash
docker run -d --name zookeeper -p 2181:2181 -t wurstmeister/zookeeper
```
```bash
docker run  -d --name kafka \
-p 9092:9092 \
-e KAFKA_BROKER_ID=0 \
-e KAFKA_ZOOKEEPER_CONNECT=10.12.140.89:2181 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://10.12.140.89:9092 \
-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -t wurstmeister/kafka
````

**10.12.140.89===》宿主机ip**

##### 2. 创建 kafka topic
- 到  `/opt/kafka_2.12-1.1.0/` 目录下
```bash
cd /opt/kafka_2.12-1.1.0/
```
- 执行如下命令，注意zookeeper 的IP地址
```bash
bin/kafka-topics.sh --create --zookeeper 172.17.0.2:2181 --replication-factor 1 --partitions 1 --topic mykafka
```
- 查看你的topic状态
```bash
bin/kafka-topics.sh --describe --zookeeper 172.17.0.2:2181 --topic mykafka
```
- 发送消息
```bash
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic mykafka
```
- 接收消息
```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic mykafka --from-beginning
```
