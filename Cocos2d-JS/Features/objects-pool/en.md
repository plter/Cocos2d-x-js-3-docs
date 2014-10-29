Objects pool
=====

## Use case of cc.pool

When you have a class whose instance requires frequent creation and deletion, then it's the prefect case to put them into `cc.pool` while deleting and reuse them when you need a new instance. The advantage of using objects pool is reducing memory usage and improve performance.
 
## How to use cc.pool
 
1. **Make your class support `cc.pool`**

    Firstly, you mast have a class with `unuse` and `reuse` functions, `cc.pool` will call `unuse` function when you put it into the pool, and call `reuse` function when you retrieve an object from the pool to reinitialize it with the given parameters.

    Here is a example class `MySprite`, we will use it later on to explain other functionalities.

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

2. **Put an object into `cc.pool`**

    ```
    cc.pool.putInPool(object);
    ```

    `putInPool` function will invoke `object.unuse()` and put it into `cc.pool` for future usage.

3. **Retrieve an object from `cc.pool`**

    ```
    var object = cc.pool.getFromPool("MySprite", args);
    ```

    By the time you wanted to get an object from the pool, you can use `getFromPool` by passing the class and parameters to retrieve an usable object or null if there is none. The pool will call `reuse` function with `args` to initialize the object before return the it.

4. **Check whether there is an object available**

    ```
    var exist = cc.pool.hasObject("MySprite");
    ```

5. **Remove an object from `cc.pool`**

    ```
    cc.pool.removeObject(object);
    ```

6. **Drain the pool**

    ```
    cc.pool.drainAllPools();
    ```
    
    When you switch from a game scene to another, maybe the objects in old scene will be no longer useful. In this case, to avoid unnecessary memory usage, you can remove all objects in the pool with `drainAllPools` function.