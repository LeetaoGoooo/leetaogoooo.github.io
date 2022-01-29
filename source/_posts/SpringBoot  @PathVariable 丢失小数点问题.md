---
title: SpringBoot  @PathVariable 丢失小数点问题
tags: [Spring Boot]

date: 2019-10-25
published: true
hideInList: false
feature: 
isTop: false
---



# 前言

最近在使用` Spring Boot` 的 `@PathVariable` 碰见一个有趣的问题, `@PathVariable`  默认一般情况下匹配字符串都是十分正常的，一旦遇到带有 `.后缀`  的 字符串就会出现将 `.后缀` 丢失的问题。举个例子，当我们使用 `@PathVariable`   去匹配 `a.b.c.d`  的时候，匹配的结果为 `a.b.c`


```java
    @ApiOperation(value = "pathvariable 匹配测试", notes = "pathvariable 匹配测试")
    @GetMapping("/pathvariable/test/{test}")
    public ResponseSo getNetWorkElements(@PathVariable("test") String test) {
        // 当 匹配为 a.b.c.d 
        // 匹配结果为 a.b.c
    }
```

# 原因

为什么会出现这种问题呢？早期 Spring Boot 是能够做到完整匹配的，但是这种全匹配会导致一些使用文件拓展名的 Restful URLs 出现异常，具体内容可以参考 [ReSTful URLs with content type extension do not work properly [SPR-5537]](https://github.com/spring-projects/spring-framework/issues/10208)。然后在后续的更新中，会默认截断最后的所谓的文件拓展 `.后缀`。这个截断的代码我们可以在代码的修改 [commits c178888](https://github.com/spring-projects/spring-framework/commit/c178888efd6db95ac62a4044bdfeac1b36be2d5b)


# 解决办法

1. 修改匹配的表达式

```java
@GetMapping("/pathvariable/test/{test:.+}")
```

**注意**:使用这个方法，在后续匹配类似 **xx.txt** 等问题，会出现 406 错误，建议使用第二种(未测试)或者第三种方法

2. 修改默认配置

```java
@Configuration
public class PolRepWebConfig extends WebMvcConfigurationSupport {

    @Override
    @Bean
    public RequestMappingHandlerMapping requestMappingHandlerMapping() {
        final RequestMappingHandlerMapping handlerMapping = super
                .requestMappingHandlerMapping();
        // disable the truncation after .
        handlerMapping.setUseSuffixPatternMatch(false);
        // disable the truncation after ;
        handlerMapping.setRemoveSemicolonContent(false);
        return handlerMapping;
    }
}
```
3. 修改 **ContentNegotiationConfigurer** 配置

```java
@SpringBootApplication
public class Application extends WebMvcConfigurerAdapter {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void configureContentNegotiation(
            ContentNegotiationConfigurer configurer) {
        configurer.favorPathExtension(false);
    }
}
```

# 参考链接

[Spring MVC @PathVariable getting truncated](https://stackoverflow.com/questions/3526523/spring-mvc-pathvariable-getting-truncated)

[spring requestmapping http error 406 on file extension](https://stackoverflow.com/a/28149037/9319791)