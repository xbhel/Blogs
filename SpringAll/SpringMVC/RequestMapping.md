### `@RequestMapping`

> 介绍`Spring Web MVC`中核心注解之一 —— `RequestMapping`，简单的说，该注解用于映射`web request`到`controller`中的方法，表示被标记的方法用于处理`web request`。

#### 基本使用

如下为`@RequestMapping`的简单示例，将HTTP请求`/simple`映射到`controller`中的`simple()`方法：

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

#### 类级别映射

当然我们也可以定义类级别的请求映射，只需在类级别上使用`@RequestMapping`注解，此时`controller`中的所有请求处理方法都会映射为相对于类级别，示例如下：

``` java
//类级别映射
@RequestMapping("/requestMapping")
@Controller
public class RequestMappingController {
    @ResponseBody
    //方法级别未指定映射路径，将继承类级别映射路径
    @RequestMapping
    public String simple(){
        return "simple";
    }
}
// url : http://127.0.0.1:8080/requestMapping
// output : simple
```

当方法和类同时存在该注解时，方法上的注解会对类级别的注解声明进行补充，即将类级别与方法级别组合：

``` java
@RequestMapping("/requestMapping")
@Controller
public class RequestMappingController {
    @ResponseBody
    //组合类级别、方法级别
    @RequestMapping("/simple")
    public String simple(){
        return "simple";
    }
}
// url : http://127.0.0.1:8080/requestMapping/simple
// output : simple
```

#### `@RequestMapping - vaule | path`

`@RequestMapping`提供了`value | path`属性来指定请求映射路径，示例如下：

``` java
//使用value属性来指定映射路径
@ResponseBody
@RequestMapping(value = "/useValue")
public String useValue() {
   return "Hello MVC!";
}
//url : http://127.0.0.1:8080/useValue
//output : Hello MVC!

//使用path属性来指定映射路径
@ResponseBody
@RequestMapping(path = "/usePath")
public String usePath() {
   return "Hello MVC!";
}
//url : http://127.0.0.1:8080/usePath
//output : Hello MVC!
```

`value | path`属性为`String[]`类型，即可为一个请求方法指定多个映射路径，示例如下：

```java
//使用value属性指定多个请求路径，`value`为注解默认属性,当只有一个属性时value名称可以省略
@ResponseBody
@RequestMapping({"/value1","value2"})
public String multiMapPathByValueAttr(){
   return "multiMapPathByValueAttr";
}
//url : http://127.0.0.1:8080/value1 or http://127.0.0.1:8080/value2
//output : multiMapPathByValueAttr

//使用path属性指定多个请求路径
@ResponseBody
@RequestMapping(path = {"/path1", "path2"})
public String multiMapPathByPathAttr() {
   return "multiMapPathByPathAttr";
}
//url : http://127.0.0.1:8080/path1 or http://127.0.0.1:8080/path2
//output : multiMapPathByPathAttr
```

#### `@RequestMapping - method`

`@RequestMapping`默认支持所有的`HTTP Method`(`即DispatcherServlet默认支持映射的HTTP Method`)类型映射，`RequestMapping`允许我们通过`method`属性来限制`HTTP Method`类型，示例如下：

``` java
//限制请求处理方法仅仅处理GET类型请求
@ResponseBody
@RequestMapping(value = "/onlyGet", method = RequestMethod.GET)
public String onlyGet() {
   return "Hello MVC!";
}
//HTTP Method : GET
//url : http://127.0.0.1:8080/onlyGet  
//output : multiMapPathByPathAttr
```

#### `@RequestMapping - headers`

我们还可以通过`headers`属性来指定请求头，从而进一步缩小映射范围，示例如下：

``` java
//http 请求头中需指定 key1:val 字样，若请求头无该字样，则请求处理方法不会执行
@ResponseBody
@RequestMapping(value = "/setHeader", method = RequestMethod.GET, headers = "key1=val")
public String setHeader() {
   return "Hello MVC!";
}
//使用Postman测试，Header栏中添加 key1: val 
//url : http://127.0.0.1:8080/setHeader 
//output : Hello MVC!
```

`headers`属性为数组类型，即允许指定多个请求头，示例如下：

``` java
@ResponseBody
@RequestMapping(value = "/setMultiHeader", method = RequestMethod.GET, headers = {"key1=val", "key2=val2"})
public String setMultiHeader() {
   return "Hello MVC!";
}
//使用Postman测试，Header栏中添加 key1: val ，key2: val2
//url : http://127.0.0.1:8080/setMultiHeader 
//output : Hello MVC!
```

需要注意的一点，`headers`属性的匹配方式为包含，即只需前端请求的请求头中包含`headers`属性中指定的请求头即可，如访问`http://127.0.0.1:8080/setHeader `，此时请端自定义请求头为`key1: val ,key2: val2`，同样可以访问成功！

#### `@RequestMapping - Consumes & Produces `



