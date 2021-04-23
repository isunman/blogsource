---
title: DiscuzX3.1使用Composer安装第三方类库  
tags: [PHP, Discuz]
categories: 技术日记
date: 2021-04-23 17:48
---

Discuz X3.1 是一个成熟的基于PHP开发的社区系统（广义上来讲是一个建站系统），自从被企鹅收购后悄无声息的被遗弃了，伤感啊。如今要在Discuz上加什么功能，改bug什么的只能依靠自力更生了；好在有各种开源社区，开发好的功能、类库你可以无偿的直接拿来用。

Composer 是一个PHP类库管理器（官网http://docs.phpcomposer.com）。Composer 优雅的解决了PHP项目中第三方类库的安装及依赖关系。很多成熟的项目（如 Laravel ）已经把Composer作为首选的安装方式；

所以，在Discuz X3.1 上安装第三方类库使用Composer后，会很大提高生产力。

下面我来说说如何在 Discuz X3.1中使用 Composer 安装并使用第三方类库 ；

## 先说安装Composer到Discuz X3.1框架下

### 一. 修改 `class_core.php (\source\class\class_core.php)` 如下

    $autoloadfile=DISCUZ_ROOT.'/vendor/autoload.php';
    if(!file_exists($autoloadfile)){  
    	if(function_exists('spl_autoload_register')) {
    		spl_autoload_register(array('core', 'autoload'));
    	} else {
   			function __autoload($class) {
    			return core::autoload($class);
    		}
   		}
    }else{
    	require_once  $autoloadfile;//如果存在Composer 加载器 ，则使用 ；
    }

### 二. 在 Discuz 目录下，新建一个 composer.json 内容如下：

    {

		"require": {
         	"guzzlehttp/guzzle": "~6.0"
		},

        "autoload":{
        	"classmap": ["source/class/"]
        }
	}

### 三. 终端上执行命令 `composer install`

看到类似这样的执行结果，表示已经安装成功了

    Loading composer repositories with package information
    Updating dependencies (including require-dev)
    Package operations: 10 installs, 0 updates, 0 removals
      - Installing ralouphie/getallheaders (3.0.3): Loading from cache
      - Installing psr/http-message (1.0.1): Loading from cache
      - Installing guzzlehttp/psr7 (1.8.1): Loading from cache
      - Installing guzzlehttp/promises (1.4.1): Loading from cache
      - Installing symfony/polyfill-php72 (v1.19.0): Loading from cache
      - Installing symfony/polyfill-intl-normalizer (v1.19.0): Loading from cache
      - Installing paragonie/random_compat (v2.0.20): Loading from cache
      - Installing symfony/polyfill-php70 (v1.19.0): Loading from cache
      - Installing symfony/polyfill-intl-idn (v1.19.0): Loading from cache
      - Installing guzzlehttp/guzzle (6.5.5): Loading from cache
    guzzlehttp/psr7 suggests installing laminas/laminas-httphandlerrunner (Emit PSR-   7 responses)
    symfony/polyfill-intl-normalizer suggests installing ext-intl (For best performa   nce)
    paragonie/random_compat suggests installing ext-libsodium (Provides a modern cry   pto API that can be used to generate random bytes.)
    symfony/polyfill-intl-idn suggests installing ext-intl (For best performance)
    guzzlehttp/guzzle suggests installing psr/log (Required for using the Log middle   ware)
    Writing lock file
    Generating autoload files
    4 packages you are using are looking for funding.
    Use the `composer fund` command to find out more!


另外，也可以查看项目根目录下的vendor下面的文件，核验新引入的类库是否下载更新成功。

具体如何安装及使用 Composer 请参阅 http://docs.phpcomposer.com

## 在Discuz程序中如何使用 “composer安装”的第三方类库呢？

通过Composer autoload 加载器会把这些第三方库自动加载进来，这些第三方类库程序中的类、方法(函数)等等 在Discuz 程序中你想怎么用都可以。

例如，以使用guzzle为例。在任何一个Discuz X3.1框架下的PHP文件中，直接使用下面代码即可。

    use GuzzleHttp\Client;
    
    $client = new Client([
    	// Base URI is used with relative requests
    	'base_uri' => 'http://httpbin.org',
    	// You can set any number of default request options.
    	'timeout'  => 2.0,
    ]);
    
    $response = $client->get('http://httpbin.org/get');
    
    $code = $response->getStatusCode(); // 200
    $reason = $response->getReasonPhrase(); // OK
    
    // Check if a header exists.
    if ($response->hasHeader('Content-Length')) {
    	echo "It exists";
    }

另外，Discuz X3.2 也适用上述方法。

