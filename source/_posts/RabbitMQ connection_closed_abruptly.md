---
title: RabbitMQ connection_closed_abruptly
tags: [RabbitMQ]

date: 2020-08-28
published: true
hideInList: false
feature: 
isTop: false
---



# 前言
前段时间在服务器上安装 RabbitMQ，安装完成之后启动程序发现一个很奇怪的问题，会出现下面错误导致启动失败：

```java
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'bssInteractiveController': Unsatisfied dependency expressed through field 'bssInteractiveRabbitMQProducer'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'bssInteractiveRabbitMQProducer': Invocation of init method failed; nested exception is org.springframework.amqp.AmqpTimeoutException: java.util.concurrent.TimeoutException
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor$AutowiredFieldElement.inject(AutowiredAnnotationBeanPostProcessor.java:588)
	at org.springframework.beans.factory.annotation.InjectionMetadata.inject(InjectionMetadata.java:88)
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor.postProcessPropertyValues(AutowiredAnnotationBeanPostProcessor.java:366)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.populateBean(AbstractAutowireCapableBeanFactory.java:1264)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:553)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:483)
	at org.springframework.beans.factory.support.AbstractBeanFactory$1.getObject(AbstractBeanFactory.java:306)
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:230)
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:302)
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:197)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:761)
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:867)
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:543)
	at org.springframework.boot.context.embedded.EmbeddedWebApplicationContext.refresh(EmbeddedWebApplicationContext.java:122)
	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:693)
	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:360)
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:303)
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1118)
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1107)
	at com.dimpt.netres.RestUiApplication.main(RestUiApplication.java:30)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.springframework.boot.loader.MainMethodRunner.run(MainMethodRunner.java:48)
	at org.springframework.boot.loader.Launcher.launch(Launcher.java:87)
	at org.springframework.boot.loader.Launcher.launch(Launcher.java:50)
	at org.springframework.boot.loader.JarLauncher.main(JarLauncher.java:51)
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'bssInteractiveRabbitMQProducer': Invocation of init method failed; nested exception is org.springframework.amqp.AmqpTimeoutException: java.util.concurrent.TimeoutException
	at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor.postProcessBeforeInitialization(InitDestroyAnnotationBeanPostProcessor.java:137)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.applyBeanPostProcessorsBeforeInitialization(AbstractAutowireCapableBeanFactory.java:409)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1620)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:555)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:483)
	at org.springframework.beans.factory.support.AbstractBeanFactory$1.getObject(AbstractBeanFactory.java:306)
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:230)
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:302)
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202)
	at org.springframework.beans.factory.config.DependencyDescriptor.resolveCandidate(DependencyDescriptor.java:208)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1138)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1066)
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor$AutowiredFieldElement.inject(AutowiredAnnotationBeanPostProcessor.java:585)
	... 27 common frames omitted
Caused by: org.springframework.amqp.AmqpTimeoutException: java.util.concurrent.TimeoutException
	at org.springframework.amqp.rabbit.support.RabbitExceptionTranslator.convertRabbitAccessException(RabbitExceptionTranslator.java:74)
	at org.springframework.amqp.rabbit.connection.AbstractConnectionFactory.createBareConnection(AbstractConnectionFactory.java:371)
	at org.springframework.amqp.rabbit.connection.CachingConnectionFactory.createConnection(CachingConnectionFactory.java:573)
	at org.springframework.amqp.rabbit.core.RabbitTemplate.doExecute(RabbitTemplate.java:1430)
	at org.springframework.amqp.rabbit.core.RabbitTemplate.execute(RabbitTemplate.java:1411)
	at org.springframework.amqp.rabbit.core.RabbitTemplate.execute(RabbitTemplate.java:1387)
	at org.springframework.amqp.rabbit.core.RabbitAdmin.declareQueue(RabbitAdmin.java:207)
	at com.dimpt.message.service.AbstractRabbitMQBase.init(AbstractRabbitMQBase.java:33)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor$LifecycleElement.invoke(InitDestroyAnnotationBeanPostProcessor.java:366)
	at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor$LifecycleMetadata.invokeInitMethods(InitDestroyAnnotationBeanPostProcessor.java:311)
	at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor.postProcessBeforeInitialization(InitDestroyAnnotationBeanPostProcessor.java:134)
	... 39 common frames omitted
Caused by: java.util.concurrent.TimeoutException: null
	at com.rabbitmq.utility.BlockingCell.get(BlockingCell.java:77)
	at com.rabbitmq.utility.BlockingCell.uninterruptibleGet(BlockingCell.java:120)
	at com.rabbitmq.utility.BlockingValueOrException.uninterruptibleGetValue(BlockingValueOrException.java:36)
	at com.rabbitmq.client.impl.AMQChannel$BlockingRpcContinuation.getReply(AMQChannel.java:372)
	at com.rabbitmq.client.impl.AMQConnection.start(AMQConnection.java:297)
	at com.rabbitmq.client.impl.recovery.RecoveryAwareAMQConnectionFactory.newConnection(RecoveryAwareAMQConnectionFactory.java:63)
	at com.rabbitmq.client.impl.recovery.AutorecoveringConnection.init(AutorecoveringConnection.java:99)
	at com.rabbitmq.client.ConnectionFactory.newConnection(ConnectionFactory.java:900)
	at com.rabbitmq.client.ConnectionFactory.newConnection(ConnectionFactory.java:859)
	at com.rabbitmq.client.ConnectionFactory.newConnection(ConnectionFactory.java:1013)
	at org.springframework.amqp.rabbit.connection.AbstractConnectionFactory.createBareConnection(AbstractConnectionFactory.java:356)
	... 52 common frames omitted

```

