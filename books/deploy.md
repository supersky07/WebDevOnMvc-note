# 部署

========

## 性能

提高性能，最简单的也是最显著的方法就是：减少HTTP请求的数量。每一个HTTP请求除了有TCP开销以外，还包含了大量的头信息。

减少请求的方式有很多，常见的就是将多个脚本合并成一个脚本文件，或将多个CSS合成一个样式表，再将图片拼成CSS Sprites，然后使用background-image和background-position属性在一诶按中显示对应的图片。

避免重定向也是减少请求的一个方法，一般URL后面的/不应该被省略，它会导致重定向。

## 缓存

将静态资源设置Expires头来设置缓存失效时间。

	Expires: Thu, 20 March 2015 00:00:00 GMT

HTTP1.1引入了Cache-Control头，它带来了更高级的缓存，同时还弥补了Expires的不足。Cache-Control的控制头信息有很多选项，可用逗号分隔：

	Cache-Control: max-age=3600, must-revalidate

以下为Cache-Control可能会用到的选项：

- max-age
	- 以秒为单位，指定资源的最大有效时间
- public
	- 标记资源是可被缓存的
- no-store
	- 完全关闭缓存，动态内容才会更多的使用这个选项
- must-revalidate
	- 告诉缓存它们必须遵循任何你给定的信息，并基于这些信息来判断资源的新旧程度。

给提供服务的资源增加Last-Modified头信息也有助于缓存。浏览器在对该资源后续的请求中，就能指定If-Modified-Since头信息，这是一个时间戳，用来判断缓存是否已更新。若未更新，则直接返回304状态码，浏览器仍然可以请求，但不会返回具体内容，会从浏览器缓存中读取。

Last-Modified的替代方式是Etag。比较Etag就像比较两个文件的hash值，请求头中带的If-None-Match与服务器端的Etag比较，若不同证明资源已更新，否则返回304状态码。

Etag通常是使用服务器指定的一些属性来构建——也就是说，两个独立的服务器对同样的资源生成的Etag可能不一样——随着服务器集群越来越普遍，这成为一个现实的问题。当然当前也已经有很成熟的解决方案。

## 源码压缩

源码压缩主要做的事情是删除空格、换行和注释，并将可替代的变量名缩短，来保证文件越来越小。另外一个压缩的好处就是可以让代码很难读懂，从而减少因为代码暴露的安全问题。

YUI Compressor是一款比较常见的压缩工具，假设代码如下：
	
	function per (value, total) {
		return (value / total) * 100;
	}

压缩后则变为：
	
	fucntion per(a,b){return (a/b)*100}

Yui Compressor在压缩时若检测到使用eval()或with()语句时，则不会替换其内部的变量。但eval()和with()都会导致浏览器的性能问题，同时还有可能引起XSS漏洞。故尽量少用此语句。

Yui Compressor的最简单的使用方法是下载其二进制jar文件，并在命令行中执行如下代码：

	java -jar yuicompressor.jar test.js | test.min.js

## Gzip压缩

Gzip是互联网最流行也是支持最广泛的压缩方式，它是由GNU项目组开发的，在HTTP1.1中开始支持。

如何开启Gzip压缩？在客户端发送请求时增加头信息：
	
	Accept-Encoding: gzip, deflate

若服务器端支持Gzip压缩，则会在压缩后，在响应头增加头信息：
	
	Content-Encoding: gzip

客户端浏览器在看到此头后，就会进行Gzip解压缩，进而得到请求的内容。

Gzip通常能减少响应70%的体积，同时服务器都支持此压缩方式。

## 使用CDN

由于用户与请求资源的物理地址的距离极大的影响了资源的加载速度，内容分发网络（CDN）应运而生。他在多个地理位置都部署服务器，而我们只需要把静态资源文件托管到CDN上即可。

这样用户在访问你的网站时，就可以到距离较近的服务器去获取资源了。据Yahoo公司的调查使用CDN能减少20%左右的请求时间。

## 审查工具

YSlow可以查看网站的性能详情，用来统计页面有哪些需要的优化点。

Chrome的Audits也可以监控网页性能，告诉你的网站有哪些可以改进的点。
