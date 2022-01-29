---
title: SpringBoot 使用 @Valid 配合 BindingResult 校验数据
tags: [Java,Spring Boot]

date: 2019-12-25
published: true
hideInList: false
feature: 
isTop: false
---



#  前言

在实际开发过程中，经常需要对前端传来的数据模型进行校验，一般情况下都是写一个专门的校验函数去校验，这种方式很显然不适合复杂的项目，因为随着项目复杂度的增加需要校验的数据模型越来越多，并且在开发过程中可能也需要时常修改数据模型，这就导致原来的校验方法可能需要时常的变动，也不利于维护。因为我们需要一种更简单的方式去完成这个工作。


# @Valid 和 BindingResult 

SpringBoot 在内部通过集成 hibernate-validation 已经实现了JSR-349验证规范接口，在SpringBoot项目中只要直接使用就行了。接下来通过一个简单的例子完整的介绍使用方法


## Example

以注册用户为例

### 定义 Bean

```java
public class User{
    @NotBlank(message="手机号不能为空")
    private String phone;
    @NotBlank(message="手机号不能为空")
    private String password;
    
    public void setPhone(String phone) {
        this.phone = phone;
    }
    
    public void setPassword(String password) {
        this.password = password;
    }
    
    public String getPhone() {
        return phone;
    }
    
    public String getPassword() {
        return password;
    }
}
```

### 定义 Controller 

在 controller 类里面对相应的接口添加 @Valid,BindingResult 验证，如果检验失败，则获取错误信息返回前台

```java
@RestController
@RequestMapping(value = "/user")
public class UserController {
    @PostMapping(value = "/register")
    public User login(@RequstBody @Valid User userInfo, BindingResult bindingResult) {
        if (bindingResult.hasErrors()) {
            System.out.print(bindingResult.getFieldError().getDefaultMessage());
            return null;
        }
        return userInfo;
    }
}
```


# 总结

上面给出了常用的处理方法，在实际开发过程中，通常为了返回数据格式的统一，可能会全局捕获异常，然后统一返回，这个时候上述 Controller 中的 BindResult 和 if 判断语句就可以省略了，然后在全局中捕获


# 参考

[Java SpringBoot上的参数校验JSR 303 Validation](https://juejin.im/post/5b2f4514f265da597a6108f8)