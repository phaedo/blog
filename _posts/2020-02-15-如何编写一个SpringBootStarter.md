---
layout: post
title:  "2020-02-15-如何编写一个SpringbootStarter"
date:   2020-02-15 21:03:36 +0530
categories: Web
tags: [springboot]
description: Springboot核心理念是约定大于配置，据此Springboot的开发团队提供一种定义开箱即用组件的能力，称为starter
---

Springboot核心理念是约定大于配置，据此Springboot的开发团队提供一种定义开箱即用组件的能力，称为starter。例如spring-boot-starter-logging, spring-boot-starter-test等。这些starter组件具备自动配置的能力，组件的使用者只需要引入该starter组件，不需要过多地关心组件的启动和回收，从而可以把更多精力聚焦到组件的业务实践上。

### Starter的启动原理

依赖Springboot的上下文，我们也可以自定义一个Springboot starter组件。开箱即用组件的核心点在于Springboot提供了一套类的自动配置流程。

1. 当Springboot应用启动时，程序会扫描`resources/META-INF/spring.factories`文件。`spring.factories`文件片段如下，它主要记录了各个组件的AutoConfig类所在位置。
    ```
    # Auto Configure
    org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
    org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
    org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration\
    ```

2. 根据`spring.factories`文件的EnableAutoConfiguration清单，加载AutoConfig类。

3. 按条件初始化AutoConfig类中的Bean，加载进入SpringContext容器中。

### 自定义一个Starter

假设Starter中需要实现的业务逻辑如下，其中`userName`是按使用者需求可配置的选型。

    ```java
    public class ExampleService {
        private String userName;

        public ExampleService(String userName) {
          this.userName = userName;
        }

        public void welcome() {
             System.out.println("Hello world! " + userName);
        }
    }
    ```

1. 引入依赖包

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-autoconfigure</artifactId>
        </dependency>
    </dependencies>
    ```

2. 编写配置读取类

    ```java
    @Data
    @ConfigurationProperties("example.setting")
    public ExampleProperties {
        private Boolean enabled;

        private String userName;
    }
    ```

3. 编写自动配置类

    ```java
    @Configuration
    @ConditionalOnClass(ExampleService.class)
    @EnableConfigurationProperties(ExampleProperties.class)
    public class ExampleAutoConfiguration {

        @Autowired
        private ExampleProperties exampleProperties;

        @Bean
        @ConditionalOnMissingBean
        @ConditionalOnProperty(prefix = "example.setting", value = "enabled", havingValue = "true")
        public ExampleService ExampleService() {
            return new ExampleService(exampleProperties.getUserName());
        }
    }
    ```

    其中自动配置类使用到了几个autoconfigure的条件注解，它们的作用如下
    ```
    @ConditionalOnBean:当容器中有指定的Bean的条件下  
    @ConditionalOnClass：当类路径下有指定的类的条件下  
    @ConditionalOnExpression:基于SpEL表达式作为判断条件  
    @ConditionalOnJava:基于JVM版本作为判断条件  
    @ConditionalOnJndi:在JNDI存在的条件下查找指定的位置  
    @ConditionalOnMissingBean:当容器中没有指定Bean的情况下  
    @ConditionalOnMissingClass:当类路径下没有指定的类的条件下  
    @ConditionalOnNotWebApplication:当前项目不是Web项目的条件下  
    @ConditionalOnProperty:指定的属性是否有指定的值  
    @ConditionalOnResource:类路径下是否有指定的资源  
    @ConditionalOnSingleCandidate:当指定的Bean在容器中只有一个，或者在有多个Bean的情况下，用来指定首选的Bean
    @ConditionalOnWebApplication:当前项目是Web项目的条件下  
    ```


4. 在`resources/META-INF/`文件夹下创建一个`spring.factories`文件，内容如下

    ```
    org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.example.ExampleAutoConfiguration
    ```

这样一个自定义的Springboot Starter就完成了。
