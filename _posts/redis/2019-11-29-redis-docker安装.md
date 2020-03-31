---
layout: post
title:  "redis docker 安装"
date:   2019-11-29 14:15:48 +0800
categories: redis
tags: redis docker
excerpt: redis docker
---
#### redis 安装
##### 1. 查找镜像包
```bash
docker search redis
```

```
NAME                             DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
redis                            Redis is an open source key-value store that…   7576                [OK]                
bitnami/redis                    Bitnami Redis Docker Image                      133                                     [OK]
sameersbn/redis                                                                  78                                      [OK]
grokzen/redis-cluster            Redis cluster 3.0, 3.2, 4.0 & 5.0               62                                      
rediscommander/redis-commander   Alpine image for redis-commander - Redis man…   32                                      [OK]
kubeguide/redis-master           redis-master with "Hello World!"                30                                      
redislabs/redis                  Clustered in-memory database engine compatib…   24                                      
arm32v7/redis                    Redis is an open source key-value store that…   20                                      
oliver006/redis_exporter          Prometheus Exporter for Redis Metrics. Supp…   18                                      
redislabs/redisearch             Redis With the RedisSearch module pre-loaded…   17                                      
webhippie/redis                  Docker images for Redis                         10                                      [OK]
s7anley/redis-sentinel-docker    Redis Sentinel                                  9                                       [OK]
insready/redis-stat              Docker image for the real-time Redis monitor…   9                                       [OK]
bitnami/redis-sentinel           Bitnami Docker Image for Redis Sentinel         8                                       [OK]
redislabs/redisgraph             A graph database module for Redis               8                                       [OK]
arm64v8/redis                    Redis is an open source key-value store that…   6                                       
redislabs/redismod               An automated build of redismod - latest Redi…   5                                       [OK]
centos/redis-32-centos7          Redis in-memory data structure store, used a…   4                                       
frodenas/redis                   A Docker Image for Redis                        2                                       [OK]
circleci/redis                   CircleCI images for Redis                       2                                       [OK]
runnable/redis-stunnel           stunnel to redis provided by linking contain…   1                                       [OK]
wodby/redis                      Redis container image with orchestration        1                                       [OK]
tiredofit/redis                  Redis Server w/ Zabbix monitoring and S6 Ove…   1                                       [OK]
xetamus/redis-resource           forked redis-resource                           0                                       [OK]
cflondonservices/redis           Docker image for running redis                  0
```
用star最多的，也就是第一个

#### 2.镜像拉取
```bash
docker pull redis
```

#### 3.启动镜像
```bash
docker run -p 6379:6379 -d redis:latest redis-server
```

#### 4.进入容器
```bash
docker  exec -it myredis  redis-cli
```
