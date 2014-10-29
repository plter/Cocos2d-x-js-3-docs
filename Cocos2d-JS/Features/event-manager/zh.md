# 事件分发机制

## 简介
游戏开发中一个很重要的功能就是交互，如果没有与用户的交互，那么游戏将变成动画，而处理用户交互就需要使用事件监听器了。

总概：

- **事件监听器(cc.EventListener)** 封装用户的事件处理逻辑
- **事件管理器(cc.eventManager)** 管理用户注册的事件监听器，根据触发的事件类型分发给相应的事件监听器
- **事件对象(cc.Event)** 包含事件相关信息的对象

如何使用呢？ 首先需要创建一个事件监听器，事件监听器包含以下几种类型：

* 触摸事件监听器 (cc.EventListenerTouch)
* 键盘事件监听器 (cc.EventListenerKeyboard)
* 加速计事件监听器 (cc.EventListenerAcceleration)
* 鼠标事件监听器 (cc.EventListenerMouse)
* 自定义事件监听器 (cc.EventListenerCustom)

在监听器中实现各种事件的处理逻辑，然后将监听器加入到事件管理器中, 当事件触发时，事件管理器会根据事件类型分发给相应的事件监听器。下面以一个简单的示例来演示使用的方法。

## 使用方法

现在会在一个场景中添加三个按钮(cc.Sprite)，三个按钮将会互相遮挡，并且都需要能够监听和处理触摸事件，以下是具体实现

### 首先创建三个精灵，作为三个按钮的显示图片

```javascript

	var sprite1 = new cc.Sprite("Images/CyanSquare.png");
	sprite1.x = size.width/2 - 80;
	sprite1.y = size.height/2 + 80;
    this.addChild(sprite1, 10);
    
    var sprite2 = new cc.Sprite("Images/MagentaSquare.png");
	sprite2.x = size.width/2;
	sprite2.y = size.height/2;
    this.addChild(sprite2, 20);
    
    var sprite3 = new cc.Sprite("Images/YellowSquare.png");
	sprite3.x = 0;
	sprite3.y = 0;
    sprite2.addChild(sprite3, 1);
	
```

![touchable_sprite](res/touchable_sprite.png)

### 创建一个单点触摸事件监听器(事件类型：TOUCH_ONE_BY_ONE)，并完成逻辑处理内容

```javascript

	// 创建一个事件监听器 OneByOne 为单点触摸
    var listener1 = cc.EventListener.create({
	    event: cc.EventListener.TOUCH_ONE_BY_ONE,
	    swallowTouches: true,						// 设置是否吞没事件，在 onTouchBegan 方法返回 true 时吞掉事件，不再向下传递。
	    onTouchBegan: function (touch, event) {		//实现 onTouchBegan 事件处理回调函数
		    var target = event.getCurrentTarget();	// 获取事件所绑定的 target, 通常是cc.Node及其子类 
		    
			// 获取当前触摸点相对于按钮所在的坐标
		    var locationInNode = target.convertToNodeSpace(touch.getLocation());	
		    var s = target.getContentSize();
		    var rect = cc.rect(0, 0, s.width, s.height);
		    
		    if (cc.rectContainsPoint(rect, locationInNode)) {		// 判断触摸点是否在按钮范围内
			    cc.log("sprite began... x = " + locationInNode.x + ", y = " + locationInNode.y);
			    target.opacity = 180;
			    return true;
		    }
		    return false;
	    },
	    onTouchMoved: function (touch, event) {			//实现onTouchMoved事件处理回调函数, 触摸移动时触发
		    // 移动当前按钮精灵的坐标位置
			var target = event.getCurrentTarget();
		    var delta = touch.getDelta();              //获取事件数据: delta
		    target.x += delta.x;
		    target.y += delta.y;
	    },
	    onTouchEnded: function (touch, event) {			// 实现onTouchEnded事件处理回调函数
		    var target = event.getCurrentTarget();
		    cc.log("sprite onTouchesEnded.. ");
		    target.setOpacity(255);
		    if (target == sprite2) {					
		    	sprite1.setLocalZOrder(100);			// 重新设置 ZOrder，显示的前后顺序将会改变
		    } else if (target == sprite1) {
		    	sprite1.setLocalZOrder(0);
		    }
	    }
    });
	
```

