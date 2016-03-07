---
title: Markdown的基本语法
tags: [Hexo, Markdown]
categories: Web构建
---

## 什么是 Markdown
Markdown 是一种方便记忆、书写的纯文本标记语言，用户可以使用这些标记符号以最小的输入代价生成极富表现力的文档。

特点就是：易读、易写、跨平台。

## Markdown基本语法

### 1、分级标题
#### 一级标题 H1

	# 我是一级标题

#### 二级标题 H2

	## 我是二级标题

#### 三级标题 H3

	### 我是三级标题


以此类推，最多到六级标题，没有七级标题。

### 2、粗体和斜体

使用 \* 和 \*\* 表示斜体和粗体

示例：
	
	*斜体* 和 **粗体**
	
*斜体* 和 **粗体**

### 3、引用

使用 > 表示文字引用

>野火烧不尽，更上一层楼。

多行引用需要使用两个空格。

>人生若只如初见，  
>一枝红杏出墙来。

### 4、行内代码

使用 \`代码` （~ 键的下档键）表示行内代码块。

例如我是行内代码 `Hello World` 看到了吧，融为一体。


### 5、代码块

使用四个缩进空格或者一个Tab表示代码块
    
示例：

	我是代码块
	这是第二行
	我是最后一行

### 6、无序列表

使用 *，+，- 表示无序列表


示例：

	* 无序列表1
	+ 无序列表2
	- 无序列表3

* 无序列表1

+ 无序列表2

- 无序列表3

### 7、有序列表

使用数字和点表示有序列列表，注意点后有一个空格。

示例：

	1. 有序列表一
	2. 有序列表二
	3. 有序列表三
	
1. 有序列表一

2. 有序列表二

3. 有序列表三

### 8、外链接

使用 \[描述](链接地址) 为文字增加外链接。

示例：

	这是去往[太阳侠](http://isunman.com)的链接

这是去往[太阳侠](http://isunman.com)的链接

### 9、插入图像

使用 \!\[描述]（图片链接地址）插入图像

示例：

![太阳侠的头像](http://isunman.com/images/avatar.jpg)

第三方云存储图片示例（此处存储在七牛云存储，福利啊）：

示例：

![腿玩年](http://7xrl2u.com1.z0.glb.clouddn.com/isunman1ad5ad6eddc451da23540d51b4fd5266d11632ed.jpg)


### 10、插入HTML代码

Markdown完全兼容html，可以插入任意的html代码，包括分享的视频和音乐等。

例如音乐

<embed src="http://www.xiami.com/widget/0_1774250852/singlePlayer.swf" type="application/x-shockwave-flash" width="257" height="33" wmode="transparent"></embed>

例如视频：

<embed src="http://player.youku.com/player.php/sid/XODI1MDg0MDQ4/v.swf" allowFullScreen="true" quality="high" width="480" height="400" align="middle" allowScriptAccess="always" type="application/x-shockwave-flash"></embed>
