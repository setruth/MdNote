# Kotlin使用Spring框架注入bean失败
> 报错:java.lang.ClassNotFoundException: kotlin.reflect.KotlinReflectionInternalError  
>
>解决:升级Jackson版本到2.9.2

```groovy
implementation "com.fasterxml.jackson.module:jackson-module-kotlin:2.9.5"
```
