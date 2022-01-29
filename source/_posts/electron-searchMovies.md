---
title: electron-searchMovies
tags: []

date: 2017-09-23
published: true
hideInList: false
feature: 
isTop: false
---







之前学了electron,前段时间又学了一下vue,为了增加熟练度决定将两者结合做个有趣的东西.想来想去最后决定将原来用 PyQt 写的[MovieHeavens](https://github.com/lt94/MovieHeavens)重新写一遍,使用electron-vue构建的项目地址[electron-searchMovies](https://github.com/lt94/electron-searchMovies),最后用*electron-packager*打包了成exe,在项目主页的releases可以找到打包后的安装程序.总结一下整个问题中遇见的几点问题以及解决办法.

## 无法下载打包所需的工具

由于一些神奇的力量导致打包过程中下载必需的工具包失败

### 解决办法

确定你需要工具包的名称,然后先手动将这些工具包下载下来,然后解压到对应的缓存目录中.各个系统对应的路径如下

```
macOS: ~/Library/Caches/electron-builder
Linux: ~/.cache/electron-builder
windows: %LOCALAPPDATA%\electron-builder\cache
```

我的最终目录树如下:

```
.
├── appimage-packages
│   └── appimage-packages-28-08-17
├── nsis
│   └── nsis-3.0.1.13
├── nsis-resources
│   └── nsis-resources-3.3.0
└── winCodeSign
    └── winCodeSign-1.9.0
```

## 用 yarn 而不是 npm

当解决了工具包的问题,仍然打包成功,但是应用没有任何内容只是一个空白页,后来在github[No contents only a blank page after build](https://github.com/electron-userland/electron-builder/issues/1615)看见建议用 *yarn* 而不是 *npm*.这里有一篇[Yarn vs npm: Everything You Need to Know](https://www.sitepoint.com/yarn-vs-npm/)的详细文章,正如官方文档说道的:yarn是为了弥补npm的一些缺陷而出现的.

## 短暂的空白页

一切问题都解决了,再次将软件打包成 exe,似乎接下来就是体验胜利的果实了,但是事实上并不是这样,打包的第一个版本[Pre-release](https://github.com/lt94/electron-searchMovies/releases/download/0.0.0/search-movies.Setup.0.0.0.exe),在打开软件最开始会出现短暂的空白页,显然这对用户不是一个很棒的体验,最后决定加了一个简单的加载动画,核心代码如下:

```javascript
  mainWindow.webContents.on('did-finish-load', () => {
    mainWindow.show()
    if (loadingScreen) {
      let loadingScreenBounds = loadingScreen.getBounds()
      mainWindow.setBounds(loadingScreenBounds)
      loadingScreen.close()
    }
  })
```

完整代码自然就需要去[electron-searchMovies](https://github.com/lt94/electron-searchMovies)上查看了.这里遇见了一个新的问题.


## Cannot Get Route 

在解决上个问题的同时遇见了一个新的问题,加载动画页是通过路由映射的，我在 *src/routes.js* 中添加了如下的路由:

```vue
    {
      path: '/loading',
      name: 'loading-page',
      component: require('@/components/LoadingPage')
    }
```

然后让 *loadingScreen* 加载 *http://localhost:9080/loading* 但是却使用获得 *cannot get /loading* 的错误,后来最后查找资料下找到了解决方案,对于新的路由访问地址应该是*http://localhost:9080/#/new_route*,打包后的访问地址应该是:*file://${__dirname}/index.html#new_route*,到此所有问题圆满解决.最后上一下软件的使用截图.

## 使用

![](http://ww1.sinaimg.cn/large/006wYWbGly1fjt9glqbn1g310r0imqv6.gif)
最后欢迎大家star Or fork~


