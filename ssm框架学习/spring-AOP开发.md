[TOC]



# SpringdAOP开发



## 代码环境

1. 语言：Kotlin
2. 环境：Gradle
3. 软件：IDEA



## 概述

>AOP面向切面编程,在不改动原有代码的情况下增加新的功能，无侵入式的开发

## 主要结构

>切入点切入连接点在连接点启动通知

### 切入点

>程序运行的任意位置，执行方法，抛出异常，定义变量等
>
>> spring基本上指方法的执行

### 连接点

>用于描述在哪运行

### 通知

>运行的内容也就是新增的功能

### 通知类

>定义通知的类

### 切面

>表述切入点与通知的关系

## 使用
>导入AspectJ

``` kotlin
//在com.setruth.dao.impl包下
//一个普通的Bean
@Component
class T{
    fun show(){
        print("显示")
    }
}
```

```kotlin
//定义通知类
@Component
@Aspect //标注这是一个切面不会被当成Bean识别
class MyAdvice {
    //定义切入点，execution(方法返回类型 包名.类名.方法(形参))
    //要标注在一个private函数上
    @Pointcut("execution(void com.setruth.dao.impl.DaoImpl.show())")
     fun pt(){}
    //描述在这个通知在哪个位置运行下面的方法
    @Before("pt()")
    fun showDate(){
        print(System.currentTimeMillis())
    }
}
```

```kotlin
//配置文件
@Configuration
@ComponentScan("com.setruth")
@EnableAspectJAutoProxy //启动切面文件

open class SpringConfig {
}
```

```kotlin
//使用
fun main() {
    val c=AnnotationConfigApplicationContext(SpringConfig::class.java)
    var t:T=c.getBean(Dao::class.java)
    t.show() //1653016604186显示
}
```

## 切入点表达式

>传统表达式是@Pointcut("execution(方法返回类型 包名.类名.方法(形参))")  
>使用切入点表达式可以更简便高效的定义切入点

### * 表达式

>匹配任意的包,类,方法,参数

```kotlin
//@Pointcut("execution(void com.setruth.dao.impl.DaoImpl.show())")
@Pointcut("execution(void com.setruth.dao.impl.DaoImpl.*())")
@Pointcut("execution(void *.*.*.*.*.*())")
@Pointcut("execution(* *.*.*.*.*.s*())")
@Pointcut("execution(* *.*.*.*.*.*w())")
```


### .. 表达式

>任意数量

```kotlin
//@Pointcut("execution(void com.setruth.dao.impl.DaoImpl.show())")
@Pointcut("execution(void com..show(..))")
```

## 通知类型

@Before

>在切入点执行前执行

@After

>在切入点执行完执行

@Around

>在切入点中执行
>
>>使用时会覆盖原有内容，需要操作回调方法执行切入点的方法

@AfterThrowing

> 在抛出异常时执行

```kotlin
//测试类
@Component
class T{
    fun show(){
        println("显示")
    }
}

//定义通知类
@Component
@Aspect 
class MyAdvice {

    @Pointcut("execution(void com..T.show())")
     fun pt(){}
    //描述在这个通知在哪个位置运行下面的方法
    @Around("pt()")
    fun showDate(){
        print("A")
        print("B")
    }
}

//main
t.show() //只会打印A B

//要使用回调函数

@Around("pt()")
fun showDate(proceedingJoinPoint: ProceedingJoinPoint){
    print("A")

    proceedingJoinPoint.proceed() //使用切入点的方法，如果有返回值可以在这里获取
    print("B")
}
```

## 通知类型获取数据

> @Around可通过proceedingJoinPoint的args方法获取参数

```kotlin
@Around("pt()")
fun showDate(proceedingJoinPoint: ProceedingJoinPoint){
    //这里的args是一个object数组，按照传入的参数顺序传入
     val args = proceedingJoinPoint.args
}
```

> @AfterReturning获取返回的值

```kotlin
@AfterReturning(returning = "value",pointcut = "pt()")
//这里的参数名要和注解returning的名字一样 
//如果参数中有JoinPoint 必须放在第一位
fun showRe(value:Any){
    print(value)
}
```

>@Before可以传入JoinPoint类型的参数通过args获取参数

```kotlin
@Before("pt()")
fun showBefore(joinPoint:JoinPoint){
    var args=joinPoint.args
}
```

>@AfterThrowing

```kotlin
@AfterThrowing(returning = "value",throwing = "pt()")
//这里的参数名要和注解throwing的名字一样 
//如果参数中有JoinPoint 必须放在第一位
fun showRe(value:Exception){
    print(value)
}
```