引擎提供了**cc.EventListener.create**统一来创建各类型的事件监听器，可以通过指定不同的 `event` 来设置想要创建的监听器类型，如上例中的cc.EventListener.TOUCH_ONE_BY_ONE 为单点触摸事件监听器。

可选`event`类型列表: 

1. cc.EventListener.TOUCH_ONE_BY_ONE (单点触摸)
2. cc.EventListener.TOUCH_ALL_AT_ONCE (多点触摸)
3. cc.EventListener.KEYBOARD (键盘)
4. cc.EventListener.MOUSE  (鼠标)	
5. cc.EventListener.ACCELERATION (加速计)
6. cc.EventListener.CUSTOM (自定义)


### 将事件监听器添加到事件管理器中

```javascript

	// 添加监听器到管理器
	cc.eventManager.addListener(listener1, sprite1);
	cc.eventManager.addListener(listener1.clone(), sprite2);
	cc.eventManager.addListener(listener1.clone(), sprite3);
```

这里的**cc.eventManager** 是一个单例对象，可直接拿来使用。通过调用 `addListener` 函数可以将listener加入到管理器中，需要注意的是第二个参数，如果传入的是一个Node对象，则加入的是SceneGraphPriority(精灵以显示优先级) 类型的listener,如果是一个数值类型的参数，则加入到的是FixedPriority 类型的listener。

_注意：_ 这里当我们想给不同的节点使用相同的事件监听器时，需要使用 `clone()` 函数克隆出一个新的监听器，因为在使用 `addListener` 方法时，会对当前使用的事件监听器添加一个已注册的标记，这使得它不能够被添加多次。另外，有一点非常重要，FixedPriority 类型的 listener添加完之后需要手动删除，而SceneGraphPriority 类型的 listener是跟node绑定的，在node调用cleanup时会被移除。具体的示例用法可以参考引擎自带的tests。

### 更快速的添加事件监听器到管理器的方式
下面提交一种更快捷绑定事件到节点的方式， 不过这样做就不会得到监听器的引用，无法再对监听器进行其他操作，适用于一些简单的事件操作， 代码如下：

```javascript

    cc.eventManager.addListener({
	    event: cc.EventListener.TOUCH_ALL_AT_ONCE,
	    onTouchesMoved: function (touches, event) {
		    var touch = touches[0];
		    var delta = touch.getDelta();
		    
		    var node = event.getCurrentTarget().getChildByTag(TAG_TILE_MAP);
		    var diff = cc.pAdd(delta, node.getPosition());
		    node.setPosition(diff);
	    }
    }, this);
```

**cc.eventManager**的 `addListener` 的第一个参数也支持两种类型的参数: `cc.EventListener` 类型对象和json格式的对象，如果是json格式对象，方法会根据传入的`event`属性来创建对应的监听器。

### 新的触摸机制

以上的步骤相对于 2.x 版本触摸机制实现，稍显复杂了点。在老的版本中只需在节点中重载 `onTouchBegan`/`onTouchesBegan` 等方法, 处理对应的触摸事件逻辑，然后调用`cc.registerTargetedDelegate` 或`cc.registerStandardDelegate` 将节点加入到触摸事件分发器中就可以了，甚至有些已封装的类只需要调用`setTouchEnabled`, 就可以开启触摸事件，比如：`cc.Layer`。

而新机制将事件处理逻辑独立出来，封装到一个 监听器(listner) 中，使得不同对象可以使用同一份监听器代码（使用`clone()`来达到目的)。另外，**cc.eventManager**加入了精灵以显示优先级 (SceneGraphPriority)排序的功能，以这种类型注册的监听器，事件管理器会根据屏幕显示的情况来决定事件会优化分发给哪个事件监听器。 而2.x要实现这样的功能却非常的麻烦，需要用户自己通过调用`setPriority`来管理节点的事件响应优先级。

_注意：_与 **SceneGraphPriority** 所不同的是 **FixedPriority** 将会依据手动设定的 `Priority` 值来决定事件相应的优先级，值越小优先级越高， 后面章节中会作更具体的讲解。

## 其它事件派发处理模块

