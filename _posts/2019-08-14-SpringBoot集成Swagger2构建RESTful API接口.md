---
layout:     post
title:      SpringBoot集成Swagger2构建RESTful API接口
subtitle:   Swagger2构建RESTful API接口
date:       2019-08-14
author:     Spencer
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - SpringBoot
    - Swagger2
    - Maven
---

# 如何使用SpringBoot集成Swagger2构建RESTful API接口
## 第一步我们创建SpringBoot（2.1.7）的maven项目,pom.xml如下

```xml
    <?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.7.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>tech.wintercloud</groupId>
    <artifactId>swagger-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>swagger-demo</name>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <!-- Exclude the Tomcat dependency-->
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!-- User the Jetty dependency-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jetty</artifactId>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```
## 第二步我们在SpringBoot启动类同级包创建个Swagger2Config类并添加@EnableSwagger2注解，当然也可以使用配置文件形成实现本文不作赘述。

```java
package tech.wintercloud.swagger;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@EnableSwagger2
@SpringBootApplication
public class SwaggerDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(SwaggerDemoApplication.class, args);
    }

}

```
```java
package tech.wintercloud.swagger;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;

/**
 * @author <a href="mailto:zhangnet14@gmail.com">Spencer Chang</a>
 * @date 2019-08-14 22:06
 */
@Configuration
@ComponentScan(basePackages = {"tech.wintercloud.api.controller.v1" })
public class Swagger2Config {
    @Bean
    public Docket createApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .build();
    }

    private ApiInfo apiInfo(){
        return new ApiInfoBuilder()
                .title("Swagger2构建RESTful API接口")
                .version("0.0.1")
                .build();
    }
}
```

## 第三步我们创建个pojo类和controller接口类

```java
package tech.wintercloud.domain.entity;

import com.fasterxml.jackson.annotation.JsonIgnore;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;

import java.io.Serializable;
import java.util.Objects;

/**
 * @author <a href="mailto:zhangnet14@gmail.com">Spencer Chang</a>
 * @date 2019-08-14 21:27
 */
@ApiModel("用户")
public class Users implements Serializable {

    @ApiModelProperty("用户ID")
    private Long userId;
    @ApiModelProperty("用户名")
    private String userName;
    @ApiModelProperty("年龄")
    @JsonIgnore
    private Integer age;

    public Long getUserId() {
        return userId;
    }

    public void setUserId(Long userId) {
        this.userId = userId;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Users users = (Users) o;
        return Objects.equals(userId, users.userId) &&
                Objects.equals(userName, users.userName) &&
                Objects.equals(age, users.age);
    }

    @Override
    public int hashCode() {
        return Objects.hash(userId, userName, age);
    }

    @Override
    public String toString() {
        return "Users{" +
                "userId=" + userId +
                ", userName='" + userName + '\'' +
                ", age=" + age +
                '}';
    }
}
```
```java
package tech.wintercloud.api.controller.v1;

import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import tech.wintercloud.domain.entity.Users;

/**
 * @author <a href="mailto:zhangnet14@gmail.com">Spencer Chang</a>
 * @date 2019-08-14 21:38
 */

@Api(tags = {"用户查询接口"})
@RestController
public class UsersController {

    @ApiOperation(value = "通过传入的用户ID查询用户信息")
    @GetMapping(value = "/v1/query/{userId}")
    public Users query(@PathVariable("userId") Long userId){
        Users users = new Users();
        users.setUserId(userId);
        users.setUserName("Demo");
        return users;
    }
}
```
## 另外我修改了配置文件application.yml，如下
```yml
server:
  port: 9000
  servlet:
    context-path: /

```
## 接下来我们启动SpringBoot工程，访问<http://127.0.0.1:9000/swagger-ui.html>
![](https://spencerzhang.github.io/resource/swagger2-1.png)
## 选择我们开发的接口并Try it out测试一下
![](https://spencerzhang.github.io/resource/swagger2-2.png)
## 输入必输参数并点击Execute
![](https://spencerzhang.github.io/resource/swagger2-3.png)
## 可以看到发起的请求Request URL是什么以及请求结果
![](https://spencerzhang.github.io/resource/swagger2-4.png)

## 到这里简单使用SpringBoot集成Swagger2构建RESTful API接口就集成完毕！
--EOF--
