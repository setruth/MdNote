# 提高Kotlin开发效率的一些技巧 let,also...

- [提高Kotlin开发效率的一些技巧 let,also...](#提高kotlin开发效率的一些技巧-letalso)
  - [let](#let)
  - [also](#also)
  - [with](#with)
  - [run](#run)
  - [apply](#apply)
- [结语](#结语)

## let

>let是一种作用域函数具体用法常用于变量的空值判断，以及在作用域中访问对象的属性
>
>let作用域中的it代表使用let函数的变量，最后一排的数为返回值

```kotlin
var i?:String

i.let{
    //这里的it代表i并且it为val不可变
}

/*使用let判断是否为空*/
i?.let{
    //当i不为空的时候运行作用域函数中的内容
}

/*let的返回值*/
i?.let{
    3 //此时的返回值为3
}

/*使用实例*/
i=1
i=i?.let{
  3 //或者return 3
}
println(i) 此时的i为3
```

实例对比java的写法

```java
int i;
if(i!==0){
    i=3;
}
System.out.println(i);
```

## also

>also 与let的用法大致相同，但是also的返回值是使用also作用域函数的对象，而不像let一样返回值是在最后一排
>
>如果不使用返回值，let与also的用法基本是一样的


```kotlin
var i:String
var b=i.also{
    //返回值就是i本身
}
//此时的b就是i
```

## with

>使用同个对象的多个属性和方法时建议使用with函数，能省去对象名直接写属性或者方法

```kotlin
class Test{
    var i=1;
    var b=2;
}
val test=Test()
with(test){
    println(" i=$i b=$b") //i=1 b=2
}
```

对比java的写法

```java
class Test{
    int i=1;
    int b=2;
}
Test test=new Test()
System.out.println(" i="+test.i+" b="+test.b) //i=1 b=2
```

## run

>run函数可以看作是let与with的融合
>
>可以做空判断而且可以直接使用其中的属性，并且最后一行也可以写返回

```kotlin
class Test{
    var age=10;
}

fun getAge(test:Test?):Int{
    var age=-1;
    age= test?.run{
        println("传入的对象不为空")
        age
    }
    return age
}

println(getAge(null)) //-1
println(getAge(Test())) //10
```

## apply

>和run的用法相识，就和let与also一样，没有最后一行的返回值，返回的就是使用函数的对象本身

```kotlin
class Test{
    var age=10;
}

fun jude(test:Test?){
    val age= test?.apply{
        println("传入的对象不为空")
    }
    //此时的age就是使用作用域函数的test
}

```

# 结语

>使用判空的时候可以使用let或者also区别就是返回值的问题
>
>频繁连续的使用一个对象的属性方法时建议使用with避免重复代码
>
> run与apply与with效果差不多，但是存在返回值并且返回值方式不同
