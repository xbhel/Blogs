`@RequestMapping`

> 介绍`Spring Web MVC`中核心注解之一 —— `RequestMapping`，简单的说，该注解用于映射`web request`到`controller`组件中的方法。

如下为`@RequestMapping`的简单示例，将HTTP请求`/simple`映射到`controller`组件中的`simple()`：

```java
public class RequestMappingController(){
    @ResponeseBody
    @RequestMapping("/simple")
    public String simple(){
        return "simple";
    }
}
// url : http://127.0.0.1:8080/simple
// output : simple
```

