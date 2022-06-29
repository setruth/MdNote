- [IOC与DI](#ioc与di)
  - [配置文件方式](#配置文件方式)
    - [IOC 反转控制(Inversion of Control)](#ioc-反转控制inversion-of-control)
    - [DI 依赖注入(Dependency Injection)](#di-依赖注入dependency-injection)
    - [Bean注意事项](#bean注意事项)
    - [Bean的自动装配](#bean的自动装配)
    - [Bean集合的注入](#bean集合的注入)
    - [jdbc.properties文件加载](#jdbcproperties文件加载)
  - [注解开发](#注解开发)
    - [定义Bean](#定义bean)
    - [创建配置文件](#创建配置文件)
    - [注解使用bean](#注解使用bean)
    - [依赖注入](#依赖注入)
    - [管理第三方bean](#管理第三方bean)
    - [第三方Bean依赖注入](#第三方bean依赖注入)

# IOC与DI

## 配置文件方式

### IOC 反转控制(Inversion of Control)

>用于解决程序耦合度高的问题
>
>如果当有一个新的类TestImpl2实现了Test接口，想把test的实例化换了  
>如果写test=TestImpl2()，那么程序就改变了，要重新编译非常麻烦  

``` kotlin
 //有个接口
 interface Test{
    fun show()
 }

//有个实现类
class TestImpl1:Test{
    override fun show()[
        print("这是实现类1")
    ]
}

fun main() {
    var test:Test
    test=TestImpl1()
   // test=TestImpl2()
}
```

>可以通过IOC来创建管理的配置文件，里面创建你要管理的Bean 就可以从
>外部导入
>
>在resources创建Bean管理文件ApplicationContext.xml

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.orgschema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
       <!-- 配置一个名字叫 bean1 实例化class引入的类的bean -->
       <!-- id是配置的bean的名称，不能重复 -->
        <Bean id="bean1" class="包名.类名">
</beans>
```

```kotlin
fun main(){
    //创建容器,可以多个，逗号隔开
    val beans=ClassPathXmlApplicationContext("创建的管理文件名字.xml")
    var test:Test
    //test=TestImpl1()
    test=beans.get("bean1") as Test
    test.show() //这是实现类1
    //此时在管理文件中更改bean引入的类就能在不重新编译的情况下换实力类
}
```

### DI 依赖注入(Dependency Injection)

>配置的bean引入的类中有别的类或者数据如何在创建的时候实例化

```kotlin
//类1
class C1{

}
//类2
class C2{
     lateinit var c1: C1
    /* 要给set方法 提供注入的入口
    fun setC1(ci:C1){
         this.ci = ci
     } *
     /
}
```

>然后配置文件中配置

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.orgschema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <Bean id="bean1" class="xxxx.C1">
        <Bean id="bean2" class="xxxx.C2">
            <!-- 给c1变量注入bean1引入的实例化类 -->
            <property name="c1" ref="bean1"/>
        </Bean>
</beans>
```

### Bean注意事项

> 如果要配置多个名字可以加name标签(空格隔开)

```xml
<Bean id="bean1" name="name1 name2" class="xxxx.C1">
```

> 获取的Bean默认是单例，如果不想是单例可以用scope

```xml
<Bean id="bean1" scope="prototype" class="xxxx.C1">
```

> bean的本质就是创建对象，如果构造方法设置成private 会抛出异常

>可以用静态工厂实例化Bean

```kotlin
class T{

}
object Factory{
   fun getTest():T=T()
}
```

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.orgschema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <Bean id="factory" class="xxxx.Factory" factory-method="getTest">
</beans>
```

>Bean的生命周期

```kotlin
class Test{
    fun init(){

    }
    fun destroy(){
        
    }
}
```

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.orgschema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <Bean id="bean1" class="xxxx.Test" init-method="init" destroy-method="destroy"/>
</beans>
```

>Bean普通类型的注入

```kotlin
class Test{
    //要提供set方法
    lateinit var 1:Int
    lateinit var s:String
}
```

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.orgschema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <Bean id="bean1" class="xxxx.Test" init-method="init">
            <property name="i" value="10">
            <property name="s" value="hello">
        </Bean>
</beans>
```

>Bean构造器注入

```kotlin
class T1{

}

class Test(i:Int,t:T1){

}
```

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.orgschema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean name="t1" class="xxxx.T1">
        <Bean id="bean1" class="xxxx.Test" init-method="init">
            <constructor-org name="ii" value="10">
            <constructor-org name="t" ref="t1">
            <!-- 按照类型判断 -->
            <!-- <constructor-org type="Int" value="10"> -->
            <!-- <constructor-org name="xxx.T1" ref="t1"> -->

            <!-- 位置判断 -->
             <!-- <constructor-org index="0" value="10"> -->
            <!-- <constructor-org index=1" ref="t1"> -->
        </Bean>
</beans>
```

### Bean的自动装配

```kotlin
class T1{

}

class Test(i:Int,t:T1){
    //要提供set方法
    lateinit var t:T1
}
```

>通过 autowire属性进行自动装配

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.orgschema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean name="t" class="xxxx.T1">
        <Bean id="bean1" class="xxxx.Test" autowire="byName"/>
        <!-- 按照名字装配 -->
        <!-- <Bean id="bean1" class="xxxx.Test" autowire="byType"/> -->
</beans>
```

### Bean集合的注入

```kotlin

class Test(i:Int,t:T1){
    //都要提供set方法
    lateinit var arrays:IntArray
    lateinit var map:MutableMap
    lateinit var list:MutableList
    lateinit var set:MutableSet
}
```

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.orgschema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <Bean id="bean1" class="xxxx.Test">
            <property name="array">
                <array>
                    <value>100</value>
                    <value>200</value>
                    <value>300</value>
                </array>
            </property>
            <property name="list">
                <list>
                    <value>qwe</value>
                    <value>rty</value>
                    <value>uio</value>
                </list>
            </property>
            <property name="set">
                <set>
                    <value>qwe</value>
                    <value>rty</value>
                    <value>uio</value>
                </set>
            </property>
            <property name="map">
                <map>
                    <entry key="name" value="李华"/>
                    <entry key="sex" value="男"/>
                    <entry key="age" value="20"/>
                </map>
            </property>
        </Bean>
</beans>
```

### jdbc.properties文件加载

>在resources下创建jdbc.properties文件 
```properties
jdbc.account="root"
jdbc.password="root"
xxxxx
```
>然后在配置文件中导入

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation=
       "http://www.springframework.orgschema/beans 
       http://www.springframework.org/schema/beans/spring-beans.xs

        http://www.springframework.orgschema/context 
       http://www.springframework.org/schema/context/spring-context.xsd"
       >
       <!-- 在上方开启context命名控件，(spring提供的命名空间) -->
       <!-- 导入jdbc配置文件 -->
       <!-- 默认是系统属性模式，要用jdbc.xxx来定义属性 -->
       <!-- 使用system-properties-mode="NEVER"标签可以取消系统属性模式，可以自己自定义 -->
       <context:property-placeholder location="jdbc.properties" system-properties-mode="NEVER">
       <!-- 建议格式 -->
       <context:property-placeholder location="classpath:*.properties">
       <!-- 使用：${jdbc.account} -->
</beans>
```
## 注解开发

### 定义Bean

@Component
>通过@Component注册可选@Component("xxx")可定义bean名字
```kotlin
    @Component //定义bean
    @Scope("prototype") //设置不为单列
    class DaoImpl :Dao {
        override show() {
            println("显示")
        }
    }
```

### 创建配置文件

@Configuration

```kotlin
    @Configuration //定义这个SpringConfig是配置文件
    @ComponentScan("com.setruth") //扫描com.setruth包下所有被定义的bean
    open class SpringConfig {
    }
```

### 注解使用bean

```kotlin
    val ctx= AnnotationConfigApplicationContext(SpringConfig::class.java)
    val dao:Dao=ctx.getBean(Dao::class.java)//通过类型获取
    val dao:Dao=ctx.getBean("通过定义的名字获取")//通过类型获取
    dao.show()//显示
```

### 依赖注入

@Autowired

>当添加autowired注解后 在运行的时候会寻找之前定义的bean来匹配类型进行注入
```kotlin
    @Component
    class ServiceImpl : Service {
    @Autowired //自动寻找类型为Dao的bean
    lateinit var dao: Dao
    override fun show() {
        println("服务")
        dao.show()
    }
}
```

@Qualifier
>当有两个bean同时继承一个接口时可以通过Qualifier注解区分用哪个bean  
>Qualifier注解必须配合Autowired注解使用，不能单独使用

```kotlin
    @Component
    class ServiceImpl : Service {
    @Autowired //自动寻找类型为Dao的bean
    @Qualifier("dao2") //使用名字为dao2的bean 
    lateinit var dao: Dao
    override fun show() {
        println("服务")
        dao.show()
    }
}
```

@value

>实现简单的类型注入

```kotlin
    @Component
    class ServiceImpl : Service {
    
    @Value("setruth")
    lateinit var name: String
    override fun show() {
        println("服务")
    }
}
```

@PropertySource
> 引入外部配置文件

```properties
    # jdbc.properties文件内容
    name="setruth"
```

```kotlin
    //配置文件中用引入jdbc.properties
    @Configuration 
    @ComponentScan("com.setruth")
    //不支持*选取全部文件和路径
    @PropertySource("jdbc.properties") //引入配置文件
    //@PropertySource("jdbc.properties","xxx","xxx") //引入多个文件
    open class SpringConfig {
    }
```

```kotlin
    //使用
    @Component
    class ServiceImpl : Service {
    //在kotlin中要加入\来区分为$符号 因为与原来kotlin语法冲突
    @Value("\${name}") 
    lateinit var name: String
    override fun show() {
        println("服务")
    }
}
```

### 管理第三方bean

@Bean

> 可通过定义返回值为一个bean的方式管理

```kotlin
    @Configuration 
    @ComponentScan("com.setruth")
    open class SpringConfig {
        //返回是一个Bean 
        //Bean("xxx")名字可选
        //这里的Date假设是一个第三方库中的类
        @Bean
        fun getDate():Date{
            return Date
        }
    }
```

```kotlin
    val ctx= AnnotationConfigApplicationContext(SpringConfig::class.java)
    val date:Date=ctx.getBean(Date::class.java)
    //val date:Date=ctx.getBean("")
```

>可以把配置内容分类放入一个类然后导入，便于管理

```kotlin
class Date{
    @Bean
    fun getDate():Date{
        return Date
    }
}
```

```kotlin
    @Configuration 
    @ComponentScan("com.setruth")
    @Import(Date::class.java) //导入别的配置内容
    open class SpringConfig {
       
    }
```


### 第三方Bean依赖注入

```kotlin
//假设Date类需要name，和id
@Value("name")
private lateinit var name:String
@Value("id")
private lateinit var id:Int

class Date{
    @Bean
    fun getDate():Date{
        var date=Date()
        date.setName(name)
        date.setId(id)
        return Date
    }
}
```

>引用注入

```kotlin
class Date{
    @Bean
    fun getDate(dao:Dao):Date{
        var date=Date()
        date.setDao(dao)
        return Date
    }
}

//定义一个Dao的bean即可，spring进行类型匹配的自动装配
```