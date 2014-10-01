# 2.2 Cross native / browser game with Cocos Console (JSB dev environment)

The best thing about Cocos2d-JS is its perfect multi-platform ability: one code base really runs everywhere with Cocos2d-JS v3.0. In this first version of Cocos2d-JS, we have merged the web engine (Cocos2d-html5) and the native engine (Cocos2d-x JavaScript Bindings) together, so that we can provide a unified workflow for all platforms. With Cocos2d-JS, developers can focus on the JavaScript codes, the Cocos Console tool will help you to manager the project creation and publish process.

**Cocos2d-JS v3.0 Framework Structure**:

![](./Cocos2d-JS-Structure.jpg)

As we can see from the structure graph, Cocos2d-JS supports web platform via the web engine Cocos2d-html5, and the native platforms support is based on Cocos2d-x with JavaScript Binding technology. JavaScript Bindings use SpiderMonkey as JavaScript runtime engine and as a bridge to map Cocos2d-x's APIs to JavaScript APIs. Both web and native solutions were already verified by many games in 2.x versions, but this is the first time these two solutions are truly combined, unified and simplified. The Cocos2d-JS API level shown in the structure plays the real magic, it unified and simplified two base engine's APIs so that the differences of different platforms and different devices are transparent for developers. All this leads to the same goal: Make multiplatform development incredibly easy!

**All platforms supported by Cocos2d-JS**：

- Desktop Browsers（Chrome, Safari, Firefox, Opera, IE9+）
- Mobile Browsers（Android, iOS）
- iOS
- Android
- Mac OS X
- Windows

##1. Download Cocos2d-JS Engine Package

