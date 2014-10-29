如何在IOS平台上使用js直接调用OC方法
=========

在Cocos2d-JS v3.0 RC2中，与Android上js调用Java一样，Cocos2d-JS也提供了在iOS和Mac上js直接调用Objective-C的方法，示例代码如下：

```
	var ojb = jsb.reflection.callStaticMethod(className, methodNmae, arg1, arg2, .....);
```

在`jsb.reflection.callStaticMethod`方法中，我们通过传入OC的类名，方法名，参数就可以直接调用OC的静态方法，并且可以获得OC方法的返回值。

## 类

- 参数中的类名，只需要传入OC中的类名即可，与Java不同，类名并不需要路径。比如你在工程底下新建一个类`NativeOcClass`，只要你将他引入工程，那么他的类名就是`NativeOcClass`，你并不需要传入它的路径。

```
	import <Foundation/Foundation.h>
	@interface NativeOcClass : NSObject
	+(BOOL)callNativeUIWithTitle:(NSString *) title andContent:(NSString *)content;
	@end
```
	
## 方法

- js到OC的反射仅支持OC中类的静态方法。
- 方法名比较要需要注意，我们需要传入完整的方法名，特别是当某个方法带有参数的时候，你需要将他的**:**也带上。根据上面的例子。此时的方法名字是**callNativeUIWithTitle:andContent:**，不要漏掉了他们之间的**:**。
- 如果是没有参数的函数，那么他就不需要**:**,如下代码，他的方法名是`callNativeWithReturnString`，由于没有参数，他不需要**:**，跟OC的method写法一致。

```
	+(NSString *)callNativeWithReturnString;
```

## 使用示例

- 下面的示例代码将调用上面`NativeOcClass`的方法，在js层我们只需要这样调用：
 
```
	var ret = jsb.reflection.callStaticMethod("NativeOcClass", 
											   "callNativeUIWithTitle:andContent:", 
											   "cocos2d-js", 
											   "Yes! you call a Native UI from Reflection");
```

- 这里是这个方法在OC的实现，可以看到是弹出一个native的对话框。并把`title`和`content`设置成你传入的参数，并返回一个boolean类型的返回值。

```
	+(BOOL)callNativeUIWithTitle:(NSString *) title andContent:(NSString *)content{
    	UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:title message:content delegate:self cancelButtonTitle:@"Cancel" otherButtonTitles:@"OK", nil];
    	[alertView show];
    	return true;
	}
```

- 此时，你就可以在`ret`中接受到从OC传回的返回值（true）了。

## 注意

在OC的实现中，如果方法的参数需要使用float、int、bool的，请使用如下类型进行转换：

- **float，int 请使用NSNumber类型**
- **bool请使用BOOL类型。**
- 例如下面代码，我们传入2个浮点数，然后计算他们的合并返回，我们使用NSNumber而不是int、float去作为参数类型。

```
+(float) addTwoNumber:(NSNumber *)num1 and:(NSNumber *)num2{
	float result = [num1 floatValue]+[num2 floatValue];
	return result;
}
```

- 目前参数和返回值支持 **int, float, bool, string**，其余的类型暂时不支持。