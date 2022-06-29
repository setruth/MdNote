# Java快速的转Kotlin

- [Java快速的转Kotlin](#java快速的转kotlin)
  - [前言](#前言)
    - [1.发展由来](#1发展由来)
    - [2.kotlin的简单介绍](#2kotlin的简单介绍)
    - [3.kotlin的优势](#3kotlin的优势)
  - [快速转换](#快速转换)
    - [1.在变量和对象的创建上](#1在变量和对象的创建上)
    - [2.条件与循环](#2条件与循环)
      - [1.条件](#1条件)
        - [If表达式](#if表达式)
        - [区间](#区间)
        - [when表达式](#when表达式)
      - [2.循环](#2循环)
        - [For循环](#for循环)
        - [while 和do while循环](#while-和do-while循环)
    - [3.在类的创建上](#3在类的创建上)
      - [kotlin的两种构造器](#kotlin的两种构造器)
        - [主构造器](#主构造器)
        - [次构造器](#次构造器)
      - [init](#init)

## 前言

### 1.发展由来

>kotlin是16年由JetBrains开发的编程语言，在19年的谷歌I/O大会上宣布Kotlin正式作为安卓开发的主要语言，并且在几年的趋势中也能看出谷歌对于kotlin的推荐，比如AndroidStudio默认首选的是kotlin，谷歌的安卓开发文档默认显示的也是kotlin的开发方式

### 2.kotlin的简单介绍

>kotlin可以看作是java语言的高度封装拥有着java的特性也优化了java的内容并且新增了自己的特点

### 3.kotlin的优势

- 完全兼容java语言
- 避免空指针造成程序出错
- 更少的代码量
- 比java8更好的支持lambda表达式
- 聪明的类型推断
- 默认参数
- 许多的扩展函数更加灵活

>用一个安卓开发中的监听例子你应该就比较了解了

 ```kotlin
//kotlin的
button.setOnClickListener{
    ......
}
```

```Java
//java的
button.setOnClickListener(new View.OnClickListener(){
    @override
    public void onClick(View view){
        ......
    }
});
```

## 快速转换

### 1.在变量和对象的创建上

>kotlin是 var(创建变量)/val(创建常量) 变量名 : 变量类型 = 初始值  
>kotlin允许不用分号作为语句的结束语

```kotlin
//变量的创建
var i:Int=1
/*由于kotlin具有优秀的类型推导机制，所以创建对象或者变量的的时候初始化值的话可以不用写哪种类型，kotlin会自动推导*/
//可以简写为
var i=1
```

>kotlin对象的创建和java一样但是不用new关键字

```kotlin
//对象的创建
var test:Test=Test() 
//由于上面说过kotlin拥有优秀的类型推导机制，所以我们可以简写为
var test=Test()
```

### 2.条件与循环

#### 1.条件

##### If表达式

>kotlin的if表达式和java语法基本一摸一样，只是kotlin的if具有返回的特性  
>kotlin的if返回值就是代码块的最后一行，而且必须带上else才能使用(可以多个else)

```kotlin
var a=10
var b=20
var res=if(a>b){
    1
}else{
    2
}
print(res) //2
```

##### 区间

>一种范围的定义，左闭右开(x...y)

```kotlin
var i=10
var res=if(i in 1...10){ //使用in关键字判断i是否在区间内
    true
}else{
    false
}
print(res) //false
```

##### when表达式

>与java不同 kotlin抛弃了switch而使用when表达式，但是这两者的效果都是差不多的

```kotlin
var i=1
when(i){
    1-> print("i是1")
    2-> print("i是2")
    else-> print("i不是1也不是2")
}
//最后结果是:1是1
/*这里的else相当于是switch中的default，当上述不满足的时候才会运行这部分*/
```

>一些别的用法

```kotlin
//判断类型
var i=1
when(i){
    is Int-> print("i是Int类型")
    is Float-> print("i是Float类型")
    else -> print("i不是Int也不是Float")
} /*最后输出:i是Int类型 这里的is相当于java中的instanceof关键字*/

//判断年纪
var age=20
var res=when{
    age>15&&age<18 ->"青少年"
    age>=18&&age<35 ->"成年人"
    age>=35&&age<50 ->"中年人"
    age>=50&&age<100 ->"老人"
    else ->"找不到年龄范围"
}
print(res) //成年人
/*when表达式也和if一样具有返回值，在一些情况下它可能比if还要好用*/
```

#### 2.循环

##### For循环

##### while 和do while循环

### 3.在类的创建上

>kotlin与java一样也是使用(class关键字 类名{}) 创建对象  

```kotlin
class Test{}
```

#### kotlin的两种构造器

##### 主构造器

>主构造器是在类名后面写constructor关键字然后后面跟着括号写参数

```kotlin
class Test constructor(i:Int) {}
```

>如果主构造器没有注解和可见修饰符，那么constructor关键字可以省略

```kotlin
//上面的创建可以写为
class Test(i:Int) {}
```

##### 次构造器

>在类中写constructor关键字创造次构造器(次构造器能有多个，主构造器只能有一个)

```kotlin
 class Test { 
    constructor(i: Int) {
        xxxx
    }
}
```

>如果写次构造器的时候有主构造器，那么次构造器都要使用主构造器

```kotlin
class Test(int:i) { 
    constructor(i: Int,b:Int):this(i) { //通过this关键字使用主构造器
        xxxx
    }
}
```

>如果主构造器的参数有默认值，当实例化不传入新的值时会使用这个默认值来创建对象

```kotlin
class Test(int:i=1) { 
    var i=i
}
var test=Test()
print(test.i) //1
```

#### init

>对于kotlin的类来说，在创建对象的时候会运行init函数，相当于对类的初始化，可以在类中增加init函数来初始化类的一些信息

```kotlin
class Test() { 
    init{
        print("初始化类开始")
    }
}
var test=Test() //初始化类开始
```
