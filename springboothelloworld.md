# Spring Boot 的 Hello World入门 

**创建 Maven 项目**

打开命令行工具,执行以下命令创建一个 Maven 项目:

```shell
mvn archetype:generate -DgroupId=com.example -DartifactId=hello-world -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

该命令将创建一个名为 `hello-world` 的 Maven 项目,在当前目录下。

**添加 Spring Boot 依赖**

打开 `pom.xml` 文件,在 `<dependencies>` 标签内添加 Spring Boot 依赖:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>2.7.11</version>
</dependency>
```

**添加 Spring Boot 插件**

在 `pom.xml` 的 `<build><plugins>` 标签内添加 Spring Boot 插件:

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>2.7.11</version>
</plugin>
```

**创建 REST 控制器**

在 `src/main/java/com/example/app` 目录下创建一个 REST 控制器类,例如 `HelloController.java`:

```java
package com.example.app;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello, World!";
    }
}
```

**运行应用程序**

在项目根目录下执行以下命令运行应用程序:

```shell
mvn spring-boot:run
```

**测试**

在浏览器中访问 `http://localhost:8080/hello`,你应该可以看到输出 `Hello, World!`
