- DrawCall
DrawCall是CPU对底层图形接口的调用，目的是在设备上绘制出对象。DrawCall的消耗就在于每次只提交少量数据，这样会导致GPU会在等待CPU提交数据，耗费时间。
优化的方向就是合并数据为一次
-- DrawCall的优化方案
打包图集，尽可能减少光照阴影等消耗
静态批处理：部分游戏对象可以选择static
动态批处理：是Unity自动进行的，按照一定规则即可，mesh顶点数不能超过900，非统一缩放轴可以合并批次
对渲染顺序要注意，渲染是可能被打断的，所以同材质的对象，尽可能放一起

- GC
在托管堆上，一些不合理或者不正确的代码风格也是会导致托管堆内存碎片化
-- 字符拼接，采取stringBuild
-- 用for循环代替foreach迭代。
-- 能使用对象池就使用对象池
-- 能用Struct就使用Struct，而不是Class



drawcall是啥？其实就是对底层图形程序（比如：OpenGL ES)接口的调用，以在屏幕上画出东西。所以，是谁去调用这些接口呢？CPU。


https://www.cnblogs.com/murongxiaopifu/p/4284988.html