Please go to the [download page](http://www.cocos2d-x.org/download) to download the latest version. The package content is showing below:

|Directory or file|Content description|
|----------|-------|
|build                  |Include test cases and Moon Warriors sample's Xcode project and Visual Studio project|
|docs                   |Include JavaScript coding style guide, latest release note and upgrade guide|
|frameworks             |Include the web engine and the native engine|
| - cocos2d-html5       |Web engine|
| - js-bindings         |Cocos2d-x engine with JavaScript Bindings|
|samples                |Samples folder|
| - js-moonwarriors     |MoonWarriors sample game|
| - js-tests            |Cocos2d-JS test cases|
|templates              |Templates for Cocos Console|
|tools                  |Tools folder|
| - bindings-generator  |Automatic bindings generator tool|
| - cocos2d-console     |Cocos Console tool|
| - tojs                |Automatic JavaScript bindings configuration files and generation script|
|AUTHORS                |Authors list, including all contributors on github repository|
|CHANGELOG              |Change logs through all old versions|
|LICENSE                |Cocos2d-JS license|
|README.md              |Cocos2d-JS project introduction|
|setup.py               |Cocos Console's installation script|

##2. Install Cocos Console

We mentioned previously the unification and simplification of Cocos2d-JS's APIs, on the other hand, we have unified also the workflow for different platforms. Next up, we will introduce you how to use Cocos Console from project creation to publish.

After download the engine package, you can install Cocos Console by executing setup.py (Windows user may need to install [python 2.7](https://www.python.org/downloads/release/python-278/) first). This script will setup the `cocos` command and all its dependencies, during the installation, you need to provide your NDK root path, Android SDK path for Android compilation and ANT path for web source compilation. Please note that you need to re-execute setup.py while you upgrade the engine to make sure it's referencing to the newest engine.

Some useful links and installation tips:

* [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)
* [Android SDK](https://developer.android.com/sdk/index.html?hl=sk)
* [NDK](https://developer.android.com/tools/sdk/ndk/index.html)
* Download and install Ant：

    - [Download Ant](http://ant.apache.org/).
    - Decompress Ant to a destination folder.
    - Set the Java path as JAVA\_HOME environment variable, the Ant destination folder as ANT\_HOME environment variable. Then add ${ANT\_HOME}/bin (Unix) or %ANT\_HOME%/bin (Windows) to your system path.
    
    ```
    // Example: execute in terminal or add them to .bash_profile(Mac)
    export ANT_ROOT=/usr/local/ant/bin
    export JAVA_HOME=/usr/local/jdk1.7.0_51
    ```

##3. Project creation

It's very simple to create a project using Cocos Console, after installation of `cocos` command, you can go to you project's destination folder and open the terminal/command line tool, then execute the following command:

```
cocos new -l js ProjectName
```

If the creation succeed, you will find ProjectName folder under the destination folder, this project can be published to all supported platform. The newly created folder structure is different with the engine folder, here is a list of content and descriptions:

|Directory or file|Content description|
|----------|-------|
|frameworks             |Include the web engine and the native engine|
| - cocos2d-html5       |Web engine|
| - js-bindings         |Cocos2d-x engine with JavaScript Bindings|
| - runtime-src         |Project folders for all native platforms, including iOS/MacOSX/Android/Windows|
|res                    |The projects resources folder, should be used to save all images, audios, fonts, animations, etc|
|src                    |The projects scripts folder, should be used to save all JavaScript codes for your game|
|publish                |Initially not exist, when you publish your project under release mode, it will be created and contain the publish package|
|runtime                |This folder contains all runtime executables packaged under debug mode|
|tools                  |Tools folder|
| - bindings-generator  |Automatic bindings generator tool|
| - tojs                |Automatic JavaScript bindings configuration files and generation script|
|index.html             |Main web page for the project, it can be accessed via a web server and shows the game in action|
|main.js                |Entrance script for the game logic, contains initialization code|
|project.json           |Project's configuration file, detailed descriptions can be found in main.js|

##4. Fast development ability

Concrete APIs of Cocos2d-JS and their usage will not be discussed in this chapiter. However it's very important to mention that web platform support is not only a distribution channel for Cocos2d-JS, but also an important and useful tool for developers to make prototypes and to boost their development. 

The reasons that we suggest developers to use web platform during prototyping and development are the following:

- Click to play, no need to wait for compiling, you can see the result of your changes with a simple page reload.
- Browser's great JavaScript debug tool, you can refer to [Google Chrome DevTools Introduction](https://developer.chrome.com/devtools). It can help developers to quickly locate and solve issues, it also solved the problem that native IDEs can not debug JavaScript code.
- This approach suits better JavaScript developers.

##5. Native platform compilation

###5.1 Cocos Console

After finishing the development and debug on web platform, if developers want to test or publish their games on native platforms, then you will need to compile your project onto these platforms. Again, Cocos Console provided a very easy-to-use compilation command: `cocos compile`.

```
// Open the terminal or command line tool under the project's folder
// -p specify the targeted platform, it can be one of web, ios, mac, android, win32
cocos compile -p web|ios|mac|android|win32

// Besides, you can choose the publish mode with -m option and its value:  debug or release
// Under web platform, you can also add --advanced option to use Closure Compiler's advanced mode to compress and obfuscate your JavaScript scripts
cocos compile -p android -m release
cocos compile -p web -m release --advanced

// Replace compile command with run to execute the published executable on real devices or simulators
cocos run -p web
cocos run -p ios
```

More details about Cocos Console can be found in the [Cocos Console documentation](http://cocos2d-x.org/wiki/Cocos2d-console).

###5.2 Use IDE to compile the project

Under `frameworks/runtime-src/` folder, you can found project files of some platforms, including:

- proj.ios_mac: Xcode project for iOS and Mac OS X
- proj.win32：Visual Studio project for Windows
- proj.android：Eclipse project for Android

##6. Project publishing

###6.1 Native platform publishing

- Mac / Win32

    Execute this command can publish your project for Mac or Windows, you can found the executables under `publish` folder.

    ```
    cocos compile -p mac|win32 -m release
    ```

- iOS

    Execute this command can publish your project for iOS, specify your own code signing identity with `--sign-identity` option, you can also found the result under `publish/` folder.

    ```
    cocos compile -p ios -m release --sign-identity "iPhone Distribution:xxxxxxxx"
    ```

- Android
    
    1. Execute the command `cocos compile -p android -m release --ndk-mode release` to package an apk file.
    2. Find the apk file under `publish/` folder, then use jarsigner tool to sign the apk with your own android key store, detailed steps can be found in [Google's official document](http://developer.android.com/tools/publishing/app-signing.html)。

###6.2 Web platform publishing

Execute `cocos compile -p web -m release` will publish your project under `publish/` folder, you can deploy this individual folder on your web server, then it can be visited.

As mentioned before, add `--advance` option in the publish command can activate closure compiler's advanced compressing mode, you can benefit a higher compression ratio and even better performance. But it has some requirement in your code, if you found your game have issues with advanced mode, please refer to the [Closure Compiler Advanced Compilation Document](https://developers.google.com/closure/compiler/docs/api-tutorial3) for suggestions and solution.