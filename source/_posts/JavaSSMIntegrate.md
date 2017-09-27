---
title: 构建SSM前后分离项目实例
date: 2017-09-27 10:40:00
categories: Java
tags:
- Spring
- SPringMVC
- MyBatis
- Ajax
---

![440H](JavaSSMIntegrate\440H.jpg)

# 前言

本章选用Spring+SpringMVC+MyBatis开发后台接口，主要目的是实现前后端分离。

前后端分离是最大好处是前后端可以实现较好的人员分工和并行开发。双方只需要约定数据接口，而不必在代码层面有任何的耦合。传统的JSP或者后端模板方式都存在着较为严重的前后端耦合，前后端人员不仅需要掌握自身所需知识，还需要对对方的领域有一定了解；开发时进度会因为双方的大量沟通而被拖慢，且项目存在缺陷时难以分清是前后端哪一方的责任，调试困难。目前较为流行的解决方案是后端提供纯数据接口，前端使用MVC/MVVM等框架实现数据绑定、界面路由等功能，且未来的发展趋势是后端负责的任务将逐步减少，进入“大前端”时代。

[源码下载！！！](https://github.com/huyida/SSM.git)

<!--MORE-->

# 后端

## 项目结构

首先我们打开IED，我这里用的是eclipse（你们应该也是用的这个，对吗？），创建一个动态web项目，建立好相应的**目录结构**（重点！）

![1](JavaSSMIntegrate\1.jpg)

各个目录的作用。 这个目录结构同时也遵循maven的目录规范~

| 文件名       | 作用                                       |
| --------- | ---------------------------------------- |
| src       | 根目录，没什么好说的，下面有main和test。                 |
| main      | 主要目录，可以放java代码和一些资源文件。                   |
| java      | 存放我们的java代码，这个文件夹要使用Build Path -> Use as Source Folder，这样看包结构会方便很多，新建的包就相当于在这里新建文件夹咯。 |
| resources | 存放资源文件，譬如各种的spring，mybatis，log配置文件。      |
| mapper    | 存放dao中每个方法对应的sql，在这里配置，无需写daoImpl。       |
| spring    | 这里当然是存放spring相关的配置文件，有dao service web三层。 |
| sql       | 其实这个可以没有，但是为了项目完整性还是加上吧。                 |
| webapp    | 这个貌似是最熟悉的目录了，用来存放我们前端的静态资源，如jsp js css。  |
| resources | 这里的资源是指项目的静态资源，如js css images等。          |
| WEB-INF   | 很重要的一个目录，外部浏览器无法访问，只有羡慕内部才能访问，可以把jsp放在这里，另外就是web.xml了。你可能有疑问了，为什么上面java中的resources里面的配置文件不妨在这里，那么是不是会被外部窃取到？你想太多了，部署时候基本上只有webapp里的会直接输出到根目录，其他都会放入WEB-INF里面，项目内部依然可以使用classpath:XXX来访问，好像IDE里可以设置部署输出目录，这里扯远了~ |
| test      | 这里是测试分支。                                 |
| java      | 测试java代码，应遵循包名相同的原则，这个文件夹同样要使用Build Path -> Use as Source Folder，这样看包结构会方便很多。 |
| resources | 没什么好说的，好像也很少用到，但这个是maven的规范。             |

------

我先新建好几个必要的**包**，并为大家讲解一下每个包的作用，顺便理清一下后台的思路~

![2](JavaSSMIntegrate\2.jpg)

| 包名          | 名称        | 作用                                       |
| ----------- | --------- | ---------------------------------------- |
| dao         | 数据访问层（接口） | 与数据打交道，可以是数据库操作，也可以是文件读写操作，甚至是redis缓存操作，总之与数据操作有关的都放在这里，也有人叫做dal或者数据持久层都差不多意思。为什么没有daoImpl，因为我们用的是mybatis，所以可以直接在配置文件中实现接口的每个方法。 |
| entity      | 实体类       | 一般与数据库的表相对应，封装dao层取出来的数据为一个对象，也就是我们常说的pojo，一般只在dao层与service层之间传输。 |
| dto         | 数据传输层     | 刚学框架的人可能不明白这个有什么用，其实就是用于service层与web层之间传输，为什么不直接用entity（pojo）？其实在实际开发中发现，很多时间一个entity并不能满足我们的业务需求，可能呈现给用户的信息十分之多，这时候就有了dto，也相当于vo，记住一定不要把这个混杂在entity里面，答应我好吗？ |
| service     | 业务逻辑（接口）  | 写我们的业务逻辑，也有人叫bll，在设计业务接口时候应该站在“使用者”的角度。额，不要问我为什么这里没显示！IDE调皮我也拿它没办法~ |
| serviceImpl | 业务逻辑（实现）  | 实现我们业务接口，一般事务控制是写在这里，没什么好说的。             |
| controller  | 控制器       | springmvc就是在这里发挥作用的，一般人叫做controller控制器，相当于struts中的action。 |

------

还有最后一步基础工作，导入我们相应的jar包，我使用的是maven来管理我们的jar，所以只需要在`pom.xml`中加入相应的依赖就好了，如果不使用maven的可以自己去官网下载相应的jar，放到项目WEB-INF/lib目录下。关于maven的学习大家可以看[慕课网的视频教程](http://www.imooc.com/learn/443)，这里就不展开了。我把项目用到的jar都写在下面，版本都不是最新的，大家有经验的话可以自己调整版本号。

## Maven

### pom.xml

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.huyd</groupId>
    <artifactId>SpringMVCRestfulRedis</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>SpringMVCRestfulRedis Maven Webapp</name>
    <url>http://maven.apache.org</url>
    <dependencies>
        <!-- 单元测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
        </dependency>

        <!-- 1.日志 -->
        <!-- 实现slf4j接口并整合 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.1.1</version>
        </dependency>

        <!-- 2.数据库 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.37</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
        </dependency>

        <!-- DAO: MyBatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.3.0</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.2.3</version>
        </dependency>

        <!-- 3.Servlet web -->
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.5.4</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
        </dependency>

        <!-- 4.Spring -->
        <!-- 1)Spring核心 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>4.1.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>4.1.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.1.7.RELEASE</version>
        </dependency>
        <!-- 2)Spring DAO层 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>4.1.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>4.1.7.RELEASE</version>
        </dependency>
        <!-- 3)Spring web -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>4.1.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.1.7.RELEASE</version>
        </dependency>
        <!-- 4)Spring test -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>4.1.7.RELEASE</version>
        </dependency>

        <!-- redis客户端:Jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.7.3</version>
        </dependency>
        <dependency>
            <groupId>com.dyuproject.protostuff</groupId>
            <artifactId>protostuff-core</artifactId>
            <version>1.0.8</version>
        </dependency>
        <dependency>
            <groupId>com.dyuproject.protostuff</groupId>
            <artifactId>protostuff-runtime</artifactId>
            <version>1.0.8</version>
        </dependency>

        <!-- Map工具类 -->
        <dependency>
            <groupId>commons-collections</groupId>
            <artifactId>commons-collections</artifactId>
            <version>3.2</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.31</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
        </dependency>

    </dependencies>
    <build>
        <finalName>SpringMVCRestfulRedis</finalName>
    </build>
