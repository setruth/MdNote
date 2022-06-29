# Mybatis

## 代码环境

1. 语言：Kotlin
2. 环境：Gradle
3. 软件：IDEA



## 介绍

> 简化了JDBC的许多繁琐操作，不用再使用麻烦的结果集进行数据库的操作



## 操作的数据表

> mybatis数据库下的info数据表

| id(自增) | name | sex  |
| -------- | ---- | ---- |
| 1        | yang | 1    |
| 2        | yu   | 0    |



## 搭建环境

```groovy
// https://mvnrepository.com/artifact/mysql/mysql-connector-java
//导入mysql驱动
implementation("mysql:mysql-connector-java:8.0.21")

// https://mvnrepository.com/artifact/org.mybatis/mybatis
//导入mybatis
implementation("org.mybatis:mybatis:3.4.6")
```



## 基本使用



### 核心配置文件

> 在resources文件中新建mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--    引入jdbc配置文件-->
    <properties resource="jdbc.properties"/>
    <!--  数据库连接配置      -->
    <!--  default：默认使用的配置      -->
    <environments default="testEv">  
        <environment id="testEv">
   			<!--  事务管理器类型      -->
            <transactionManager type="JDBC"></transactionManager>
   			<!--  何种连接池方式      -->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

> jdbc.properties文件内容

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.username=xxxx
jdbc.password=xxxx
jdbc.url=jdbc:mysql://localhost:3306/mybatis
```



### 编写表中数据对应的数据类

```kotlin
package com.setruth.pojo

class UserInfo {

    lateinit var name:String
    var sex:Int=0

    constructor(name: String, sex: Int) {
        this.name = name
        this.sex = sex
    }

    override fun toString(): String {
        return "UserInfo(name='$name', sex=$sex)"
    }
}
```



### 编写Mapper管理接口

> mapper接口类似于dao提供操作方法，但是不提供实体类，具体操作内容要单独写对应的配置文件

```kotlin
package com.setruth.mappers

import com.setruth.pojo.Info

interface UserMapper {

    fun insert(info: Info):Int
    fun updateSex(nameKey:String,newSex:Int)
    fun updateName(nameKey:String,newName:String)
    fun delete(name:String)
    fun getByName(@Param("name") name:String):Info
    fun getAll():List<Info>
}
```



### 编写Mapper对应的映射文件

> 在resources文件内夹下创建map
>
> pers文件夹，在其中创建mapper.xml

> #{}：引用 a #{’123‘} ==a '123'
>
> ${}：连接 a${’123‘} ==a 123

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- 创建命名空间，也就是对应的Mapper管理接口-->
<mapper namespace="com.setruth.mappers.UserMapper">
    <!--     id名与方法名相同，闭合标签中编写sql语句-->
    
    <!--     fun insert()-->
    <!--     使用传入的引用类型可以直接使用里面的属性-->
    <insert id="insert">
        insert into info values (null ,#{name},#{sex})
    </insert>
	<!--     fun updateName(nameKey:String,newName:String)-->
    <!--     也可以把参数改为一个map 自己传一个map，使用键访问-->
    <update id="updateSex">
        update info
        set name= #{arg1}
        where name = #{arg0}
    </update>
    <!--     fun updateSex(nameKey:String,newSex:Int)-->
    <!--     多参数的时候会把参数按顺序放入一个map集合中，{arg0,arg1...},{param1,param2,...}任选一种即可，可以混合-->
    <update id="updateSex">
        update info
        set sex= #{arg1}
        where name = #{arg0}
    </update>
    <!--     fun delete(name:String)-->
    <!--     当只有一个参数的时候可以使用任意的名字访问它-->
    <delete id="delete">
        delete
        from info
        where name = '${lala}'
    </delete>
	<!--	fun getByName(@Param("nameKey") name:String):Info-->
    <!--     推荐使用@Param注解 通过设置的值使用参数-->
    <!--     resultType定义从数据库中返回的数据对应哪种数据类-->
    <!--     resultType当返回的数据与类中的属性名字不匹配时可使用map按照类型匹配-->
    <select id="getByName" resultType="com.setruth.pojo.UserInfo">
        select *
        from info
        where name = #{nameKey}
    </select>
    <!--	fun getAll():List<Info>-->
    <select id="getByName" resultType="com.setruth.pojo.UserInfo">
        select *
        from info
    </select>
</mapper>
```



### 使用SeqlSession操作数据库

```kotlin
//把配置文件读为流
var configInputStream:InputStream=Resources.getResourceAsStream("mybatis.xml")
//创建SqlSession工厂建造者
var sqlSessionFactoryBuilder=SqlSessionFactoryBuilder()
//通过SqlSession工厂建造者的build方法指定配置文件创建SqlSession工厂
var sqlSessionFactory=sqlSessionFactoryBuilde.build(configInputStream)
//通过SqlSession工厂的openSession方法获取SqlSession
var sqlSession=sqlSessionFactory.openSession(true) //开启自动提交，不写默认时false
//使用sqlSession的getMapper方法获取指定的mapper管理对象
val mapper = sqlSession.getMapper(UserMapper::class.java)
//操作数据库内容
mapper.insert(UserInfo("李华",1))

```



## 技巧

### 类型别名

> 通常设置mapper映射文件中数据类型的时候都是写全包名 麻烦可以定义它的别名来使用

> 在配置文件中使用typeAliases标签来定义别名

```xml
<configuration>
    xxxxx
	<typeAliases>
        <!--    type设置类型  alias 定义别名 当不使用alias 默认别名是类名   -->
        <typeAlias type="com.setruth.pojo.UserInfo" alias="UserInfo"/>
    </typeAliases>
    xxxxx
</configuration>
```

> 当使用的类名多的时候，很多条别名的定义会很麻烦，可以使用包的形式定义这个包下的所有数据类的别名

```xml
    <typeAliases>
        <!--        com.setruth.pojo下的所有数据类都定义了别名，名字就是类名-->
        <package name="com.setruth.pojo"/>
    </typeAliases>
```

### 批量引入mapper映射文件

> resources/com/setruth/mappers/UserMapper.xml

> com.seturth.mappers.UserMapper

```xml
<configuration>
    <mappers>
        <!--        以包引入的时候映射文件目录要和接口的包一致,，并且文件名要和类名字也要一致-->
        <package name="com/setruth/mappers/"/>
    </mappers>
</configuration>
```

