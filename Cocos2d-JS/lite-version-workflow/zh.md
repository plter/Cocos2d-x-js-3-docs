# 2.1 用Cocos2d-JS Lite Version开发网页游戏/微信游戏（浏览器开发环境介绍）

Cocos2d-JS为纯Web开发者提供了特别定制的一套更友好的工作流，对于这些开发者来说，如果Cocos2d-JS可以像其他Web框架一样用单个文件被引入到用户页面中并可以直接使用的话，会更符合他们的开发习惯。而Cocos2d-JS引擎包看上去对于Web开发者来说实在是太庞大和复杂了，对于Web开发者来说，就好像被扔进了一个巨大的迷宫，完全不知道从哪下手。这甚至会给开发者一种错误的认知："Cocos2d-JS是一个非常重的引擎，不适合做Web游戏开发。"

实际上，在Cocos2d-JS的Web引擎实现模块化之后，可以非常优美得做到既强大又轻量。也正是以上原因，促使我们推出了Lite Version以及专为Lite Version定制的工作流供纯Web开发者使用。

这一章节中，我们会介绍Cocos2d-JS Lite Version的工作流。

## 下载Cocos2d-JS Lite Version

请在[下载页面](http://cocos2d-x.org/filecenter/jsbuilder/)中下载最新版本的Lite Version引擎。

除了Lite Version以外你还可以选择下载完整版引擎或定制一个自己专属的引擎版本。

与此同时，选中压缩选项可以帮助你减小引擎脚本的大小，但是会导致引擎代码不可读，所以请在开发阶段选择未压缩版，发布阶段替换为压缩版。当然，你也可以同时下载压缩版与未压缩版，方便切换。

## 下载内容

为了让开发者更易于使用，下载页面中下载下来的将是一个完整的HelloWorld项目，包含以下内容：

|文件名|描述|
|---------|---------|
|build.xml|[Google Closure Compiler](https://developers.google.com/closure/compiler/)代码压缩工具所使用的ant build配置文件，你可以下载Closure Compiler，修改配置文件中的js文件列表，并使用ant来压缩所有代码以获得非常高的压缩比|
|cocos2d-js-v3.0.js|这是Cocos2d-JS引擎的js文件，只需要将这个文件引入页面中，就可以使用Cocos2d-JS来开发游戏|
|HelloWorld.html|HelloWorld工程的主页面，其中包含HelloWorld游戏代码|
|HelloWorld.png|游戏所使用的图片文件|
|README.md|Cocos2d-JS Lite Version使用指南|

## 使用指南

你有可能下载了下面三个版本中的一个：

- **Cocos2d-JS Full Version**: 完整版引擎包含Cocos2d-JS引擎的所有功能特性以及所有扩展，使用这个版本可以帮助你发掘Cocos2d-JS令人惊艳的创造力和可能性。你可以从[官方文档首页](http://www.cocos2d-x.org/docs/manual/framework/html5/zh)中查看Cocos2d-JS所支持的特性列表。

- **Cocos2d-JS Lite Version**: 精简版本只包含Cocos2d-JS的核心特性，它的优势是稳定，轻量，简单易用。它所包含的特性列表如下：
    + Canvas渲染器 (不支持WebGL)
    + 场景和图层
    + 事件管理器
    + 计时器
    + 精灵和贴图
    + TTF文本
    + 声音
    + 动作
    + 菜单和菜单项

- **Customized Version**: 如果你选择了定制版本的引擎，引擎脚本文件将包含你所选择的所有特性。

### 使用方式

单文件引擎的使用方式和其他Web库完全一样，你只需要将脚本嵌入到你的页面中即可，可以参考下载目录中的HelloWorld.html。

请注意脚本的加载时机，在HelloWorld的示例代码中：

```
window.onload = function(){
    cc.game.onStart = function(){
        //...
    };
    cc.game.run("gameCanvas");
};
```

可以看到，`cc.game.onStart`和`cc.game.run`都在window的onload函数中，因为只有在这时才能保证cocos2d-js-v3.0.js已经被加载成功，并且可以在你的用户脚本中使用Cocos2d-JS的API。在我们后面章节中将提到Cocos2d-JS的另一种传统工作流，那种工作流中由引擎脚本控制脚本的加载顺序，所以不需要检测页面的加载。

另外，你需要将这些文件放到一个本地服务器或在线服务器并通过服务器访问才可以看到正确的结果。

### 帮助链接

- [在线API reference](http://www.cocos2d-x.org/reference/html5-js/V3.0/index.html)
- [下载API Reference](http://www.cocos2d-x.org/filedown/Cocos2d-JS-v3.0-API.zip)
- [文档目录](http://cocos2d-x.org/docs/manual/framework/html5/en)
- [Github仓库](https://github.com/cocos2d/cocos2d-js)
- [在线论坛](http://www.cocoachina.com/bbs/thread.php?fid=59)
- [在线测试例](http://cocos2d-x.org/js-tests/)

### 关于脚本压缩

你可以使用[Google Closure Compiler](https://developers.google.com/closure/compiler/)来将所有js文件压缩成一个文件，Closure Compiler的高级压缩压缩比非常高，即便你下载的是压缩版引擎，也可以获得可观的压缩比。具体压缩步骤如下：

1. 下载Closure Compiler的jar程序文件
2. 按照自己的环境配置build.xml
3. 在控制台运行ant命令
4. 将页面中的所有js引用删除，引入打包出的`game.min.js`

请注意，你不可以在html页面中写任何js脚本，所有js脚本都必须一起打包起来，否则会引起错误。

### 注意

当你通过服务器访问游戏页面的时候，你将在控制台中发现一个错误提示：
"Failed to load resource: the server responded with a status of 404 (Not Found)"，提示`project.json`文件找不到

这是正常现象，并不是问题，你可以忽略它。或者可以添加一个空的`project.json`文件来避免这个错误报告。

## 下一步

这一章节中讲述的主要是适合纯Web开发者所使用的工作流，如果你想更完整得体验Cocos2d-JS的强大跨平台能力，我们在下一章节将讲述如何使用Cocos2d-JS完整包来开发跨浏览器以及原生平台的跨端游戏。