---
title: 关于在Flask页面中的定时调用
tags: [Javascript]

date: 2017-06-02
published: true
hideInList: false
feature: 
isTop: false
---





| 
| code  | 必需,要推迟执行的函数名或者一段js代码 |  
| millisec     |   必须,推迟执行的毫秒数 |
| lang | 可选,JScript/VBScript/JavaScript|


Talk is cheep.show me the code~~看下面一段代码

```javascript
function func() {
    // code1
    setTimeout(function(){
    // code3    
    },1000);
    // code2
}
```
在讲述这段代码之前，我们首先还得明确一件事情:
js任务分为同步任务和异步任务，只有当主线程空闲，才会去读取“任务队列”，不幸的是，异步任务就是进入到“任务队列”的任务，只有当“任务列队”通知主线程，某个异步任务可以执行了并且同步任务执行完毕，该任务才会进入主线程执行。额外补充这些知识，之后再往下看。

假设该函数被执行了,首先执行到 code1 的内容,然后运行到 setTimeout 部分,setTimeout 这个时候告诉浏览器，我这里有段代码将会在 1000 ms之后插入到你的任务队列当中去,然后浏览器主线程接着执行了 code2,等到了 1000ms,会出现什么情况呢，这个时候定时器将 code3 插入到了任务队列,这并不意味着code3就会被立即执行,如果 code2 的执行时间超过了 1000ms,那么这个时候很显然 code3不会被执行,需要等待 code2 执行完毕，当然如果 code2 的执行时间不超过 1000ms,那么到了1000ms,code3就会被执行了,这个时候定时器就正常发挥作用了。

把上面的代码改一下:

```javascript
function func() {
    // code1
    setInterval(function(){
    // code3    
    },1000);
    // code2
}
```
对于这段代码可能会出现三种情况:
1. 正常执行
2. 时间间隔可能会跳过
3. 时间间隔可能小于定时调用的代码的执行时间

正常的情况就不多说了，说说第二种可能会被跳过的原因，整个函数执行下来要1500ms,假设 code1 在200ms执行完成,以此作为时间点,setInterval将在1200ms时将code3插入到任务队列当中,然后func()函数顺利执行，不幸的是,code3代码执行的时间比较长,到下个 时间点2200ms时又插入了一份code3,你觉得接下来的时间点中,还会再插入新的code3吗~~实际上并不会，所以3200ms这个时间点会被跳过
为了避免这种情况,可以使用如下的代码:

```javascript
setTimeout(function(){ 
//processing 
setTimeout(arguments.callee, interval); 
}, interval); 
```

arguments.callee表示引用当前正在执行的函数，或者说是调用arguments.callee的函数对象的引用，它给匿名函数提供了一种自我引用的方式。
