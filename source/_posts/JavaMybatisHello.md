---
title: IDEA-MyBatis 逆向工程生成与使用
date: 2017-08-20 19:49:51
categories: Java
tags:
- MyBatis
- IDEA
- 逆向生成
---

![367H](JavaMybatisHello\367H.jpg)

# 前言

MyBatis是一个流行的数据库框架之一，另外一个是hibernate，但是MyBatis是一个半自动映射的框架，它可以解决hibernate全映射的不足。所以MyBatis比hibernate更加的灵活，因此掌握MyBatis是必要的。

本文主要讲解使用IDEA对MyBatis 逆向工程生成以及实现一个小例子。

源码已上传Github，[点击下载](https://github.com/huyida/SSM.git)！！！

<!--MORE-->

- 创建maven项目
- 项目结构
- 在pom.xml文件添加jar包依赖
- 数据库以及数据库表的创建
- 添加generatorConfig.xml配置文件
- 逆向生成
- MyBatis的配置文件
- 运行测试

# 一. 创建maven项目

![1](JavaMybatisHello\1.jpg)

# 二. 项目结构

如下为最终创建好的项目结构

![2](JavaMybatisHello\2.jpg)

# 三. 在pom.xml文件添加jar包依赖

~~~
<dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.38</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.4</version>
        </dependency>

~~~

然后在pom.xml中build里添加需要逆向工程的插件以及读取src/main/java下的mapper中xml映射文件

~~~
<build>

        ............
        
        <plugins>
            <!-- mybatis generator逆向工程生成代码插件 -->
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <!-- 使用版本1.3.2 -->
                <version>1.3.2</version>
                <configuration>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
            </plugin>
        </plugins>

        <!--读取src/main/java下的mapper中xml文件-->
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
        
        .............
        
    </build>

~~~

相应的插件在右侧会出现

![3](JavaMybatisHello\3.jpg)

# 四. 数据库以及数据库表的创建

![4](JavaMybatisHello\4.jpg)

# 五. 添加generatorConfig.xml配置文件

在src/main/resources下面加入generatorConfig.xml配置文件.

如下添加是主要mysql驱动jar的位置、数据库连接账号密码、及各生成文件的路径等

~~~
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE generatorConfiguration PUBLIC
        "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd" >
<generatorConfiguration>

    <!--mybatis生成工具的帮助文档可以看：-->
    <!--英文：http://www.mybatis.org/generator/usage/mysql.html-->
    <!--中文：http://www.mybatis.tk/-->
    <!--中文：http://mbg.cndocs.tk/-->

    <!--步骤1：添加你本地的驱动jar（低版本）-->
    <classPathEntry
            location="E:\mvnrepo\repository\mysql\mysql-connector-java\5.1.38\mysql-connector-java-5.1.38.jar"/>

    <context id="MysqlTables" targetRuntime="MyBatis3">

        <!--设置编码格式-->
        <property name="javaFileEncoding" value="UTF-8"/>

        <commentGenerator>
            <property name="suppressAllComments" value="false"/>
            <property name="suppressDate" value="true"/>
        </commentGenerator>

        <!--步骤2：数据库账号密码-->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql://localhost:3306/user"
                        userId="root" password="zhelishimima"/>

        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!--步骤3：要生成的 pojo 模块位置-->
        <javaModelGenerator targetPackage="com.huyd.model"
                            targetProject="F:\OneDrive\USERPROG\JAVA\MybatisHello\src\main\java">
            <property name="enableSubPackages" value="false"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!--步骤4：要生成的 Mapper.xml 文件位置-->
        <sqlMapGenerator targetPackage="com.huyd.mapper"
                         targetProject="F:\OneDrive\USERPROG\JAVA\MybatisHello\src\main\java">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>

        <!--步骤5：要生成的 Mapper 接口类-->
        <javaClientGenerator targetPackage="com.huyd.dao"
                             targetProject="F:\OneDrive\USERPROG\JAVA\MybatisHello\src\main\java"

                             type="XMLMAPPER">
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>


        <!--步骤6：要根据哪张表生成，要在这里配置-->
        <!--用百分号表示生成所有表,可以直接省去一个一个写 <table tableName="%" /> -->
        <table tableName="user_inf" domainObjectName="User" enableCountByExample="false" enableDeleteByExample="false"
               enableSelectByExample="false" enableUpdateByExample="false"/>
    </context>
</generatorConfiguration>

~~~

# 五. 逆向生成

点击下图红线框命令即可运行！

![5](JavaMybatisHello\5.jpg)

生成成功就与第二中项目结构一样。



# 六. MyBatis的配置文件

在resources中创建mybatis-config.xml

~~~
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--加载连接数据库的基本信息文件-->
    <properties resource="jdbc.properties"></properties>
    <typeAliases>
        <typeAlias alias="User" type="com.huyd.model.User"/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/huyd/mapper/UserMapper.xml"/>
    </mappers>
</configuration>

~~~

在resources中创建jdbc.properties

~~~
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/user
username=root
password=manager
~~~

# 七. 运行测试

Test.java

~~~
package com.huyd.test;

import com.huyd.model.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.Reader;

/**
 * Author: huyd
 * Date: 2017/8/20 17:53
 * Description:
 */
public class Test {
    private static SqlSessionFactory sqlSessionFactory;
    private static Reader reader;

    static {
        try {
            reader = Resources.getResourceAsReader("mybatis-config.xml");
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSessionFactory getSqlSessionFactory() {
        return sqlSessionFactory;
    }

    public static void main(String[] args) {
        SqlSession session = sqlSessionFactory.openSession();
        try {
            User user = (User) session.selectOne("selectByPrimaryKey", 2);
            session.commit();
            System.out.println(user.getUsername());
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            session.close();
        }
    }

}
~~~

结果展示：

![6](JavaMybatisHello\6.jpg)