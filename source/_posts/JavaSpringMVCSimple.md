---
title: SpringMVC-表单处理&表单标签库
date: 2017-08-19 10:35:16
categories: Java
tags:
- SpringMVC
- 文件上传
---

![HNCK9085](JavaSpringMVCSimple\HNCK9085.jpg)

# 前言

**Spring MVC**框架是一个开源的Java平台，为开发强大的基于Java的Web应用程序提供全面的基础架构支持非常容易和非常快速。

**Spring web MVC**框架提供了MVC(模型 - 视图 - 控制器)架构和用于开发灵活和松散耦合的Web应用程序的组件。 **MVC**模式导致应用程序的不同方面(输入逻辑，业务逻辑和UI逻辑)分离，同时提供这些元素之间的松散耦合。

本章主要提供了使用SpringMVC框架对表单处理与表单标签库的使用。主要有文本框、密码处理、文本域、复选框、复选框（多项）、单选按钮、多项单选按钮、下拉选项、列表多选框、文件上传处理。

源码已放Github上，[点击我查看](https://github.com/huyida/SSM.git)！！！

<!--MORE-->



- 文本框
- 密码处理
- 文本域
- 复选框
- 复选框（多项）
- 单选按钮
- 多项单选按钮
- 下拉选项
- 列表多选框
- 文件上传处理

## 基础标签库

### 1.Model

user.java

```
public class User {
    private String username;//用户名
    private String password;//密码
    private String address;//地址
    private boolean receivePaper;//是否订阅
    private String[] favoriteFrameworks;//喜欢的框架
    private String gender;//性别
    private String favoriteNumber;//喜欢的数字
    private String country;//所在国家
    private String[] skills;//技术
    
    -----setter/getter----
    
    }
```



### 2.Action

UserController.java

这里的第一个服务方法`user()`，我们已经在`ModelAndView`对象中传递了一个名称为“`command`”的空对象，因为如果要在JSP中使用`<form:form>`标签，spring框架需要一个名称为“`command`”的对象文件。当调用`user()`方法时，它返回`user.jsp`视图。

第二个服务方法`addUser()`将在URL `HelloWeb/addUser上的POST方法调用。将根据提交的信息准备模型对象。 最后，将从服务方法返回“`userlist`”视图，这将渲染`userlist.jsp` 页面。

```
@Controller
public class UserController {
    @RequestMapping(value = "/user", method = RequestMethod.GET)
    public ModelAndView user() {
        User user = new User();
        user.setFavoriteFrameworks((new String[]{"Spring MVC", "Struts 2"}));
        user.setGender("M");
        ModelAndView modelAndView = new ModelAndView("user", "command", user);
        return modelAndView;
    }

    @RequestMapping(value = "/addUser", method = RequestMethod.POST)
    public String addUser(@ModelAttribute("SpringWeb") User user,
                          ModelMap model) {
        model.addAttribute("username", user.getUsername());//文本框，用户名
        model.addAttribute("password", user.getPassword());//密码框，密码
        model.addAttribute("address", user.getAddress());//文本域，地址
        model.addAttribute("receivePaper", user.isReceivePaper());//复选框，是否订阅
        model.addAttribute("favoriteFrameworks", user.getFavoriteFrameworks());//多选框，喜欢的框架
        model.addAttribute("gender", user.getGender());//单选按钮，性别
        model.addAttribute("favoriteNumber", user.getFavoriteNumber());//多项单选按钮，喜欢的数字
        model.addAttribute("country", user.getCountry());//下拉选项，所在的国家
        model.addAttribute("skills", user.getSkills());//列表多选框，技术
        return "userlist";
    }

    /**
     * 多选框
     *
     * @return
     */
    @ModelAttribute("webFrameworkList")
    public List<String> getWebFrameworkList() {
        List<String> webFrameworkList = new ArrayList<String>();
        webFrameworkList.add("Spring MVC");
        webFrameworkList.add("Struts 1");
        webFrameworkList.add("Struts 2");
        webFrameworkList.add("Apache Hadoop");
        return webFrameworkList;
    }

    /**
     * 单选按钮
     *
     * @return
     */
    @ModelAttribute("numbersList")
    public List<String> getNumbersList() {
        List<String> numbersList = new ArrayList<String>();
        numbersList.add("1");
        numbersList.add("2");
        numbersList.add("3");
        numbersList.add("4");
        return numbersList;
    }

    /**
     * 下拉框
     *
     * @return
     */
    @ModelAttribute("countryList")
    public Map<String, String> getCountryList() {
        Map<String, String> countryList = new HashMap<String, String>();
        countryList.put("US", "United States");
        countryList.put("CH", "China");
        countryList.put("SG", "Singapore");
        countryList.put("MY", "Malaysia");
        return countryList;
    }

    /**
     * 列表多选框
     *
     * @return
     */
    @ModelAttribute("skillsList")
    public Map<String, String> getSkillsList() {
        Map<String, String> skillList = new HashMap<String, String>();
        skillList.put("Hibernate", "Hibernate");
        skillList.put("Spring", "Spring");
        skillList.put("Apache Hadoop", "Apache Hadoop");
        skillList.put("Struts", "Struts");
        return skillList;
    }

}
```

### 3.spring-servlet.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">


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

    <!--<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"/>-->

    <!-- 文件上传,需要配置MultipartResolver处理器 -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 设置船上文件的最大尺寸为5MB -->
        <property name="maxUploadSize" value="5242880"/>
        <property name="defaultEncoding" value="utf-8"/>
    </bean>

</beans>
```

### 4.web.xml

```
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
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
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>


    <servlet>
        <servlet-name>spring</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/spring-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>spring</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```



### 5.jsp页面

user.jsp代码如下, 

```
<%@ page contentType="text/html;charset=UTF-8" isELIgnored="false" language="java" %>
<%@taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<html>
<head>
    <title>Spring MVC表单处理</title>
</head>
<body>

<h2>用户信息</h2>
<form:form method="POST" action="./addUser">
    <table>
        <tr>
            <td><form:label path="username">用户名：</form:label></td>
            <td><form:input path="username"/></td>
        </tr>
        <tr>
            <td><form:label path="password">密码：</form:label></td>
            <td><form:password path="password"/></td>
        </tr>
        <tr>
            <td><form:label path="address">地址：</form:label></td>
            <td><form:textarea path="address" rows="5" cols="30"/></td>
        </tr>
        <tr>
            <td><form:label path="receivePaper">是否订阅：</form:label></td>
            <td><form:checkbox path="receivePaper"/></td>
        </tr>
        <tr>
            <td><form:label path="favoriteFrameworks">喜欢的框架/技术：</form:label></td>
            <td><form:checkboxes items="${webFrameworkList}"
                                 path="favoriteFrameworks"/></td>
        </tr>
        <tr>
            <td><form:label path="gender">性别：</form:label></td>
            <td><form:radiobutton path="gender" value="M" label="男"/> <form:radiobutton
                    path="gender" value="F" label="女"/></td>
        </tr>
        <tr>
            <td><form:label path="favoriteNumber">喜欢的数字：</form:label></td>
            <td><form:radiobuttons path="favoriteNumber"
                                   items="${numbersList}"/></td>
        </tr>
        <tr>
            <td><form:label path="country">所在国家：</form:label></td>
            <td><form:select path="country">
                <form:option value="NONE" label="请选择..."/>
                <form:options items="${countryList}"/>
            </form:select></td>
        </tr>
        <tr>
            <td><form:label path="skills">技术：</form:label></td>
            <td><form:select path="skills" items="${skillsList}"
                             multiple="true"/></td>
        </tr>
        <tr>
            <td colspan="2"><input type="submit" value="提交"/></td>
        </tr>
    </table>
</form:form>
</body>
</html>
```



### 6.展现结果jsp

userlist.jsp

<%@ page contentType="text/html; charset=UTF-8" isELIgnored="false" language="java" %>这一句中isELIgnored="false"一定要加，表示使用EL语句。

```
<%@ page contentType="text/html;charset=UTF-8" isELIgnored="false" language="java" %>
<%@taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<html>
<head>
    <title>Spring MVC表单处理</title>
</head>
<body>

<h2>提交用户信息 -</h2>
<table>
    <tr>
        <td>用户名：</td>
        <td>${username}</td>
    </tr>
    <tr>
        <td>密码：</td>
        <td>${password}</td>
    </tr>
    <tr>
        <td>地址：</td>
        <td>${address}</td>
    </tr>
    <tr>
        <td>是否订阅：</td>
        <td>${receivePaper}</td>
    </tr>
    <tr>
        <td>喜欢的技术/框架：</td>
        <td>
            <%
                String[] favoriteFrameworks = (String[]) request.getAttribute("favoriteFrameworks");
                for (String framework : favoriteFrameworks) {
                    out.println(framework);
                }
            %>
        </td>
    </tr>
    <tr>
        <td>性别：</td>
        <td>${(gender=="M"? "男" : "女")}</td>
    </tr>
    <tr>
        <td>喜欢的数字：</td>
        <td>${favoriteNumber}</td>
    </tr>
    <tr>
        <td>国家：</td>
        <td>${country}</td>
    </tr>
    <tr>
        <td>技术：</td>
        <td>
            <%
                String[] skills = (String[]) request.getAttribute("skills");
                for (String skill : skills) {
                    out.println(skill);
                }
            %>
        </td>
    </tr>

</table>
</body>
</html>
```



## 文件上传处理

spring-servlet.xml与web.xml和上面的相同。

### 1.Model

FileModel.jsp代码如下:

```
public class FileModel {
    private MultipartFile file;
    
    ---setter/getter-----
    
    }    
```

### 2.Action

FileUploadController.java

```
@Controller
public class FileUploadController {

    @Autowired
    ServletContext context;

    @RequestMapping(value = "/fileUploadPage", method = RequestMethod.GET)
    public ModelAndView fileUploadPage() {
        FileModel file = new FileModel();
        ModelAndView modelAndView = new ModelAndView("fileUpload", "command", file);
        return modelAndView;
    }

    @RequestMapping(value = "/fileUploadPage", method = RequestMethod.POST)
    public String fileUpload(@Validated FileModel file, BindingResult result, ModelMap model) throws IOException {
        if (result.hasErrors()) {
            System.out.println("validation errors");
            return "fileUploadPage";
        } else {
            System.out.println("Fetching file");
            MultipartFile multipartFile = file.getFile();
            String uploadPath = context.getRealPath("") + File.separator + "temp" + File.separator;
            //Now do something with file...
            FileCopyUtils.copy(file.getFile().getBytes(), new File(uploadPath + file.getFile().getOriginalFilename()));
            String fileName = multipartFile.getOriginalFilename();
            model.addAttribute("fileName", fileName);
            return "success";
        }
    }
```



### 3.jsp页面

fileUpload.jsp

```
<%@ page contentType="text/html; charset=UTF-8" isELIgnored="false" language="java" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<html>
<head>
    <title>Spring MVC上传文件示例</title>
</head>
<body>
<form:form method="POST" modelAttribute="fileUpload"
           enctype="multipart/form-data">
    请选择一个文件上传 :
    <input type="file" name="file" />
    <input type="submit" value="提交上传" />
</form:form>
</body>
</html>
```

