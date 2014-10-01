对象缓冲池
============

## cc.pool的使用场景

- 经常创建和销毁的元素，例如打飞机游戏里面的子弹等。
- 不适用的场景：不是很经常创建的物体，比如背景，建筑等。
 
## 如何使用cc.pool

1. **让你的类支持`cc.pool`**

    首先，你需在需要使用`cc.pool`来管理的类中实现`reuse`和`unuse`方法，`cc.pool`在执行`putInPool`时将调用该对象的`unuse`方法，可以在`unuse`中完成进入回收池前的操作，`reuse`是当你要从回收池中取出对象时的重新初始化操作，你可以将这个对象初始化为重新可用的状态。

    ```
	var MySprite = cc.Sprite.extend({
    	_hp: 0,
    	_sp: 0,
		_mp: 0,
    	ctor: function (f1, f2, f3) {
       		this._super(s_grossini);
    		this.initData(f1, f2, f3);
    	},
    	initData: function (f1, f2, f3) {
       		this._hp = f1;
    		this._mp = f2;
    		this._sp = f3;
		},
		unuse: function () {
    		this._hp = 0;
    		this._mp = 0;
    		this._sp = 0;
    		this.retain();//if in jsb
    		this.setVisible(false);
    		this.removeFromParent(true);
		},
		reuse: function (f1, f2, f3) {
    		this.initData(f1, f2, f3);
    		this.setVisible(true);
		}
	});	
    MySprite.create = function (f1, f2, f3) {
        return new MySprite(f1, f2, f3)
    }
    MySprite.reCreate = function (f1, f2, f3) {
        var pool = cc.pool;
        if (pool.hasObject(MySprite)) return pool.getFromPool(MySprite, f1, f2, f3);
        return  MySprite.create(f1, f2, f3);
    }
    ```

2. **放入回收池**

    ```
    cc.pool.putInPool(object);
    ```

    调用此方法将调用对象的`unuse`的方法，并将对象放入回收池。

3. **从回收池回收对象**

    ```
    var object = cc.pool.getFromPool("MySprite", args);
    ```

    当你需要从回收池中取出一个对象，你可以调用`getFromPool`传入对象的class，以及传入需要传入的初始化参数，这些参数将被传入reuse方法中，`cc.pool`将自动调用reuse方法。

4. **判断回收池中是否有可用对象**

    ```
    var exist = cc.pool.hasObject("MySprite");
    ```

    该方法用于查找回收池中是否存在`MySprite`类的可回收对象。

5. **删除回收池中的某个对象**

    ```
    cc.pool.removeObject(object);
    ```

    将你要删除的对象传入，该对象将会从回收池删除。

6. **清空回收池**

    ```
    cc.pool.drainAllPools();
    ```

    当你需要清除所有回收池中的对象，例如完成游戏要进入其他页面，旧页面中的可回收对象不再有用了，为避免不必要的内存占用，你可以使用`drainAllPools`删除所有的可回收对象。