<br />从错误信息上很容易看出是连接超时的问题。
<a name="uIONd"></a>
# 问题定位
上网检索答案，大部分出现超时的原因只有一个，他们错把 RabbitMQ 的管理端口 **15672 **当作连接端口，导致超时。但是不幸的是，我并不是这个原因。开始排查日志，日志的信息很少
```systemverilog
=INFO REPORT==== 27-Aug-2020::16:23:52 ===
accepting AMQP connection <0.31279.2> (127.0.0.1:45728 -> 127.0.0.1:5672)

=WARNING REPORT==== 27-Aug-2020::16:24:07 ===
closing AMQP connection <0.30726.2> (127.0.0.1:45562 -> 127.0.0.1:5672):
connection_closed_abruptly
```
一时间陷入瓶颈当中，然后我通过命令行看了一眼 rabbitmq 的状态，有一行信息让我灵光一闪，我觉得问题可能就在这
```systemverilog
[root@host-xx-xx-xxx workspace]# rabbitmqctl status
Status of node 'rabbit@host-xx-xx-xxx' ...
...
```
这个hostname似乎有点问题，然后我立马搜了一下 rabbitmq hostname 的相关信息，发现网上有提到修改 hostname 导致信息丢失等等，还提到要把修改后的 hostname 写到 hosts 文件中。我立马对比了一下我在本地 docker 启动的 rabbitmq 服务的 hosts 文件和服务器上的 hosts 文件，发现通过 docker 启动的，hosts 文件中已经有对应的 hostname 和 127.0.0.1 的映射了，而服务器上的并没有。
<a name="nNr4A"></a>
## docker 中的 hosts 文件
```systemverilog
root@myRabbit:/# cat /etc/hosts 
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      myRabbit # myRabit 就是 docker 中启动服务的 hostname 
```

## 服务器上的 hosts 文件
```systemverilog
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```
# 问题解决
将服务器上的 hostname 添加到 hosts 文件中，再次重启程序，问题解决。很少有看到关于 RabbitMQ 安装的时候提及 hostname 的问题，可能默认情况下 hostname 是在 hosts 中的。

# 后记
很少有看到关于 RabbitMQ 安装的时候提及 hostname 的问题，可能默认情况下 hostname 是在 hosts 中的。用 docker 安装省了不少事情，某些不能使用 docker 的场景下，难免会踩坑。

