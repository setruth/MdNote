# REST风格开发

## 介绍

> 以请求方式来区分访问的模式

## 对比

### 传统的请求

> localhost:8080/user/info?id=1

> localhost:8080/user/delete

> localhost:8080/user/register

### REST风格请求

> localhost:8080/user/1 GET

> localhost:8080/user/	DELETE

> localhost:8080/user/	POST

## 优点

> 隐藏访问资源地址，无法通过地址对资源进行操作

> 简化地址开发

## 推荐规则

>* GET(查询)
>* POST(新增/保存)
>* PUT(修改/更新)
>* DELETE(删除)
>
>规则只是建议的做法，实际还是看个人习惯

## 实现

> 这是一个简单的控制器
>
> ```kotlin
> @RestController
> @RequestMapping("/user")
> class UserController {
>     @RequestMapping( "login")
>     fun addUser(){
>     }
>     @RequestMapping("delete")
>     fun deleteUser(){
>     }
> }
> //访问要通过/user/login,/user/login来访问
> ```

> 在@RequestMapping注解中使用method指定这个请求是什么请求方法才可以访问
>
> 更改为REST风格的请求
>
> ```kotlin
> @RestController
> class UserController {
>     @RequestMapping( "user", method = [RequestMethod.POST])
>     fun addUser(){
>     }
>     @RequestMapping("user", method = [RequestMethod.DELETE] )
>     fun deleteUser(){
>     }
> }
> //此时访问/user即可，使用POST走的是addUser函数，DELETE是deleteUser函数
> ```

> 如果有很多个请求，那么就要写很多个"user" 和请求方式会非常麻烦
>
> 简化请求
>
> ```kotlin
> @RestController
> @RequestMapping("user")
> //把这个类下面的函数都配置为user,在不一样的函数上写方法即可区分
> class UserController {
>     @PostMapping
>     fun addUser(){
>     }
>     @DeleteMapping
>     fun deleteUser(){
>     }
> }
> ```

## 隐藏传递的地址参数

> 比如获取/user/1 路径上的1
>
> ```kotlin
> @RestController
> @RequestMapping("user")
> class UserController {
>     @RequestMapping( method = [RequestMethod.GET] )
>     fun getAll(){
>         
>     }
>     //要在请求的路径后面写上花括号里面加你函数参数的名字来获取
>     @RequestMapping("/{id}", method = [RequestMethod.GET])
>     //@PathVariable标志该参数是从路径上获得
>     fun getById(@PathVariable id:Int){
>         println(id)
>     }
> }
> // /user/1 控制台打印1
> //上面的RequestMapping getById 虽然是相同的请求方式，但是请求的路径不一样，也可以用于区分
> ```
