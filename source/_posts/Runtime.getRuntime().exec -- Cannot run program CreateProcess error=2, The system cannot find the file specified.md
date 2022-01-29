---
title: Runtime.getRuntime().exec -- Cannot run program CreateProcess error=2, The system cannot find the file specified
tags: [Java]

date: 2018-03-06
published: true
hideInList: false
feature: 
isTop: false
---







# 问题原因

最近有个需求用Java调用<code>nginx -V</code>并返回输出,查了一下决定使用<code>Runtime.getRuntime().exec()</code>去执行命令.代码如下:

```java
    process = Runtime.getRuntime().exec(cmd, null, dir);
```

最初调用函数将正确路径和cmd都传入进去,其中cmd为<code>nginx -V</code>,但是返回下列错误:

>Cannot run program "nginx -V": CreateProcess error=2, 系统找不到指定的文件

# 解决办法

查了一下相关资料,只需要将原有的cmd命令由<code>nginx -V</code>改为<code>cmd /c nginx -V</code>即可

# 参考链接

[CreateProcess error=2, The system cannot find the file specified
](https://stackoverflow.com/questions/19621838/createprocess-error-2-the-system-cannot-find-the-file-specified)

[Runtime.getRuntime().exec -> Cannot run program CreateProcess error=2, The system cannot find the file specified](https://stackoverflow.com/questions/32203294/runtime-getruntime-exec-cannot-run-program-createprocess-error-2-the-syste)