除了触摸事件响应之外，还可以使用相同的事件处理方式来处理其他事件。

### 键盘响应事件

除了可以监听键盘按键，还可以是终端设备的各个菜单键，都能使用同一个监听器来进行处理。

```javascript

	//给statusLabel绑定键盘事件
	cc.eventManager.addListener({
	    event: cc.EventListener.KEYBOARD,
	    onKeyPressed:  function(keyCode, event){
		    var label = event.getCurrentTarget();
			//通过判断keyCode来确定用户按下了哪个键
		    label.setString("Key " + keyCode.toString() + " was pressed!");
	    },
	    onKeyReleased: function(keyCode, event){
		    var label = event.getCurrentTarget();
		    label.setString("Key " + keyCode.toString() + " was released!");
	    }
    }, statusLabel);    
```

### 加速计事件

在使用加速计事件监听器之前，需要先启用此硬件设备, 代码如下：

`cc.inputManager.setAccelerometerEnabled(true);`

然后将相应的事件处理监听器与sprite进行绑定就可以了，如下：

```javascript

        cc.eventManager.addListener({
            event: cc.EventListener.ACCELERATION,
            callback: function(acc, event){
 				//这里处理逻辑
            }
        }, sprite);	
```
### 鼠标响应事件

对于PC和超级本，添加鼠标事件的的处理，可以加强用户的体验，其处理逻辑与触摸事件基本一样，多了一些鼠标特有的事件响应，如滚轮事件(onMouseScroll).

```javascript

    cc.eventManager.addListener({
	    event: cc.EventListener.MOUSE,
	    onMouseMove: function(event){
		    var str = "MousePosition X: " + event.getLocationX() + "  Y:" + event.getLocationY();
		    // do something...
	    },
	    onMouseUp: function(event){
		    var str = "Mouse Up detected, Key: " + event.getButton();
		    // do something...
	    },
	    onMouseDown: function(event){
		    var str = "Mouse Down detected, Key: " + event.getButton();
		    // do something...
	    },
	    onMouseScroll: function(event){
		    var str = "Mouse Scroll detected, X: " + event.getLocationX() + "  Y:" + event.getLocationY();
		    // do something...
	    }
    },this);

```

_注意：_ 由于在PC浏览器中，没有触摸事件，而此时强制要求用户写鼠标事件的响应代码，必然会让开发者多写很多代码，事实上触摸响应的逻辑与鼠标相差不大，所以引擎在检测到不支持触摸事件时，会让鼠标事件模拟成触摸事件进行分发，开发者只需编写触摸事件监听器就能完成大部分工作，而对于针对鼠标操作而设计的游戏，需要判断用户按下什么键，响应滚轮等，这就需要开发者编写鼠标事件监听器了。

*（开发者反馈，鼠标事件监听器也需要有swallowTouches这个选项，我们将会有v3.1版本中加入这个项.）*

### 自定义事件

以上是系统自带的事件类型，这些事件由系统内部自动触发，如 触摸屏幕，键盘响应等，除此之外，还提供了一种 自定义事件，简而言之，它不是由系统自动触发，而是人为的干涉，如下：

```javascript

    var _listener1 = cc.EventListener.create({
	    event: cc.EventListener.CUSTOM,
	    eventName: "game_custom_event1",
	    callback: function(event){
			// 可以通过getUserData来设置需要传输的用户自定义数据
	    	statusLabel.setString("Custom event 1 received, " + event.getUserData() + " times");
	    }
    });    
    cc.eventManager.addListener(this._listener1, 1);
```

以上定义了一个 “自定义事件监听器”，实现了一些逻辑, 并且添加到事件分发器。那么以上逻辑是在什么情况下响应呢？请看如下：

```javascript
	
    ++this._item1Count;
    var event = new cc.EventCustom("game_custom_event1");
    event.setUserData(this._item1Count.toString());
    cc.eventManager.dispatchEvent(event);		
```

创建了一个自定义事件（`EventCustom`）对象 ，并且设置了其用户自定义(UserData)数据，手动调用 `cc.eventManager.dispatchEvent(event);` 将此事件分发出去，从而触发之前监听器中所实现的逻辑。

cc.eventManager加入自定义事件的处理，开发者就可以很方便的使用该功能来实现观察者模式。

