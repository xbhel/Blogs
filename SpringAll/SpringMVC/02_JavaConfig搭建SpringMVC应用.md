### JavaConfig 搭建 SpringMVC 应用

> 基于 JavaConfig 搭建 SpringMVC 应用，Demo环境：`IDE：IDEA`，`Web服务器：Tomcat`，`构建工具：Maven`

- 第一步：创建一个 `Maven` 项目，这里就不再演示了~
- 第二步：`pom.xml`中添加如下依赖

``` java
<dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.1.5.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <scope>provided</scope>
    </dependency>
  </dependencies>
```

- 第三步：配置前端控制器`DispatcherServlet`

  - **通过实现`WebApplicationInitializer`接口**，通过`ServletContext`注册并初始化`DispatcherServlet`.

    ```java
    public class MyWebApplicationInitializer implements WebApplicationInitializer {
    
        @Override
        public void onStartup(ServletContext servletCxt) {
    
            // Load Spring web application configuration
            AnnotationConfigWebApplicationContext ac = new AnnotationConfigWebApplicationContext();
            //AppConfig 是 spring 配置文件,相当于applicationContext.xml
            ac.register(AppConfig.class);
            ac.refresh();
    
            // Create and register the DispatcherServlet
            DispatcherServlet servlet = new DispatcherServlet(ac);
            ServletRegistration.Dynamic registration = servletCxt.addServlet("dispatcher", servlet);
            registration.setLoadOnStartup(1);
            registration.addMapping("/");
        }
    }
    ```

    

  - 除了使用`ServletContext`注册`DispatcherServlet`外，还可以通过继承`AbstractAnnotationConfigDispatcherServletInitializer`并重写指定的方法，通过查看它的源码，可以发现它的继承链上实现了`WebApplicationInitializer`

    ```java
    public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    
        @Override
        public void onStartup(ServletContext servletContext) throws ServletException {
            super.onStartup(servletContext);
            //配置编码过滤器
            FilterRegistration.Dynamic encodingFilter = servletContext.addFilter("encodingFilter", encodingFilter());
            encodingFilter.setAsyncSupported(true);
            //filter映射的类型：若为 null 默认就是 DispatcherType.REQUEST
            //isMatchAfter true:表示当前过滤器在任何声明的过滤器映射之后匹配 ，false 在任何声明的过滤器映射之前匹配
            //urlPatterns：filter 匹配url路径
            encodingFilter.addMappingForUrlPatterns(EnumSet.of(DispatcherType.REQUEST),true,"/*");
        }
    
        /**
         * Spring 配置文件，相当于xml中的applicationContext.xml
         * 这里仅仅只使用SpringMVC功能，该配置可以暂时不配置
         * @return
         */
        @Override
        protected Class<?>[] getRootConfigClasses() {
            return null;
        }
    	
    	/**
         * SpringMVC Java Configuration 类
         * @return
         */
        @Override
        protected Class<?>[] getServletConfigClasses() {
            return new Class[]{MyServletConfig.class};
        }
    
    	/**
         * Servlet映射路径，/ 即拦截所有请求
         */		
        @Override
        protected String[] getServletMappings() {
            return new String[]{"/"};
        }
    
        private Filter encodingFilter(){
            CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
            encodingFilter.setEncoding("UTF-8");
            encodingFilter.setForceEncoding(true);
            return encodingFilter;
        }
    }
    ```

- 第四步：配置 Spring MVC 配置文件，通过实现`WebMvcConfigurer`并重新其中的默认方法实现.

  ``` java
  package com.xbhel.config;
  
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.ComponentScan;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.servlet.ViewResolver;
  import org.springframework.web.servlet.config.annotation.*;
  import org.springframework.web.servlet.view.InternalResourceViewResolver;
  
  /**
   * @EnableWebMvc 开启Spring MVC配置
   * @Configuration 表示MyServletConfig 是一个 Spring 配置类
   * @ComponentScan 表示启用自动扫包
   */
  @EnableWebMvc
  @Configuration
  @ComponentScan("com.xbhel.controller")
  public class MyServletConfig implements WebMvcConfigurer {
  
      /**
       * 配置视图解析器
       * @return
       */
      @Bean
      public ViewResolver viewResolver() {
          InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
          viewResolver.setPrefix("/WEB-INF/content/");
          viewResolver.setSuffix(".jsp");
          return viewResolver;
      }
  
      /**
       * 对静态资源放行， 等价于 <mvc:default-servlet-handler/>
       * @param configurer
       */
      @Override
      public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
          configurer.enable();
      }
   	/**
       * 静态资源访问映射
       */
      @Override
      public void addResourceHandlers(ResourceHandlerRegistry registry) {
          //addResourceHandler 指的是对外暴露的访问路径
          //addResourceLocations 指的是文件放置的目录
          registry
                  .addResourceHandler("/static/**")
                  .addResourceLocations("/WEB-INF/static/");           
      }
  }
  
  ```

- 第五步：编写`Controller`并启动服务器访问

```java
@Controller
public class HelloController {
	@ResponseBody
    @RequestMapping(value = "/hello",method = RequestMethod.GET)
    public String hello(){
        return "Hello SpringMVC!";
    }
}
```

