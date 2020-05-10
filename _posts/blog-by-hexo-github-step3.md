---
title: 用Hexo和github搭建博客（3）：Hexo介绍
tags: [Hexo]
categories: Web构建
date: 2016-02-26 13:33
---

## 为什么是博客

对于个人来说，博客可以发布记录并沉淀自己的观点、经验和想法。在锻炼了自己的表达能力和思维的同时，还可以形成自己的“社交货币”。

## 为什么是静态博客

当然，直接使用现成的博客平台的就不在此讨论了，只说个人独立博客。很多人选择在虚拟主机或 VPS 上面搭建动态博客。但是这些主机商通常“免费的不稳定，稳定的不免费”。

正如阮一峰所说：

>喜欢写Blog的人，会经历三个阶段。
>
>第一阶段，刚接触Blog，觉得很新鲜，试着选择一个免费空间来写。
>
>第二阶段，发现免费空间限制太多，就自己购买域名和空间，搭建独立博客。
>
>第三阶段，觉得独立博客的管理太麻烦，最好在保留控制权的前提下，让别人来管，自己只负责写文章。

静态博客编译之后是纯html页面,优点就是支持它的环境十分好找,例如 Github、gitcafe、七牛云存储等站点都支持静态页面托管,自然是我们的首选了。

但是静态博客并非没有缺点。动态博客更新文章时,脚本是不变的,只需要更新数据库。静态博客要频繁改动文件,不支持增量式上传的东西,比如ftp,就难于管理。此外,还要十分熟悉 git 各种命令,才能部署页面。

不过对于极客来说，使用静态博客正好可以来练手和显示极范。

## Hexo是什么
Hexo 是一款基于 Node.js 的静态博客框架。目前在 Github上已有8712  Star 和 1435 Fork。

Hexo 是一个轻量的静态博客框架。通过 Hexo 可以快速生成一个静态博客框架,仅需要几条命令就可以完成,相当方便。

而架设 Hexo 的环境更简单，不需要 lnmp/lamp 这些繁琐复杂的环境 仅仅需要一个简单的 http 服务器即可使用 或者使用互联网上免费的页面托管服务。比如，Hexo 的官方网站 http://hexo.io 就是托管于 Github 的 pages 服务上。

## Hexo特性



- 风一般的速度

	Hexo基于Node.js，支持多进程，几百篇文章也可以秒生成。

- 流畅的撰写

    支持GitHub Flavored Markdown和所有Octopress的插件。

- 扩展性

    Hexo支持EJS、Swig和Stylus。通过插件支持Haml、Jade和Less.

## Hexo 快速入门
### Hexo 安装方法

1、Mac OS X/Linux或其他UNIX/类UNIX系统

2、node环境 请去 官方网站 下载源代码编译安装 

	wget http://nodejs.org/dist/v0.12.4/node-v0.12.7.tar.gz
	tar zxvf node-v0.12.0.tar.gz
	cd node-v0.12.0
	./configure --prefix=/usr
	make
	make install

3、npm(node包管理器)

redhat系

	yum install npm -y

debain系

	apt-get install npm -y

其他发行版请自行寻找wiki获得帮助

4、通过npm安装 Hexo-Cli 和 Hexo

	npm install hexo-cli -g
	npm install hexo --save

如果以上命令不能安装 可以尝试把官方源替换为 淘宝npm源 再执行安装Hexo 

	npm install -g cnpm --registry=https://registry.npm.taobao.org

或者你直接通过添加 npm 参数 alias 一个新命令: 

	alias cnpm="npm --registry=https://registry.npm.taobao.org \ --cache=$HOME/.npm/.cache/cnpm \ --disturl=https://npm.taobao.org/dist \ --userconfig=$HOME/.cnpmrc"

Or alias it in .bashrc or .zshrc

	$ echo '\n#alias for cnpm\nalias cnpm="npm --registry=https://registry.npm.taobao.org \ --cache=$HOME/.npm/.cache/cnpm \ --disturl=https://npm.taobao.org/dist \ --userconfig=$HOME/.cnpmrc"' >> ~/.zshrc && source ~/.zshrc


