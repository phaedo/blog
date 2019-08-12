---
layout: post
title:  "从0到1实践SpringCloud的微服务架构系统（二）"
date:   2019-08-12 21:03:36 +0530
categories: Web
tags: [springboot, web]
description: 实践SpringCloud的微服务架构系统-Eureka
---

## Eureka简介

Eureka是一个基于REST HTTP协议的服务注册中心，包含Eureka Server和Eureka Client两部分。Eureka Server用于记录和路由服务的地址和端口号，Eureka Client用于向Eureka Server登记和调用服务。一般Eureka Client会与其他组件配置，完成复杂的负载均衡，如基于网络拥塞率，资源利用率，错误率的负载均衡方法。Eureka Client也支持非JAVA版本的实现。

Netflix公司建议按如下图部署Eureka服务，首先它包含了一个Eureka Server集群，分部在各个域内（一个云服务单位，一般一个域是一个机房），Eureka Client只向域内的Eureka Server注册服务。Eureka Client每30s发送一次心跳，同时会将当前的服务注册表缓存在本地（所以一般注册表的变化最长需要花费2min才能完成传播）。如果某个服务90秒内无心跳记录，则该服务会被下线。接着服务注册表的变更会在Eureka Server集群内传播，保证Eureka Client可以在域间调用服务。

![eureka-architecture](https://phaedo.github.io/blog/post-assets/2019-08/eureka-architecture.png)


## 配置Eureka Server

1. 从[SpringInitializer](https://start.spring.io/)中选择eureka-server作为依赖，下载初始zip包。

2. 在启动类中增加`@EnableEurekaServer`注解

3. 设置eureka-server集群，包括两台机器server1 && server2保证高可用（其他默认属性可参考[EurekaInstanceConfigBean](https://github.com/spring-cloud/spring-cloud-netflix/blob/master/spring-cloud-netflix-eureka-client/src/main/java/org/springframework/cloud/netflix/eureka/EurekaInstanceConfigBean.java)和[EurekaClientConfigBean](https://github.com/spring-cloud/spring-cloud-netflix/blob/master/spring-cloud-netflix-eureka-client/src/main/java/org/springframework/cloud/netflix/eureka/EurekaClientConfigBean.java)）

4. 打包eureka-server.jar   
```bash
$ mvn install
$ cp target/eureka-server.jar /path/to/dockerfile
```

5. 制作eureka-server的镜像，注意要将jar包放入上下文路径中，示例的dockerfile如下。   
```bash
FROM ubuntu:latest
RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list \
    && DEBIAN_FRONTEND=noninteractive \
    && apt-get clean \
    && apt-get update -y \
    && apt-get install -y openjdk-8-jdk-headless
COPY ./eureka-server.jar /root/
CMD java -jar eureka-server.jar
```

6. docker镜像打包（最后一个点是上下文）
```bash
$ docker build -t eureka-server:latest .
```

7. 启动eureka-server集群，示例映射为7001和7002端口    
```bash
$ docker run -d --name eureka-server1 -p 7001:7001 eureka-server:latest
$ docker run -d --name eureka-server2 -p 7001:7002 eureka-server:latest
```

**另外注意**，要保证Eureka Server的版本高于Eureka Client避免未知的异常。

## 测试Eureka Client

1. 初始化一个SpringBoot工程，引入下列依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

2. 设置Eureka Client的属性

```yaml
server:
    port: 8081

spring:
    application:
        name: demo-client

eureka:
    instance: demo-client
        prefer-ip-address: true
        instance-id:  ${spring.application.name}(${spring.cloud.client.ip-address}:${server.port})
    client:
        register-with-eureka: true
        fetch-registry: true
        serviceUrl:
            defaultZone: http://server1:7001/eureka, http://server1:7002/eureka
```

3. 在启动器中增加`@EnableEurekaClient`注解

```java
@EnableEurekaClient
@SpringBootApplication
public class WebApplication {

	public static void main(String[] args) {
		SpringApplication.run(WebApplication.class, args);
	}

}
```

4. 启动demo应用和上一节的Eureka Server集群，即可看到服务已注册到Eureka Server中。

![eureka-client.png](https://phaedo.github.io/blog/post-assets/2019-08/eureka-client.png)


## Ref
1. [Netflix-WIKI](https://github.com/Netflix/eureka/wikiM)
