# UEditor单页面使用问题
==========================================================

# 问题定位

UEditor 富文本编辑器在单页面中多次创建或使用 ueditor实例, 使用ue.setConten(html片段)会出现类似jquery中的追加html代码问题
	比如: 第一次实例化ue实例, 文本域值为"hello", 第二次实例化是添加"world", 实际上却显示"hello" "world"
# 问题剖析

出现这种问题主要和js引擎加载和执行js的时机有关. 按照我的理解, 页面中引入js, 在页面被浏览器加载时, js文件被解析和执行, 创建js变量, 这时的变量在这个页面中使用且仅被创建此, 在每一个进行实例化的时候,都会使用页面加载时候的ue实例. 所以ue值一直存在, 即使被创建, 也是使用的同一个实例, 除非你创建容器id不同的ue实例.
```javascript
    UE.getEditor = function (id, opt) {
        var editor = instances[id];
        if (!editor) {
            editor = instances[id] = new UE.ui.Editor(opt);
            editor.render(id);
        }
        return editor;
    };
```
# 解决办法
1. 每次使用UE.getEditor('container');时都使用不同的容器id, 以创建不同的实例
2. 将单页面应用改为多页面应用.也就是说将需要的多次使用的html片段单独拿出做一个页面,在引入ue相关的js. 尽量避免多次实例化情况或者是实例化一次在多次使用