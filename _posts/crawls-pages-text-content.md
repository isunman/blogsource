---
title: 抓取网页正文内容
tags: [正则表达式,PHP]
categories: 技术日记
---


近来做了有关抓取网页正文内容（含正文中图片）的功能，这里小结一下。

服务器端语言：PHP

已知：源文章的 URL

## 1、常规方法

使用 PHP 内置函数 file_get_contents 或者 CURL 方法直接获取 URL 对应的网页源代码。

这两种方法的使用都很简单，此处不展开讲。注意 CURL 需要模块开启。

问题：可以提取到大部分网页源代码，但是同时也提取到了页面上的其他信息，例如广告、侧栏、通用顶部、通用底部、用户评论等等“噪点数据”，我们其实需要的是“原创文正的正文部分”，不需要其他乱七八糟的内容。

此时，这个源代码就需要进一步处理，使用普通的正则匹配不可用，因为网站内容格式千差万别。如果只是提取固定网站或者有限几个网站的内容，单独进行正则匹配也可凑合使用。这里要介绍的是一种通用的方法，不限网站个数和内容格式，适配几乎所有内容类的网站。

## 2、进阶方法

此处需要一个算法，把已经提取到的内容源文件处理一下，计算出正文部分内容（含图片）。

此方法叫做：Readability，一个开源的提取网页正文内容的类。Readability 是一个很不错的实现，它通过遍历Dom对象，通过标签和常用文字的加减权，来重新整合出页面的内容。

JS 版本的 Readability 是最好用的，它可以直接在浏览器完成分析，于是用户还可以人工对分析出来的内容进行修改和校正。

由于 Readability 解决的需求很通用，于是其他语言的程序员纷纷移植了该算法。

PHP 版本 [https://github.com/feelinglucky/php-readability](https://github.com/feelinglucky/php-readability)

Java 版本 [https://github.com/wuman/JReadability](https://github.com/wuman/JReadability)

Node 版本 [https://www.npmjs.org/package/node-readability](https://www.npmjs.org/package/node-readability)

本人就是使用了 PHP 版本的。只是需要与自己的具体业务相结合，需要自己再修改即可，核心算法不变。

这个方法很有效，做内容聚合类的项目很适合（注意版权问题）。

## 3、特殊方法

为什么有特殊方法？

因为有些网站有版权保护意识，增加了一些措施防止原创内容被盗取。通常都是图片防盗链，也有正文文字也防盗取的。

此处举2例。

### （1）微信公众号文章

微信公众号文章目前数据量就不用说了，发的人多，看的人超级多，不乏一些优秀的原创。

它这个是要是图片防盗链。提取内容没有问题，但是所有的图片都被防盗链机制过滤掉了。

解决方案：

例如，其中一篇截图：

![蜜糖少女孙怡](http://7xi3f2.com1.z0.glb.clouddn.com/sunyi00000.png)

下面对应的图片部分的代码为：

	<img data-s="300,640" data-type="jpeg" data-src="http://mmbiz.qpic.cn/mmbiz/9aMpMo0Xap52pCAdiaicq59bbawuXKt0Epws5Qc845SIgM059HHuBIkYpUzl9zUtLLjgwvKjwCkf7VGW6buZAIXQ/0?wx_fmt=jpeg" data-ratio="1.5" data-w="" src="http://mmbiz.qpic.cn/mmbiz/9aMpMo0Xap52pCAdiaicq59bbawuXKt0Epws5Qc845SIgM059HHuBIkYpUzl9zUtLLjgwvKjwCkf7VGW6buZAIXQ/640?wx_fmt=jpeg&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" style="width: auto !important; visibility: visible !important; height: auto !important;">

其中，src 中的数据是懒加载的同时防盗链，在当前位置到达浏览器窗口时才加载（使用了JS），所以如果直接获取源代码，所有图片属性 src 都是指定的防盗链的静态图片 URL，不是图片的实际 URL。

但是，观察发现，图片实际 URL，已经包括在属性 data-src 中，同时返回了（方便 JS 处理）。

因此，我们在这里替换一次，把所有的图片属性 src 的值替换为图片实际 URL（即 data-src 中的值）即可。 

	$text= preg_replace("/data-src/ie", "src", $text);

之后，按普通方法继续处理。

### （2）36氪文章

科技媒体36氪文章的保护意识更强，直接抓取，别说图片，连正文文字都获取不到。

这里引用一下 V2EX 上的一篇文章的说法：

> 本人使用 PHP 的： file_get_contents 和 curl 函数都未曾获取到。就认真研究了下 36 氪的正文加载过程。

> 看了一下，它应该是使用 js 加载的正文内容。

> 如果禁用 js 调试，正文就没有内容；否则，就可以正常显示。但是查看了下所有的 js 文件也没看出来头绪，如果使用“网页另存为”本地文件，再用浏览器打开也是没有正文。

> 是不是使用了什么加密技术来获取正文（为了防止爬虫）？各位探讨一下

就是这个意思，我也遇到了同样的问题，不过我怀疑：它的正文时动态加载的。

看了之后，我又去仔细研究了下，得出结论：

因为它的正文内容的确是**使用 JS 动态加载的**。

幸运的是，实际的数据也已经提前随源代码返回来了，只是没有经过 JS 处理，浏览器中不可见而已。

参考截图：

![](http://7xi3f2.com1.z0.glb.clouddn.com/36krarticle00000.png)

经过仔细观察发现，可以获取 html 源代码中的 data-dom-id 值为 App-react-component-0 的属性 data-props 的值，就是包含正文内容的 JSON 格式数据。

此处可以使用 DOM 获取，也可以使用正则匹配，本人使用了正则匹配。

正则表达式：

	data-dom-id="App-react-component-0"([\s\S]*?)data-props=['|\"]([\s\S]*?)['|\"]\s+data-trace=

匹配结果的第2个元素即为包含正文数据的 JSON 格式数据。

之后，把 JSON 格式数据转换为 数组，即可进一步处理。

	preg_match("/data-dom-id=\"App-react-component-0\"([\s\S]*?)data-props=['|\"]([\s\S]*?)['|\"]\s+data-trace=/i", $source, $matches);
	$matchesstr = htmlspecialchars_decode($matches[2]);
	$matchestarget = json_decode($matchesstr,true);

此处注意，转移实体的反转义。否则 JSON 解码函数结果为空。

## 4、小结

以上，就是已经用到的方法，总体思路就是：

直接获取 --> 间接获取 --> 正则匹配 --> 替换成可处理格式

不过，这些具体方法本身可能需要随时修正的，具体就看源网站的规则什么时候修改，修改为什么样子了。

掌握核心思考方法和处理机制，任其万变，不离其宗。

