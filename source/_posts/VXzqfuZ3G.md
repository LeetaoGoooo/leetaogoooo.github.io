---
title: 'Python 中 assert 的最佳实践'
date: 2021-09-16 20:12:21
tags: [python]
published: true
hideInList: false
feature: 
isTop: false
---
> 原文连接： [When to use assert](https://archive.is/5GfiG)

# 前言

`assert` 又称为断言，在 Python 代码中经常被使用，但是显然也存在滥用的情况。那么在什么时候使用 `assert` 呢？又或者 `assert` 的最佳实践是怎么样的呢？

# assert 的使用

Python 的 `assert` 通常用来检查一个条件，如果它是真的，则不做任何事情，如果它是假的，则引发一个 AssertionError，并给出一个可选的错误信息:

```python
py> x = 23
py> assert x > 0, "x is not zero or negative"
py> assert x%2 == 0, "x is not an even number"
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AssertionError: x is not an even number
```

许多人喜欢通过 `assert` 来简单地快速地触发异常，比如下面的代码：

```python
if not isinstance(x, int):
    raise AssertionError("not an int")
```

通过检验参数，并抛出 `AssertionError` ，实际上，这种做法是错误的，并且还很危险。正确的做法是应该抛出一个 `TypeError` 。

之所以危险是英文，`assert`  有一个特性：**使用 `-O` 或 `-OO` 优化指令去运行 Python 的话，它会被被编译掉，而永远不会被执行。**当你能够正确使用 `assert` 时，这是会是一个 feature，但当使用不当的话，则会导致代码在使用 `-O` 标志运行时出问题。

所以应该在什么情况下使用 assert 呢

# 什么时候使用 assert

在下面这四种情况下都可以考虑使用 `assert`:

- 防御性编程。
- 对程序逻辑进行运行时检查。
- 契约检查（如前条件和后条件）。
- 程序不变性
- 检查文件

对断言的看法各不相同，它可以看作是一种对代码正确性的信心的表现形式吧。如果你确定你的代码毫无问题的话，显然断言（`assert`）是没有任何意义的，完全可以把这些断言移除；如果你确定断言可能会被触发，那你也完全可以不用断言（`assert`），毕竟它在某些情况下肯定会编译掉，导致你的检查被跳过。

介于这两种情况之间的情况才是有趣的，当你确定代码是正确的，但又不完全确定的时候。
你确定代码是正确的，但又不是**绝对确定**。有可能漏掉一些场景，在这种情况下，通过断言（assert）进行运行时检查有助于我们尽早的发现错误。

**断言的另一个用途是检查程序的不变量**。所谓的不变量就是一些你可以依赖的真实条件，除非一个错误导致它变成假的。如果有一个bug，最好能尽早发现所以我们要对它进行测试，但我们不想因为这样的测试而降低代码的速度。但我们不想因为这些测试而使代码变慢。因此我们可以通过可在开发中打开而在生产中关闭断言（assert）。

不变量的一个例子是，如果你的函数预期一个数据库连接是 open，并承诺它在返回时仍然是open，这就是函数的一个不变式。open 就是该函数的一个不变量。

```python
def some_function(arg):
    assert not DB.closed()
    ... # code goes here
    assert not DB.closed()
    return result
```

断言也是很好的检查性评论，而不是写一个评论：

```python
# when we reach here, we know that n > 2
# 我们可以通过将其转换为断言来确保在运行时对其进行检查
assert n > 2
```

**断言也是防御性编程的一种形式**。你不是在保护现在的代码中的错误，而是在保护以后引入错误的变化。理想情况下，单元测试会发现这些错误，但是实际上，即使存在测试，它们也是往往是不完整的。有可能在几周内都没有人注意到构建机器人，或者有的时候在提交代码前忘记运行测试。有一个内部检查是防止错误潜入的另一道防线，特别是阻止那些会导致代码故障和返回错误结果的静默错误。

假设你有个代码中有 if...elif 代码块，这个时候你是知道这些分支对应了哪些变量，并且了解逻辑是什么:

```python
# target is expected to be one of x, y, or z, and nothing else.
if target == x:
    run_x_code()
elif target == y:
    run_y_code()
else:
    run_z_code()
```

假设这个代码现在是完全正确的。但它会一直正确吗？需求改变了,代码也会改变。现在要求改变为当 target == w，执行 run_w_code。如果我们改变了设置目标的代码，但忽略了改变这个代码块，它将错误地调用 run_z_code()：

```python
target = w
# ...
if target == x:
    run_x_code()
elif target == y:
    run_y_code()
else:
    run_z_code()
```

最好是把这段代码写成防御性的，哪怕之后有变化，它也是要么是正确的，要么立即失败。

写代码块一开始加注释是很好的习惯，但是通常时间久了，我们就没有阅读和更新注释的习惯。有可能注释很快就会被淘汰。但通过一个断言，我们既可以记录这个块的假设，也可以在断言检验没通过的时候直接抛出错误。

```python
assert target in (x, y, z)
if target == x:
    run_x_code()
elif target == y:
    run_y_code()
else:
    assert target == z
    run_z_code()
```

断言既是防御性编程，也是经过检查的文档，下面这个代码会更好一点：

```python
if target == x:
    run_x_code()
elif target == y:
    run_y_code()
elif target == z:
    run_z_code()
else:
    # This can never happen. But just in case it does...
    raise RuntimeError("an unexpected error occurred")
```

**契约设计是另一种对断言的不错的应用**。在按契约设计中，我们认为函数与它们的调用者签订了 "契约"。
例如,"如果你传递给我一个非空的字符串，我保证返回该字符串的第一个的第一个字符转换成大写字母"。

如果这个契约被函数或调用它的代码所破坏,代码就是有问题的。我们说，函数有预设条件（即
参数的约束）和后置条件（返回结果的约束） 因此，这个函数可能被代码为：

```python
def first_upper(astring):
    assert isinstance(astring, str) and len(astring) > 0
    result = astring[0].upper()
    assert isinstance(result, str) and len(result) == 1
    assert result == result.upper()
    return result

```

契约设计的目的是，在一个正确的程序中，前条件和后条件总是成立的。当我们发布无错误的程序并将其投入生产时，我们可以安全地删除这些断言。

# 什么时候不使用assert

- 永远不要用它们来测试用户提供的数据，或者用于在任何情况下必须进行检查的地方。
- 不要用断言来检查任何你认为在你的程序的正常使用中可能失败的东西。断言是为特殊失败条件。你的用户不应该看到 `AssertionError`。如果他们看到了，这就是一个需要修复的错误。
- 特别是，不要因为 `assert` 比一个测试和 raise 短就使用它。
- 不要用它们来检查公共库的输入参数函数的输入参数，因为你无法控制调用者，也不能保证它不会破坏函数的契约。
- 不要将 `assert` 用于任何你期望恢复的错误。 换句话说，你没有理由在生产代码中捕捉一个`AssertionError` 异常。
- 不要使用太多的断言，以至于它们掩盖了代码。


