# Cocos2d-JS v3.0 Final发布说明

<img src="http://www.cocos2d-x.org/attachments/download/1508" height=180> 

Cocos2d-JS是Cocos2d-x的JavaScript版本，融合了Cocos2d-html5和Cocos2d-x JavaScript Bindings。它支持Cocos2d-x的所有核心特性并提供更简单易用的JavaScript风格API，并且天然支持原生、浏览器跨平台应用。

在3.0版中，Cocos2d-JS完成了不同平台工作流的彻底整合，为不同平台提供了统一的开发体验。无论开发web应用还是原生应用，都可以便捷地采用Cocos2d-JS实现“一次开发，全平台运行”。采用Cocos2d-JS开发的同一套JavaScript游戏代码，可以同时运行在Mac OS X, Windows, iOS, Android等原生平台、以及所有现代浏览器上，这将使得我们的开发者轻松覆盖几乎所有发行渠道，带来前所未有的机遇。另一方面，若开发者只想开发一款Web轻度休闲游戏，Cocos2d-JS也专门为此类游戏定制了Lite Version，直接将Cocos2d-JS Lite Version集成到页面中即可使用。

作为工作流整合后的第一个版本，Cocos2d-JS v3.0兼具了简单和强大：新的JavaScript风格API使得编码，测试和发布环节都变得异常轻松简单；同时v3.0还提供了诸多强大的新特性，比如Spine动画支持，支持热更新的资源管理器，对象缓冲池，JS到Objective-C/JAVA反射等等。

## 工作流

- 跨平台游戏开发者可以使用Cocos Console来创建项目，用Web引擎加速游戏开发，最终用Cocos Console将游戏发布到所有原生平台和Web平台。

- Web端轻度休闲游戏开发者可以直接下载单文件的Cocos2d-JS Lite Version，嵌入Web页面中，像使用jQuery那样进行开发。

![](./workflows.jpg)

## 核心特性

* 统一了Web引擎和JSB引擎的API，保障“一次开发，全平台运行”的极致开发体验。
* JavaScript风格API重构：new构造方式，属性风格API，简化的动作API。
* 添加资源管理器来支持资源和脚本的热更新。
* 添加更为强大和灵活的新事件管理器。
* 添加JavaScript到JAVA/Objective-C反射。
* 添加Spine骨骼动画支持

## 注意事项

关于JSB编译环境，还有一些限制条件需要满足：

- [Android编译] NDK版本必须使用r9d
- [iOS编译] Xcode版本必须在5.1.1以上

## 下载

- [Cocos2d-JS v3.0 Final](http://www.cocos2d-x.org/filedown/cocos2d-js-v3.0.zip)
- [Cocos2d-JS v3.0 Lite Version](http://www.cocos2d-x.org/filecenter/jsbuilder)
- [在线API索引](http://www.cocos2d-x.org/reference/html5-js/V3.0/index.html)
- [下载版API索引](http://www.cocos2d-x.org/filedown/Cocos2d-JS-v3.0-API.zip)
- [在线测试例](http://cocos2d-x.org/js-tests/)

## 详细更改

更详细的改动列表和升级文档可以参见:

- [Cocos2d-JS v3.0改动说明](http://www.cocos2d-x.org/docs/manual/framework/html5/release-notes/v3.0/changelog/en)
- [Cocos2d-JS v3.0升级指南](http://www.cocos2d-x.org/docs/manual/framework/html5/release-notes/v3.0rc0/upgrade-guide/zh)

## 关于Cocos2d家族

- Cocos2d-JS v3.0 Final使用Cocos2d-x 3.2正式版作为JSB的底层实现
- Cocos2d-JS v3.0 Final兼容Cocos Code IDE v1.0.0 RC2+
- Cocos2d-JS v3.0 Final兼容Cocos Studio v1.2 - v1.5.0.1

如果遇到任何问题，你都可以向Cocos2d-JS开发者社区寻求帮助： 

- [官方论坛](http://discuss.cocos2d-x.org/category/cocos2d-x/javascript)
- [文档目录](http://cocos2d-x.org/docs/manual/framework/html5/zh)
- [Github仓库地址](https://github.com/cocos2d/cocos2d-js)

## 路线图

Cocos2d-JS已经极大简化了跨平台游戏开发。在接下来的进化中，我们会专注在优化性能，增强工具链和完善文档这几个方面。下面是v3.1中我们会尽力完成的核心任务：

- Web引擎的性能优化已经取得了长足的进展，Canvas渲染模式下的新渲染框架已经接近完成并会在v3.1中合并到主分支。根据多项性能测试，性能优化相较于当前版本最高达到50%。
- Facebook SDK for Cocos2d-JS也跟随v3.1发布，开发者将可以通过一致的JS代码使用Facebook在iOS/Android/Web平台上的所有特性。
- 研究JSB中的内存管理模式，提升JSB内存管理的稳定性和简化内存管理使用方式。
- 完成Cocos2d-JS使用指南，这个指南手册将覆盖Cocos2d-JS开发的方方面面。