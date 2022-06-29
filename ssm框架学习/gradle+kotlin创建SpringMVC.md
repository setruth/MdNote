使用Gradle+Kotlin构建SpringMVC

## SpringMVC介绍

> 以前的前后端不分离使得开发人员既要负责前端也要负责后端，造成了巨大的工作量，也使一个开发人员的学习负担很重

> 传统的三层架构 Web Service Dao 向Service程序发送请求通过Dao操作再返回需要的Web页面

> MVC架构 Controller service view model dao 把请求委托给控制器来负责，调用service来进行dao操作 最后返回model与页面来渲染需要获取的内容，

> 最后把View分出去，model分离，只留下service controller dao层 后端返回model数据前端获取选软 分开页面和服务器开发的融合，分工更明确，更好维护，并且降低工作量，后端人员只需要根据业务编写服务器程序和接口文档，前端人员通过接口文档无需了解服务器程序的内容，根据文档进行请求获取数据进行渲染即可

## 开发环境

> 工具：IDEA 2021.2.3专业版
>
> 构建环境：Gradle 7.1
>
> 编程语言：kotlin 

## 开发

### 1.新建项目

> 新建一个新的项目，选择Gradle项目，勾选Kotlin和Web选项，接下来和平常一样最后直接finish

> 基本包结构com.setruth

### 2. 导入依赖

> 导入环境

```groovy
// https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api
compileOnly('javax.servlet:javax.servlet-api:4.0.1') //导入servlet
implementation 'org.springframework:spring-webmvc:5.3.10' //导入SpringMVC依赖
```

### 3. 构建SpringConfig

> 在resources下新建xml config file下的Spring Config文件，配置下面内容

```xml
<!--文件名springmvc-->

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!--扫描配置controller-->
    <context:component-scan base-package="com.setruth"/>
    <!--开启注解-->
    <mvc:annotation-driven/>
</beans>
```

### 4.构建Web.xml配置文件

> 因为gradle项目中webapp下没有web.xml文件，所以要自己建

> webapp下新建文件夹WEB-INF,WEB-INF下新建web.xml文件，配置下面内容

```xml
<?xml version="1.0" encoding="UTF-8"?>

<web-app xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         version="2.5">
    <!--servlet控制器-->
    <servlet>
        <servlet-name>myservlet</servlet-name>
        <!--选择servlet依赖-->
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--引入springmvc.xml的位置-->
        <init-param>
            <param-name>springConfig</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!--配置初始化参数，启动时创建servlet对象-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

### 5.配置控制器

> 在com.setruth包下新建controller包，在包下新建一个名为UserController的Kotlin类

```kotlin
package com.setruth.controller

import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController
//使用Controller要手动在方法上面写@ResponseBody,RestController注解中集成了@ResponseBody
@RestController
/*可以在最外面配置一个基本路径@RequestMapping("user")
里面的Controller就再配置访问路径,访问就是:/user/login 避免访问路径重复*/
class UserController {
    @RequestMapping("login")//配置访问路径
    fun helloWorld(): String {
        return "Hello World!"
    }
}
```

### 6.改war包生成路径

> 在工具栏的file下的Project Structure中，在左边Artifacts中，右边选有exploded后缀的，在Output directory的路径中删掉exploded这段接上

> 比如xxx\build\libs\exploded\spring1MvcGradlexxxx，改为xxx\build\libs\springMvcGradlexxxx然后应用即可

### 7.配置Tomact

> 记得选择Tomact server 别选择EE

> 按照平常配置即可

### 注意

> 在Deployment中，deployment at the server startup选择exploded的，用于热启动

> application contenxt更改的路径别改成/路径，无法访问

> 改了application contenxt的路径后记得改server的访问路径，不会同步更新的

## 注解配置

> 在Servlet3.0以后，就不用配置web.xml文件，可以直接使用注解进行编写配置内容
>
> springMVC也推荐不使用配置文件而是使用纯注解开发的形式

### SpringMVC配置文件

> 通过注解的方式创建SpringMVC的配置文件

```kotlin
//使用注解创建配置文件
@Configuration
//扫描controller的controller
@ComponentScan("com.setruth.controller")
open class SpringConfig {

}
```

### Web的配置文件

> 创建一个类继承AbstractDispatcherServletInitializer的类

```kotlin
class WebConfig : AbstractDispatcherServletInitializer() {
    //配置拦截器
    override fun getServletMappings(): Array<String> {
        return arrayOf("/")
    }
    //加载springMVC中的SPring配置
    override fun createRootApplicationContext(): WebApplicationContext? {
        return null
    }
    //加载SPringmvc配置文件
    override fun createServletApplicationContext(): WebApplicationContext {
        val annotationConfigWebApplicationContext=AnnotationConfigWebApplicationContext()
        annotationConfigWebApplicationContext.register(SpringConfig::class.java)
        return annotationConfigWebApplicationContext
    }
}
```

> 也可以继承AbstractAnnotationConfigDispatcherServletInitializer类，更加简便

```kotlin
class WebConfig:AbstractAnnotationConfigDispatcherServletInitializer(){
    override fun getServletMappings(): Array<String> {
        return arrayOf("/")
    }
    override fun getRootConfigClasses(): Array<Class<*>>? {
        return null
    }

    override fun getServletConfigClasses(): Array<Class<*>>? {
        return arrayOf(SpringConfig::class.java)
    }
}
```
