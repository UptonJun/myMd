

- 骨骼碰撞

WeldJoint要解决标枪连接刚体的问题，但是想让标枪一直跟随着人物移动，那么人物必须得是一个刚体，后来就想着在骨骼节点下创建一个刚体，但是事实上没有直接的接口提供这样直接操作，creator（v1.9.3）本身不支持对骨骼节点的碰撞修改。

- 浮点计算的误差

因为实时对战类游戏，需要考虑一个设备不同的问题，不同设备浮点数计算存在误差，这个误差不容忽视，物体之间的碰撞，角度大小、力度大小如果存在差异，那么就会出现不一致的问题，导致体验非常差

-- 定点数计算
有些游戏，不需要达到高精度的计算一致，只需要基本一致，从而使得游戏结果一致，那么我们可以考虑采取定点数，保留小数点固定几位，并且客户端手写四舍五入的实现函数，这样的计算偏差是基本可控的

-- “回合制”物理模拟
部分游戏，要求物体碰撞，但却不是完全实时的，它所要求的是回合制表现。
我选择的处理方式是，自己回合自己的客户端直接用物理引擎跑效果，并且将这个过程中的物体坐标，全部按一个与后台约定好的帧率发送给服务器，服务器纯转发给其他客户端，其他客户端收到消息后，立即修改物体坐标，保持与服务器一样的帧率去更改坐标。如果不是自己回合，那么自己就是接收者，接受到服务器消息就去更改坐标

-- 有些移动，可以考虑不使用物理引擎
部分动作系统的方法，是可以达到简单的移动需求的
只需要准确控制物体移动的起点，朝向的终点，以及过程的耗时，那么两个客户端出来的效果，通常是一致的

-- 无限大地图


# Cocos 的物理引擎

Cocos用的物理引擎是Box2D

## 关于Box2D

https://github.com/erincatto/Box2D

## Cocos 中的物理步长


## 资源处理

场景选择延迟加载资源

## 图集的意义
- 合并批次渲染
- 合并图集会去除图片的空白区域，减少游戏包体和内存占用


## UI 渲染批次合并指南

    在游戏开发中，Draw call 作为一个非常重要的性能指标，直接影响游戏的整体性能表现。Draw call 就是 CPU 调用图形 API，比如 OpenGL，命令 GPU 进行图形绘制。一次 Draw call 就代表一次图形绘制命令，由于 Draw call 带来的 CPU 及 GPU 的渲染状态切换消耗，往往需要通过批次合并来降低 Draw call 的调用次数。批次合并的本质就是在一帧的渲染过程中，保证连续节点的渲染状态一致，将尽可能多的节点数据合并一次性提交，从而减少绘图指令的调用次数，降低图形 API 调用带来的性能消耗，同时也可以避免 GPU 进行频繁的渲染状态切换。渲染状态就包括：纹理状态，Blend 模式，Stencil 状态，Depth Test 状态等等。

https://docs.cocos.com/creator/manual/zh/advanced-topics/ui-auto-batch.html

## 静态合图

    静态合图即为编辑器提供的自动图集功能，以及其他第三方图集打包工具如：TexturePacker 等。在资源层面进行散图合并，保证UI节点使用的都是同一张贴图，因为同一张图集的纹理状态都是一致的，所以能够达到渲染批次合并对纹理状态的要求。对于 Label 组件，为了保证所有的 Label 节点使用相同的纹理，通常会使用 BMFont 将要使用的 UI 文字提前进行打包，并使用引擎的自动图集与散图一起合并进一张大的纹理，即可与其他相邻的 Sprite 节点进行批次合并。自动图集的创建与设置可以参考 自动图集资源 新建一个自动图集资源配置，然后把所有你希望进行合图的 UI 图片，BMFont 和艺术数字都拖到自动图集资源所在的目录即可。BMFont的文本制作可以通过 BMFont 字体制作工具，将常用的美术字或者文本制作生成一张字体图片及其字体映射文件。然后直接拖入编辑器中即可使用。

为了降低DrawCall , 需要进行合并批次，静态合图可以把散图合并到一个图集中，但是如果图集打包出来后，如果太大，会导致加载图集的时间特别长。

## 动态合图

动态合图是按照渲染顺序来选取要将哪些贴图合并到一张大图中的，这样就能确保相邻的 DrawCall 能合并为一个 DrawCall。

Cocos 2.0

```
cc.dynamicAtlasManager.insertSpriteFrame(spriteFrame);
```

# 资源的动态加载

动态加载资源要注意两点，一是所有需要通过脚本动态加载的资源，都必须放置在 resources 文件夹或它的子文件夹下。resources 需要在 assets 文件夹中手工创建，并且必须位于 assets 的根目录


# 对象池

    对象池就是一组可回收的节点对象，我们通过创建 cc.NodePool 的实例来初始化一种节点的对象池。通常当我们有多个 prefab 需要实例化时，应该为每个 prefab 创建一个 cc.NodePool 实例。 当我们需要创建节点时，向对象池申请一个节点，如果对象池里有空闲的可用节点，就会把节点返回给用户，用户通过 node.addChild 将这个新节点加入到场景节点树中。


# CCClass 进阶

https://docs.cocos.com/creator/manual/zh/scripting/reference/class.html#deferred-definition


# 分包加载

随着游戏玩法越来越丰富，游戏的代码量也越来越大，开发者对于扩大包大小的需求越来越强烈，同时微信小游戏也支持了分包加载的功能。所以 Cocos Creator 推出了 分包加载 这样一个功能，该功能支持 代码 和 资源 的分包加载。其中 资源分包 从 v2.0.7 开始支持。分包加载目前只支持各类小游戏，如微信小游戏、OPPO 小游戏等。

分包加载，即把游戏内容按一定规则拆分在几个包里，在首次启动的时候只下载必要的包，这个必要的包称为 主包，开发者可以在主包内触发下载其他子包，这样可以有效降低首次启动的消耗时间。

https://docs.cocos.com/creator/manual/zh/scripting/subpackage.html

# This

https://zhuanlan.zhihu.com/p/23804247



# Cocos 进阶


## JSBinds


https://docs.cocos.com/creator/manual/zh/advanced-topics/jsb/JSB2.0-learning.html


## 热更新



## 定制引擎

https://docs.cocos.com/creator/manual/zh/advanced-topics/engine-customization.html

## 渲染流

https://docs.cocos.com/creator/manual/zh/advanced-topics/render-flow.html


## Java原生反射机制

https://docs.cocos.com/creator/manual/zh/advanced-topics/java-reflection.html