### 移除事件监听器

我们可以通过以下方法移除一个已经被添加了的监听器。

```javascript

	cc.eventManager.removeListener(listener);			//移除一个已添加的监听器
```

也可以使用`removeListeners`，移除注册到`cc.eventManager`中指定类型的所有监听器，当然使用该函数时，传入的参数如果是一个节点（cc.Node及其子类）对象， 事件管理器将移除与该对象相关的所有事件监听器， 代码如下：

```javascript

	cc.eventManager.removeListeners(cc.EventListener.TOUCH_ONE_BY_ONE);			//移除所有单点触摸事件监听器
	cc.eventManager.removeListeners(aSprite);									//移除所有与aSprite相关的监听器
```

事件管理器还提供了函数用来移除已注册的所有监听器。

```javascript

	cc.eventManager.removeAllListeners();
```
当使用 `removeAllListeners` 的时候，此节点的所有的监听将被移除，推荐使用 指定删除的方式。

_注意：调用`removeAllListeners` 之后 菜单(`cc.Menu`) 也不能响应。因为它内部有一个触摸事件监听器，也会从事件管理器中删除。

### 暂停/恢复 与场景相关(SceneGraph类型)的监听器

开发过程中，我们经常会遇到这样的情况：想要让一个Layer中所有的Node对象的事件都停止响应。 在响应用户事件后，又要恢复该Layer的所有事件响应。如： 用户想要显示一个模式对话框，显示对话框后，禁止对话框后所有对象的事件响应。 在用户关闭对话框后，又恢复这些对象的事件响应。

我们只需要暂停根node的事件，就可以让根节点以及其子节点暂停事件响应。 代码如下：

```javascript

	cc.eventManager.pauseTarget(aLayer, true);						//让aLayer对象暂停响应事件
```
而恢复对象的事件响应也非常简单：

```javascript

	cc.eventManager.resumeTarget(aLayer, true);						//让aLayer对象恢复响应事件
```

_注意_: 第二个参数为可选参数，默认值为false, 表示是否递归调用子节点的暂停/恢复操作.

## 进阶话题

### SceneGraphPriority类型与FixedPriority类型详解

事件管理器将监听器类型分为两大类：SceneGraphPriority和FixedPriority， 下面将会详细说明它们之间的区别， 并介绍FixedPriority的使用场景与使用方法。

**SceneGraphPriority**事件类型是指事件的响应优先级与监听器关联对象在场景中显示顺序（zOrder）相关， 比如一个精灵对象在场景的显示在最上层时，它对事件的响应优先级最高。 这样开发者就不需要再像v2.x中那样在场景对象的zOrder变化后，手动再调用`setPriority`来改变相应的优先级了，这些事将交由管理器来处理。

而 **FixedPriority** 事件类型则是相对于 **SceneGraphPriority** 来定义的，不需要与场景显示顺序相关的事件监听器 也就是优化级固定的(fixed)，就可以注册成FixedPriority类型事件。 我们的SceneGraphPriority定义的系统优先级是**0**, 在添加监听器（`addListener`）时， 如果第二个参数设置为负数时，该监听器就会拥有比所有场景相关监听器都高的优先级， 而如果是正数，则反之。

那么什么情况下使用FixedPriority类型的监听器呢？ 比如，一个冒险类的游戏中，游戏主角应该要最先响应触摸事件，而UI界面的按钮往往会安排在界面的最上层。但是，如果主角移动到了按钮的后面，这时点击游戏主角，如果游戏主角注册的是SceneGraphPriority类型监听器，响应的将会是按钮事件。而如果注册成FixedPriority类型，并把它的优先级设置为负数，将会响应游戏主角的事件。

有开发者反馈想保持他们在v2.x的响应优先级管理机制，因为他们有特殊的需求，那么这部分开发者也可以使用FixedPriority来管理，`cc.eventManager` 也提供了一个`setPriority`函数来管理优先级。 

### UI控件的事件处理详解

Cocos提供一套UI控件，许多开发者对于控件的事件响应，特别是对于容器类控件(如:ccui.PageView, ccui.ScrollView)的事件响应有些疑惑。这里将详细说明控件的事件处理流程。

