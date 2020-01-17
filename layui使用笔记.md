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
## 打开弹窗页面
### layer的级别 layer = parent.layer 和 layer = layui.layer区别
```javascript
// 从浏览器打开一个新页面, 会覆盖侧边栏目录, 简单的说就是整个浏览器窗口
layer = parent.layer
// 从点击的页面打开新页面, 相当于点击的页面下的子页面. 最大长宽不会超过父页面
layer = layui.layer
```
### 一般代码
```javascript
addRcCoursePackageOpen: function (){
	layer.open({
		id: "addRcCoursePackage",
		type: 2,
		title: '添加远程教室框架课程包',
		shadeClose: true,
		shade: 0.5,
		area: ['640px', '70%'],
		content: 'rcCoursePackageAdd.html?', //iframe的url,
		success: function (layero, index) {
			layer.full(index);
			$(window).bind("resize", resizeFun = function () {
				layer.full(index);
				layer.iframeAuto(index);
			});
		},
		cancel: function (index) {
			$(window).unbind("resize", resizeFun);
		},
		end: function () {
			// 刷新本页面
			window.location.reload();
		}
	});
}

```
## layui表单自定义验证
```javascript
//表单验证 包括汉字
    form.verify({
        careerPlan_code: [
            /^[A-Za-z0-9\u4e00-\u9fa5]{0,254}$/,
            '不能超过254个字符哦！'
        ],name:[
            /^[A-Za-z0-9\u4e00-\u9fa5]{0,99}$/,
            '不能超过99个字符哦！'
        ],
        limitNum:[
            /^\d{0,10}$/,
            '不能超过10个数字哦！'
        ]
    });
```