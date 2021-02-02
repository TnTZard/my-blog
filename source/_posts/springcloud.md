---
title: springcloud
date: 2020-06-02 10:38:44
tags: Spring
---

ELK

```yam
version: '3'
services: 
  elasticsearch:
    image: elasticsearch:6.4.1
    container_name: elasticsearch
    environment:
      - "cluster.name=elasticsearch" #集群名称为elasticsearch
      - "discovery.type=single-node" #单节点启动
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - /az/elasticsearch/plugins:/usr/share/elasticsearch/plugins
      - /az/elasticsearch/data:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
  kibana:
    image: kibana:6.4.1
    container_name: kibana
    links: 
      - elasticsearch:es #配置elasticsearch的域名为es
    depends_on: 
      - elasticsearch
    environment:
      - "elasticsearch.hosts=http://es:9200"
    ports:
      - 5601:5601
  logstash:
    image: logstash:6.4.1
    container_name: logstash
    volumes:
      - /az/logstash/logstash-az.conf:/usr/share/logstash/pipeline/logstash.conf 
    depends_on:
      - elasticsearch
    links: 
      - elasticsearch:es
    ports:
      - 4560:4560         

```

带着变量启动jar包  

```sh
java(java命令) -jar(执行jar包命令) xxx.jar(要执行的jar包) --params(参数名)=aaa(参数值) --params2(参数名)=bbb(参数值)
java -jar xxx.jar --params=aaa --params=bbb
```

参数如下

```sh
redis.url=47.112.16.168;
rabbitmq.url=127.0.0.1;
az-gateway=127.0.0.1;
az-monitor-admin=127.0.0.1;
az-register=127.0.0.1
```

Dockerfile

```dockerfile
FROM openjdk:8u212-jre
MAINTAINER anzhi 562966183@qq.com

COPY az-auth-1.0-SNAPSHOT.jar /az/az-auth-1.0-SNAPSHOT.jar
ENTRYPOINT ["java", "-Xmx512m", "-jar", "/az/az-auth-1.0-SNAPSHOT.jar"]

COPY az-auth-1.0-SNAPSHOT.jar /az/az-auth-1.0-SNAPSHOT.jar  表示将当前目录（/az/az-auth）az-auth-1.0-SNAPSHOT.jar拷贝到openjdk:8u212-jre镜像里的/az目录下，名称也为az-auth-1.0-SNAPSHOT.jar；


# 创建镜像
docker build -t xxx .
```

删除image，通过image的id来指定删除谁

docker rmi <image id>