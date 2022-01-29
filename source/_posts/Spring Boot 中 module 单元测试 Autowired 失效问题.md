---
title: Spring Boot 中 module 单元测试 Autowired 失效问题
tags: [Java,Spring Boot]

date: 2019-02-28
published: true
hideInList: false
feature: 
isTop: false
---






# 前言

最近写 Spring Boot 中 module 发现使用 **Autowired** 注入失败.

# 解决办法

在需要测试的类上添加 **ContextConfiguration(classes = ClassToAutowire.class)** 注解


```java
@RunWith(SpringRunner.class)
@ContextConfiguration(classes = ExampleSerivceImpl.class)
public class TestExampleSerivceImpl {
    @Autowired
    private ExampleService exampleService;

    @Test
    public void TestExample() {
	//
    }
}
```

我们常用的 **SpringBootTest** 是用在集成测试当中的声明