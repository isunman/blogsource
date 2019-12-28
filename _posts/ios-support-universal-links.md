---
title: iOS 支持通用链接UniversalLinks
tags: [iOS, 爬虫, lnmp]
categories: 服务器
---


iOS 支持通用链接UniversalLinks

关于iOS中的Universal Links（通用链接）分三部分讲解一下。
这个功能是从 iOS 9 之后开始支持的。

----------
### 第一部分：一篇文章《iOS微信里打开app，Universal Links》

从第三方应用或者浏览器打开自己app的东西

传统的方式是通过URL Scheme的方式，但是iOS9以后又出了新的更完美的方式Universal Links。

传统的URL Scheme方式微信内置的浏览器都是封锁了所有它投资的公司之外的，只有白名单的才可以（比如京东，B站，等）。Universal Links也不能算是完美的实现，但是已经是目前为止最好的了。

就说一下这个东西的介绍，就像在windows里面为某一类型的文件添加了默认的打开方式。只不过这个文件类型变成了某一个“域名”（并非完全是某个域名，或者可以称之为某段有特殊意义的URL）的URL。这个东西只能在iOS9以后才有。

具体的表现在在iOS系统中，当你邮件或者短信或者其他方式收到含有某段“特定标示”的URL的时候如果安装有你的app，就可以直接默认调用app去打开，否则只能调用你的Safari去打开。具体到微信里面就是打开了分享的H5，右上角更多－在Safari中打开，可以直接调用你的app去打开这个URL。具体在app的中怎么展示相关的逻辑就看你的了。

#### 具体介绍的相关文章：

苹果文档：
https://developer.apple.com/library/ios/documentation/General/Conceptual/AppSearch/UniversalLinks.html

苹果检测链接有效性：
https://search.developer.apple.com/appsearch-validation-tool/

很棒的一个英文的步骤文章：
https://blog.branch.io/how-to-setup-universal-links-to-deep-link-on-apple-ios-9

相关的文章：http://www.jackivers.me/blog/2015/9/17/list-of-universal-link-ios-9-apps

http://tech.glowing.com/cn/deferred-deep-linking-and-branch-sdk-in-ios/（URL Scheme和Universal Links优缺点介绍的很棒，这个团队的技术博客也很不错哦）

http://www.cocoachina.com/ios/20150911/13321.html（里面的那个运行流程的总结图很不错）

具体的步骤就不再赘述了，因为无论是官方文档，还是其他人写的文章都很棒了，我就说一下实际开发中可能遇到的的坑。

1.注意拼写那个json文件的时候，应该是你的开发者中心，Identifiers---App IDs--- 应用的证书名字（bundle id所有对应的那个）---Prefix
.英文的点号，再跟上你的bundle id。并且文件的名字必须为“apple-app-site-association”，不能自己私自定义。用浏览器打开https://<domain>/apple-app-site-association文件，看一下成功否就可以了。

2.生成后的json文件一定是要放到你的那些网页所在的站点的目录下面的HTTPS web server根目录下面。比如你的网站是https://www.google.com（所有的网页都是以这个开头的），然后你应用的地址baseurl是https://api.google.com，其实你应该放在www的那个HTTPS web server根目录下面。然后过两三分钟吧，就用苹果有效性检测工具的那个去试一下，找一个你要分享出去的h5链接试一下，只要不出错，出来很多的相关信息就可以了（就跟用这个URL测试出来的结果一样就可以了https://www.yelp.com/biz/michael-winnetka）。

3.在工程的设置Associated Domains的时候，一定先要去苹果的开发者中心打开你的应用证书Associated Domains Enabled。然后记得更新你的证书关联的东西，然后更新本地的证书。才可以打开工程的Associated Domains。记住填写链接的时候是applinks:<domain>。这个<domain>就是第1点中调试通的那个（如果你的应用的api的和页面的不区分服务器就一样了）。

4.然后编译你的应用。如果你在短信中含有类似的https://<domain>?a=1&b=2 已经通过了后，直接长按会出来“在“XXX”中打开”就说明成功了。可以去微信微博等其他的地方去看看了。

这一段内容来源链接（版权归属原作者）：https://www.cnblogs.com/ysk-china/p/5777299.html

----------

