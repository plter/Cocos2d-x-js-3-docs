#Cocos2d Console使用手册

##背景

Cocos2d Console是一个可以为Cocos2d-JS极大简化游戏创建和环境配置过程的工具。

在Cocos2d-JS v3.0 alpha版中，由于重构了大量以前C++风格的API并为Javascript开发者提供了很友好的API设计，JSB的API也如此改造以保证Html5和JSB之间的代码共享，详细信息请参见[发布说明](../../release-notes/v3.0a/release-note/zh.md)。更重要的是，我们将Cocos2d-html5和Cocos2d-JSBinding合并为一个仓库：[Cocos2d-JS](https://github.com/cocos2d/cocos2d-js)。所以只要开发者使用JS开发游戏，那么不论游戏会发布到哪个平台，都可以使用Cocos2d-JS来进行开发。

##终端脚本工具

Cocos2d-JS提供了Cocos Console工具来简化html5和JSB游戏的创建和开发。开发者可以使用它来创建新项目，编译并发布游戏到android，iOS，Mac OS，iOS或者Web平台。并且它非常简单易用，下面将会展示它的安装与使用。

##安装

首先，你需要安装这个工具，当开发者将Cocos2d-JS仓库下载下来以后，会在根目录下发现`setup.py`安装文件。打开终端并进入Cocos2d-JS文件夹，然后运行`./setup.py`。在安装过程中，你可能需要提供你的NDK，Android SDK和ANT目录。请注意，这个工具是使用python来开发的，你将需要首先安装python 2.7，但是`setup.py`并不支持python3。

一些有用的链接和安装指引:

* [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)
* [Android SDK](https://developer.android.com/sdk/index.html?hl=sk)
* [NDK](https://developer.android.com/tools/sdk/ndk/index.html)
* [Ant binary release](http://ant.apache.org/)
    - 下载Ant。
    - 将Ant解压到你指定的文件夹。
    - 设置JAVA\_HOME环境变量到你的Java路径，ANT\_HOME到刚刚解压的文件夹，再将${ANT\_HOME}/bin (Unix)或%ANT\_HOME%/bin (Windows)加入到PATH中。（添加环境变量是指将某个可执行文件的路径导出为终端或命令行环境中可识别的命令）
    
    ```
    // 添加环境变量示例: 在命令行中运行或添加到.bash_profile(Mac)
    export ANT_ROOT=/usr/local/ant/bin
    export JAVA_HOME=/usr/local/jdk1.7.0_51
    ```

##Usage

成功安装以后，开发者就可以开始在终端中使用`cocos`命令。

###创建一个新项目

* 创建一个Cocos2d-JS项目:

	```
	cocos new projectName -l js
	```

* 创建一个仅支持web平台的项目:

	```
	cocos new projectName -l js --no-native
	```

* 创建项目到指定目录:

	```
	cocos new projectName -l js -d ./Projects
	```

###运行项目

* 使用浏览器运行web版项目:

	```
	cd directory/to/project
	cocos run -p web
	```

* 使用Closure Compiler高级模式压缩脚本并发布web版本 :

    ```
    cd directory/to/project
    cocos compile -p web -m release --advanced
    ```

* 编译并将项目运行在native平台上:

	```
	cd directory/to/project
	cocos compile -p ios|mac|android|web
	cocos run -p ios|mac|android
	```

* 选项

	```
	-p platform : 平台：ios|mac|android|web.
	-s source   : 项目目录，如果没有指明会使用当前路径。
	-q          : 静默模式，不打印log信息。
	-m mode     : 选择debug或release模式，默认为debug模式
	--source-map: 生成source-map文件。（仅限Web平台）
    --advanced  : 使用Closure Compiler高级模式压缩脚本。（仅限Web平台）
	```

###帮助命令

如果你对使用有任何疑问，可以在命令后使用`-h`来获取对应命令的帮助。下面是所有的命令：

* 创建：`new`
* 编译：`compile`
* 运行：`run`
