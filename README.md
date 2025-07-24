# Spring Boot Microservice with Spring Cloud Eureka Demo

这是一个简单的Spring Boot微服务，集成了Spring Cloud Eureka进行服务发现。

## 项目结构

本项目包含两个主要部分：

1.  **`eureka-server`**: Spring Cloud Eureka 服务注册中心。所有微服务都会向它注册，并从它那里发现其他服务。
2.  **`springboot-demo`**: 一个Spring Boot微服务，作为Eureka客户端，它会注册到 `eureka-server` 并提供一个简单的REST API。

## 环境要求

*   JDK 17
*   Maven (通过项目自带的 `mvnw` 脚本使用)

## 设置与运行

请按照以下步骤启动整个系统：

### 1. 启动 Eureka Server

首先，您需要启动Eureka Server。如果您还没有 `eureka-server` 项目，请按照以下步骤创建：

1.  **创建 `eureka-server` 目录并进入：**
    ```bash
    cd /mnt/c/demos/
    mkdir eureka-server
    cd eureka-server
    ```
2.  **下载 Eureka Server 项目骨架：**
    ```bash
    curl https://start.spring.io/starter.zip -o eureka-server.zip -d dependencies=web,cloud-eureka-server -d language=java -d type=maven-project -d javaVersion=17 -d groupId=com.example -d artifactId=eureka-server -d name=eureka-server -d description=EurekaServerDemo
    unzip eureka-server.zip -d .
    ```
3.  **配置 Eureka Server：**
    *   在 `eureka-server/src/main/java/com/example/eurekaserver/EurekaServerApplication.java` 文件中，添加 `@EnableEurekaServer` 注解：
        ```java
        import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

        @SpringBootApplication
        @EnableEurekaServer
        public class EurekaServerApplication {
            // ...
        }
        ```
    *   在 `eureka-server/src/main/resources/application.properties` 文件中，添加以下配置：
        ```properties
        server.port=8761
        eureka.client.register-with-eureka=false
        eureka.client.fetch-registry=false
        eureka.instance.hostname=localhost
        ```
4.  **构建并运行 Eureka Server：**
    ```bash
    cd /mnt/c/demos/eureka-server
    ./mvnw clean install
    ./mvnw spring-boot:run
    ```
    Eureka Server 启动后，您可以通过浏览器访问 `http://localhost:8761/` 查看其仪表盘。

### 2. 启动 Spring Boot 微服务 (Eureka Client)

接下来，启动 `springboot-demo` 微服务。

1.  **更新 `pom.xml` (已完成)：**
    我们已经将Spring Cloud依赖和Spring Boot版本调整为兼容版本。
    *   Spring Boot 版本: `3.3.0`
    *   Spring Cloud 版本: `2023.0.3` (与Spring Boot 3.3.x 兼容)
    *   添加 `spring-cloud-starter-netflix-eureka-client` 依赖。

2.  **启用 Eureka Client (已完成)：**
    在 `springboot-demo/src/main/java/com/example/springboot_demo/SpringbootDemoApplication.java` 文件中，添加 `@EnableDiscoveryClient` 注解：
    ```java
    import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

    @SpringBootApplication
    @EnableDiscoveryClient
    public class SpringbootDemoApplication {
        // ...
    }
    ```

3.  **配置 `application.properties` (已完成)：**
    在 `springboot-demo/src/main/resources/application.properties` 文件中，添加以下配置：
    ```properties
    spring.application.name=springboot-demo
    eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/
    ```

4.  **构建并运行 Spring Boot 微服务：**
    ```bash
    cd /mnt/c/demos/springboot-demo
    ./mvnw clean install
    ./mvnw spring-boot:run
    ```

## 验证

1.  **检查 Eureka Server 仪表盘：**
    访问 `http://localhost:8761/`。在 "Instances currently registered with Eureka" 部分，您应该能看到 `SPRINGBOOT-DEMO` 服务已注册。

2.  **访问微服务端点：**
    访问 `http://localhost:8080/`。您应该会看到 "Hello, Spring Boot Microservice!" 的响应。

至此，您已经成功运行了一个集成了Spring Cloud Eureka的服务发现功能的Spring Boot微服务。