</project>

```

------

下面真的要开始进行编码工作了，坚持到这里辛苦大家了~

## 配置文件

第一步：我们先在`spring`文件夹里新建`spring-dao.xml`文件，因为spring的配置太多，我们这里分三层，分别是dao service web。

1. 读入数据库连接相关参数（可选）
2. 配置数据连接池
3. 配置连接属性，可以不读配置项文件直接在这里写死
4. 配置c3p0，只配了几个常用的
5. 配置SqlSessionFactory对象（mybatis）
6. 扫描dao层接口，动态实现dao接口，也就是说不需要daoImpl，sql和参数都写在xml文件上

### spring-dao.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 配置整合mybatis过程 -->
    <!-- 1.配置数据库相关参数properties的属性：${url} -->
    <context:property-placeholder location="classpath:jdbc.properties" />

    <!-- 2.数据库连接池 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!-- 配置连接池属性 -->
        <property name="driverClass" value="${jdbc.driver}" />
        <property name="jdbcUrl" value="${jdbc.url}" />
        <property name="user" value="${jdbc.username}" />
        <property name="password" value="${jdbc.password}" />

        <!-- c3p0连接池的私有属性 -->
        <property name="maxPoolSize" value="30" />
        <property name="minPoolSize" value="10" />
        <!-- 关闭连接后不自动commit -->
        <property name="autoCommitOnClose" value="false" />
        <!-- 获取连接超时时间 -->
        <property name="checkoutTimeout" value="10000" />
        <!-- 当获取连接失败重试次数 -->
        <property name="acquireRetryAttempts" value="2" />
    </bean>

    <!-- 3.配置SqlSessionFactory对象 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource" />
        <!-- 配置MyBaties全局配置文件:mybatis-config.xml -->
        <property name="configLocation" value="classpath:mybatis-config.xml" />
        <!-- 扫描entity包 使用别名 -->
        <property name="typeAliasesPackage" value="com.huyd.model" />
        <!-- 扫描sql配置文件:mapper需要的xml文件 -->
        <property name="mapperLocations" value="classpath:mapper/*.xml" />
    </bean>

    <!-- 4.配置扫描Dao接口包，动态实现Dao接口，注入到spring容器中 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 注入sqlSessionFactory -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
        <!-- 给出需要扫描Dao接口包 -->
        <property name="basePackage" value="com.huyd.dao" />
    </bean>

</beans>
```

