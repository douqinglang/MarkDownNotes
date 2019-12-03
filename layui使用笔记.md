# layui使用笔记
## layui使用select 在option使用自定义属性, 并获取其值
```javascript
form.on('select(goods)', function (data) {
	var isTrainCourse = data.elem[data.elem.selectedIndex].getAttribute("data-val");
}
```
## layui 弹出层的icon样式
icon: 1		--> 图标: √, 颜色: 绿色
icon: 2		--> 图标: ×, 颜色: 红色
icon: 3		--> 图标: ?, 颜色: 黄色
icon: 4		--> 图标: 锁, 颜色: 灰色
icon: 5		--> 图标: 难过脸, 颜色: 红色
icon: 6		--> 图标: 微笑脸, 颜色: 绿色
icon: 7		--> 图标: !, 颜色: 黄色
[layer的Icon样式以及一些常用的layer窗口使用](https://blog.csdn.net/beauxie/article/details/60959971)
## 