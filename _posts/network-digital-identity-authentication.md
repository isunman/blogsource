---
title: 网络数字身份认证术
tags: [HTTP,API]
categories: Web构建
date: 2023-05-16 20:15
---

这篇文章是[《HTTP API 认证授权术》](https://coolshell.cn/articles/19395.html)的姊妹篇，在那篇文章中，主要介绍了 HTTP API 认证和授权技术中用到的 HTTP Basic, Digest Access, HMAC, OAuth, JWT 等各种方式，主要是 API 上用到的一些技术，这篇文章主要想说的是另一个话题——身份认证。也就是说，怎么确认这个数据就是这个人发出来的？


## 用户密码
要解决这个问题，我们先来看一个最简单的解——使用密码，通常来说，在网络上要证明一个人的身份的话，都需要这个人的一些私密而唯一的东西。比如，像密码这样的东西，很多地方，只要你提供了你的用户名+密码，就可以确定这个人是你（注明：关于密码管理，强密码设定，密码泄漏，密码破解以及密码哄骗不在这篇文章的话题中），也就是说，这个密码是非常私密的事，我们可以假设，这个事全世界只能有当事人一个人知道，所以，当事人得供正确的密码，我们就可以认证这个人了。

为了加强密码的安全程度，一般会使用 2FA（Two-factor authentication）或 MFA（Multi-factor authentication），双因认证或多因认证，这需要用户提供一个唯一的可信设备，比如用户的手机，然后通过验证手机短信，或是像 Google Authenticator  这样的动态口令来完成。这样的安全级别已经算是比较高了。如果能够再加上经常性的变更密码，那么安全级别就更好了。


另外，一些公司还使用了生物密码来进行用户的身份验证，比如人脸识别。但是，我个人觉得人脸识别或是生物识别是比较糟糕的方式，因为：

- 目前能被验证的生物信息（如人脸和指纹）太容易被别人获得和伪造了。
- 这样东西不能被变更和吊销，密码可以被吊销和重置，人脸则不能。

## 密钥对和证书
密码可以解决身证认证的问题有很多问题，最重要的一个问题就是，你要把你的密码提供给对方，对方才能验证你的身份。你不可能把你的密码提供给全世界的人吧，这样的话，全世界的人都有你的密码了，那么任何人都能变成你了。所以，用户密码这个事只能存在于权威机构和普通用户之间，不能存在于普遍应用中。所以，这里需要使用更好的解决方案。

使用 ECC（Elliptic-Curve Cryptography）椭圆曲线密码术，可以通过一个“密钥对”进行非对称加密。这种技术，在对信息进行加密和解密时，使用两个不同的密钥，其中一个用来做加密，另一个做解密。这样一来，我们就可以把其中一个密钥公布出去，称之为公钥，另一个密钥私密地保管好，称之为私钥。

比如，我用我的私钥加密信息，然后，我把这个私钥所配对的公钥发布给所有人，大家都用公钥解密信息，不用我的公钥你解密不了这个信息。这样一来，就可以保证这个信息是我发出来的，不但保证了信息安全，还完成了身份认证。

![](https://coolshell.cn/wp-content/uploads/2022/01/key.pair_-768x293.png)

这样的现实案例一般用于网站，也就是用户得要知道我访问的这个网站是真实的，不是别人做的。因为 DNS 很容易被 hack，你连上一个不可信的网络，这个网络里的 DNS 把这个网站的 IP 地址解析成什么 就是什么了。但是有了这个加密的机制后，网站把自己的信息加密后连同公钥给到访问者，访问解密后就知道是不是这个网站了。

但是，这里还是会有一个很严重的问题，那就是中间人攻击。如下图所示：

![](https://coolshell.cn/wp-content/uploads/2022/01/key.pair_-768x293.png)

中间人 Chad 把自己伪装成 Bob 向 Alice 要信息，然后，再伪装成 Alice 对 Bob 说，这就是 Alice 的公钥，于是 Bob 也无法验证是不是 Alice 的公钥，因为公钥里就是一堆乱七八糟的数据，我们完全不能分辨哪个公钥属于 Alice 的。试想，如果我们收到声称属于银行的密钥。我们怎么知道它确实属于你的银行？

这里的答案就是**使用数字证书**。证书跟我们的身份证非常类似，其需要一个可信机构来颁发和验证的。这个证书机构 CA（Certificate Authority）是一个是大家都相信的权威机构，他用他的人品保证（当然一般会被严格管理和审计），CA 机构同样使用这样的非对称加密的技术来完成颁发和验证的事。下图展示了这一过程。

![](https://coolshell.cn/wp-content/uploads/2022/01/certificate-768x399.png)

说明一下上面这个图：

1. 为了解决公钥认证的问题的，我们需要一个权威的CA 机构。
2. Alice 把自己的信息（姓名、组织，地址，电邮，网址等）和自己的公钥打包成一个 CSR 的文件，发给 CA 机构，
3. CA 机构会来找 Alice 做物理世界的认证，如果通过后，就会用自己的机构私钥，把CSR 变成一个签名证书。
4. Bob 同学拿到 Alice 的证书，用 CA 机构的公钥解密后，得到 Alice 的公钥
5. 后面就可以签证 信息是否来自 Alice 了。

是的，这个过程就是在“套娃”，这种证书机构还可以给下级的证书机构发证，于是就会一层套一层地，形成一个证书链，顶层的叫根证书，你得绝对信任之。对于验证证书真实性的客户端，它需要能够验证链中所有 CA 的签名，这意味着客户端需要访问链中所有 CA 的证书。

## 证书生成过程演示
并不是所有的场景都需要向这些大型的 CA 机构申请公钥证书，在任何一个企业，组织或是团体内都可以自己形这样的“小王国”，也就是说，你可以自行生成这样的证书，只需要你自己保证自己的生成证书的私钥的安全，以及不需要扩散到整个互联网。下面，我们用 openssl命令来演示这个过程。

1）生成 CA 的证书（公钥） ca.crt 和私钥 ca.key

    openssl req -newkey rsa:2048 \
    -new -nodes -x509 \
    -days 365 \
    -out ca.crt \
    -keyout ca.key \
    -subj "/C=SO/ST=Earth/L=Mountain/O=CoolShell/OU=HQ/CN=localhost"

2)  生成 alice 的私钥

    openssl genrsa -out alice.key 2048

3）生成 Alice 的 CSR – Certificate Signing Request

    openssl req -new -key alice.key 365 -out alice.csr \
    -subj "/C=CN/ST=Beijing/L=Haidian/O=CoolShell/OU=Test/CN=localhost.alice"

4）使用 CA 给 Alice 签名证书

    openssl x509  -req -in alice.csr \
    -extfile <(printf "subjectAltName=DNS:localhost.alice") \ 
    -CA ca.crt -CAkey ca.key  \
    -days 365 -sha256 -CAcreateserial \
    -out alice.crt

##双向认证 mTLS
上面，我们说的基本上都是单向认证，大量的场景都是确保用户方访问的是真正的服务方，如：银行，电商网站，等。这样可以保证用户不会被钓鱼网站或是中间人攻击。但是，很多时候，我们也是需要双向认证的。下面是一个典型的场景——微信支付和商户间交互

- 用户到商家那边买东西，商家要求用户进行支付。
- 用户选择了微信支付，于是，界面从商户侧切到了微信侧
- 微信那边支付完成后，商户这边收到微信那边支付完成的通知，于是开始发货。

这个过程中有件事非常重要——就是微信通知商户支付完成的时候。

- 微信得确保通知到的就是用户所支付商户，而不是别个。
- 商户也得要能确认，来通知我的就是微信，不是别人。

一般来说，微信会给商户一个 AppID和一个 AppSerct，用这个来确保是我认证过的商户来调用我，然后，需要商户在自己的系统里填一个回调的 URL，并通过平台设置的 key来做 MD5/HMAC的签名来确保是官方的回调。这都是在《HTTP API 认证授权术》中提到过的技术，是相对传统的技术。

如今，**mTLS**是确保云原生应用程序中服务之间的通信安全的首选协议。 也就是双向认证。

传统的 TLS 认证过程是：

1. 客户端连接到服务器
2. 服务器提供其 TLS 证书
3. 客户端验证服务器的证书
4. 客户端和服务器通过加密的 TLS 连接交换信息

在 mTLS 中，客户端和服务器都有一个证书，双方都使用他们的公钥/私钥对进行身份验证。与常规 TLS 相比，mTLS 中有额外的步骤来验证双方（以粗体显示的额外步骤）：

1. 客户端连接到服务器
2. 服务器提供其 TLS 证书
3. 客户端验证服务器的证书
4. **客户端出示其 TLS 证书**
5. **服务器验证客户端的证书**
6. **服务器授予访问权限**
7. 客户端和服务器通过加密的 TLS 连接交换信息

mTLS 需要“根”TLS 证书；这我们自己来完成证书颁发机构的职责。授权客户端和服务器使用的证书必须与此根证书相对应。根证书是自签名的，这意味着我们需要自己创建它。（注：此方法不适用于公共 Internet 上的单向 TLS，因为外部证书颁发机构必须颁发这些证书）

那么，为什么整个互联网上都用了 TLS 了，为什么 不升级一下使用 mTLS？这里有两方面的原因：

- 公共互联网上要解决的问题是：A) 确保用户访问到的是正确的网站，而不是钓鱼网站。B）网站传输的内容是安全和私密且不会被篡改的。
- 将 TLS 证书分发到所有最终用户设备将非常困难。生成、管理和验证为此所需的数十亿个证书几乎是不可能的任务。

在较小的范围内，mTLS 对于单个组织非常有用且非常实用，尤其是当这些组织采用零信任方法来确保网络安全时。由于默认情况下零信任方法不信任任何用户、设备或请求，因此组织必须能够在每次尝试访问网络中的任何点时对每个用户、设备和请求进行身份验证。mTLS 通过对用户进行身份验证和设备验证来帮助实现这一目标。

关于 mTLS，这里有一个我用 Golang 写的示例 – https://github.com/haoel/mTLS，大家可以参考一下。

P.S. 本文图版中的卡司来自安全圈的标准 Cast，参看 Alice and Bob。

（全文完）

原文链接：[《网络数字身份认证术》](https://coolshell.cn/articles/21708.html)