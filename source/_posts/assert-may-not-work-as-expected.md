---
title: assert may not work as expected
date: 2022-10-12
tags: [python]
categories:
comments: true
---

assert (断言) 是在编码过程中常用的手段，使用方法如下:

```python
# example.py
# assert_stmt ::=  "assert" expression ["," expression]
def is_admin(user_roles):
    assert isinstance(user_roles,list) and user_roles != [], "No user roles found"

    assert 'admin' in user_roles, "No admin role found."
    print("You have full access to the application.")
```

上面的表达式其实等价于

```python
def is_admin(user_roles):
    if __debug__:
        assert isinstance(user_roles,list) and user_roles != [], "No user roles found"
    if __debug__:
        assert 'admin' in user_roles, "No admin role found."
    print("You have full access to the application.")
```

其中 `__debug__` 是内置变量，默认情况下为 `True`,这个时候我们传入一个 `user_roles = ['admin','guest']`:

```python
>>> is_admin(['admin','guest'])
You have full access to the application.
```

如果传入 `['guest']`:

```python
 line 4, in is_admin
    assert 'admin' in user_roles, "No admin role found."
AssertionError: No admin role found.
```

如果我们有办法改变 `__debug__` 的值为 `False`,那么上面的函数将永远不会出现 `AssertionError`,尝试直接去修改这个参数

```python
>>> __debug__ = False
  File "<stdin>", line 1
SyntaxError: cannot assign to __debug__
```

显然直接修改是行不通的，但是可以在命令行下运行的时候设置 **[PYTHONOPTIMIZE](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONOPTIMIZE)** 达到修改 `__debug__` 的目的

```python
❯ python -O example.py
You have full access to the application.
```

所在在使用 assert 时候需要注意启动程序的命令，避免 assert 失效导致程序出现问题。
