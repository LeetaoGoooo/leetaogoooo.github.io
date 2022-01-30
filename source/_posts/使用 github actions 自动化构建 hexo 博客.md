---
title: Automated deployment of hexo via github action
date: 2022-01-30
tags: [github-action, hexo]
categories:
comments: true
---


# 前言
每年都要折腾一下自己的博客，今年准备把所有的博客再次迁移到静态博客 Hexo 中，配合 Github Action 实现，只需要一个浏览器就可以自动部署博客。
<-- more -->

# 思路
整体思路很简单，在你的 github-pages 的项目建立两个 分支 `master` 和 `gh-pages`，在 `gh-pages` 分支上完成 `hexo` 的构建，然后将构建后的静态网页推送到 `master` 分支上

![](https://raw.githubusercontent.com/LeetaoGoooo/leetaogoooo.github.io/images/%E6%9E%84%E5%BB%BA%E6%B5%81%E7%A8%8B.png)

其中在 github actions 涉及的主要 action 为 [github-pages-deploy-action](https://github.com/JamesIves/github-pages-deploy-action)，参考说明文档，需要使用到 `token` 或者 `ssh-key`，这里我使用到是前者，获取 token 的方法可以参考 [encrypted-secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)，然后在你的 github-pages 仓库 `secrets` 设置一下 `token`，命名为 `ACCESS_TOKEN`

# 脚本

```yaml
name: Blog CI/CD

# 触发条件：在 push 到 gh-pages 分支后触发
on:
  push:
    branches: 
      - gh-pages

env:
  TZ: Asia/Shanghai

jobs:
  blog-cicd:
    name: Hexo blog build & deploy
    runs-on: macos-latest # 系统环境，可以自由设置

    steps:
    - name: Checkout codes
      uses: actions/checkout@v2

    - name: Setup node
      # 设置 node.js 环境
      uses: actions/setup-node@v1
      with:
        node-version: '12.x'

    - name: Cache node modules
      # 设置包缓存目录，避免每次下载
      uses: actions/cache@v1
      with:
        path: ~/.npm
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

    - name: Install hexo dependencies
      # 下载 hexo-cli 脚手架及相关安装包
      run: |
        npm install -g hexo-cli
        npm install

    - name: Generate files
      # 编译 markdown 文件
      run: |
        hexo clean
        hexo generate

    - name: Deploy hexo blog
      # 部署到 GitHub Pages
      uses: JamesIves/github-pages-deploy-action@releases/v3
      with:
        ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
        BRANCH: master
        FOLDER: public
```


这里略过了关于 hexo 的配置，如果对 hexo 的配置不太了解可以参考 [hexo官网](https://hexo.io/zh-cn/),到此，只要每次直接更新 gh-pages 就可以触发自动构建流程了。
