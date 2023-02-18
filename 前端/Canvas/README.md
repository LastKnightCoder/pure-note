## 系列文章

```antd
const { NavList } = components;
const data = [{
	title: '起步',
	desc: 'Canvas 介绍'
}, {
	title: '坐标系统',
	desc: 'Canvas 中坐标系统的介绍以及角度旋转'
}, {
	title: '绘制路径',
	desc: '如何在 Canvas 中绘制路径，直线和折线'
}, {
	title: '绘图机制',
	desc: 'Canvas的绘图机制：基于状态'
}, {
	title: '绘制曲线',
	desc: '使用 Canvas 绘制曲线，包括圆、椭圆、贝塞尔曲线'
}, {
	title: '绘制形状',
	desc: '在画布中绘制常见的形状，包括三角形，矩形和五角星'
}, {
	title: '绘制文字',
	desc: '如何在 Canvas 中绘制文字，文字对齐，字体属性'
}, {
	title: '绘制图像',
	desc: '操作图像数据并绘制到 Canvas 上，马赛克，滤镜'
}, {
	title: '动画',
	desc: '如何使用 Canvas 制作动画，模拟真实物理系统'
}, {
	title: '变换',
	desc: '对画布进行位移，缩放，旋转等变换'
}, {
	title: 'Canvas高级',
	desc: '高级特性介绍，阴影、裁剪、透明、层叠、非零环绕'
}];
ReactDOM.createRoot(el).render(<NavList data={data} />);
```

## 踩坑

```antd
const { NavList } = components;
const data = [{
	title: 'Canvas绘图模糊',
	desc: '绘图模糊的原因及解决办法'
}];
ReactDOM.createRoot(el).render(<NavList data={data} />);
```

## 前沿资讯

```antd
const { NavList } = components;
const data = [{
	title: 'Canvas新增API',
	desc: 'Canvas新增的一些API，增加新功能，改善能力'
}];
ReactDOM.createRoot(el).render(<NavList data={data} />);
```
