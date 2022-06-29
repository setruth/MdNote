# springmvc的响应

## 响应页面

> 不用@ResponseBody注解
>
> 返回字符串
>
> 返回的内容写页面名字即可

> webapp下有个hello.jsp即可

```kotlin
@RequestMapping("/login")
fun login():String {
    return "hello.jsp"
}
```

## 响应普通文本

> 加上@ResponseBody注解，标识你返回的数据就是返回体的内容

```kotlin
@RequestMapping("/login")
@ResponseBody //如果控制器是RestController就不用
fun login():String {
    return "这是响应的内容"
}
```

## 响应JSON字符串

> spring的配置文件开启@EnableWebMvc即可

> 这时返回的对象，springmvc就会自动转为json字符串

> 记得添加jackson库，spring是使用这个库进行转换的
>
> ```groovy
> implementation("com.fasterxml.jackson.core:jackson-databind:2.13.2.2")
> ```

```kotlin
//自定义的类
data class LoginMessage(var status:Int, var message:String )
@RequestMapping("/login")
@ResponseBody //如果控制器是RestController就不用
fun login():LoginMessage {
    //开启EnableWebMvc注解后直接返回对象即可
    return LoginMessage(500,"登录失败，账号或者密码错误")
}
```