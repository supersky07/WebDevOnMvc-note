# 使用文件

========

## 浏览器支持
	
	if (window.File && window.FileReader && window.FileList) {}

## 获取文件信息

File对象的三个属性：name，文件名；size，文件大小；type，文件的MIME类型；均只读。不能获取文件路径。

## 文件输入

HTML5增强了文件上床功能，并修正了很多问题。比如，提供了multiple属性来支持多文件。

	<input type="file" multiple>

利用files的属性过滤上传文件：
	
	var input = $("input[type=file]");
	input.change(function(){
		var files = this.files;

		for (var i = 0; i < files.length; i++) {
			if (files[i].type.match(/image.*/)) {}
		}
	});

## 拖拽

### 拖拽事件

	- dragestart
	- drag
	- dragover
	- dragenter
	- dragleave
	- drop
	- dragend

### 开始拖拽

将元素的draggable属性设置为true
	
	<div id="test" draggable="true">Drag</div>

监听dragstart事件并调用事件的setData()函数：
	
	var element = $("#test");
	element.bind("dragstart", function (event) {
		event = event.originalEvent;

		event.dataTransfer.effectAllowd = "move";

		//设置拖拽内容
		event.dataTransfer.setData("text/plain", $(this).text());
		event.dataTransfer.setData("text/html", $(this).html());
		event.dataTransfer.setData("text/uri-list", "http://www.weibo.com");
		event.dataTransfer.setDragImage("/images/drag.png", -10, -10);
	});

可以让用户拖拽文件到浏览器之外，只需设置DownloadURL即可，即将邮件附件拖拽至桌面。

	$("#preview").bind("dragstart", function(e){
		e.originalEvent.dataTransfer.setData("DownloadURL", [
			"application/octet-stream", 	//MIME类型
			"File.exe",						//文件名
			"http://example.com/file.png"	//文件地址
		].join(":"));
	});

### 释放拖拽

可监听drag事件，它可以对释放拖拽的文件和其他元素做出响应。

	var element = $("#test");
	element.bind("dragenter", function (e) {
		e.stopPropagation();
		e.preventDefault();
	});
	element.bind("dragover", function (e) {
		e.stopPropagation();
		e.preventDefault();
	});
	element.bind("drop", function (e) {});

也可在dragover事件中通过设置dropEffect来设置鼠标样式。只有当我们撤销了dragenter和dragover事件，才能开始监听drop事件。当拖拽的元素或文件在目标区域释放时才会触发drop事件。

drop事件的dataTransfer对象有一个files属性，它返回拖拽的所有文件的FileList：
	
	element.bind("drop", function (e) {
		e.stopPropagation();
		e.preventDefault();

		e = e.origianlEvent;

		var files = event.dataTransfer.files;
		var text = event.dataTransfer.getData('text');

		for (var i = 0; i < filses.length; i++) {
			//something else
		}
	});

### 撤销默认的Drag/Drop

默认情况下，将一个文件拖拽到Web页面中会让浏览器重定向到这个文件。我们当然不希望应用的URL地址发生变化，因此需要阻止将文件拖拽进应用的非目标位置时的页面跳转。
	
	$("body").bind("dragover", function (e) {
		e.stopPropagation();
		e.preventDefault();
		return false;
	});

## 复制和粘贴

复制和粘贴同拖拽的API很类似，只是使用了不同的对象：复制粘贴使用了clipbordData，拖拽使用了dataTransfer。

### 复制

相关事件如下：
	
	- beforecopy
	- copy
	- beforecut
	- cut

具体使用方法：
	
	$('textarea').bind("copy", function (e) {
		e.stopPropagation();
		e.preventDefault();

		var cd = e.originalEvent.clipboardData;

		//IE
		if (!cd) cd = window.clipboardData;

		//ff
		if (!cd) return;

		cd.setData("text/plain", $(this).text());
	});

### 粘贴

相关事件如下：
	
	- beforepaste
	- paste

具体使用方法：
	$('textarea').bind("paste", function (e) {
		e.stopPropagation();
		e.preventDefault();

		e = e.originalEvent;

		//IE
		if (!cd) cd = window.clipboardData;

		//ff
		if (!cd) return;

		$("#result").text(cd.getData("text/plain"));

		//safari中的事件支持文件的粘贴
		var files = cd.files;
	});

## 读文件

当获得一个File对象时，可以用它来实例化一个FileReader对象，将文件内容读入内存。文件读取是异步的，需要一个回调函数。

FileReader中包含四个方法来读取文件数据：
	
	- readAsBinaryString(Blob|File)
	- readAsDataURL(Blob|File)
	- readAsText(Blob|File, encoding="UTF-8")
	- readAsArrayBuffer(Blob|File)

FileReader实例包含很多事件，其中常用的如下：

	- onerror
	- onprogress
	- onload

具体使用方法：
	
	var reader = new FileReader();
	reader.onload = function (e) {
		var data = e.target.result;
	};
	reader.readAsDataURL(file);

### 二进制文件和文件切割

有时我们需要将文件中的某个片段存入内存，而并不是整个文件，file对象的splice方法可以帮忙实现，它的第一个参数是起始字节位置，第二个参数是以字节为单位的偏移量。它将返回Blob对象。

	var bufferSize = 1024;
	var pos = 0;

	var onload = function (e) {
		console.log('Read: ' + e.target.result);
	};

	var onerror = function (e) {
		console.log('Error: ' + e);
	};

	while(pos < file.size) {
		var blob = file.splice(pos, bufferSize);
		var reader = new FileReader();
		reader.onload = onload;
		reader.onerror = onerror;
		reader.readAsText(blob);
		pos += bufferSize;
	}

## 上传文件

使用Ajax，不懂自己Google、baidu吧～

### Ajax进度条

XHR2规范加入了对progress事件的支持，下载和上传都支持这个事件，使用它即可实现实时文件上传进度条。
	
	var req = new XMLHttpRequest();

	req.addEventListener('progress', updateProgress, false);
	req.addEventListener('load', transferComplete, false);
	req.open();

progress事件包括上传的postion（已经上传的字节数）和total（上传的总字节数）。