因为数据库配置相关参数是读取配置文件，所以在`resources`文件夹里新建一个`jdbc.properties`文件，存放我们4个最常见的数据库连接属性，这是我本地的，大家记得修改呀~还有喜欢传到github上“大头虾们”记得删掉密码，不然别人就很容易得到你服务器的数据库配置信息，然后干一些羞羞的事情，你懂的！！

### jdbc.properties

```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssm?useUnicode=true&characterEncoding=utf8
jdbc.username=root
jdbc.password=
```

**友情提示**：配置文件中的jdbc.username，如果写成username，可能会与系统环境中的username变量冲突，所以到时候真正连接数据库的时候，用户名就被替换成系统中的用户名（有得可能是administrator），那肯定是连接不成功的，这里有个小坑，我被坑了一晚上！！

因为这里用到了mybatis，所以需要配置mybatis核心文件，在`recources`文件夹里新建`mybatis-config.xml`文件。

1. 使用自增主键
2. 使用列别名
3. 开启驼峰命名转换 create_time -> createTime

### mybatis-config.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 配置全局属性 -->
    <settings>
        <!-- 使用jdbc的getGeneratedKeys获取数据库自增主键值 -->
        <setting name="useGeneratedKeys" value="true"/>

        <!-- 使用列别名替换列名 默认:true -->
        <setting name="useColumnLabel" value="true"/>

        <!-- 开启驼峰命名转换:Table{create_time} -> Entity{createTime} -->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
</configuration>
```

第二步：刚弄好dao层，接下来到service层了。在`spring`文件夹里新建`spring-service.xml`文件。

1. 扫描service包所有注解 @Service
2. 配置事务管理器，把事务管理交由spring来完成
3. 配置基于注解的声明式事务，可以直接在方法上@Transaction

### spring-service.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd">
    <!-- 扫描service包下所有使用注解的类型 -->
    <context:component-scan base-package="com.huyd.service"/>

    <!-- 配置事务管理器 -->
    <bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 配置基于注解的声明式事务 -->
    <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```

------

第三步：配置web层，在`spring`文件夹里新建`spring-web.xml`文件。

1. 开启SpringMVC注解模式，可以使用@RequestMapping，@PathVariable，@ResponseBody等
2. 对静态资源处理，如js，css，jpg等
3. 配置jsp 显示ViewResolver，例如在controller中某个方法返回一个string类型的"login"，实际上会返回"/WEB-INF/login.jsp"
4. 扫描web层 @Controller

### spring-web.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/mvc
	http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd">
    <!-- 配置SpringMVC -->
    <!-- 1.开启SpringMVC注解模式 -->
    <!-- 简化配置：
        (1)自动注册DefaultAnootationHandlerMapping,AnotationMethodHandlerAdapter
        (2)提供一些列：数据绑定，数字和日期的format @NumberFormat, @DateTimeFormat, xml,json默认读写支持
    -->
    <!-- 开启spring的扫描注入，使用如下注解 -->
    <!-- @Component,@Repository,@Service,@Controller-->
    <context:component-scan base-package="com.huyd"/>

    <!-- 开启springMVC的注解驱动，使得url可以映射到对应的controller -->
    <mvc:annotation-driven/>

    <!-- 视图解析 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>

