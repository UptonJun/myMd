

# 多边形碰撞检测

# 分离轴定理

分离轴定理适用于凸多边形

从多边形的法线方向投影，可以得到两个投影出来的向量，如果两个多边形得到的投影向量任何一对都是有重叠的，那么肯定有碰撞。如果存在一对投影有不重叠情况，那么两个多边形肯定不碰撞。

凹多边形需要分解为凸多边形才能进行检测。

# 分离轴定理的优点

- 计算特别快




http://acuilab.com/articles/2019/08/07/1565161401832.html