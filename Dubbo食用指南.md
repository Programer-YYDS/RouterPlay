# Apache Dubbo食用指南

**Apache Dubbo 是一款高性能、轻量级的开源 Java 服务框架**

![image-20210216092037742](https://kauizhaotan.oss-cn-shanghai.aliyuncs.com/img/image-20210216092037742.png)

## 具体操作

### 1.打开Zookeeper

简介: Zookeeper是注册中心 对应上面的 Registy

步骤:运行zkServer.cmd文件(目录:D:\Environment\zookeeper-3.4.14\zookeeper-3.4.14\bin)

需要使用 3.4.14版本, 以上版本会报错, 未知原因......

成功截图:

![image-20210216125441659](https://kauizhaotan.oss-cn-shanghai.aliyuncs.com/img/image-20210216125441659.png)

### 2.下载运行 dubbo-admin

何为Dubbo-admin : Dubbo的管理面板, 类似于 swagger, 界面化操作.

步骤:

1. 从[dubbo-admin](https://github.com/apache/dubbo-admin)下载master分支下的压缩文件

2. 更改E:\GitRepositories\dubbo-master\dubbo-admin-master\dubbo-admin-master\dubbo-admin\src\main\resources下的application配置文件

   ```properties
    #网页端口
   server.port=7001    
   spring.velocity.cache=false
   spring.velocity.charset=UTF-8
   spring.velocity.layout-url=/templates/default.vm
   spring.messages.fallback-to-system-locale=false
   spring.messages.basename=i18n/message
   #账号:root 密码: root
   spring.root.password=root 
   spring.guest.password=guest
   #注册中心地址
   dubbo.registry.address=zookeeper://127.0.0.1:2181
   ```

3. 项目运行  ```mvn clean package -Dmaven.test.skip=true```, target目录下生成 *dubbo-admin-0.0.1-SNAPSHOT.jar*

4. 在Zookeeper打开的前提下, 运行 ```java -jar dubbo-admin-0.0.1-SNAPSHOT.jar```

5. 成功截图

6. 打开 http://localhost:7001

![image-20210216133545877](http://qiniu.imtyf.icu/typora_img/image-20210216133545877.png)

![image-20210216133714124](http://qiniu.imtyf.icu/typora_img/image-20210216133714124.png)

### 3.编写代码测试

#### Provider(提供者)模块代码

1. 导入pom依赖

   ```xml
    <!--导入Zookeeper和Dubbo-->
           <dependency>
               <groupId>org.apache.dubbo</groupId>
               <artifactId>dubbo-spring-boot-starter</artifactId>
               <version>2.7.8</version>
           </dependency>
           <!-- https://mvnrepository.com/artifact/com.101tec/zkclient -->
           <dependency>
               <groupId>com.101tec</groupId>
               <artifactId>zkclient</artifactId>
               <version>0.10</version>
           </dependency>
           <!-- https://mvnrepository.com/artifact/org.apache.zookeeper/zookeeper -->
           <dependency>
               <groupId>org.apache.zookeeper</groupId>
               <artifactId>zookeeper</artifactId>
               <version>3.4.14</version>
               <exclusions>
                   <exclusion>
                       <artifactId>slf4j-log4j12</artifactId>
                       <groupId>org.slf4j</groupId>
                   </exclusion>
               </exclusions>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-framework</artifactId>
               <version>2.12.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-recipes</artifactId>
               <version>2.12.0</version>
           </dependency>
   
   ```

2. 编写Service及其实现

   ```java
   package com.tanyongfeng.service;
   
   public interface TicketService {
       public String getTickets();
   }
   
   ```

   ```java
   package com.tanyongfeng.service;
   
   import org.apache.dubbo.config.annotation.DubboService;
   import org.springframework.stereotype.Component;
   
   @DubboService
   @Component
   public class TicketServiceImpl implements TicketService{
       @Override
       public String getTickets() {
           return "从注册中心拿到了一张票";
       }
   }
   
   ```

3. 编写application.properties

   ```properties
   server.port=8001
   
   #服务应用名字
   dubbo.application.name=provider-server
   #注册中心地址
   dubbo.registry.address=zookeeper://127.0.0.1:2181
   #哪些服务要被注册
   dubbo.scan.base-packages=com.tanyongfeng
   ```

4. 启动 转到dubboj-admin面板 发现服务

   ![image-20210216140929606](http://qiniu.imtyf.icu/typora_img/image-20210216140929606.png)

5. 项目目录结构:

   ![image-20210216141142153](http://qiniu.imtyf.icu/typora_img/image-20210216141142153.png)

#### Consumer(消费者)模块

1. 导入Pom依赖

   ```xml
    <!--导入Zookeeper和Dubbo-->
           <dependency>
               <groupId>org.apache.dubbo</groupId>
               <artifactId>dubbo-spring-boot-starter</artifactId>
               <version>2.7.8</version>
           </dependency>
           <!-- https://mvnrepository.com/artifact/com.101tec/zkclient -->
           <dependency>
               <groupId>com.101tec</groupId>
               <artifactId>zkclient</artifactId>
               <version>0.10</version>
           </dependency>
           <!-- https://mvnrepository.com/artifact/org.apache.zookeeper/zookeeper -->
           <dependency>
               <groupId>org.apache.zookeeper</groupId>
               <artifactId>zookeeper</artifactId>
               <version>3.4.14</version>
               <exclusions>
                   <exclusion>
                       <artifactId>slf4j-log4j12</artifactId>
                       <groupId>org.slf4j</groupId>
                   </exclusion>
               </exclusions>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-framework</artifactId>
               <version>2.12.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.curator</groupId>
               <artifactId>curator-recipes</artifactId>
               <version>2.12.0</version>
           </dependency>
   ```

2. 编写Service及其实现

   ```java
   package com.tanyongfeng.service;
   
   import org.springframework.stereotype.Service;
   
   //与provider保持一致
   @Service
   public interface TicketService {
   
       public String getTickets();
   }
   ```

   ```java
   package com.tanyongfeng.service;
   
   import org.apache.dubbo.config.annotation.DubboReference; 
   import org.springframework.stereotype.Service; //注意这里的Service是spring中的
   
   @Service
   public class UserService {
       //拿到Provider提供的票
       @DubboReference //引入
       TicketService tickService;
   
       public void buyTickets(){
           tickService.getTickets();
       }
   }
   ```

3. 编写 application.properties

   ```properties
   server.port=8002
   
   #消费者暴露自己的名字
   dubbo.application.name=consumer-server
   
   #注册中心地址
   dubbo.registry.address=zookeeper://127.0
   ```

4. 启动consumer启动类

5. 编写测试类 在dubbo管理面板发现消费者

**就此,Dubbo操作基本告一段落. 开始 Spring Cloud历程.:happy:**