```

------

第四步：最后就是修改`web.xml`文件了，它在`webapp`的`WEB-INF`下。

### web.xml

这里面添加了一个过滤，是因为前端调用后端的接口的时候，需要后端接口支持跨域CORS调用，需要添加一个头，需要在后端过滤掉这种问题。见如下testFilter。

```
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1" metadata-complete="true">
    <display-name>Archetype Created Web Application</display-name>
    <!--增加编码过滤器，如下（注意，需要设置forceEncoding参数值为true）-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter>
        <filter-name>testFilter</filter-name>
        <filter-class>com.huyd.filter.SimpleCORSFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>


    <filter-mapping>
        <filter-name>testFilter</filter-name>
        <url-pattern>*</url-pattern>
    </filter-mapping>


    <servlet>
        <servlet-name>spring</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/spring-*.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>spring</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>

```

------

我们在项目中经常会使用到日志，所以这里还有配置日志xml，在`resources`文件夹里新建`logback.xml`文件，所给出的日志输出格式也是最基本的控制台s呼出，大家有兴趣查看[logback官方文档](http://logback.qos.ch/manual/)。

### logback.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<!-- encoders are by default assigned the type ch.qos.logback.classic.encoder.PatternLayoutEncoder -->
		<encoder>
			<pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
		</encoder>
	</appender>

	<root level="debug">
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```

------

到目前为止，我们一共写了7个配置文件，我们一起来看下最终的**配置文件结构图**。

![3](JavaSSMIntegrate\3.jpg)

## 接口设计

> 以为本项目主要是为了实现企业级中前后分离的效果，这样可以达到前后端程序员的职能不冲突，各司其职。
>
> 同时也能掌握SSM框架的使用，以及前端异步调用接口的问题。

首先新建数据库名为`ssm`，再创建两张表：图书表`book`和预约图书表`appointment`，并且为`book`表初始化一些数据，sql如下。

## 数据库设计

### ssm.sql

```
SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for appointment
-- ----------------------------
DROP TABLE IF EXISTS `appointment`;
CREATE TABLE `appointment` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `bookid` int(11) DEFAULT NULL,
  `studentid` int(11) DEFAULT NULL,
  `appointtime` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of appointment
-- ----------------------------

-- ----------------------------
-- Table structure for book
-- ----------------------------
DROP TABLE IF EXISTS `book`;
CREATE TABLE `book` (
  `bookid` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `number` int(11) DEFAULT NULL,
  PRIMARY KEY (`bookid`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of book
-- ----------------------------
INSERT INTO `book` VALUES ('1', 'SpringMVC实战', '10');
INSERT INTO `book` VALUES ('2', 'MyBatis详解', '8');
INSERT INTO `book` VALUES ('3', 'Spring从入门到精通', '12');
```

------

在`entity`包中添加两个对应的实体，图书实体`Book.java`和预约图书实体`Appointment.java`。

## Model层

### Book.java

```
package com.soecode.lyf.entity;

public class Book {

	private long bookId;// 图书ID

	private String name;// 图书名称

	private int number;// 馆藏数量

	// 省略构造方法，getter和setter方法，toString方法

}
```

### Appointment.java

```
package com.soecode.lyf.entity;

import java.util.Date;

/**
 * 预约图书实体
 */
public class Appointment {

	private long bookId;// 图书ID

	private long studentId;// 学号

	private Date appointTime;// 预约时间

	// 多对一的复合属性
	private Book book;// 图书实体
	
	// 省略构造方法，getter和setter方法，toString方法

}
```

------

在`dao`包新建接口`BookDao.java`和`Appointment.java`

## Dao层

### BookDao.java

```
package com.soecode.lyf.dao;

import java.util.List;

import com.soecode.lyf.entity.Book;

public interface BookDao {

	/**
	 * 通过ID查询单本图书
	 * 
	 * @param id
	 * @return
	 */
	Book queryById(long id);

	/**
	 * 查询所有图书
	 * 
	 * @param offset 查询起始位置
	 * @param limit 查询条数
	 * @return
	 */
	List<Book> queryAll(@Param("offset") int offset, @Param("limit") int limit);

	/**
	 * 减少馆藏数量
	 * 
	 * @param bookId
	 * @return 如果影响行数等于>1，表示更新的记录行数
	 */
	int reduceNumber(long bookId);
}
```



