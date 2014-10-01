# 2.2 用Cocos Console工作流开发网页/原生平台游戏（JSB开发环境简介）

Cocos2d-JS最引以为傲的能力就是完美的一次开发，全平台部署得能力。得益于3.0版中Web引擎和Native引擎的整合，Cocos2d-JS为Web和各原生平台开发提供了一套统一的工作流，开发者只需要关注自己的JavaScript代码，我们提供了Cocos Console工具帮助用户管理开发以及发布的流程。

**Cocos2d-JS v3.0框架**：

![](./Cocos2d-JS-Structure.jpg)

从这张架构图中可以看出，Cocos2d-JS对Web平台的支持主要是通过Web引擎Cocos2d-html5做到的，而对Native平台的支持是以Cocos2d-x为核心，通过SpiderMonkey这个JavaScript编译器来执行JavaScript代码，并通过JavaScript绑定技术将其API映射到Cocos2d-x的API上。这两方面的技术从引擎的v2.x时代就已经成熟了，而Cocos2d-JS v3.0首次将两者整合到一起，统一并简化它们的API，真正做到一套代码，全平台运行。这也就是图中的Cocos2d-JS API层，不同平台，不同设备之间的差异被透明化，开发者开发跨平台游戏自然也变得前所未有得简单。

**Cocos2d-JS引擎所支持的平台包括**：

- 桌面浏览器（Chrome, Safari, Firefox, Opera, IE9+）
- 移动浏览器（Android, iOS）
- iOS
- Android
- Mac OS X
- Windows

##1. 下载引擎包

