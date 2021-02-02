---
title: 自定义SpringBoot-Starter
date: 2020-12-15 17:00:38
tags: Spring
---

使用工具IDEA 2020.1.1

1.首先任意创建一个project，使用Spring initializer即可

![](step1.png)

2.接下来正常取名，一直next就可以了

3.项目成功后，删除一些不必要的部分，例如mvn、test文件夹等

<!--more--> 

4.有了一个清爽的项目结构后，创建三个文件夹：configure, properties, service，并且在resources文件夹下创建文件夹META-INF，继续在里面创建spring.factories文件，如下图所示

![](step2.png)

5.在pom.xml引入

```java
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
</dependency>
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-configuration-processor</artifactId>
   <optional>true</optional>
</dependency>
```

6.编写starter所需的properties文件，下面是sms短信服务例子

```java
@ConfigurationProperties(prefix = "sms")
public class SmsProperties {
    private String accessKeyId;
    private String accessKeySecret;
}
```

上面省略setter/getter方法，用到了<font color=#FF0000>  @ConfigurationProperties </font> 注解，规定了两个属性：accessKeyId和accessKeySecret 

7.接下来在service文件夹中创建一个发送信息的服务，相当于一个普通的方法

```java
public void sendSms(args...) {}
```

8.在configure文件夹下创建<font color=blue>  SmsAutoConfiguration </font>，指定<font color=#FF0000>  @EnableConfigurationProperties(SmsProperties.class) </font> 注解

```java
@EnableConfigurationProperties(SmsProperties.class)
public class SmsAutoConfiguration {
    @Bean
    public SmsSendService sendSmsService(SmsProperties smsProperties) {
        return new SmsSendService(smsProperties);
    }
}
```

9.最后在spring.factories中写入

```java
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  com.anzhi.sms.starter.configure.SmsAutoConfiguration
```

10.用maven打包一下，就会存放在本地仓库，然后就可以在别的项目中使用这个starter了！