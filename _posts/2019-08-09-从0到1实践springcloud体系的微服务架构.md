---
layout: post
title:  "从0到1实践SpringCloud体系的微服务架构（一）"
date:   2019-08-09 21:03:36 +0530
categories: Web
tags: [springboot, web]
description: 实践SpringCloud体系的微服务架构
---

微服务是面向人的敏捷开发模式经过深入实践后的产物。按照《人月神话》的说法，系统开发速度和资源投入的关系是存在饱和度的，并不是投入足够的资源，就能有对应的线性产出。当一个系统维护的人过多时，反而会导致开发和运维的成本上升。

另外当一个系统足够庞大时，内部错综复杂的依赖和调用关系，导致了学习和维护成本上升。因此如果把复杂的系统分解为一个个足够内聚足够微小的（代码量在10w左右）系统，对外暴露其核心业务接口，通过一系列不同业务系统的服务接口编排，表达一个完整的业务语义，实现统一的对外功能。这样使得复杂系统间的依赖和调用是可视，可治理的。同时每个微服务内部的开发和维护成本也变低。

因此一个新的系统架构伴随着研发协作模式诞生了。

微服务架构的核心就是服务治理，在中国常常有两个被提起的服务治理框架，Dubbo和SpringCloud，它们都是开源的。

Dubbo是基于NettyNIO的TCP协议通信的，客户端Bean由spring容器管理，因此服务调用的过程和使用本地的service方法一样，访问效率也很高。目前只支持基于Spring的Web系统，因此对异构的微服务不够友好。

SpringCloud是基于HTTP协议通信的，鼓励用RestAPI的风格去定义接口，使得它天然地支持系统内部异构。它使用一套组件实现了服务注册，负载均衡，智能路由等能力。但是调用过程的效率不高。

对比于Dubbo，SpringCloud的服务调用效率，参考网上的一篇[实验](https://mp.weixin.qq.com/s/omVAEzQTcV5o5AGsSU_u7Q)，有如下结论，无论是数据量大或小，Dubbo的性能都远高于SpringCloud（高出约约2-3倍）。但是由于Dubbo在2017年前后断断续续已经没有更新，所以更加活跃SpringCloud体系得到广泛使用。

![dubbo-vs-springcloud-t](https://phaedo.github.io/blog/post-assets/2019-08/dubbo-vs-springcloud-t.png)  
![dubbo-vs-springcloud-f](https://phaedo.github.io/blog/post-assets/2019-08/dubbo-vs-springcloud-f.png)  

一般地，SpringCloud的服务治理的核心组件包括以下（不断补充当中）
1. Eureka 服务发现
2. Ribbon 负载均衡
3. Feign 服务调用
4. Hystrix 服务隔离熔断降级
5. Zuul 服务网关
6. Config 配置隔离

![springcloud](https://phaedo.github.io/blog/post-assets/2019-08/springcloud.png)
