---
title: DiscuzX二次开发之目录结构和运行逻辑
tags: [Discuz,PHP]
categories: 技术日记
date: 2021-04-23 03:15
---


## 1.目录结构

DISCUZ使用自己的框架，与现在主流的web框架不同，DISCUZ没有路由表，他的路由是由入口文件来实现的。

### api

- uc.php UCenter 通信文件
- /api/addons 应用中心
- /api/connect 通讯互联
- /api/google Google引擎结构处理
- /api/javascript 数据和广告的js调用
- /api/manyou manyou应用及搜索等相关服务
- /api/remote 远程更新
- /api/trade 支付宝、财付通等交易接口

### archiver (论坛Archiver静态化目录)

### config

- config_global.php 论坛核心参数配置文件
- config_ucenter.php UCenter核心参数配置文件

### data (论坛数据缓存目录)

### install (论坛安装目录(初始化运行时直接请求到此处将重新安装论坛))

### source (程序后端功能处理目录)

- discuz_version.php 程序版本号文件
- /source/admincp 后台管理
- /source/archiver 论坛archiver静态化程序目录
- /source/class 核心类库
- /source/function discuzX自定义函数库
- /source/include 程序功能组件目录
- /source/language 程序语言包(kv结构)
- /source/module 程序功能模块程序包
- /source/plugins 插件扩展目录

### static (程序资源目录(头像、图片、下载文件、js文件等等))

### template (前端模板目录)

- /default/common 基础css文件、header、footer等公共引入文件
- /default/collage 大学计划页面
- /default/dige dige专区页面
- /default/forum 首页、帖子页面
- /default/member 会员页面
- /default/home 家园页面
- /default/group 群组页面
- /default/mobile 移动端页面
- /default/portal 文章页面
- /default/search 搜索页面

### uc_client (UCenter客户端)

- /uc_client/control UC业务处理操作类
- /uc_client/data 缓存文件目录
- /uc_client/lib 类库目录(包括数据库操作类,XML类,UCCODE类,邮件发送类)
- /uc_client/model UC业务模型类

### uc_server (UCenter服务端 后台ucenter功能实现目录)

### 根目录文件

- admin.php 后台入口文件
- api.php API输出 入口文件
- collage.php 大学计划入口文件
- composer.json composer依赖版本记录文件
- composer.lock composer依赖版本控制文件
- connect.php 云平台接口文件
- dige.php dige专区入口文件
- forum.php 帖子信息入口文件
- group.php 群组入口文件
- home.php 家园入口文件
- index.php 首页
- member.php 用户入口文件（登录、注册、退出等）
- misc.php 程序杂项扩展入口
- plugin.php 插件入口文件
- portal.php 门户入口文件
- robots.txt 搜索引擎限制文件
- search.php 搜索频道入口文件

## 2. 运行逻辑

discuz的入口文件起到了路由的作用。一个标准的discuz请求如下：

    http://localhost/home.php?mod=space&uid=1&do=profile

当在浏览器输入以上url时，首先执行的是跟目录下的 home.php 文件

    <?php
    
    define('APPTYPEID', 1);
    define('CURSCRIPT', 'home');
    
    if(!empty($_GET['mod']) && ($_GET['mod'] == 'misc' || $_GET['mod'] == 'invite')) {
    	define('ALLOWGUEST', 1);
    }
    
    require_once './source/class/class_core.php';  //引入核心类文件，作用为：自动引入类规则，错误和异常处理，单例创建discuz_application类实例，引入默认函数库function.core.php
    require_once './source/function/function_home.php';  //引入discuzX函数库
    
    $discuz = C::app();  //实例化discuz_application类
    $cachelist = array('magic','usergroups', 'diytemplatenamehome','forumlinks','identity'); //加身份组缓存
    $discuz->cachelist = $cachelist; //设置缓存列表
    $discuz->init();  //初始化应用:数据库、系统设置、用户、session、任务、等(discuz_appication类里面)
    $space = array();
    
    $mod = getgpc('mod'); //接收$_GET['mod']数据
    if(!in_array($mod, array('space', 'spacecp', 'misc', 'magic', 'editor', 'invite', 'task', 'medal', 'rss', 'follow'))) {
    	$mod = 'space';
    	$_GET['do'] = 'home';
    }
    
    if($mod == 'space' && ((empty($_GET['do']) || $_GET['do'] == 'index') && ($_G['inajax']))) {
    	$_GET['do'] = 'profile';
    }
    $curmod = !empty($_G['setting']['followstatus']) && (empty($_GET['diy']) && empty($_GET['do']) && $mod == 'space' || $_GET['do'] == 'follow') ? 'follow' : $mod;
    define('CURMODULE', $curmod);
    runhooks($_GET['do'] == 'profile' && $_G['inajax'] ? 'card' : $_GET['do']);
    require_once libfile('home/'.$mod, 'module');  //根据请求时传的mod参数经由上方判断确定加载对应文件
    
    ?>

注: libfile()函数在会将对应的模块字符串替换为模块的实际url。
`libfile('home/'.$mod, 'module')`的实际访问地址是 `/source/module/home/$mod.php`

进入到`/source/module/home/home_space.php` 文件中 根据请求参数 uid和do的值判断具体的执行过程，有些时候会直接引入响应的程序组件完成逻辑处理(这里引入了 `/include/space/space_profile.php`)

在 `space_profile.php`中判断运行之后输出到模板文件`/template/default/home/space_profile.htm`中。
注：`template()`函数在 核心函数库 `function_core.php` 中 用于加载当前使用模板的模板文件 。根据传参此时加载了 `/template/default/home/space_profile.htm` 文件

在模板文件`space_profile.htm`中 可以直接使用php文件中的数据变量进行赋值渲染输出到浏览器。
注：template模板语法不赘述 详见 [模板语法](https://open.dismall.com/?ac=document&page=dev_template)



