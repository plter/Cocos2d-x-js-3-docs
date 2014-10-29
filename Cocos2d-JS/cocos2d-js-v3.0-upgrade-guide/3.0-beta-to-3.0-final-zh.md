#从Cocos2d-JS v3.0 beta到Cocos2d-JS v3.0 RC3升级指南

## 0. 首先你需要知道如何从Cocos2d-JS v2.x升级到v3.0 beta

如果你还在使用Cocos2d-html5或者JSB v2.x，你将需要首先完成2.x到3.0 beta的升级：请参考这篇的文档：[#从Cocos2d-html5 v2.2.x到Cocos2d-JS v3.0 alpha2升级指南](../../v3.0a/upgrade-guide/zh.md)

## 1. cc.Layer的静态缓存功能

在v3.0中，你可以将一个图层静态缓存起来，这将极大得减少该图层的绘制数，使你的游戏更加高效，当然，请只缓存那些不经常更新的图层，否则缓存将消耗更大。

```
var layer = new cc.Layer();
// 启用静态缓存
layer.bake();

// 取消静态缓存
layer.unbake();
```

详细信息请查看[Bake功能使用说明](../../../v3.0/bake-layer/zh.md)。

## 2. 对象缓冲池：`cc.pool`

一个通用的对象缓冲池实现被添加到了v3.0中：`cc.pool`，当你的游戏中有需要大规模创建或重用的对象的时候，缓冲池可以帮助你显著改善运行的效率。

一些常见的应用场景：
    - 游戏中的子弹（生命周期短，大规模创建和重用，对其他对象没有非常大的影响）
    - Candy Crash中的方块（大规模创建和重用）
    - 其他很多情况...

当你不再需要一个对象并且希望它可以被复用的时候，你就可以用`cc.pool.putInPool(obj)`将它加到缓冲池中。

```
layer.removeChild(sprite);
cc.pool.putInPool(sprite);
```

当你希望从缓冲池中获取一个对象的时候，你可以使用`cc.pool.getFromPool()`。

```
cc.pool.getFromPool(cc.Sprite, "a.png");
```

你可以使用`cc.pool.hasObj(cc.Sprite)`检测缓冲池中是否有`cc.Sprite`类的可用对象。

你也可以使用`cc.pool.drainAllPool()`去清空所有缓冲池。

## 3. 新的缓动动作

我们在v3.0中添加了一系列方便的缓动动作供大家使用：

```
cc.easeBezierAction(p0, p1, p2, p3) // JSB暂不支持
cc.easeQuadraticActionIn()
cc.easeQuadraticActionOut()
cc.easeQuadraticActionInOut()
cc.easeQuarticActionIn()
cc.easeQuarticActionOut()
cc.easeQuarticActionInOut()
cc.easeQuinticActionIn()
cc.easeQuinticActionOut()
cc.easeQuinticActionInOut()
cc.easeCircleActionIn()
cc.easeCircleActionOut()
cc.easeCircleActionInOut()
cc.easeCubicActionIn()
cc.easeCubicActionOut()
cc.easeCubicActionInOut()
```

重提一下缓动动作在v3.0中的使用方式，直接调用目标action的`easing`函数：`action.easing(cc.easeQuadraticActionIn())`。

## 4. jsb命名空间

我们添加了jsb命名空间来包含所有仅限于jsb的API：

```
cc.fileUtils        -->     jsb.fileUtils
cc.Reflection       -->     jsb.Reflection
cc.AssetsManager    -->     jsb.AssetsManager
```

同时也给`jsb.fileUtils`添加了一些新的函数绑定：

```
jsb.fileUtils.getSearchPaths()
jsb.fileUtils.setSearchPaths(paths)
jsb.fileUtils.getSearchResolutionsOrder()
jsb.fileUtils.setSearchResolutionsOrder(orders)
jsb.fileUtils.addSearchResolutionsOrder(order)
```

## 5. ccui

### 5.1 ccui.Widget的边界获取函数按照-x的函数名重命名

```
getLeftInParent     -->     getLeftBoundary
getBottomInParent   -->     getBottomBoundary
getRightInParent    -->     getRightBoundary
getTopInParent      -->     getTopBoundary
```

### 5.2 添加`getContentSize`和`setContentSize`函数

我们还添加了`getContentSize`和`setContentSize`给`ccui.Widget`，这样在大小的存取上`ccui.Widget`就和`cc.Node`共享同样的API了。

```
// 新增
ccui.Widget#getContentSize
ccui.Widget#setContentSize
```

### 5.3 重命名所有`addEventListenerXXX`函数

ccui中所有`addEventListenerXXX`都被重命名为`addEventListener`以保障更好的开发体验，以下是具体修改列表

```
ccui.CheckBox#addEventListenerCheckBox      --> ccui.CheckBox#addEventListener
ccui.Slider#addEventListenerSlider          --> ccui.Slider#addEventListener
ccui.TextField#addEventListenerTextField    --> ccui.TextField#addEventListener
ccui.PageView#addEventListenerPageView      --> ccui.PageView#addEventListener
ccui.ScrollView#addEventListenerScrollView  --> ccui.ScrollView#addEventListener
ccui.ListView#addEventListenerListView      --> ccui.ListView#addEventListener
```

## 6. 其他API改动

### 6.1 cc.FadeIn

```
cc.FadeIn.create(duration, toOpacity)   -->     cc.FadeIn.create(duration)
```

### 6.2 cc.log支持格式化字符串

```
var str = "The number is";
var number = cc.random0To1() * 10;
cc.log("%s : %d", str, number);
```

### 6.3 资源管理器

在v3.0中，你可以使用`cc.AssetsManager`的`downloadFailedAssets`函数重启未成功下载的资源，v3.0中的资源管理器还支持了非常多优秀的特性，详情请参见新的[资源管理器文档](../../../v3/assets-manager/zh.md)

### 6.4 仿射变换工具函数

为了符合引擎整体的命名风格，所有函数都必须小写字母开头，我们将放射变换函数重命名为这种形式，下面是API改动列表：

```
cc.AffineTransformMake              ->  cc.affineTransformMake
cc.PointApplyAffineTransform        ->  cc.pointApplyAffineTransform
cc.\_PointApplyAffineTransform       ->  cc._pointApplyAffineTransform
cc.SizeApplyAffineTransform         ->  cc.sizeApplyAffineTransform
cc.AffineTransformMakeIdentity      ->  cc.affineTransformMakeIdentity
cc.AffineTransformIdentity          ->  cc.affineTransformIdentity
cc.RectApplyAffineTransform	        ->  cc.rectApplyAffineTransform
cc.\_RectApplyAffineTransformIn      ->  cc._rectApplyAffineTransformIn
cc.AffineTransformTranslate         ->  cc.affineTransformTranslate
cc.AffineTransformScale             ->  cc.affineTransformScale
cc.AffineTransformRotate            ->  cc.affineTransformRotate
cc.AffineTransformConcat            ->  cc.affineTransformConcat
cc.AffineTransformEqualToTransform  ->  cc.affineTransformEqualToTransform
cc.AffineTransformInvert            ->  cc.affineTransformInvert
```

### 6.5 cc.RenderTexture

```
cc.RenderTexture.beginWithClear(r, g, b, a, depthValue, stencilValue)
```

`cc.RenderTexture`的`beginWithClear`函数不再支持浮点颜色值作为参数，与引擎中其他API一样接受0-255的整型颜色值作为`r`, `g`, `b`, `a`的参数值。

### 6.6 cc.sys.platform

添加获取平台的API：`cc.sys.platform`。

### 6.7 [JSB]console.log

绑定`console.log`。

### 6.8 cc.formatStr

增加格式化字符串函数`cc.formatStr`，使用方法： 

```
cc.formatStr("a: %d, b: %b", a, b);
```

同时，所有log函数也支持格式化字符串作为参数：

```
cc.log(message, ...)
cc.assert(condition, message, ...)
cc.warn(message, ...)
cc.error(message, ...)
```

### 6.9 修改部分函数名以适应引擎命名风格

```
cc.pool.hasObj                  -> cc.pool.hasObject
cc.pool.removeObj               -> cc.pool.removeObject
cc.textureCache.textureForKey   -> cc.textureCache.getTextureForKey
cc.TMXTilemap#propertiesForGID  -> cc.TMXTilemap#getPropertiesForGID
```

### 6.10 添加设备的目标像素密度控制函数

```
// 添加API
cc.DENSITYDPI_DEVICE = "device-dpi";
cc.DENSITYDPI_HIGH = "high-dpi";
cc.DENSITYDPI_MEDIUM = "medium-dpi";
cc.DENSITYDPI_LOW = "low-dpi";

cc.view.setTargetDensityDPI(targetDensityDPI)
cc.view.getTargetDensityDPI()
```