### Spring Web MVC

> 记录于阅读 Spring MVC 官方文档 ，链接：https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-servlet

- `the very beginning ` 一开始
- `covers ` n.覆盖 v.包涵
- `baseline information and compatibility ` 基线信息和兼容性
- `the front controller pattern ` 前端控制器模式
- `declared` adj.公然的 v.断然，宣称
- `according`adj. 相符的；相应的；一致的 v. 给予；符合，一致；
- `diverse` adj. 不同的，相异的；多种多样的
- `in addition to` 除...之外

### DispatcherServlet

```wiki
Spring MVC, as many other web frameworks, is designed around the front controller pattern where a central Servlet, the DispatcherServlet, provides a shared algorithm for request processing, while actual work is performed by configurable delegate components. This model is flexible and supports diverse workflows.

The DispatcherServlet, as any Servlet, needs to be declared and mapped according to the Servlet specification by using Java configuration or in web.xml. In turn, the DispatcherServlet uses Spring configuration to discover the delegate components it needs for request mapping, view resolution, exception handling, and more.
```

翻译：Spring MVC，和大多数web框架一样，是围绕着前端控制器模式设计的，有一个中心的Servlet ->`DispatcherServlet`，提供请求处理的共享算法`（即分发请求）`，真正工作的是由可配置委托组件执行，该模型灵活并支持多种工作流.

**`DispatcherServlet`，和其他Servlet一样，需要使用`JAVA Configuration`或在`web.xml`根据Servlet规范声明和映射**，然后，`DispatcherServlet`使用spring配置来发现请求映射、视图解析、异常处理等所需的委托组件。