首先来看一下`ccui.Widget`的事件实现, 所有的控件的事件监听器都是单点触摸事件，并且会吞食事件，注册代码如下：

```javascript

	this._touchListener = cc.EventListener.create({
		event: cc.EventListener.TOUCH_ONE_BY_ONE,
        swallowTouches: true,
        onTouchBegan: this.onTouchBegan.bind(this),
        onTouchMoved: this.onTouchMoved.bind(this),
        onTouchEnded: this.onTouchEnded.bind(this)
     });
     cc.eventManager.addListener(this._touchListener, this);
```

然后看一下它的各事件响应函数，会发现每个函数都会有类似这样的语句：
`if (widgetParent) widgetParent.interceptTouchEvent(ccui.Widget.TOUCH_XXXXX, this, touch);`

这句的意思是，在控件处理完自己的触摸事件之后，都会向父节点(widgetParent)发送事件响应通知。那么，interceptTouchEvent的实现是什么呢？ 代码如下：

```javascript

	interceptTouchEvent: function(eventType, sender, touch){
        var widgetParent = this.getWidgetParent();
        if (widgetParent)
            widgetParent.interceptTouchEvent(eventType,sender,touch);
    }

``` 
对于像ccui.Button, ccui.ImageView这样的控件，它只是简单的向父类发送事件通知就行了，而对于像ccui.PageView这样的容器类控件，会对这些通知做出响应，代码如下：

```javascript

    interceptTouchEvent: function (eventType, sender, touch) {
        var touchPoint = touch.getLocation();
        switch (eventType) {
            case ccui.Widget.TOUCH_BEGAN:
                this._touchBeganPosition.x = touchPoint.x;
                this._touchBeganPosition.y = touchPoint.y;
                break;
            case ccui.Widget.TOUCH_MOVED:
                this._touchMovePosition.x = touchPoint.x;
                this._touchMovePosition.y = touchPoint.y;
                var offset = 0;
                offset = Math.abs(sender.getTouchBeganPosition().x - touchPoint.x);
                if (offset > this._childFocusCancelOffset) {
                    sender.setFocused(false);
                    this._handleMoveLogic(touch);  
                }
                break;
            case ccui.Widget.TOUCH_ENDED:
            case ccui.Widget.TOUCH_CANCELED:
                this._touchEndPosition.x = touchPoint.x;
                this._touchEndPosition.y = touchPoint.y;
                this._handleReleaseLogic(touch);
                break;
        }
    }
```
这样的处理，就能实现在按钮上滑动时，也能让其父节点的PageView触摸事件。不然，如果不采用这种机制，当一个PageView中填满了子控件时，PageView将无法响应触摸事件。

## 属性与方法列表

### cc.Event (事件类)
| 属性/方法 | 类型 | 参数说明 | 用法说明 |
|:------:|:--:|:----------:|:-------|
| getType | Number | no | 返回事件类型，包含：TOUCH, KEYBOARD, ACCELERATION, MOUSE, CUSTOM|
| stopPropagation | void | no | 停止当前事件的冒泡 |
| isStopped | Boolean | no | 事件是否已停止 |
| getCurrentTarget | cc.Node | no | 返回事件相关的Node对象, 如果事件未与cc.Node对象关联，则返回null |

### cc.EventCustom (自定义事件)
`cc.EventCustom` 继承自 `cc.Event`

| 属性/方法 | 类型 | 参数说明 | 用法说明 |
|:------:|:--:|:----------:|:-------|
| setUserData | void | data: 要设置的自定义数据 | 设置用户自定义数据 |
| getUserData | * | no | 返回用户设置的自定义数据 |
| getEventName | String | no | 返回自定义事件名称 |

### cc.EventMouse (鼠标事件)
`cc.EventMouse` 继承自 `cc.Event`

| 属性/方法 | 类型 | 参数说明 | 用法说明 |
|:------:|:--:|:----------:|:-------|
| setScrollData | void | scrollX， scrollY | 设置滚轮数据 |
| getScrollX | Number | no | 返回x轴滚轮数据 |
| getScrollY | Number | no | 返回y轴滚轮数据 |
| setLocation | void | x, y | 设置鼠标光标位置 |
| getLocation | cc.Point | no | 获取鼠标光标位置 |
| getLocationInView | cc.Point | no | 返回鼠标光标在屏幕上的位置 |
| getDelta | cc.Point | no | 获取当前光标与上一光标的偏移量 |
| setButton | void | button | 设置鼠标按键 |
| getButton | Number | no | 获取鼠标按键 |

