# JavaScript常用知识点
=================================================
## js中onload函数和jquery的ready函数
```JavaScript
window.onload = function(){
	do something
}
```
```jQuery
$(document).ready(function(){});或者
$().ready(function(){});或者
$(function(){});
```
1. onload函数是整个页面加载完(包括图片也加载完)后执行; ready函数是dom加载完(图片可能还未加载)后执行
2. onload函数只执行一次, 最后一次加载的内容覆盖之前的, 只会执行最后一次加载的内容; ready可以执行多次加载

## Closure(闭包概念)
### 概念
闭包就是一个对象（一种特殊的数据结构），它包含一个指向构成该闭包的函数的指针以及在闭包创建时外部函数的词法环境的引用。同时在闭包里面访问的外部函数词法环境里面属性也叫做非本地变量。
### 什么时候会创建闭包
当在一个内部函数被暴露到定义它的外部函数的外面时，也就是说如果一个内部函数能够从定义它的函数的外面被访问时，这个时候闭包就被创建了（这里说创建是因为闭包也是一个对象），同时该闭包函数的[[scope]]属性有对外部函数词法环境的引用，所以在外部函数调用结束后，闭包依然能够访问外部函数的词法环境对象。
```javascript
function Person(name) {
  this._name = name;
  this.getName = function() {
    return this._name;
  };
}

var p = new Person();
```
### 什么时候使用闭包
Javascript里面的闭包可以做许多有用的事情，如配置回调函数或者模仿Java语言中的private data等等。
```javascript
window.addEventListener("load", function() {
  var showMessage = getClosure("some message<br />");
  window.setInterval(showMessage, 1000);
});

function getClosure(message) {
  function showMessage() {
    document.getElementById("message").innerHTML += message;
  }
  return showMessage;
}
```