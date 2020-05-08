### `SpringMVC `常用注解

> 介绍 `SpringMVC `的常用注解

#### 控制器组件注解：@Controller 

`@Controller`用于标记一个类，表示该类是一个`SpringMVC`控制器类，用于处理请求，分发处理器`DispatcherServlet`会扫描该类中的方法，并将使用了`@RequestMapping `注解的方法作为处理请求处理器，示例如下：

``` java
//表示HelloController是一个控制器
@Controller
public class HelloController {

    //@RequestMapping 用于映射请求，表示将'/hello'URL的请求交给hello()方法处理
    //@ResponseBody 表示以JSON格式返回数据
    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello SpringMVC!";
    }
    
}
```

#### 参数绑定注解

`SpringMVC`中用于参数绑定的注解有很多，都在`org.springframework.web.bind.annotation`包中，根据它们处理`request`的不同内容部分可以分为6类：

- 处理请求参数和内容部分的注解
  - `@RequestMapping`
  - `@GetMapping`
  - `@PostMapping`
  - `@PutMapping`
  - `@DeleteMapping`
  - @PatchMapping
  - @RequestParam
  - @RequestBody
  - @ResponseBody
  - @RequestPart （用于绑定`multipart/formdata`），常用于文件上传
  - @RestController
- 处理URL部分的注解
  - @PathVariable
  - @MatrixVariable
  - @CrossOrigin
- 处理请求头的注解
  - @RequrstHeader
  - @CookieValue
- 处理属性类型的注解
  - @RequestAttribute
  - @SessionAttribute
  - @SessionAttributes
  - @ModelAttribute
- 处理异常类型的注解
  - @ExceptionHandler
  - @ControllerAdvice
  - @RestContorllerAdvice
  - @ResponseStatus
- 绑定表单数据的注解
  - InitBinder (常用于数据类型转换)

#### `@RequestParam`

`@RequestParam`用于绑定`Servlet`请求参数（查询参数，表单参数)至`controller`形参，简单示例：

```java
@ResponseBody
@GetMapping(value = "/book")
public String getBook(@RequestParam String id){
   return "ID: " + id;
}
//url : http://localhost:8080/book?id=001
//output : ID: 001
```

如上所示，我们使用`@RequestParam`绑定了查询参数`id`，这是由于查询参数名称和`controller`形参名称一致，当两者名称不一致时，`@RequestParam`提供了`name | value`属性去指定查询参数名称，示例如下：

```java
@ResponseBody
@GetMapping(value = "/book")
public String getBook(@RequestParam(value = "id") String bookId, @RequestParam(name = "name") String bookName){
   return "ID: " + bookId + " Name: " + bookName;
}
//url : http://localhost:8080/book?id=001&name=Java
//output : ID: 001 Name: Java
//在注解中只有默认属性value时也可以简写为 @RequestParam(id")
```

当我们使用`@RequestParam`绑定查询参数至`controller`形参时，该参数默认是必需的；`@RequestParam`提供了`required`属性去指定该参数是否必需，默认为`true`即必需，`false`为可选，示例如下：

```java
@ResponseBody
@GetMapping(value = "/book")
public String getBook(@RequestParam(required = false) String id){
   return "ID: " + id;
}
//url : http://localhost:8080/book
//output : ID: null
//当没有指定参数时，id绑定为null
```

在`JDK8+`中，我们也可以使用`java.util.Optional` 去包装形参，从而实现可选，示例如下：

```java
@ResponseBody
@GetMapping(value = "/book")
public String getBook(@RequestParam Optional<String> id){
   return "ID: " + id.get();
}
//url : http://localhost:8080/book
//output : ID: null
```

我们还可以为形参设置默认值，`@RequestParam`提供了`defaultValue`属性去设置默认值，此时，`required`属性会自动置为`false`，示例如下：

```java
@ResponseBody
@GetMapping(value = "/book")
public String getBook(@RequestParam(defaultValue = "C") String bookName){
   return " Name:" + bookName;
}
//url : http://localhost:8080/book
//output : Name: C
```

`@RequestParam`也允许我们通过 `Map`或`MultiValueMap`去绑定多个参数，如果没有在注解中指定参数命名，那么默认会绑定所有，并会将请求中每一个参数的名称和值填充到`Map`中，示例如下：

```java
//使用Map,同名参数只会绑定第一个参数的值
@GetMapping(value = "/useMap")
public @ResponseBody Map<String,String> useMap(@RequestParam Map<String,String> map){
    return map;
}
//url : http://localhost:8080/useMap?id=001&name=Java&name=C
//output : 
{
    "id": "001", 
    "name": "Java"
}     

//使用MultiValueMap,同名参数会绑定所有值并填充到`List`中，由于默认输出MultiValueMap格式并不是标准Json格式，所以我这里做了转换
@GetMapping(value = "/useMultiValueMap")
public @ResponseBody Map<String,List<String>> useMultiValueMap(@RequestParam MultiValueMap<String,String> map){
    Map<String,List<String>> responseMap = new HashMap<>();
  	for(Map.Entry<String, List<String>> entry : map.entrySet()){
  		responseMap.put(entry.getKey(),entry.getValue());
  	}
  	return responseMap;
}
//url : http://localhost:8080/useMultiValueMap?id=001&name=Java&name=C
//output : 
{
    "name": [
        "Java",
        "Spring"
    ],
    "id": [
        "001"
    ]
}
```

使用`Map`和`MultiValueMap`去绑定参数时，它们之间有些不同：**对于同名参数，`Map`只会绑定第一个的值，而`MultiValueMap`则会绑定所有值并填充到`List`中**，这是由于它们的数据结构决定的，`MultiValueMap`结构如下：

```java
public interface MultiValueMap<K, V> extends Map<K, List<V>> {....}
```

`@RequestParam`也允许我们将指定参数的多个值绑定到一个列表，通过`List`，多个值使用`,` 分隔或定义多次，示例如下：

```java
@GetMapping(value = "/useList")
public @ResponseBody List<String> useList(@RequestParam List<String> id){
	return id;
}
//url : http://localhost:8080/useMultiValueMap?id=1,2,3
//url : http://localhost:8080/useMultiValueMap?id=1&id=2&id=3
//如上两个url将产生相同的结果，output : 
[
    "1",
    "2",
    "3"
]
```