**提示**：这里为什么要给方法的参数添加`@Param`注解呢？是因为该方法有两个或以上的参数，一定要加，不然mybatis识别不了。上面的`BookDao`接口的`queryById`方法和`reduceNumber`方法只有一个参数`book_id`，所以可以不用加 `@Param`注解，当然加了也无所谓~

------

注意，这里不需要实现dao接口不用编写daoImpl， mybatis会给我们动态实现，但是我们需要编写相应的mapper。 在`mapper`目录里新建两个文件`BookDao.xml`，分别对应上面两个dao接口，代码如下。

### BookDao.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.soecode.lyf.dao.BookDao">
	<!-- 目的：为dao接口方法提供sql语句配置 -->
	<select id="queryById" resultType="Book" parameterType="long">
		<!-- 具体的sql -->
		SELECT
			book_id,
			name,
			number
		FROM
			book
		WHERE
			book_id = #{bookId}
	</select>
	
	<select id="queryAll" resultType="Book">
		SELECT
			book_id,
			name,
			number
		FROM
			book
		ORDER BY
			book_id
		LIMIT #{offset}, #{limit}
	</select>
	
	<update id="reduceNumber">
		UPDATE book
		SET number = number - 1
		WHERE
			book_id = #{bookId}
		AND number > 0
	</update>
</mapper>
```

**mapper总结**：`namespace`是该xml对应的接口全名，`select`和`update`中的`id`对应方法名，`resultType`是返回值类型，`parameterType`是参数类型（这个其实可选），最后`#{...}`中填写的是方法的参数，看懂了是不是很简单！！我也这么觉得~ 

------

`dao`层写完了，接下来`test`对应的`package`写我们测试方法吧。 因为我们之后会写很多测试方法，在测试前需要让程序读入spring-dao和mybatis等配置文件，所以我这里就抽离出来一个`BaseTest`类，只要是测试方法就继承它，这样那些繁琐的重复的代码就不用写那么多了~

**BaseTest.java**

```
package com.soecode.lyf;

import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

/**
 * 配置spring和junit整合，junit启动时加载springIOC容器 spring-test,junit
 */
@RunWith(SpringJUnit4ClassRunner.class)
// 告诉junit spring配置文件
@ContextConfiguration({ "classpath:spring/spring-dao.xml", "classpath:spring/spring-service.xml" })
public class BaseTest {

}
```

因为`spring-service`在`service`层的测试中会时候到，这里也一起引入算了！

新建`BookDaoTest.java`和`AppointmentDaoTest.java`两个dao测试文件。

**BookDaoTest.java**

```
package com.soecode.lyf.dao;

import java.util.List;

import org.junit.Test;
import org.springframework.beans.factory.annotation.Autowired;

import com.soecode.lyf.BaseTest;
import com.soecode.lyf.entity.Book;

public class BookDaoTest extends BaseTest {

	@Autowired
	private BookDao bookDao;

	@Test
	public void testQueryById() throws Exception {
		long bookId = 1000;
		Book book = bookDao.queryById(bookId);
		System.out.println(book);
	}

	@Test
	public void testQueryAll() throws Exception {
		List<Book> books = bookDao.queryAll(0, 4);
		for (Book book : books) {
			System.out.println(book);
		}
	}

	@Test
	public void testReduceNumber() throws Exception {
		long bookId = 1000;
		int update = bookDao.reduceNumber(bookId);
		System.out.println("update=" + update);
	}

}
```

嗯，到这里一切到很顺利~~那么我们继续service层的编码吧~~可能下面开始信息里比较大，大家要做好心理准备~

咱们终于可以编写业务代码了，在`service`包下新建`BookService.java`图书业务接口。

## Service层

### BookService.java

```
package com.soecode.lyf.service;

import java.util.List;

import com.soecode.lyf.dto.AppointExecution;
import com.soecode.lyf.entity.Book;

/**
 * 业务接口：站在"使用者"角度设计接口 三个方面：方法定义粒度，参数，返回类型（return 类型/异常）
 */
public interface BookService {

	/**
	 * 查询一本图书
	 * 
	 * @param bookId
	 * @return
	 */
	Book getById(long bookId);

	/**
	 * 查询所有图书
	 * 
	 * @return
	 */
	List<Book> getList();

	/**
	 * 预约图书
	 * 
	 * @param bookId
	 * @param studentId
	 * @return
	 */
	AppointExecution appoint(long bookId, long studentId);

}
```