### cc.EventTouch ()
`cc.EventTouch` 继承自 `cc.Event`

| 属性/方法 | 类型 | 参数说明 | 用法说明 |
|:------:|:--:|:----------:|:-------|
| getEventCode | Number | no | 获取触摸事件类型代码: BEGAN, MOVED, ENDED, CANCELLED |
| getTouches | Array | no | 获取触摸事件中所有点信息 |


### cc.EventListener (事件监听器)

| 属性/方法 | 类型 | 参数说明 | 用法说明 |
|:------:|:--:|:----------:|:-------:|
| checkAvailable | boolean | no | 检测监听器是否有效 |
| clone | cc.EventListener | no | 克隆一个监听器,其子类会重写本函数 |
| create <static> | cc.EventListener | json object | 通过json对象创建事件监听器 |

### `cc.EventListener.create` 函数参数列表：

**创建EventListenerTouchOneByOne对象:**

event: cc.EventListener.TOUCH_ONE_BY_ONE
 
可选参数:

1. swallowTouches, boolean, 是否吞下该touch点
2. onTouchBegan, function, TouchBegan 事件回调
3. onTouchMoved, function, TouchMoved 事件回调
4. onTouchEnded, function, TouchEnded 事件回调
5. onTouchCancelled, function, TouchCancelled 事件回调

**创建EventListenerTouchAllAtOnce对象：**

event: cc.EventListener.TOUCH_ALL_AT_ONCE

可选参数:

1. onTouchesBegan, function, TouchesBegan 事件回调
2. onTouchesMoved, function, TouchesMoved 事件回调
3. onTouchesEnded, function, TouchesEnded 事件回调
4. onTouchesCancelled, function, TouchesCancelled 事件回调


**创建EventListenerKeyboard对象:**

event: cc.EventListener.KEYBOARD

可选参数:

1. onKeyPressed, function, KeyPressed (键按下) 事件回调
2. onKeyReleased, function, keyRelease (键放开) 事件回调

**创建EventListenerMouse对象:**

event: cc.EventListener.MOUSE

可选参数:

1. onMouseDown, function, MouseDown 事件回调
2. onMouseUp, function, MouseUp 事件回调
3. onMouseMove, function, MouseMove 事件回调
4. onMouseScroll, function, MouseScroll 事件回调

**创建EventListenerAcceleration对象:**

event: cc.EventListener.ACCELERATION

可选参数:

1. callback, function, Acclerometer 事件回调

**创建EventListenerCustom对象:**

event: cc.EventListener.CUSTOM

可选参数:

1. callback, function, 自定义事件回调

### cc.eventManager

| 属性/方法 | 类型 | 参数说明 | 用法说明 |
|:------:|:--:|:----------:|:-------:|
| pauseTarget | void | node, recursive(是否递归调用子类)  | 暂停传入的node相关的所有监听器的事件响应 |
| resumeTarget | void | node, recursive | 恢复传入的node相关的所有监听器的事件响应 |
| addListener | void | json对象或cc.EventListener, node对象或优化值 |  向事件管理器添加一个监听器 |
| addCustomListener | void | eventName, callback | 向事件管理器添加一个自定义事件监听器 |
| removeListener | void | listener | 移除一个事件监听器 |
| removeListeners | void | listenerType|cc.Node, recursive | 移除某一类型或某一node对象相关的所有监听器 |
| removeCustomListeners | void | customEventName | 移除同一事件名的自定义事件监听器 |
| removeAllListeners | void | no | 移除所有事件监听器 |
| setPriority | void | listener, fixedPriority | 设置FixedPriority类型监听器的优先集 |
| setEnabled | void | enabled | 是否允许分发事件 |
| isEnabled | boolean | no | 检测事件管理器是否分发事件 |
| dispatchEvent | void | event | 分发事件 |
| dispatchCustomEvent | void | eventName, optionalUserData | 分发自定义事件 |
