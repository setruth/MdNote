# SpringMVC获取请求参数

## 地址携带参数

### 普通获取参数

> springMVC请求参数只需要在@RequestMapping注解得方法里写参数，然后对应参数名字传参就行

```kotlin
@RequestMapping("/login")
fun login(account: String, password: String) {
    print("$account $password") //setruth root
}
```

> localhost:1028/springmvc/login?account=setruth&password=root 

### 获取数组参数

```kotlin
@RequestMapping("at")
@ResponseBody
fun arrary(array: Array<String>):String{
    array.forEach {
        println(it) //123 456
    }
    return "123"
}
```

> localhost:1028/springmvc/user/at?array=123&array=456

### 获取集合

```kotlin
@RequestMapping("list")
@ResponseBody
/*和数组的获取一样，但是因为list是一个引用类，springmvc不知道如何赋值，
所以在参数前加上RequestParam注释，告诉springMVC把请求的参数全部给list赋值*/
fun setList(@RequestParam list:List<String>){
        list.forEach {
            println(it)
        }
}
```

### 获取自定义对象

> 现在有个自定义类

```kotlin
class USerInfo {
    var name: String = ""
    var number: Int = 0
    var userAddress: UserAddress = UserAddress()
    override fun toString(): String {
        return "USerInfo(name='$name', number=$number，${userAddress.toString()})"
    }

}
```

> 里面的userAddress

```kotlin
class UserAddress {
    var province: String = ""
    var city: String = ""
    override fun toString(): String {
        return "UserAddress(province='$province', city='$city')"
    }
}
```

> controller中的请求

```kotlin
@RequestMapping("/register")
@ResponseBody
fun registerUser(userinfo: USerInfo):String{
    println(userinfo.toString())
    return "注册成功"
}
```

> 请求的时候直接写类中的属性名字即可，如果类中还有别的引用类，那就别的类的变量名.别的类属性名

localhost:1028/springmvc/user/register?name=setruth&number=123&userAddress.province=贵州省&userAddress.city=毕节市

> 控制台USerInfo(name='setruth', number=123，UserAddress(province='贵州省', city='毕节市'))

### 注意

>正常传参，请求参数的key就是函数的参数名字，如果嫌麻烦，可以使用@RequestParam指定传入参数的名称

```kotlin
//例如
@RequestMapping("/login")
@ResponseBody
//这时传入的参数key就要写RequestParam指定的名称，会把key的值给对应的参数
fun login(@RequestParam("account") account: String,@RequestParam("pwd") password: String): LoginMessage {
   .....
}
```

## 请求体携带参数

> 如果你直接调到这部分学习，那么前提是你最好掌握了普通的地址携带参数的获取

### 1.使用@EnableWebMVC注解

>EnableWebMvc注解可以对传入的JSOn字符串来解析到参数中，使用Jackson库的API进行操作

### 2.添加jackson库

```groovy
// https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind
implementation("com.fasterxml.jackson.core:jackson-databind:2.13.2.2")
```

### 使用

> 在参数的前面添加@RequestBody  就可以获取请求体中的参数

> 发起请求的时候和上面的方式一样 只是写的是json的字符串

#### 注意 

> 因为请求体只有一个，所以@RequestBody注解只能写一个，如果是多个参数可以用一个Map<String,Any>来获取

```kotlin
//比如上面登录的例子
@RequestMapping("/login")
@ResponseBody
//使用@RequestBody注解, 把原来的account:String,password:string改造为Map
fun login(@RequestBody loginInfo:Map<String,Any>) {
    println("${loginInfo.get("account")} ${loginInfo.get("password")}")

}
```

```json
//请求的json数据
{
    "account":"Setruth",
    "password":"123"
}
```

### 获取自定义对象参数

> 现在有个自定义类

```kotlin
class USerInfo {
    var name: String = ""
    var number: Int = 0
    var userAddress: UserAddress = UserAddress()
    override fun toString(): String {
        return "USerInfo(name='$name', number=$number，${userAddress.toString()})"
    }

}
```

> 里面的userAddress

```kotlin
class UserAddress {
    var province: String = ""
    var city: String = ""
    override fun toString(): String {
        return "UserAddress(province='$province', city='$city')"
    }
}
```

> controller中的请求

```kotlin
@RequestMapping("/register")
@ResponseBody
fun registerUser(@RequestBody userinfo: USerInfo):String{
    println(userinfo.toString())
    return "注册成功"
}
```

```json
{
    "name":"Setruth",
    "number":1234566,
    "userAddress":{
        "province":"贵州省",
        "city":"贵阳市"
    }
}
```

## 获取时间

> 定义参数的类型是Date即可，开启了@EnableWebMVC注解后会自动转换到参数中
>
> 默认的格式是xxx/xxxx/xxx ’/‘的格式

```kotlin
@RequestMapping("/date")
@ResponseBody
fun getDate(date1: Date){
    println(date1)
}
```

### 自定义时间格式

```kotlin
fun getDate(date1: Date,@DateTimeFormat(pattern = "yyy-mm-dd hh:mm:ss") date2: Date):String{
    println(date1)
    println(date2)
    return "获取到了时间"
}
```

> localhost:1028/springmvc/user/date?date1=1222/12/12&date2=2022-12-12 8:23:12

### 获取请求体中的时间

```kotlin
//因为只能有一个请求体，所以把多个参数放在一个自定义的pojo中
class MyDate {
    lateinit var date1: Date
    //因为是解析Json字符串中的日期所以要用@JsonFormat
    @JsonFormat(pattern = "yyy-mm-dd hh:mm:ss")
    lateinit var date2: Date
}
```

```json
{
    "date1":"2022-12-12",
    "date2":"2022-12-12 12:12:12"
}
```

## 关于中文的乱码问题

> 在获取中文的时候是乱码

> 在web的配置文中(继承的是AbstractAnnotationConfigDispatcherServletInitializer类)重写过滤器getServletFilters函数即可

```kotlin
override fun getServletFilters(): Array<Filter>? {
    val filter=CharacterEncodingFilter()
    //给过滤器配置解码使用UTF-8
    filter.encoding="UTF-8"
    return arrayOf(filter)
}
```