Windows系统（具体环境配置请参考 [点击查看](http://www.cnblogs.com/seanlv/archive/2011/11/22/2258716.html) ）
node环境
npm(node包管理器)

### Hexo 配置方法

新建一个需要当做博客目录的文件夹

	mkdir blog

进去之后加入 hexo 主程序和安装 npm

	hexo init
	npm install

完成之后，文件夹大致结构如下

- scaffolds 工具模板

- scripts hexo的功能js

- source 博客资源文件夹

- source/_posts 文章文件夹

- themes 存放主题的文件夹

- themes/landscape 默认皮肤文件夹

- _config.yml 全局配置文件

- db.json json格式的静态常量数据库

- _posts 目录：Hexo存放博客文章的文件夹

- themes 目录：存放皮肤的文件夹，默认使用官方的主题。你也可以从 Hexo 主题页面下载你喜欢的主题。

## Hexo 配置文件详解

即根目录下 _config.yml 全局配置文件

	# Hexo Configuration
    ## Docs: https://hexo.io/docs/configuration.html
    ## Source: https://github.com/hexojs/hexo/
    
    # Site 这里的配置，都是网站本身的全局参数，可以参考我的博客
    title: 太阳侠
    subtitle: 我是一颗恒星
    description: love IT, love Movie, love Love
    author: isunman
    language: zh-Hans
    timezone:
    
    # URL #这项可以绑定域，指定博客的域名映射的目录对子目录有用
    ## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
    url: http://isunman.com
    root: /
    permalink: :year/:month/:day/:title/
    permalink_defaults:
    
    # Directory # 指定每一个功能对应的目录
    source_dir: source
    public_dir: public
    tag_dir: tags
    archive_dir: archives
    category_dir: categories
    code_dir: downloads/code
    i18n_dir: :lang
    skip_render:
    
    # Writing 文章布局、写作格式的定义，不修改
    new_post_name: :title.md # File name of new posts
    default_layout: post
    titlecase: false # Transform title into titlecase
    external_link: true # Open external links in new tab
    filename_case: 1
    render_drafts: false
    post_asset_folder: false
    relative_link: false
    future: true
    highlight:
      enable: true
      line_number: true
      auto_detect: false
      tab_replace:
    
    # Category & Tag # 分类和标签
    default_category: uncategorized
    category_map:
    tag_map:
    
    # Date / Time format # 日期时间格式，不修改
    ## Hexo uses Moment.js to parse and display date
    ## You can customize the date format as defined in
    ## http://momentjs.com/docs/#/displaying/format/
    date_format: YYYY-MM-DD
    time_format: HH:mm:ss
    
    # Pagination 每页显示文章数，可以自定义，默认值10
    ## Set per_page to 0 to disable pagination
    per_page: 10
    pagination_dir: page
    
    # Extensions 扩展，此处可以设置要使用的主题
    ## Plugins: https://hexo.io/plugins/
    ## Themes: https://hexo.io/themes/
    theme: next
    
	# 打开RSS订阅功能
    feed:
      type: atom
      path: atom.xml
      limit: 20
      hub:
    
	# 网站版权信息中的开始年份
    since: 2011
    
	# 以下两项  Social links 和 links 都是自定义的链接
    # Social links
    social:
      github: https://github.com/isunman
      # 等等
    
    # title, chinese available
    links_title: Links
    # links
    links:
      知乎: http://www.zhihu.com/
    
    # Deployment 站点部署到 Github 要配置
    ## Docs: https://hexo.io/docs/deployment.html
    deploy:
      type: git
      repo: https://github.com/isunman/isunman.github.com.git
      branch: master

## Hexo 配置注意事项

1、用文本编辑器修改_config.yml这个文件，大致如上，只需要自行修改几个，其他保持默认即可。

2、通常需要修改站点名称 /URL格式 /归档设置 /disqus评论用户名 /部署配置 这几项就可以了 注意冒号后面都要添加一个半角空格，之后才是设置参数。

3、自定义域名设置，在 source 我文件夹下面新建 CNAME 文件，里面写入你的自定义域名并设置您的dns配置cname方式到服务提供商的给的地址即可 。

## 使用 Hexo 发布文章

站点配置好，如何发布文章呢？

	hexo new "newpost"

在 Hexo根目录的 \source\_posts 中打开这个文件（建议使用 Sublime 打开，其他的编程IDE也可以），配置开头。

	---
	title: newpost #文章标题，需要修改
	date: 2016-02-26 16:39:43 #发表日期，一般不改动
	categories: blog #文章文类
	tags: [博客，文章] #文章标签，多于一项时用这种格式
	---

然后命令执行：

	hexo s

浏览器访问 localhost：4000 预览效果。

	hexo d

没问题之后，使用下面命令同步到 Github。完成之后刷新可以看到实际效果。