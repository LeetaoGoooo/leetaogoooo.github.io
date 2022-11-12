---
title:  Python 中调用 Go 代码
tags: [python,go]
date: 2022-10-28
published: true
---

# 前言

Python 可以通过 [ctypes](https://docs.python.org/3/library/ctypes.html) 模块调用 C 语言实现的函数，调用的方式很简单：

1. 创建一个 c 文件，然后编写对应的函数
2. 使用 c 编译器创建一个动态链接库文件（.so）
3. 在 Python 文件中创建一个 `CDLL` 的实例
4. 最后通过 `{CDLL_instance}.{function_name}({function_parameters})` 格式调用 c 语言对应的函数

## 步骤一

```C
//  my_functions.c.
#include <stdio.h>

int square(int i) {
	return i * i;
}
```

## 步骤二

使用下述命令，创建 so 文件

```shell
cc -fPIC -shared -o my_functions.so my_functions.c
```

![编译生成 so 文件](https://user-images.githubusercontent.com/10162120/198480454-c0ee5a37-8143-415b-b45d-9291880d9f82.png)

### 步骤三&四

调用 C 的函数

```shell
>>> from ctypes import *
>>> so_file = "/Users/leetao/workspace/python/test/my_functions.so"
>>> my_functions = CDLL(so_file)
>>> 
>>> print(type(my_functions))
<class 'ctypes.CDLL'>
>>> 
>>> print(my_functions.square(10))
100
>>> print(my_functions.square(8))
64
>>> 
```

# 调用 GO 代码

从上面的 C 语言的调用过程，不难发现，最重要的是生成的 `.so` 文件，如果我们可以将 go 编辑成 `.so` 文件就可以得到 Python 调用 Go 的目的了。

## CGO

Go 有个标准库 [CGO](https://pkg.go.dev/cmd/cgo) 可以用来创建调用 C 代码的 Go 包，当然反过来也可以。

### 例子

```go
package main
​
import "C"  //必须引入C库
​
import "fmt"
​
//加入下面注释代码，表示导出，可以被python调用
​
//export Sum
func Sum(a int, b int) int {
  return a + b
}
​
​
func main() {
  //必须加一个main函数，作为CGO编译的入口，无具体实现代码
}
```
需要注意的是：

- 注释 //export Sum 在编译 动态库（dll ,so）的时候是必须的，说明了动态库需要输出的函数
-  main函数必须写，没有执行代码，作为CGO编译的入口
- 必须引入C库（import "C"）

### 编译动态库

使用下面的命令进行编译

```shell
go build -buildmode=c-shared -o s1.so s1.go
```

注意📢：

- -o表示输出，s1.so 表示编译成so文件的名字，s1.go表示编译哪个go文件
- "-s -w" 选项用于减小生成动态链接库的体积，-s 是压缩，-w 是去掉调试信息
   `go build -ldflags "-s -w" -o main.dll -buildmode=c-shared s1.go`
- 编译模式buildmode:

<img width="707" alt="编译模式" src="https://user-images.githubusercontent.com/10162120/198488629-bc443f2b-6e8a-4fc7-9654-458066afe740.png">

### 使用 Python 语言调用

```python
from ctypes import cdll

lib = cdll.LoadLibrary('./s1.so')

# 调用go语言的Sum
result = lib.Sum(100, 200)
print(result)
```
# 最后

Python 和 GO 之间的参数传递需要经过 C 的疏忽类型转换，所以还需要了解 python 中 ctypes 数据类型和 python 数据类型以及 C 的数据类型对应关系

三种数据类型使用场景：

1. ctypes数据类型为指定调用函数时的传入参数和返回值的数据类型
2. python数据类型为调用函数时传入的参数的数据类型
3. C的数据类型为go代码中定义的函数所需的参数和返回值数据类型

[对应关系](https://docs.python.org/3.9/library/ctypes.html#fundamental-data-types)如下：

<img width="799" alt="对应关系" src="https://user-images.githubusercontent.com/10162120/198492039-adffc174-0fb9-411d-a2b3-c5e43036c6dc.png">

也就是说，如果写如下的 go 代码：

```go
//export hello
func hello(namePtr *C.char){
   name := C.GoString(namePtr)
   log.Println("Hello", name)
}
```

也就意味着，我们的 Python 调用代码中参数类型也需要对应起来：

```python
hello = library.hello
hello.argtypes = [ctypes.c_char_p]
hello("everyone".encode('utf-8'))
```

# 参考文章

- [cgo](https://pkg.go.dev/cmd/cgo)
- [Running Go code from Python](https://pkg.go.dev/cmd/cgo)
- [Python调用Go语言编译的动态链接库-Python和Go语言混编](https://zhuanlan.zhihu.com/p/355538331)