请前往[引擎下载页面](http://www.cocos2d-x.org/download)下载最新的引擎包。下载完成后包内容如下所示：

|目录或文件名|内容简介|
|----------|-------|
|build                  |包含测试例和MoonWarriors示例的Xcode工程以及Visual Studio工程|
|docs                   |包含JavaScript代码风格规范，当前发布说明和当前版本升级指南|
|frameworks             |包含Web引擎以及Native引擎|
| - cocos2d-html5       |Web引擎|
| - js-bindings         |Cocos2d-x引擎以及JSB框架|
|samples                |示例项目文件夹|
| - js-moonwarriors     |MoonWarriors示例项目|
| - js-tests            |测试例项目|
|templates              |Cocos Console使用的模版|
|tools                  |工具文件夹|
| - bindings-generator  |JSB自动绑定生成工具|
| - cocos2d-console     |Cocos Console工具|
| - tojs                |JSB自动绑定配置文件以及生成脚本|
|AUTHORS                |作者目录，包含所有给Cocos2d-JS项目贡献代码的开发者|
|CHANGELOG              |所有历史版本详细改动列表|
|LICENSE                |Cocos2d-JS许可协议|
|README.md              |Cocos2d-JS项目简介|
|setup.py               |Cocos Console的安装脚本|

##2. 安装Cocos Console

前文提到Cocos2d-JS的API简化和统一，另一方面，我们还通过Cocos Console工具统一了不同平台下的工作流，下面将介绍如何安装和使用Cocos Console完成从工程创建到发布的工作流。

引擎包下载完成后，请在控制台中运行setup.py（windows平台用户可能需要先安装[python 2.7](https://www.python.org/downloads/release/python-278/)），这个脚本会添加cocos命令及它依赖的环境变量，在安装过程中，你可能需要提供你的NDK，Android SDK和ANT目录。请注意，如果是升级引擎，同样需要重新运行最新版本的setup.py，以保证cocos命令的引用是最新引擎的。

一些有用的链接和安装指引：

* [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)
* [Android SDK](https://developer.android.com/sdk/index.html?hl=sk)
* [NDK](https://developer.android.com/tools/sdk/ndk/index.html)
* Ant下载与安装：

    - [下载Ant](http://ant.apache.org/)。
    - 将Ant解压到你指定的文件夹。
    - 设置JAVA\_HOME环境变量到你的Java路径，ANT_HOME到刚刚解压的文件夹，再将${ANT\_HOME}/bin (Unix)或%ANT\_HOME%/bin (Windows)加入到PATH中。
    
    ```
    // Example: 在命令行中运行或添加到.bash_profile(Mac)
    export ANT_ROOT=/usr/local/ant/bin
    export JAVA_HOME=/usr/local/jdk1.7.0_51
    ```

##3. 工程创建

使用Cocos Console创建工程非常简单，安装完cocos命令之后，只需要在需要创建工程的目标目录下打开终端或命令行工具，输入下面的命令即可：

```
cocos new -l js ProjectName
```

创建成功之后就会在目标目录下发现ProjectName文件夹，这个工程将可以被发布到所有支持的平台。新创建工程的目录结构与引擎包有一些区别，下面是详细的列表介绍：

|目录或文件名|内容简介|
|----------|-------|
|frameworks             |包含Web引擎以及Native引擎|
| - cocos2d-html5       |Web引擎|
| - js-bindings         |Cocos2d-x引擎以及JSB框架|
| - runtime-src         |项目的各平台工程文件，包含iOS/MacOSX/Android/Windows|
|res                    |项目资源文件夹，应该用来存储所有图片，音频，字体，动画等资源|
|src                    |项目脚本文件夹，应该用来存储游戏的所有JavaScript代码|
|publish                |该目录初始状态下不存在，当工程以发布模式打包后，会创建该文件夹并保存对应平台的发布包|
|runtime                |该目录用来存储调试模式打包的工程执行文件|
|tools                  |工具文件夹|
| - bindings-generator  |JSB自动绑定生成工具|
| - tojs                |JSB自动绑定配置文件以及生成脚本|
|index.html             |Web工程的主页面，通过本地服务器访问这个页面即可看到游戏效果|
|main.js                |游戏入口文件，其中包含游戏初始化代码以及启动代码|
|project.json           |工程配置文件，详细配置方法可参考main.js中的注释|

##4. 快速开发能力

具体的Cocos2d-JS API使用以及开发技巧并不在本章的讨论范围内，但在本章工作流的讨论中需要提到的是，Web平台支持给Cocos2d-JS带来了非常优秀的快速原型和快速开发能力。

我们建议开发者在原型阶段甚至开发阶段使用Web平台进行游戏逻辑调试的原因有下面几点：

- 点击即玩，不需要编译，并且修改代码后只需要刷新页面即可看到效果。
- 浏览器强大的JavaScript调试工具，可以参考[Google Chrome DevTools简介](https://developer.chrome.com/devtools)，它可以帮助开发者快速定位和解决问题。同时也解决了Xcode和Visual Studio等IDE无法调试JavaScript脚本代码的问题。
- 更符合JavaScirpt开发人员的习惯。

##5. Native平台编译

###5.1 Cocos Console

在Web平台调试完成之后，如果开发者需要将游戏发布到原生平台，那么还需要针对相应平台进行编译调试工作。Cocos Console再次提供了非常便捷并且统一的编译能力，使用`cocos compile`命令即可将工程编译到不同平台：

```
// 在项目目录下打开终端并执行
// -p指示目标平台，包括web，ios，mac，android，win32，每次编译请选择其中之一
cocos compile -p web|ios|mac|android|win32

// 除此之外，还可以选择编译模式：-m debug|release
// Web平台还可以添加--advanced来使用Closure Compiler的高级混淆功能对代码进行混淆
cocos compile -p android -m release
cocos compile -p web -m release --advanced

// 将compile命令替换为run即可在模拟器或真机上运行游戏
cocos run -p web
cocos run -p ios
```

关于Cocos Console更详细的使用方式，请参考[Cocos Console文档](http://cocos2d-x.org/wiki/Cocos2d-console)。

###5.2 使用各平台IDE编译

在`frameworks/runtime-src/`目录下，开发者可以找到各平台的工程文件，包括：

- proj.ios_mac：可用于Xcode的iOS和Mac OS X项目文件夹
- proj.win32：可用于Visual Studio的Windows项目文件夹
- proj.android：可用于Eclipse的Android项目文件夹

##6. 项目发布

###6.1 Mac / Win32平台发布

直接运行`cocos compile -p mac|win32 -m release`即可打包出发布包，运行成功后可以在`publish/`目录下找到可执行文件。

###6.2 iOS平台发布

运行`cocos compile -p ios -m release --sign-identity "iPhone Distribution:xxxxxxxx"`使用你自己的发布签名来打包出发布包，结果同样可以在`publish/`目录下找到。

###6.3 Android平台发布

1. 运行`cocos compile -p android -m release --ndk-mode release`打包出apk包。
2. 在`publish/`目录下找到打包出的apk，使用jarsigner工具对该apk添加签名，具体步骤见[Google官方文档](http://developer.android.com/tools/publishing/app-signing.html)。

###6.4 Web平台发布

运行`cocos compile -p web -m release`既可在`publish/`目录下生成独立的发布文件夹，将该文件夹部署到你的服务器上，用户即可访问。

命令中添加`--advance`可以开启closure compiler工具高级压缩功能，该模式对代码有一定要求，如果发现打包之后无法正常运行请参考[Closure Compiler Advanced Compilation文档](https://developers.google.com/closure/compiler/docs/api-tutorial3)。