### 第二部分：一篇文章《iOS 9 通用链接（Universal Links）》

1.apple-app-site-association文件不能带后缀，务必把".json"的后缀去掉！有些人的电脑是隐藏文件后缀的，这要格外注意；

2.apple-app-site-association一定要传到域名根目录下；

3.WebServer需要支持https，且https使用有效的证书（是私密链接）。笔者在这里卡了很久，原因就是后台给配的https使用无效的证书，导致应用安装的时候不能正确的下载apple-app-site-association文件；

4.如果想测试这个功能，可以让后台搭一个测试服务器，并配置虚拟主机生成域名，然后iOS这边通过host访问域名就可以了。注意"applink:"后面写的一定是域名，不能是IP；

5.抓包的结果显示，网络顺畅的情况下，应用会在在刚安装（不是打开）的时候会去applink中的地址下载apple-app-site-association文件，所以如果需要测试，请保证网络通畅；

6.当所有都准备好，需要测试该功能的时候，只需要在记事本或短信中输入App能识别的链接，然后直接点击或是长按就可以了，直接点的效果是跳转到你的App，然后右上角是“去网页”的箭头，长按的效果是弹出的菜单中第二项是“在'XXX'中打开”，这也代表着成功。直接在Safari中输入链接是无效的，必须从一处跳入才可以（比如上一级网页）。

7.苹果有个网址（这里）可以检测你的apple-app-site-association是否是有效的，准备好了可以测试一下。

8.测试的时候，建议使用dev证书打包，之后安装到手机上测试功能。未安装应用的情况下直接在手机上跑好像也是可以的，因为抓到过请求。

9.如果你有问题，但上面没有列出，Stackoverflow上面有个问题，可以看下其他人的建议。

#### 继续之前的补充几点

1.如果需要支持此功能，需要在开发者中心做配置。具体是这样：开发者中心->Identitiers->App IDs->需要支持的应用App ID，在Application Services列表里有Associated Domains一条，把它变为Enabled就可以了。

2.当你在项目targets->Capabilities->Associated Domains中配置app link之后，系统会自动帮你写入.entitlements文件，所以不需要自己去手动加。需要注意的是，有人遇到过.entitlements文件没有被加入工程的，这也会产生问题，需要手动把.entitlements加入工程。

3.apple-app-site-association文件里有个paths字段，这个是用来控制可识别链接的，星号的写法代表了可识别域名下所有链接。这篇博客里有很多其他公司的例子，可以参考一下。

这一段内容来源链接（版权归属原作者）：https://www.jianshu.com/p/c2ca5b5f391f

----------
### 第三部分 关于apple-app-site-association文件自己遇到的坑

Configure your website to host the apple-app-site-association file

1. Buy a domain name or pick from your existing.
2. Acquire SSL certification for the domain name.
3. Create structured apple-app-site-association JSON file.
4. Sign the JSON file with the SSL certification.
5. Configure the file server.

上面是一篇英文文章说明这个文件的配置过程，很准确。

但是，实际配置过程总遇到的问题有以下几点：

1、此文件在浏览器中访问时，默认是下载而不是打开显示文件内容。

**解决方法**：在服务器对应项目的配置文件中增加

    location ~ apple-app-site-association$ {
        add_header  Content-Type 'text/plain';
    }

2、上述方法解决了浏览器直接显示文件内容的问题，但是并不是返回的json格式数据，但是使用苹果的URL检测工具不通过，显示469错误。

**解决方法**：在服务器对应项目的配置文件中增加

    location ~ apple-app-site-association$ {
        default_type application/json;
    }

3、上述方法同时解决了显示文件内容和返回json格式的问题，也按上面的方案修改了，但是使用苹果的URL检测工具仍然不通过。

**解决方法**：注意苹果官网检测工具的一句话“Applebot will crawl your webpage”，需要服务器允许苹果爬的访问权限（默认是禁止所有爬虫的）。在修改允许苹果爬虫Applebot之后，经检测工具检测，apple-app-site-association文件已有效可用。

另外2个问题说明：

1、配置过程中需要多次重启nginx服务器，并且清除浏览器缓存。

2、apple-app-site-association文件的内容是由iOS开发工程师配置好的，此处只涉及服务器上传此文件，并支持检测工具检测到可用状态。




