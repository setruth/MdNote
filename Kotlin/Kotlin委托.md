# Kotlin委托

## 介绍

> 使用by委托关键字可以使接受到的操作的对象用于把操作委托与另一个对象去处理
>
> 与传统的继承大同小异，而且可以使得代码更加的美观简洁

## 类的委托

```kotlin
//一个基础的接口
interface T {
    fun print()
}
```

```kotlin
//一个实现类
class Base :T{
    override fun print() {
        println("打印")
    }
}
```

```kotlin
//另一个继承实现T接口的类
class Impl(base:Base) : T by base{
   /*
    * 这里不用再去实现T接口中的print方法
    * 因为其中的需要重写的方法已经委托给传入的base类实现了
    * 但是如果自己定义的方法内容或者属性，不能在类委托里面委托给别的类实现
    * 也不能直接调用委托的类里面它自己的方法
    * */
   /*
    * 如果类中已经重写了方法
    * 那调用的时候不会把操作委托给Base类 
    * 而是调用自己定义的方法
    * */
}
```

```kotlin
fun main() {
   var t=Impl(Base())
    println(t.print())//打印
}
```

## 属性的委托

> ```kotlin
> val/var 变量名: 数据类型(可选) by 委托给xxx
> ```

> by后面的就是指定委托给的对象，会把变量的get和Set方法委托给被委托的对象的get set ，被委托的对象必须提供getValue setValue方法

```kotlin
//定义被委托的对象
class Attribute {
    //重载提供getValue方法
    /**
     *  t2:哪个对象给的委托
     *  property.name 委托的属性名称
     */
    operator fun getValue(t2: T2, property: KProperty<*>): String {
        return name
    }
	//重载提供getValue方法
    /**
     *  t2:哪个对象给的委托
     *  property.name 委托的属性名称
     *  any 设置的值
     */
    operator fun setValue(t2: T2, property: KProperty<*>, any: String) {
        name=any
    }
    var name="lihua"

}
```

## 标准委托

### Lazy 延迟属性

#### 函数源码形式

> ```kotlin
> public fun <T> lazy(initializer: () -> T): Lazy<T> = SynchronizedLazyImpl(initializer)
> ```

#### 介绍

> lazy是一个kotlin标准库中的函数，其中的参数是传入一个无参的匿名函数(也就是lambda表达式)
>
> 通过处理传入的函数，返回一个Lazy<T>类型的数据
>
> 当使用lazy委托后，第一次使用,getter执行传递给lazy的lambda表达式，并储存返回结果,之后访问get返回储存的结果

#### 使用

```kotlin
var a=lazy{
    print("你好")
    123
}
//此时是一个class kotlin.SynchronizedLazyImpl对象，要使用value方法获取值
println(a) //Lazy value not initialized yet
println(a.value) //你好123,第一次调用getter会执行lambda的表达式内容，返回储存结果
println(a.value) //123 第二次只会获取返回结果


// by lazy
//用by lazy标识委托属性是延迟委托增强的属性,不会执行lambda的表达式内容，而是直接返回值，也就是a.value，
// 因为class kotlin.SynchronizedLazyImpl value只提供了get方法而没有set方法，所以只能是val
val a by lazy { //必须是val
    "你好"
    123
}
println(a) //123


```

### 可观察属性Observable

#### 函数源码

```kotlin
public inline fun <T> observable(initialValue: T, crossinline onChange: (property: KProperty<*>, oldValue: T, newValue: T) -> Unit)
```

#### 介绍

> 这是一个内联函数，可以通过它来观察委托的变化

> 第一个参数是初始化的值,第二个参数是一个带有三个参数的回调函数，用于在值发生变化的时候触发
>
> 回调函数的第一个值是属性
