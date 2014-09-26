How to use bindings-generator to generate lua binding automatically
===================================================================

Since cocos2d-x 3.0, we use [bindings-generator](https://github.com/cocos2d/bindings-generator) to generate lua bindings for c++ automatically.

The bindings-generator is based on *tolua++*, you could config the ini file in the *tools/tolua* directory and then run the *genbindings.py* script to generate the binding code.  By using this method, it greatly reduce the configuration work of writing pkg files.

Next, let's take a *CustomClass* as an example, we will show you how to use the bindings-generator to generate bindings.

###Create the CustomClass

Here is the code snippets:

```
// CustomClass.h

#ifndef __CUSTOM__CLASS

#define __CUSTOM__CLASS

#include "cocos2d.h"

namespace cocos2d {
class CustomClass : public cocos2d::Ref
{
public:

    CustomClass();

    ~CustomClass();

    static cocos2d::CustomClass* create();

    bool init();

    CREATE_FUNC(CustomClass);
};
} //namespace cocos2d

#endif // __CUSTOM__CLASS
```

(Note: To keep this page short and clean, we omit the cpp file and the bindings-generator only scan the header files.)

One thing you should keep in mind, the custom class should be inheritent from cocos2d::Ref class, this is mainly due to the fact that all the global object's creation and destruction are handled by the `REFID_PTR_MAPPING` map in c++ layer. If we inheritent the class from Ref, then its destructor will handle these dirty things.

###Add a new cocos2dx_custom.ini file

Navigate to the *tools/lua* folder and create a new file named `cocos2dx_custom.ini`. 

Here is the content of cocos2dx_custom.ini:

```
[cocos2dx_custom]

# the prefix to be added to the generated functions. You might or might not use this in your own

# templates

prefix = cocos2dx_custom

# create a target namespace (in javascript, this would create some code like the equiv. to `ns = ns 

# all classes will be embedded in that namespace

target_namespace = cc

android_headers =  -I%(androidndkdir)s/platforms/android-14/arch-arm/usr/include -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.7/libs/armeabi-v7a/include -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.7/include -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.8/libs/armeabi-v7a/include -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.8/include

android_flags = -D_SIZE_T_DEFINED_ 

clang_headers = -I%(clangllvmdir)s/lib/clang/3.3/include

clang_flags = -nostdinc -x c++ -std=c++11 -U __SSE__

cocos_headers = -I%(cocosdir)s/cocos -I%(cocosdir)s/my -I%(cocosdir)s/cocos/2d -I%(cocosdir)s/cocos/base -I%(cocosdir)s/cocos/ui -I%(cocosdir)s/cocos/physics -I%(cocosdir)s/cocos/2d/platform -I%(cocosdir)s/cocos/2d/platform/android -I%(cocosdir)s/cocos/math/kazmath -I%(cocosdir)s/extensions -I%(cocosdir)s/external -I%(cocosdir)s/cocos/editor-support -I%(cocosdir)s

cocos_flags = -DANDROID -DCOCOS2D_JAVASCRIPT

cxxgenerator_headers = 

# extra arguments for clang

extra_arguments =  %(android_headers)s %(clang_headers)s %(cxxgenerator_headers)s %(cocos_headers)s %(android_flags)s %(clang_flags)s %(cocos_flags)s %(extra_flags)s 

# what headers to parse

headers = %(cocosdir)s/cocos/my/CustomClass.h

# what classes to produce code for. You can use regular expressions here. When testing the regular
# expression, it will be enclosed in "^$", like this: "^Menu*$".

classes = CustomClass.*

# what should we skip? in the format ClassName::[function function]
# ClassName is a regular expression, but will be used like this: "^ClassName$" functions are also
# regular expressions, they will not be surrounded by "^$". If you want to skip a whole class, just
# add a single "*" as functions. See bellow for several examples. A special class name is "*", which
# will apply to all class names. This is a convenience wildcard to be able to skip similar named
# functions from all classes.

skip =

rename_functions = 

rename_classes = 

# for all class names, should we remove something when registering in the target VM?

remove_prefix = 

# classes for which there will be no "parent" lookup

classes_have_no_parents = 

# base classes which will be skipped when their sub-classes found them.

base_classes_to_skip =

# classes that create no constructor

# Set is special and we will use a hand-written constructor

abstract_classes =

# Determining whether to use script object(js object) to control the lifecycle of native(cpp) object or the other way around. Supported values are 'yes' or 'no'.

script_control_cpp = no
```

All of the config files under *tools/tolua* are the same format. Here is the list which you should care when writing your own ini file:

1. [title]: To config the title which will by used by the *tools/tolua/gengindings.py* scripts. Generally, the title could be the file name.
2. prefix: To config the prefix of a function name, generally, we also use the file name as the prefix.
3. target_namespace: To config the module name in scripting layer. Here we use the `cc` as the module name, when you want to ref a  name in scripting layer, you must put a prefix named `cc` in front of the name. For example, the `CustomClass` could be reference as `cc.CustomClass`.
4. headers: To config all the header files needed for parsing and the %(cocosdir)s is the engine root path of cocos2d-x.
5. classes: To config all the classes needed to bind. Here it supports regular expression. So we could set MyCustomClass.* here, for looking more specified usage, you could ref to `tools/tolua/cocos2dx.ini`.
6. skip: To config the functions needed to be omit.  Now the bindings-generator can't parse `void*` type and also the delegate type, so these types needed to be bind manually. And at this circumstance, you should omit all these types first and then to bind them manually. You could ref to the config files under path `cocos/scripting/lua-bindings/auto` .

7. rename_functions: To config the functions need to be renamed in the scripting layer. Due to some reasons, developers want more scripting friendly API, so the config option is for this purpose.

8. rename_classes: Not used any more.

9. remove_prefix: Not used any more.

10. classes_have_no_parents: To config  the parent class needed to be filter. This option is seldom modified.

11.  abstract_classes: To config the classes whose public constructor don't need to be exported.

12.  script_control_cpp:yes.  To config whether the scripting layer manage the object life time or not. If no, then the c++ layer cares about their life time.
Now, it is imperfect to control native object's life time in scripting layer. So you could simply leave it to *no*.

###Modify the `tools/tolua/genbindings.py` scripts
Open tools/tolua/genbindings.py and find the `cmd_args` option and add a new line:

```
 'cocos2dx_custom.ini' : ('cocos2dx_custom', 'lua_cocos2dx_custom'), \
```

###Run the `tools/tolua/genbindings.py` scripts
After running the `tools/tolua/genbindings.py` , there will be two more files under directory `cocos/scripting/lua-bindings/auto` :

```
lua_cocos2dx_custom_auto.cpp
lua_cocos2dx_custom_auto.h
```
###Call the binding fuctions
Open the `lua_cocos2dx_custom_auto.h` and you will find a global function declaration:

```
register_all_cocos2dx_custom(lua_State* tolua_S);
```

If should call this function at a proper place(usually we call this function in the AppDelegate.cpp file). After calling this method, you will be able to use all the exported Lua functions. One more thing, when you call the register method, you should be sure that the Lua stack top element is `_G`.

###Summary
As you can see, it's easy to generate lua bindings from c++ code by using the bindings-generator.