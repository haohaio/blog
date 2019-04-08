---
title: 'Flutter 入门'
entitle: flutter-quick-start
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1554254927
comments: true
date: 2019-04-03 09:28:47
tags:
- Flutter
keywords: Flutter
description: Flutter 快速入门
photos:
- /img/2019/flutter.png
---

## 简介

Flutter allows you to build beautiful native apps on iOS and Android from a single codebase.

Flutter 是 Google 推出并开源的移动应用开发框架，采用 Dart 语言进行开发，使用自绘引擎渲染视图，主打跨平台、高保真、高性能。

## 开发环境

### 安装 Flutter

- 使用镜像

```bash
# Flutter官方为中国开发者搭建的临时镜像
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```

以 Mac 环境为例进行安装，其它环境请看 [Windows](https://flutterchina.club/setup-windows/)、[Linux](https://flutterchina.club/setup-linux/)

- 下载 SDK，然后解压到相应目录中，并添加 flutter 相关工具到 path 中：

```bash
# 将 SDK 解压到了 ~/Flutter/sdk/ 目录下
$ export PATH=~/Flutter/sdk/flutter/bin:$PATH
```

- 运行 flutter doctor

```bash
# 通过 flutter 提供的命令检查当前环境并在终端窗口中显示报告
$ flutter doctor
```

- 根据提示直接复制命令继续执行即可

### 安装 Xcode 和 Android Studio

Xcode 和 Android Studio 是进行原生开发的 IDE，安装后也会便于进行 Flutter 开发。具体安装自行了解一下，可看 [参考链接](https://flutterchina.club/setup-macos/#ios-%E8%AE%BE%E7%BD%AE)

### IDE

- Visual Studio Code (VS Code)
  安装 Flutter 插件即可
- Android Studio
  安装 Flutter 和 Dart 插件即可

## 工程

### 新建项目（VS Code）

1. 打开命令面板（Command + shift + P），输入 “flutter”, 然后选择 “Flutter: New Project” action
2. 输入 Project 名称 (如myapp), 然后按回车键确认
3. 指定放置项目的位置，然后按蓝色的确定按钮
4. 等待项目创建继续，并显示main.dart文件

### 运行项目（VS Code）

1. 在 VS Code 的右下角选择目标设备
2. 按 F5 键进行调试

### 打包

#### Android

- 创建 keystore (若已有keystore，跳至下一步)

```bash
# 通过 Java JDK 的 keytool 在根目录下创建一个 key.jks 文件
$ keytool -genkey -v -keystore ~/key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias key
```

- 引用keystore

创建一个名为 `<app dir>/android/key.properties` 的文件，其中包含对密钥库的引用：

```bash
storePassword=<password from previous step>
keyPassword=<password from previous step>
keyAlias=key
storeFile=<location of the key store file, e.g. /Users/<user name>/key.jks>
```

- 在 gradle 中配置签名

1. 替换:

```gradle
android {
```

为：

```gradle
def keystorePropertiesFile = rootProject.file("key.properties")
def keystoreProperties = new Properties()
keystoreProperties.load(new FileInputStream(keystorePropertiesFile))

android {
```

2. 替换:

```gradle
buildTypes {
    release {
        // TODO: Add your own signing config for the release build.
        // Signing with the debug keys for now, so `flutter run --release` works.
        signingConfig signingConfigs.debug
    }
}
```

为：

```gradle
signingConfigs {
    release {
        keyAlias keystoreProperties['keyAlias']
        keyPassword keystoreProperties['keyPassword']
        storeFile file(keystoreProperties['storeFile'])
        storePassword keystoreProperties['storePassword']
    }
}
buildTypes {
    release {
        signingConfig signingConfigs.release
    }
}
```

- 执行命令打包

```bash
# 在 <app dir> 下执行命令（非 android/ 目录下）
$ flutter build apk (flutter build 默认会包含 --release选项)
```

打包好的APK位于 `<app dir>/build/app/outputs/apk/`

#### iOS

和正常的iOS项目一样打包，具体可查看 [详细介绍](https://flutterchina.club/ios-release/)

### 热补丁

目前 Flutter 是默认不支持热补丁的，暂时也未发现靠谱的热补丁方法。

## 架构

### Dart

[Dart](http://dart.goodev.org/) 是由 Google 开发并开源的应用编程语言。我们需要用其进行 Flutter 开发。

简单介绍一些语法：

- 变量声明

```dart
var t;
t="hi world";
t=1000; // error

dynamic t;
Object x;
t = "hi world";
x = 'Hello Object';
t = 1000; // ok
x = 1000; // ok

// dynamic 声明的对象编译器会提供所有可能的组合, 而 Object 声明的对象只能使用 Object 的属性与方法
dynamic a;
Object b;
main() {
    a = "";
    b = "";
    printLengths();
}

printLengths() {
    print(a.length); // ok
    print(b.length); // error: The getter 'length' is not defined for the class 'Object'
}
```

- 异步处理

```dart
Future.delayed(new Duration(seconds: 2),(){
   //return "hello world!";
   throw AssertionError("Error");
}).then((data){
   //执行成功会走到这里
   print(data);
}).catchError((e){
   //执行失败会走到这里
   print(e);
}).whenComplete((){
   //无论成功或失败都会走到这里
});

// 多个异步任务
Future.wait([
  // 2秒后返回结果  
  Future.delayed(new Duration(seconds: 2), () {
    return "hello";
  }),
  // 4秒后返回结果  
  Future.delayed(new Duration(seconds: 4), () {
    return " world";
  })
]).then((results){
  print(results[0]+results[1]);
}).catchError((e){
  print(e);
});

// 使用 Future 消除 callback hell
login("alice","******").then((id){
      return getUserInfo(id);
}).then((userInfo){
    return saveUserInfo(userInfo);
}).then((e){
   // 执行接下来的操作
}).catchError((e){
  // 错误处理  
  print(e);
});
```

### 布局

Flutter 有一套自己的布局机制。其核心就是 Widget。在Flutter中，几乎所有的对象都是一个 Widget。

简单介绍一下：

```dart
new Text('Hello World', style: new TextStyle(fontSize: 32.0));

new Icon(Icons.star, color: Colors.red[500]);

new Center(
  child: new Text('Hello World', style: new TextStyle(fontSize: 32.0)
);
```

### Widget

与原生开发中“控件”不同的是，Flutter 中的 Widget 的概念更广泛，它不仅可以表示UI元素，也可以表示一些功能性的组件如：用于手势检测的 GestureDetector Widget、用于应用主题数据传递的 Theme 等等。

其实在 Flutter 中，Widget 的功能是“描述一个UI元素的配置数据”，Flutter 中真正代表屏幕上显示元素的类是 Element，也就是说 Widget 只是描述 Element 的一个配置。不过，由于 Element 是通过 Widget 生成，所以它们之间有对应关系，所以在大多数场景，我们可以宽泛地认为 Widget 树就是指UI控件树或UI渲染树。

```Dart
@immutable
// DiagnosticableTree 即“诊断树”，主要作用是提供调试信息。
abstract class Widget extends DiagnosticableTree {
  const Widget({ this.key });
  // key属性类似于 React/Vue 中的 key，主要的作用是决定是否在下一次 build 时复用旧的widget，决定的条件在 canUpdate()方法中。
  final Key key;

  @protected
  // 由 Flutter Framework 隐式调用，来生成对应的 Element 对象，构建 UI 树。
  Element createElement();

  @override
  String toStringShort() {
    return key == null ? '$runtimeType' : '$runtimeType-$key';
  }

  @override
  void debugFillProperties(DiagnosticPropertiesBuilder properties) {
    super.debugFillProperties(properties);
    properties.defaultDiagnosticsTreeStyle = DiagnosticsTreeStyle.dense;
  }

  // 判断是否用新的 Widget 对象去更新旧UI树上所对应的 Element 对象的配置。
  static bool canUpdate(Widget oldWidget, Widget newWidget) {
    return oldWidget.runtimeType == newWidget.runtimeType
        && oldWidget.key == newWidget.key;
  }
}
```

> 在Flutter开发中，我们一般都不用直接继承 Widget 类来实现 Widget，相反，我们通常会通过继承 StatelessWidget 和 StatefulWidget 来间接继承 Widget类来实现，而 StatelessWidget 和 StatefulWidget 都是直接继承自 Widget 类。

## 资源

[Flutter 官网](https://flutter.dev/)
[Flutter 中文网](https://flutterchina.club/)
[Awesome Flutter](https://github.com/Solido/awesome-flutter)
[Flutter 掘金社区](https://juejin.im/tag/Flutter)

## 小结

| 技术类型              | UI渲染方式      | 性能 | 开发效率        | 动态化     | 框架代表       |
| --------------------- | --------------- | ---- | --------------- | ---------- | -------------- |
| H5 + 原生             | WebView渲染     | 一般 | 高              | 支持       | Cordova、Ionic |
| JavaScript + 原生渲染 | 原生控件渲染    | 好   | 高              | 支持       | RN、Weex       |
| 自绘UI + 原生渲染     | 调用系统API渲染 | 好   | Flutter高，QT低 | 默认不支持 | QT、Flutter    |
