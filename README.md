# 基于SSM（非maven）的仓库出入库管理系统

@[TOC](基于SSM（非maven）的仓库出入库管理系统)
# 项目简介
基于SSM（非maven）的仓库出入库管理系统；
功能简单，适合学习以及大作业等，jsp页面，form表单提交数据
分为二种用户：管理员和超级管理员
超级管理员功能：登录，管理员管理，货物管理（增删改查）
管理员功能：登录注册，货物管理（增删改查）
使用MVC设计模式开发

 # 项目获取
> [源码获取地址](http://www.manoncode.cn/details?id=89)

 
# 开发环境

运行环境：推荐jdk1.8；
开发工具：eclipse以及idea（推荐）；
操作系统：windows 10 8G内存以上（其他windows以及macOS支持，但不推荐）；
浏览器：Firefox(推荐)、Google Chrome(推荐)、Edge;
数据库：MySQL8.0(推荐)及其他版本（支持，但容易异常尤其MySQL5.7（不含）以下版本）；
数据库可视化工具：Navicat Premium 15（推荐）以及其他Navicat版本
是否maven项目：否


 # 项目技术
 
后端：mysql、Spring、SpringMVC、Mybatis
前端：jsp

# 相关代码

 - login.jsp
 

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<style>
	body{
		background-image: url(/images/bg.png);
		background-repeat: no-repeat;
		background-size: cover;
	}

</style>
<body style="text-align: center;margin-top:200px;">
	<form action="/user/login" method="post">
		欢迎使用仓库管理系统
		${msg}
		<table style="margin: auto">
			<tr>
				<td>用户名</td>
				<td><input name="username" type="text" ></td>
			</tr>
			<tr>
				<td>密码</td>
				<td><input name="password" type="text" ></td>
			</tr>
			<tr>
				<td>角色</td>
				<td>
					<input name="type" type="radio" value="0">超级管理员
					<input name="type" type="radio" value="1">管理员
				</td>
			</tr>
		</table>
		<td>
		<input type="submit" value="登录">
		</td>	
	</form>
	<a href="/user/toAdd">无账号?去注册</a>
</body>

</html>
```

 - UserController.java
 

```java
package ssm.controller;

import ssm.entity.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import ssm.service.UserService;

import javax.annotation.Resource;
import javax.servlet.http.HttpServletRequest;
import java.util.List;

@Controller
@RequestMapping("user")
public class UserController {
    @Resource
    private UserService userService;

    @RequestMapping("login")
    public String login(HttpServletRequest request, User user){
        String msg = userService.login(user,request);
        if(!msg.equals("success")){
            request.setAttribute("msg",msg);
            return "login";
        }else{
            return "index";
        }
    }

    @RequestMapping("getList")
    public String getList(HttpServletRequest request){
        List<User> list = userService.getList();
        request.setAttribute("list",list);
        return "userList";
    }

    @RequestMapping("delete")
    public String delete(Integer id,HttpServletRequest request){
        userService.deleteByPrimaryKey(id);
        return getList(request);
    }

    @RequestMapping("insert")
    public String insert(User user,HttpServletRequest request){
        user.setType(1);
        userService.insert(user);
        return "login";
    }

    @RequestMapping("toAdd")
    public String toAdd(){
        return "addUser";
    }

    @RequestMapping("logout")
    public String logout(HttpServletRequest request){
        request.getSession().removeAttribute("user");
        return "login";
    }
}

```

 - applicationContext.xml
 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans  xmlns="http://www.springframework.org/schema/beans" 
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans  
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 1.1 读取db.properties -->
    <context:property-placeholder location="classpath:config/db.properties"/>
    <!-- 1.2 配置数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}" />
        <property name="jdbcUrl" value="${jdbc.url}" />
        <property name="user" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
    </bean>
    
  	<!-- 2.配置mybatis：配置SqlSessionFactory -->
	<bean id="sqlSessionFactory" 
	      class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<property name="configLocation" 
		          value="classpath:config/mybatis-config.xml"/>
	</bean>	
	
	<!-- 3.Spring和MyBatis整合:配置扫描Dao接口的包，动态实现Dao接口，注入到Spring容器中 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 给出需要扫描的mapper接口包 ，set注入-->
        <property name="basePackage" value="ssm.mapper" />
    </bean>	
	<context:component-scan base-package="ssm.service"/>
</beans>
```

 # 运行截图
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/00a70d9f35964185b2e38f2d65ab205f.png#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/0ac6ba4f878443178e126b0d152c09d1.png#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/9b8fc0bbfb7745d7a0eb233ae236dcee.png#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/21da8559cc1d487c96fbc41ec9cc9a5a.png#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/26b906e96bad46aeb0ac3c4e7590c4c7.png#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/86335fae50064650905543fbacd522dc.png#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/3df7165486824cd182cad63b284b2dd5.png#pic_center)

# 运行视频

[video(video-Qkxz1WoA-1667984860618)(type-bilibili)(url-https://player.bilibili.com/player.html?aid=304955739)(image-https://img-blog.csdnimg.cn/img_convert/d22ef5bd875c466009da405cff12a3d7.jpeg)(title-【运行视频】（C00089）基于SSM（非maven）的仓库出入库管理系统)]


