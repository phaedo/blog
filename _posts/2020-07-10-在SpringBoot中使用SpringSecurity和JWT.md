---
layout: post
title:  "2020-07-10-在SpringBoot中使用SpringSecurity和JWT"
date:   2020-07-10 21:03:36 +0530
categories: Web
tags: [java]
description: 介绍在SpringBoot中使用SpringSecurity的示例程序
---


1. https://start.spring.io/


### Spring Security

### JWT

JWT是Json Web Token的缩写，规定在网络中安全地传输信息的传输标准。信息传输使用JSON对象承载，信息安全主要由数据加密算法保障，可选对称加密（HMAC）或非对称加密（RSA，ECDSA）。相应地还存在着SWT（Simple Web Token）和SAML（Security Assertion Markup Language Tokens）的传输标准。
JWT着重保障的是信息传输的不可篡改，信息主体只通过Base64编码而没有加密，因此敏感信息不适合通过JWT方式传输。

| 标准 | JWT | SWT | SAML |
| -- | -- | -- | -- |
| 格式 | JSOM | 文本 | XML |
| 加密方式 | 支持对称/非对称加密 | 支持对称加密 | 支持对称/非对称加密 |

JWT由三部分组成，分别是Header（头部），Payload（荷载），Signature（签名），各个部分由点号分割（**.**），例如`Header.Payload.Signature`。

### Ref

- [JWT.IO](https://jwt.io/introduction/)
