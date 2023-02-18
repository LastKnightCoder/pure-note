---
tags: [算法, 排序算法]
---

```antd
const { NavList } = components;
const data = [{
	title: '准备工作',
	desc: '排序算法工具类'
}, {
	title: '选择排序',
	desc: '选择排序'
}, {
	title: '插入排序',
	desc: '插入排序'
}, {
	title: '希尔排序',
	desc: '希尔排序：插入排序的改进'
}, {
	title: '归并排序',
	desc: '归并排序：分而治之'
}, {
	title: '快速排序',
	desc: '快速排序'
}, {
	title: '堆排序',
	desc: '堆排序'
}];
ReactDOM.createRoot(el).render(<NavList data={data} />);
```