在`service.impl`包下新建`BookServiceImpl.java`使用`BookService`接口，并实现里面的方法。

### BookServiceImpl

```
package com.soecode.lyf.service.impl;

import java.util.List;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import com.soecode.lyf.dao.AppointmentDao;
import com.soecode.lyf.dao.BookDao;
import com.soecode.lyf.dto.AppointExecution;
import com.soecode.lyf.entity.Appointment;
import com.soecode.lyf.entity.Book;
import com.soecode.lyf.enums.AppointStateEnum;
import com.soecode.lyf.exception.AppointException;
import com.soecode.lyf.exception.NoNumberException;
import com.soecode.lyf.exception.RepeatAppointException;
import com.soecode.lyf.service.BookService;

@Service
public class BookServiceImpl implements BookService {

	private Logger logger = LoggerFactory.getLogger(this.getClass());

	// 注入Service依赖
	@Autowired
	private BookDao bookDao;

	@Autowired
	private AppointmentDao appointmentDao;


	@Override
	public Book getById(long bookId) {
		return bookDao.queryById(bookId);
	}

	@Override
	public List<Book> getList() {
		return bookDao.queryAll(0, 1000);
	}

	@Override
	@Transactional
	/**
	 * 使用注解控制事务方法的优点： 1.开发团队达成一致约定，明确标注事务方法的编程风格
	 * 2.保证事务方法的执行时间尽可能短，不要穿插其他网络操作，RPC/HTTP请求或者剥离到事务方法外部
	 * 3.不是所有的方法都需要事务，如只有一条修改操作，只读操作不需要事务控制
	 */
	public AppointExecution appoint(long bookId, long studentId) {
		try {
			// 减库存
			int update = bookDao.reduceNumber(bookId);
			if (update <= 0) {// 库存不足
				//return new AppointExecution(bookId, AppointStateEnum.NO_NUMBER);//错误写法				
				throw new NoNumberException("no number");
			} else {
				// 执行预约操作
				int insert = appointmentDao.insertAppointment(bookId, studentId);
				if (insert <= 0) {// 重复预约
					//return new AppointExecution(bookId, AppointStateEnum.REPEAT_APPOINT);//错误写法
					throw new RepeatAppointException("repeat appoint");
				} else {// 预约成功
					Appointment appointment = appointmentDao.queryByKeyWithBook(bookId, studentId);
					return new AppointExecution(bookId, AppointStateEnum.SUCCESS, appointment);
				}
			}
		// 要先于catch Exception异常前先catch住再抛出，不然自定义的异常也会被转换为AppointException，导致控制层无法具体识别是哪个异常
		} catch (NoNumberException e1) {
			throw e1;
		} catch (RepeatAppointException e2) {
			throw e2;
		} catch (Exception e) {
			logger.error(e.getMessage(), e);
			// 所有编译期异常转换为运行期异常
			//return new AppointExecution(bookId, AppointStateEnum.INNER_ERROR);//错误写法
			throw new AppointException("appoint inner error:" + e.getMessage());
		}
	}

}

```

------

下面我们来测试一下我们的业务代码吧~因为查询图书的业务不复杂，所以这里只演示我们最重要的预约图书业务！！

**BookServiceImplTest.java**

```
package com.soecode.lyf.service.impl;

import static org.junit.Assert.fail;

import org.junit.Test;
import org.springframework.beans.factory.annotation.Autowired;

import com.soecode.lyf.BaseTest;
import com.soecode.lyf.dto.AppointExecution;
import com.soecode.lyf.service.BookService;

public class BookServiceImplTest extends BaseTest {

	@Autowired
	private BookService bookService;

	@Test
	public void testAppoint() throws Exception {
		long bookId = 1001;
		long studentId = 12345678910L;
		AppointExecution execution = bookService.appoint(bookId, studentId);
		System.out.println(execution);
	}

}

```

最后，我们写web层，也就是controller，我们在`web`包下新建`BookController.java`文件。

## Controller层

### BookController.java

这里返回JSON数据，就当与前端约束好的数据契约吧！

