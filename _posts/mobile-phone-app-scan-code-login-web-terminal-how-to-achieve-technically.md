---
title: 手机App扫码登录Web端，技术上是怎么实现的？
tags: [即时通讯,微信]
categories: 技术日记
date: 2020-02-15 11:38
---

手机App扫码登录Web端，技术上是怎么实现的？

## 1、引言

扫码登录这个功能，最早应该是微信的PC端开始搞，虽然有点反人类的功能（不扫码也没别的方式登录），但不得不说还是很酷的。

下面这张图，不管是IM开发者还是普通用户，应该很熟悉：

![](http://www.52im.net/data/attachment/forum/202001/06/202655hhi73ezfei7icd88.jpg)

于是，搞IM产品的老板和产品经理们，从此又多了一个要抛给程序员们的需求——“为什么微信有扫一扫登录，而我们的没有？”。

好吧，每次只要是微信有的功能，IM程序员们想甩锅，难度就有点大了，毕竟老板们都都会想当然认为，微信有的“我”的IM产品里也得有。

既然无法回避，那就只能老老实实搞懂技术原理，然后自已使劲撸吧。

本文将简要的介绍扫码登录功能的技术实现逻辑，并实际结合淘宝、微信的扫码登录功能，学习和研究大厂主流应用的技术实现思路。

## 2、基本技术原理

### 2.1 扫码登录功能到底是什么样的？

首先介绍下什么是扫码登录。现在大部分同学手机上都装有微信、qq和淘宝这一类的软件。而这些app都有他们相对应的网页端。为了让用户在使用他们的网页时登录更加方便和安全，使用手机扫一扫就可以登录的服务，就显得自然而然了。

几个主流大厂应用扫码登录时的界面效果如下：

![](http://www.52im.net/data/attachment/forum/202001/06/203056dycwfmmpcpoh6m8e.jpg)

有很多小伙伴可能会感到很神奇，网页上只是显示了个二维码，它怎么就知道是哪个手机扫到了二维码，并且进行登录的呢？而且，登录完成以后，还能直接把用户信息显示给用户，真的是很神奇啊。

### 2.2 扫码登录功能的完整技术逻辑

#### 1）网页端与服务器的配合逻辑：

接下来就是对于这个服务的详细实现。

首先用户打开网站的登录页面的时候，向浏览器的服务器发送获取登录二维码的请求。服务器收到请求后，随机生成一个uuid，将这个id作为key值存入redis服务器，同时设置一个过期时间，再过期后，用户登录二维码需要进行刷新重新获取。

同时，将这个key值和本公司的验证字符串合在一起，通过二维码生成接口，生成一个二维码的图片（二维码生成，网上有很多现成的接口和源码，这里不再介绍）。然后，将二维码图片和uuid一起返回给用户浏览器。

浏览器拿到二维码和uuid后，会每隔一秒向浏览器发送一次，登录是否成功的请求。请求中携带有uuid作为当前页面的标识符。这里有的同学就会奇怪了，服务器只存了个uuid在redis中作为key值，怎么会有用户的id信息呢？

这里确实会有用户的id信息，这个id信息是由手机服务器存入redis中的。具体请继续阅读“手机端与服务器的配合逻辑”。

#### 2）手机端与服务器的配合逻辑：

话说，浏览器拿到二维码后，将二维码展示到网页上，并给用户一个提示：请掏出您的手机，打开扫一扫进行登录。

用户拿出手机扫描二维码，就可以得到一个验证信息和一个uuid（扫描二维码获取字符串的功能在网上同样有很多demo，这里就不详细介绍了）。

由于手机端已经进行过了登录，在访问手机端的服务器的时候，参数中都会携带一个用户的token，手机端服务器可以从中解析到用户的userId（这里从token中取值而不是手机端直接传userid是为了安全，直接传userid可能会被截获和修改，token是加密的，被修改的风险会小很多）。手机端将解析到的数据和用户token一起作为参数，向服务器发送验证登录请求（这里的服务器是手机服务器，手机端的服务器跟网页端服务器不是同一台服务器）。

服务器收到请求后，首先对比参数中的验证信息，确定是否为用户登录请求接口。如果是，返回一个确认信息给手机端。

手机端收到返回后，将登录确认框显示给用户（防止用户误操作，同时使登录更加人性化）。用户确认是进行的登录操作后，手机再次发送请求。服务器拿到uuId和userId后，将用户的userid作为value值存入redis中以uuid作为key的键值对中。

#### 3）登录成功时的逻辑：

然后，浏览器再次发送请求的时候，浏览器端的服务器就可以得到一个用户Id，并调用登录的方法，生成一个浏览器端的token，再浏览器再次发送请求的时候，将用户信息返回给浏览器，登录成功。这里存储用户id而不是直接存储用户信息是因为，手机端的用户信息，不一定是和浏览器端的用户信息完全一致。

#### 4）详细的技术原理总结如下图所示：

![](http://www.52im.net/data/attachment/forum/202001/06/210751e2bz7c27qcbq7ugo.png)

### 3、淘宝的扫码登录技术实现

本节我们以淘宝的扫码登录为例，来实际研究分析一下淘宝的扫码登录实现逻辑。

登录界面 https://login.taobao.com/member/login.jhtml 传回来的参数为：



然后请求(GET)报文是这样的:

	https://qrlogin.taobao.com/qrcodelogin/qrcodeLoginCheck.do?
	lgToken=2c3b4d53ef0513787bf4ce711ea5ba53&defaulturl=&_ksTS=1540106757739_2804&callback=jsonp2805
关键的就是lgToken，是网页的唯一ID，当打开了二维码登录的时候，网页在轮询(应该是长轮询long polling)调用接口去请求服务器。扩展：彻底理解cookie，session，token

如果没有扫码，返回的为：


如果扫了的话则会返回：

	{
	
	    "code": "10001",
	
	    "message": "mobile scan QRCode success",
	
	    "success": true
	
	}
长时间没有扫码的话，网页端会停止轮询，二维码失效！

当手机端确认登录后，接口返回的是：

	{ 
	"code": "10006", 
	"success": true,
	 "url": "https://login.taobao.com/member/loginByIm.do?uid=cntaobaoxxx&token=ff82fc0d1d395a33d3b38ec5a4981336&time=1530179143250&asker=qrcodelogin&ask_version=1.0.0&defaulturl=https://www.taobao.com&webpas=0b7aed2d43f01825183e4a49c6cae47d1479929926"
	}
表示登录成功，当然手机端与服务端在点击"确认登录"之间的交互可能就是这样：网页端生成的lgToken去请求服务端，服务端记住了这个lgToken并认为登录了，当网页端再次轮询请求接口时，就返回真正的登录态Token，网页端此时就可以凭着这个Token来登录了。

详细的技术逻辑如下图所示：

![](http://www.52im.net/data/attachment/forum/202001/06/212653o685grmsmldylv36.png)

## 4、微信的扫码登录技术实现

### 4.1 技术原理流程图
![](http://www.52im.net/data/attachment/forum/202001/06/213927pve6vavng8iavy4v.png)

微信的网页版访问地址是：https://wx.qq.com/，有兴趣也可以自行深入研究。

### 4.2 实际的技术实现逻辑

#### 1）获取唯一的uuid, 以及包含uid信息的二维码：



// 获取uuid

	getUUID: function() {
	
	    vare = t.defer();
	
	    returnwindow.QRLogin = {},
	
	    $.ajax({
	
	        url: i.API_jsLogin,
	
	        dataType: "script"
	
	    }).done(function() {
	
	        200 == window.QRLogin.code ? e.resolve(window.QRLogin.uuid) : e.reject(window.QRLogin.code)
	
	    }).fail(function() {
	
	        e.reject()
	
	    }),
	
	    e.promise
	
	}
#### 2）浏览器轮询服务器，获取扫码状态：

// 查看扫码状态

	checkLogin: function(e, a) {
	
	    varn = t.defer()
	
	        , a = a || 0;
	
	    returnwindow.code = 0,
	
	    window.checkLoginPromise = $.ajax({
	
	        url: i.API_login + "?loginicon=true&uuid="+ e + "&tip="+ a + "&r="+ ~newDate,
	
	        dataType: "script",
	
	        timeout: 35e3
	
	    }).done(function() {
	
	        newRegExp("/"+ location.host + "/");
	
	        if(window.redirect_uri && window.redirect_uri.indexOf("/"+ location.host + "/") < 0)
	
	            returnvoid (location.href = window.redirect_uri);
	
	        vare = {
	
	            code: window.code,
	
	            redirect_uri: window.redirect_uri,
	
	            userAvatar: window.userAvatar
	
	        };
	
	        n.resolve(e)
	
	    }).fail(function() {
	
	        n.reject()
	
	    }),
	
	    n.promise
	
	}
#### 3）根据服务器返回的扫码状态，进行相应的操作：

408 扫码超时：如果手机没有扫码或没有授权登录，服务器会阻塞约25s，然后返回状态码 408 -> 前端继续轮询





400 二维码失效：大约5分钟的时间内不扫码，二维码失效



201 已扫码：如果手机已经扫码，服务器立即返回状态码和用户的基本信息 （window.code=201,window.code.userAvator="…"），-> 前端继续轮询



200 已授权：如果手机点击了确认登录，服务器返回200及token -> 前端停止轮询, 获取到token，重定向到目标页



具体的代码示例如下：

// 根据服务器返回的扫码状态，进行相应的操作

	functiono(c) {
	    switch(c.code) {
	    case200:
	        t.newLoginPage(c.redirect_uri).then(function(t) {
	            varo = t.match(/<ret>(.*)<\/ret>/)
	                , r = t.match(/<script>(.*)<\/script>/)
	                , c = t.match(/<skey>(.*)<\/skey>/)
	                , s = t.match(/<wxsid>(.*)<\/wxsid>/)
	                , l = t.match(/<wxuin>(.*)<\/wxuin>/)
	                , d = t.match(/<pass_ticket>(.*)<\/pass_ticket>/)
	                , f = t.match(/<message>(.*)<\/message>/)
	                , u = t.match(/<redirecturl>(.*)<\/redirecturl>/);
	            returnu ? void (window.location.href = u[1]) : o && "0"!= o[1] ? (alert(f && f[1] || "登录失败"),
	            i.report(i.AUTH_FAIL_COUNT, 1),
	            void location.reload()) : (e.$emit("newLoginPage", {
	                Ret: o && o[1],
	                SKey: c && c[1],
	                Sid: s && s[1],
	                Uin: l && l[1],
	                Passticket: d && d[1],
	                Code: r
	            }),
	
	            void (a.getCookie("webwx_data_ticket") || n.report(n.ReportType.cookieError, {
	                text: "webwx_data_ticket 票据丢失",
	                cookie: document.cookie
	            })))
	        });
	        break;
	
	    case201:
	        e.isScan = !0,
	        n.report(n.ReportType.timing, {
	            timing: {
	                scan: Date.now()
	            }
	        }),
	        t.checkLogin(e.uuid).then(o, function(t) {
	            !t && window.checkLoginPromise && (e.isBrokenNetwork = !0)
	        });
	        break;
	
	    case408:
	        t.checkLogin(e.uuid).then(o, function(t) {
	            !t && window.checkLoginPromise && (e.isBrokenNetwork = !0)
	        });
	        break;
	
	    case400:
	
	    case500:
	
	    case0:
	        vars = a.getCookie("refreshTimes") || 0;
	        s < 5 ? (s++,
	        a.setCookie("refreshTimes", s, .5),
	        document.location.reload()) : e.isNeedRefresh = !0;
	        break;
	
	    case202:
	        e.isScan = !1,
	        e.isAssociationLogin = !1,
	        a.setCookie("login_frequency", 0, 2),
	        window.checkLoginPromise && (window.checkLoginPromise.abort(),
	        window.checkLoginPromise = null),
	        r()
	    }
	
	    e.code = c.code,
	    e.userAvatar = c.userAvatar,
	    a.log("get code", c.code)
	
	}
### 4.3 小结

微信网页端扫码登录时，轮询的数据返回采用的是JSONP的形式，这是为了解决跨域问题。如对JSONP不了解的，可以参考：

	http://www.52im.net/thread-1038-1-1.html
微信网页端扫码登录时，轮询采用了后台根据扫码情况阻塞前台请求，优化轮询及减少前端的无效轮询。这种技术，请详见：

	http://www.52im.net/thread-338-1-1.html


## 5、本文小结

扫码登录这个功能，现在已经不只出现有IM应用里，各种带有移动端的线上网站也都有了这个功能，所以本文中介绍的技术原理并不局限于只用于实现IM应用中的扫码登录。

另外，为了方便抓取真实的数据进行分析研究，本文中的PC端案例分析是针对的是网页端，但实际上如果你的PC端是富客户端（也就是.exe、.dmg这样的安装版），原理也是一样的，而且还不需要考虑浏览器里的跨域问题等。

阅读本文时，可能涉及到传统的Web端即时通讯技术（为了扫码登录的实时性），比如长轮询等，如果您对这些技术还不太了解的话，可以系统学习一下即时通讯网整理的有关Web端即时通讯方面的资料。


----------
本文参考链接1：[来源微信公众号](https://mp.weixin.qq.com/s/ND6vF_HAueH8GuQJzPkiBg)

本文参考链接2：[即时通讯网](http://www.52im.net/forum.php?mod=viewthread&tid=2892)