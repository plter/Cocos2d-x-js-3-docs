下面是要引擎3.0-3.3的主要新功能的文档，后面标注Release Note的表示从Release Note中寻找文档：

1. [v3.0 RELEASE_NOTES.md](v3.0 RELEASE_NOTES.md)
2. [v3.1 RELEASE_NOTES.md](v3.1 RELEASE_NOTES.md)
3. [v3.2 RELEASE_NOTES.md](v3.2 RELEASE_NOTES.md)
4. [v3.3 RELEASE_NOTES.md](v3.3 RELEASE_NOTES.md)
5. [API Changes in v3.0 release note](v3.0 RELEASE_NOTES.md)搜索 API Changes，主要是介绍下新的API变更。
6. [New EventDispatcher Mechanism](EventDispatcher Mechanism.md) 详解新的API机制
7. New Renderer :
    * [Introduction to New Renderer](Introduction to New Renderer.md)
    * [Cocos2d v3.0 renderer pipeline roadmap](Cocos2d v3.0 renderer pipeline roadmap.md)
8. [New UI objects](New UI Objects.md)
9. [New physical integrations](Physics.md) 引擎在Chipmunk上封装了层自己的物理引擎API
10. [Cocos Console](Cocos Console.md)
11. 几个新的数据结构：
    * `CCDictionary` 被 [cocos2d::Map<>](Map.md)替换
    * `CCArray` 被 [cocos2d::Vector<>](Vector.md)替换
    * `CCBool`, `CCFLoat`, `CCDouble` 被[cocos2d::Value](Value.md)替换
12 `C++ patterns`: 这两篇结合起来，主要是引擎追加的C++ 11的新特性，可以先看第一篇中Release_Note中对于C++ 11 feature的介绍，然后结合第一篇详细讲解
    * [C++ coding style](C++ coding style.md)
    * [C++11 patterns in cocos2d-x-3.0 Release Note](v3.0 RELEASE_NOTES.md) 
13. Lua:
    * [Folder structure of Lua binding](Folder structure of Lua binding.md)
    * [How to use bindings-generator to generate lua binding automatically](How to use bindings-generator to generate lua binding automatically.md)
14. [Improved Shader Subsystem in 3.1 release note](v3.1 RELEASE_NOTES.md)，搜索Improved Shader Subsystem
15. [New Math Library in 3.1 release note](v3.1 RELEASE_NOTES.md)搜索 New math library
16. [UI Video Player in 3.1 release note](v3.1 RELEASE_NOTES.md)搜索 uivideo
17. [Game Controller](Game Controller.md)
18. 3D Features: 第一篇和第二篇有所重复，重复部分大体一致，第一篇主要是有讲Fbv-conv的用法，第二篇是想要翻译成英文但是还没完成。不过主题3D那块功能的内容已经用中文写好。
    * [Sprite3D, Animation3D, Fbv-conv](Sprite3D.md)
    * [Camera, Mesh, Fbx-conv, AABB, OBB, BillBoard](3d features.md)
19. [Primitive in 3.3 release note](v3.3 RELEASE_NOTES.md) 搜索 Primitive
20. [Triangles command in 3.3 release note](v3.3 RELEASE_NOTES.md) 搜索 Triangles
21. [WebView in 3.3 release note](v3.3 RELEASE_NOTES.md) 搜索 WebView
22. [New audio in 3.3 release note](v3.3 RELEASE_NOTES.md)搜索New audio