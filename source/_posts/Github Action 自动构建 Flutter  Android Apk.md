---
title: Github Action 自动构建 Flutter  Android Apk
tags: [Flutter,Github Action]

date: 2021-05-22
published: true
hideInList: false
feature: 
isTop: false
---


#  前言
前段时间用 Flutter 做了一个开源的项目 [RSSAid](https://github.com/lt94/RSSAid),因为需要打包 apk，在此之前一直是在本地签名打包的。后来和别人交流了一下，想起来可以用 Github Action 构建持续化集成，自动打包。然后就研究了一下，最后完成了根据 tag 版本自动生成 apk 的 workflows。
#  Workflows
自动化构建脚本如下：
```yaml
# main.yml
# 自动构建 Apk
name: Test, Build and Release apk

# 工作流程触发的时机，这里是当一个版本标签推送到仓库时触发
on:
  push:
    tags:
      - v*

# 这个工作流程需要执行的任务
jobs:
  process:
    name: all process
    runs-on: ubuntu-latest
    # 这个任务的步骤
    steps:
      # 拉取项目代码
      - uses: actions/checkout@v2
      # 建立 java 环境
      - name: Setup Java JDK
        uses: actions/setup-java@v1.4.3
        with:
          java-version: "12.x"
      # 建立 Flutter 环境
      - name: Flutter action
        uses: subosito/flutter-action@v1.4.0
        with:
          channel: "stable"
          flutter-version: "1.22.4"
      # 下载项目依赖
      - run: flutter pub get
      - run: echo $ENCODED_KEYSTORE | base64 -di > android/app/keystore.jks
        env:
          ENCODED_KEYSTORE: ${{ secrets.ENCODED_KEYSTORE }}
      # 打包 APK
      - run: flutter build apk --release
        env:
          KEYSTORE_PASSWORD: ${{ secrets.KEYSTORE_PASSWORD }}
          KEY_ALIAS: ${{ secrets.KEY_ALIAS }}
          KEY_PASSWORD: ${{ secrets.KEY_PASSWORD}}
      # 发布到 Release
      - name: Release apk
        uses: ncipollo/release-action@v1.5.0
        with:
          artifacts: "build/app/outputs/apk/release/*.apk"
          token: ${{ secrets.RELEASE_TOKEN }}
```
#  使用注意事项
脚本中有很多环境变量，都需要实现定义好，在项目的 secrets 中添加上。
## RELASE_TOKEN
这个环境变量需要在 [Personal access tokens](https://github.com/settings/tokens) 申请，需要注意的是，申请完成之后，不要着急关闭这个页面，因为一旦关闭就不能再次查看生成的 token 了，这个 token 需要申请 repo 和 workflow 的权限

![](/posts/1610071792.jpeg)

生成 token 成功后，找到项目的 **Settings => Secrets **选项，新建名为 RELEASE_TOKEN 的 secrets 然后 value 值为刚才生成的 token。这个完成之后，就需要对设置生成 apk 需要的签名进行变量设置了。
##  签名相关变量
正常 app 签名步骤可以参考 [app签名](https://flutterchina.club/android-release/#app%E7%AD%BE%E5%90%8D)，最终我们会创建一个 key.properties 的文件，文件内容如下：
```
storePassword=<password from previous step>
keyPassword=<password from previous step>
keyAlias=key
storeFile=<location of the key store file, e.g. /Users/<user name>/key.jks>
```
然后在 android/app/build.grade 中配置
```
    signingConfigs {
        release {
            keyAlias keystoreProperties['keyAlias']
            keyPassword keystoreProperties['keyPassword']
            storeFile keystoreProperties['storeFile'] ? file(keystoreProperties['storeFile']) : null
            storePassword keystoreProperties['storePassword']
        }
    }
```
但是将 key.properties 上传到仓库显然是不安全的。所以需要对代码进行修改，将对应的变量添加到 secrets  中，从 secrets  中获取变量。
```
           storeFile file(System.getenv("KEYSTORE") ?:"keystore.jks")
           storePassword System.getenv("KEYSTORE_PASSWORD")
           keyAlias System.getenv("KEY_ALIAS")
           keyPassword System.getenv("KEY_PASSWORD")
```
其中 **KEYSTORE_PASSWORD、KEY_ALIAS、KEY_PASSWORD **直接就可以添加，那么 **KEYSTORE **这个变量怎么处理呢？KEYSTORE 对应着 jks 文件位置。jks 文件显然也不可能上传到仓库，所以我们换种方法，在构建的时候生成 jks 文件。
## 构建时生成 jks 文件
正常情况下打开生成的 jks 文件多半是乱码，所以我们可以通过 base64 对文件进行编码，然后在构建的时候，再解码重新生成文件。
### 获取 base64 格式的 keystore
首先获取 base64 格式的 keystore 
```bash
openssl base64 -A -in <jks.文件位置>
```
然后将输出的结果复制下来
### 保存 base64 格式的 keystore
将编码后的 keystore 内容，添加到 secrets ，变量名命名为 **ENCODED_KEYSTORE，**然后在构建过程中就可以将 keystore 文件还原了。
```bash
echo $ENCODED_KEYSTORE | base64 -di > android/app/keystore.jks
```
