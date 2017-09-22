---
title: JS弹出下载对话框以及实现常见文件类型的下载
date: 2017-02-16 21:09:19
categories: JavaScript
tags: javascript,saveas,下载,对话框
description: 本文首发于 http://liuxianan.com，原文地址：http://blog.liuxianan.com/js-download.html，转载请注明署名“liuxianan”并在显眼位置保留原文链接，谢谢！
---

# 写在前面

JS要实现下载功能，一般都是这么几个过程：生成下载的URL，动态创建一个A标签，并将其href指向生成的URL，然后触发A标签的单击事件，这样就会弹出下载对话框，从而实现了一个下载的功能。

这里所说的下载，有时候也可以理解为保存。出于安全考虑，JS肯定无法直接调用FileAPI写文件到磁盘，但是却可以通过下载来变相实现保存功能。

# 几个备用知识点

## JS触发单击事件

既然是用A标签模拟，那么肯定要知道JS如何主动触发单击事件。

最简单的触发单击事件肯定是`elem.click()`，平时在不需要考虑兼容性的场合我都是这么干的，但是毕竟这个方法有兼容性（具体兼容性如何没做过测试），所以还是要掌握一个通用的方法。

以下代码是网上比较容易找到的一段代码，我在前面加了一段`MouseEvent`的判断：

```javascript
/**
 * 考虑兼容性的触发单击事件
 * @param elem 要触发单击事件的DOM对象
 */
function fireClickEvent(elem)
{
	var event;
	if(window.MouseEvent) event = new MouseEvent('click');
	else
	{
		event = document.createEvent('MouseEvents');
		event.initMouseEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
	}
	elem.dispatchEvent(event);
}
```

## HTML5的download属性

这个属性很重要，它可以指定下载文件名，并且可以告诉浏览器目标链接是一个下载链接，不是一个普通链接，我们看下面代码就能看出区别了：

```html
<a href="data:text/txt;charset=utf-8,测试下载纯文本" download="测试.txt" >下载1</a>
<a href="data:text/txt;charset=utf-8,测试下载纯文本">下载2</a>
```

可以发现，`下载1`按钮能够实现下载，点击`下载2`链接时直接在浏览器打开文件内容了。

补充说明：

* `file:///`模式下貌似不生效；
* 链接指向一些第三方链接时也不会生效，具体有待研究；

## JS弹出下载对话框

假如给我们的不是一个下载地址而是一个blob对象，我们可以通过`URL.createObjectURL`来给`blob`对象生成临时URL，并且可以利用HTML5的`download`属性来指定下载的文件名，好家伙，有了这2个东西我们就可以实现一个“万能”的弹出下载对话框方法了。

综上所述，我又在`fireClickEvent`的基础上继续简单封装了一个`openDownloadDialog`方法，使用如下：

* openDownloadDialog(url, saveName)
* openDownloadDialog(blob, saveName)

代码如下：

```javascript
/**
 * 通用的打开下载对话框方法，没有测试过具体兼容性
 * @param url 下载地址，也可以是一个blob对象，必选
 * @param saveName 保存文件名，可选
 */
function openDownloadDialog(url, saveName)
{
	if(typeof url == 'object' && url instanceof Blob)
	{
		url = URL.createObjectURL(url); // 创建blob地址
	}
	var aLink = document.createElement('a');
	aLink.href = url;
	aLink.download = saveName || ''; // HTML5新增的属性，指定保存文件名，可以不要后缀，注意，file:///模式下不会生效
	var event;
	if(window.MouseEvent) event = new MouseEvent('click');
	else
	{
		event = document.createEvent('MouseEvents');
		event.initMouseEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
	}
	aLink.dispatchEvent(event);
}
```

# JS实现常见文件类型的下载

## JS生成CSV文件并下载

csv是一种逗号分隔的表格文件格式，可以很好的被Excel支持，由于其文件格式简单，所以经常用在简单的表格上面。最重要的是它是一种纯文本格式，可以很轻松地用JS来生成而不借助第三方库。

### CSV格式示例

如下：

```
姓名,期中成绩,期末成绩
张三,58,95
李四,98,74
王二,47,38
刘能,15,100
黄五,87,68
```

excel打开效果如下：

![](http://image.liuxianan.com/201704/20170410_202545_243_4578.png)

### 初次尝试

首先想到的是使用`data:text/txt;`来实现，先看一下下载纯文本：

```html
<a download="测试.txt" href="data:text/txt;charset=utf-8,测试下载纯文本">下载</a>
```

以上代码没毛病，然后再换成csv。换csv的最大问题就是如何处理换行，很简单，用`encodeURIComponent`编码一下就可以了：

```html
<button onclick="test()">下载CSV</button>
<script>
function test()
{
	var csv = '姓名,期中成绩,期末成绩\n张三,58,95\n李四,98,74';
	var a = document.createElement('a');
	a.href = 'data:text/txt;charset=utf-8,'+encodeURIComponent(csv);
	a.download = '测试.csv';
	a.click(); // 这里偷个懒，直接用click模拟
}
</script>
```

### 解决CSV乱码问题

虽然我们用的是UTF-8编码，下载后你会发现，用文本编辑器打开没问题，但是用Excel打开乱码：

别急，原因就是