```
package com.huyd.controller;

import com.alibaba.fastjson.JSON;
import com.huyd.model.Book;
import com.huyd.service.BookService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * Author: huyd
 * Date: 2017/9/10 20:16
 * Description:
 */
@Controller
@RestController
@RequestMapping("/book")
public class BookController {
    private Logger logger = LoggerFactory.getLogger(this.getClass());

    @Autowired
    private BookService bookService;

    @RequestMapping(value = "/list", method = RequestMethod.GET, produces = "text/plain;charset=UTF-8")
    private String list(Model model) {
        List<Book> list = bookService.getList();
        model.addAttribute("list", list);
//        return "list";
        return serialize(list);
    }

    @RequestMapping(value = "/{bookid}/detail", method = RequestMethod.GET, produces = "text/plain;charset=UTF-8")
    private String detail(@PathVariable("bookid") Long bookid, Model model) {
        if (bookid == null) {
            return "redirect:/book/list";
        }
        Book book = bookService.getById(bookid);
        if (book == null) {
            return "forward:/book/list";
        }
        model.addAttribute("book", book);
//        return "detail";
        return serialize(book);
    }

    public static <T> String serialize(T object) {
        return JSON.toJSONString(object);
    }


}
```

## 接口测试

![4](JavaSSMIntegrate\4.jpg)

# 前端

前端使用技术

- HTML
- JavaScript
- JQuery
- Ajax
- Echarts(百度图表类)

本项目中有通过Ajax动态获取后端接口数据更新图表（柱状图、扇形图、折线图）的代码。

![6](JavaSSMIntegrate\6.jpg)

**调用上面后端接口的代码见如下文件。**

## testData.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <title><div>获取测试数据</div></title>
    <script src="js/jquery-1.4.1.min.js" type="text/javascript"></script>
    <script src="js/getData.js" type="text/javascript"></script>
</head>
<body>
<button id="button" type="button" value="GetData" >GetData</button>

<p>提示信息: <span id="txtHint"></span></p>
<p>图书信息：<span id="txtBook"></span></p>
</body>
</html>
```

## getData.js

```
/*
 * @Author: Huyd
 * @Date:   2017-09-09 22:22:36
 * @Last Modified by:   Huyd
 * @Last Modified time: 2017-09-27 16:13:05
 */

$(function() {
    $('#button').click(function() {
        /* Act on the event */
        $.ajax({
            url: 'http://localhost:8081/SpringMVCRestfulRedis/test/etdata',
            type: 'GET',
            dataType: 'json',
            async: true,
            crossDomain: true,
            success: function(data) {

                var str = '';
                var arr = [];
                // var obj = $.parseJSON(data);
                for (var i = 0; i < data.length; i++) {
                    var test = 1;

                    for (var key in data[i]) {
                        // alert(key+':'+data[i][key]);
                        str = str + key + ':' + data[i][key] + '  ';
                        if (key == 'age') {
                            arr.push(data[i][key]);
                        };
                    }
                }
                alert(arr);
                $('#txtHint').text(str);
                // alert('123');
                console.log(data);
            },
            error: function() {
                /* Act on the event */
                alert('数据获取错误！');
            },
        });

        $.ajax({
            url: 'http://localhost:8081/SpringMVCRestfulRedis/book/list',
            type: 'GET',
            dataType: 'json',
            async: true,
            crossDomain: true,
            success: function(data) {

                var str = '';
                var arr = [];
                // var obj = $.parseJSON(data);
                for (var i = 0; i < data.length; i++) {
                    var test = 1;

                    for (var key in data[i]) {
                        // alert(key+':'+data[i][key]);
                        str = str + key + ':' + data[i][key] + '  ';
                        if (key == 'name') {
                            arr.push(data[i][key]);
                        };
                    }
                }
                alert(arr);
                alert(data);

                $('#txtBook').text(str);
                alert('123');
                console.log(data.toString);
            },
            error: function() {
                /* Act on the event */
                alert('数据获取错误！');
            },
        })
    });


});

// });
```



# 前后端连接

![5](JavaSSMIntegrate\5.jpg)

到此，我们的SSM框架整合配置，与应用实例部分已经结束了，我把所有源码和jar包一起打包放在了我的GitHub上，需要的可以去下载，有用就给个star吧。