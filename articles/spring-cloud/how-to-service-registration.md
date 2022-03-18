---
title:  Discover and register your Spring Boot applications in Azure Spring Cloud
description: Discover and register your Spring Boot applications with managed Spring Cloud Service Registry (OSS) in Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
---

# Discover and register your Spring Boot applications

**This article applies to:** ✔️ Basic/Standard tier ❌ Enterprise tier

Service registration and discovery are key requirements for maintaining a list of live app instances to call, and routing and load balancing inbound requests. Configuring each client manually takes time and introduces the possibility of human error. Managed Spring Cloud Service Registry (OSS) in Azure Spring Cloud solves this problem. Once configured, a Service Registry server will control service registration and discovery for your applications. The Service Registry server maintains a registry of live app instances, enables client-side load-balancing, and decouples service providers from clients without relying on DNS.

::: zone pivot="programming-language-csharp"
For information about how to set up service registration for a Steeltoe app, see [Prepare a Java Spring application for deployment in Azure Spring Cloud](how-to-prepare-app-deployment.md).
::: zone-end
::: zone pivot="programming-language-java"

## Register your application using Spring Cloud Service Registry

Before your application can manage service registration and discovery using Spring Cloud Service Registry, you must include the dependency for *spring-cloud-starter-netflix-eureka-client* to your *pom.xml*

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
```

## Update the top level class

Finally, we add an annotation to the top level class of your application

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

    @SpringBootApplication
    @EnableEurekaClient
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

The Spring Cloud Service Registry server endpoint will be injected as an environment variable in your application. Applications will now be able to register themselves with the Service Registry server and discover other dependent applications.

Note that it can take a few minutes for the changes to propagate from the server to all applications.
::: zone-end
