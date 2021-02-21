# Spring Cloud Netflix

解决方案:

1. Spring Cloud Netflix
2. Apache Dubbo Zookeeper
3. Spring Cloud Alibaba 

解决方案组成:

1. API
2. HTTP, PRC
3. 服务注册与发现
4.  负载均衡

## Eureka

**SpringCloud 自带的服务注册中心** 基于REST的服务, 实现负载均衡和中间件故障转移的目的 

两个组件 : 1. **Eureka Server** 2. **Eureka Client**

在应用启动后，将会向Eureka Server发送心跳,默认周期为30秒，如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，Eureka Server将会从服务注册表中把这个服务节点移除(默认90秒)。

Eureka Server之间通过复制的方式完成数据的同步，Eureka还提供了客户端缓存机制，即使所有的Eureka Server都挂掉，客户端依然可以利用缓存中的信息消费其他服务的API。综上，Eureka通过心跳检查、[客户端缓存](https://baike.baidu.com/item/客户端缓存/10237000)等机制，确保了系统的高可用性、灵活性和可伸缩性。

### 2.集成Eureka

*集群和分布式*:

1. *分布式：一个业务分拆多个子业务，部署在不同的服务器上*
2. *同一个业务，分别部署在不同的服务器上*

总结: 所以分布式的每一个节点，完成的是不同的业务，一个节点挂了，那么这个业务功能就无法访问了，甚至可能会影响到其他业务。而集群是一个比较有组织的架构，正因为有组织性，一个服务节点挂了，其他服务节点可以顶上来，从而保证了服务的健壮性。

所以说，集群可以理解为：==**你中有我，我中有你，手拉手肩并肩，一起保证服务的健壮性**。==

#### (一) 注册中心集群

1. 导入依赖

   ```xml
       <dependencies>
           <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-eureka-server -->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-eureka-server</artifactId>
               <version>1.4.6.RELEASE</version>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-devtools</artifactId>
               <version>2.4.2</version>
           </dependency>
       </dependencies>
   ```

2. 编写application配置文件

   ```yaml
   server:
     port: 701
   #Eureka配置
   eureka:
     instance:
       hostname: eureka701.com  #Eureka服务端的实例名称
     client:
       fetch-registry: false #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
       register-with-eureka: false #是否将自己这个服务注册到EurekaServer中
       service-url: #监控页面
       #集群配置 其他eureka注册中心的地址
         defaultZone: http://eureka702.com:702/eureka/
         #单机配置
   #      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
   ```

3. Application类加注解

   ```java
   @SpringBootApplication
   @EnableEurekaServer // 开启eureka
   public class EurekaServer_701 {
       public static void main(String[] args) {
           SpringApplication.run(EurekaServer_701.class,args);
       }
   }
   ```

4. 测试

   ![image-20210221222953281](https://kauizhaotan.oss-cn-shanghai.aliyuncs.com/img/image-20210221222953281.png)



**更加详细的介绍**:[eureka集群配置](http://blog.itpub.net/31558358/viewspace-2375380/)

### (二)服务提供者注册

1. 导入依赖

   ```xml
    		 <!--Eureka-->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-eureka</artifactId>
               <version>1.4.6.RELEASE</version>
           </dependency>
           <!--完善监控信息-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-actuator</artifactId>
           </dependency>
   ```

2. 编写application配置

   ```yaml
   server:
     port: 8001
   mybatis-plus:
     type-aliases-package: com.tyf.springcloud.pojo
   
   spring:
     application:
       name: springcloud-provider-User
     datasource:
       type: com.alibaba.druid.pool.DruidDataSource
       driver-class-name: com.mysql.cj.jdbc.Driver
       url: jdbc:mysql://49.234.79.216:3306/myb atis-plus
       username: mybatis-plus
       password: root
   
   #Eureka的配置,服务注册到哪里
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:701/eureka/
     instance:
       instance-id: springcloud-provider-dept8001
   info: //定义这个服务的具体的信息
     app.name: tyf_springcloud
     company.name: tanyongfeng.xyz
   
   ```

3. Application添加注解

   ```java
   @SpringBootApplication
   @EnableEurekaClient //开启eureka
   @EnableDiscoveryClient //服务发现
   public class UserProvider_8001 {
       public static void main(String[] args) {
           SpringApplication.run(UserProvider_8001.class);
       }
   }
   ```

4. 编写其他业务层

![image-20210221223933492](https://kauizhaotan.oss-cn-shanghai.aliyuncs.com/img/image-20210221223933492.png)

## Ribbon

### 1.概念

**Springcloud NetFlix Ribbo是基于NetFlix Ribbon实现的一套客户端负载均衡的工具.**

**主要功能**: 提供客户端的软件负载均衡算法. Ribbon是客户端负载均衡，具体表现为客户端从注册中心拿到服务的所有实例，然后以负载均衡方式去调用服务，默认以轮询的方式去调用服务实例.

***负载均衡方式***

- *集中式LB: 在服务方和提供方之间使用独立的LB设施, 如Nginx : 反向代理服务器, 由该设施负责把访问请求通过某种策略转发至服务的提供方.*
- *进程式LB: 将逻辑集中在消费方, 消费方从服务注册中心获知有哪些地址可用, 然后自己再从这些地址中选出一个合适的服务器*

==Ribbo属于后者!==

### 2.集成Ribbon

1. 导入依赖

   ```xml
           <!--Ribbon-->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
               <version>2.2.6.RELEASE</version>
           </dependency>
           <!--eureka-->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-eureka</artifactId>
               <version>1.4.6.RELEASE</version>
           </dependency>
   ```

2. 编写application配置

   ```yaml
   eureka:
     client:
       register-with-eureka: false #是否向eureka注册中心注册自己
       service-url:
         defaultZone: http://eurake702.com:702/eureka/,http://eurake701.com:701/eureka/
   
   ```

3. 在启动类上添加注解

   ```java
   @SpringBootApplication
   @EnableEurekaClient //开启服务发现
   public class UserConsumer {
       public static void main(String[] args) {
           SpringApplication.run(UserConsumer.class);
       }
   }
   ```

4. 编写Configuration

   ```java
   @Configuration
   public class ConfigBean {
   
       @Bean
       @LoadBalanced //负载均衡
       public RestTemplate getRestTemplate(){
           return new RestTemplate();
       }
   }
   ```

   

