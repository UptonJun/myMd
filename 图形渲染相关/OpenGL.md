渲染管线
DrawCall
Z轴缓存

OpenGL简单流程
- 顶点着色器：将输入的坐标转化为相应的3D坐标
- 形状装配：将坐标组合为一个形状
- 几何着色器：将形状细分成一些三角形
- 光栅化：把三角形生成出多个像素点
- 片段着色器：给像素点上色
- 混合测试：因为图形有遮盖等层次关系，以及一些透明度遮挡，最后处理输出能展示的图形