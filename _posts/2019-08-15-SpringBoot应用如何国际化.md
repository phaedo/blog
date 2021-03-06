---
layout: post
title:  "SpringBoot应用如何国际化（一）"
date:   2019-08-15 21:03:36 +0530
categories: Web
tags: [springboot, web]
description: SpringBoot的WEB应用在后台支持多语言的几个思路考察。
---

后台应用的国际化主要分为两个部分，一个是应用部分的多语言支持，另一个是数据库部分的多语言支持。

## 1. 应用层面

应用层面的文本信息主要存在于用户提示指引，错误描述等地方。一般这些文本类信息会被统一抽取到几个类中统一管理，如错误描述的`ErrorEnum.java`、用户指引`GuidanceEnum.java`，避免出现String类型的魔法值。

### 1.1 定义语言环境解析器`LocaleResolver`

为了让我们的应用程序能够确定当前正在使用的语言环境，我们需要在自己的`WebMvcConfig`类中添加一个语言环境解析器`LocaleResolver`。`LocaleResolver`接口能根据会话，`Cookie`，`Accept-Language`头或固定值确定当前语言环境。

在例子中，我们使用了基于Cookie的解析器`CookieLocaleResolver`并设置了一个值为CN的默认语言环境。其他可选的语言环境解析器包括`FixedLocaleResolver`、`AcceptHeaderLocaleResolver`、`SessionLocaleResolver`、`CookieLocaleResolver`。

接下来，需要添加一个拦截器bean，该bean将根据Cookie中的lang值切换到新的区域设置。

```java
/**
 * @description web层相关配置
 **/
@Configuration
public class WebBeanConfiguration {
    /**
     * 声明基于cookie-session的语言解析，默认是中文
     * @return 基于cookie-session的语言解析器
     */
    @Bean
    public LocaleResolver localeResolver() {
        CookieLocaleResolver cookieLocaleResolver = new CookieLocaleResolver();
        cookieLocaleResolver.setDefaultLocale(Locale.CHINA);
        cookieLocaleResolver.setCookieName("lang");
        cookieLocaleResolver.setCookieMaxAge(-1);
        return cookieLocaleResolver;
    }

    /**
     * http请求拦截器，通过分析cookie中的字段设置当前访问的语言
     * @return 语言变更拦截器
     */
    @Bean
    public LocaleChangeInterceptor localeChangeInterceptor() {
        LocaleChangeInterceptor lci = new LocaleChangeInterceptor();
        lci.setParamName("lang");
        return lci;
    }
}
```

将`LocaleChangeInterceptor`添加到应用程序的拦截器注册表中。

```java
/**
 * @description 添加拦截器配置
 **/
@Configuration
public class LocaleInterceptorConfiguration implements WebMvcConfigurer {
    @Autowired
    LocaleChangeInterceptor localeChangeInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(localeChangeInterceptor);
    }
}
```

### 1.2 定义ResourceBundle

默认情况下，SpringBoot应用程序将在src/main/resources文件夹中查找包含国际化键和值的消息文件。 缺省语言环境的文件名称为translate.properties，并且每个语言环境的文件将被命名为translate_XX.properties，其中XX是语言环境代码。 要进行本地化的值的键必须在每个文件中都是相同的，其值应与其对应的语言相适应。

如果某个键在某个请求的语言环境中不存在，则应用程序将回退到默认语言环境值。示例文件如下。

```
translate.properties
greeting=你好，{0}

translate_en_US.properties
greeting=hello!{0}

translate_zh_CN.properties
greeting=你好！{0}
```

### 1.3 定义MessageSource
使用MessageSource来管理国际资源文件，获取对应的文本。注意工程文件中`translate_*.properties`文件放置在`resources/lang`目录下。

```java
@Bean
public ReloadableResourceBundleMessageSource messageSource() {
    ReloadableResourceBundleMessageSource messageSource = new ReloadableResourceBundleMessageSource();
    messageSource.setBasename("classpath:lang/translate");
    messageSource.setDefaultEncoding("UTF-8");
    return messageSource;
}
```

### 1.5 测试效果
通过POSTMAN模拟cookie值分别为`lang=en_US;`和`lang=zh_CN;`可以读取到不同的语言结果。

```java
@RestController
public class TestController {
    @Autowired
    private ReloadableResourceBundleMessageSource messageSource;

    @GetMapping("/testLocale")
    public CommonResult<String> testLocale(@RequestParam("key") String key) {
        String param = "tester";
        String greeting = messageSource
            .getMessage(key, new Object[]{param}, LocaleContextHolder.getLocale());
        return greeting;
    }
}
```

## 2. 数据库层面

见[SpringBoot应用如何国际化（二）](#)

## Ref
1. [what-are-best-practices-for-multi-language-database-design](https://stackoverflow.com/questions/929410/what-are-best-practices-for-multi-language-database-design)
