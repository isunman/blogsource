---
title: 微信小程序客服消息
tags: [微信,第三方API]
categories: 技术日记
---

### 微信小程序消息服务接入步骤

1、填写服务器配置；<br>
2、验证服务器地址的有效性；<br>
3、依据接口文档实现业务逻辑。

### 客服消息之接收消息和事件

1、消息加密方式 与 数据格式（默认使用 明文模式与XML）。<br>
2、有加密的方式必须使用数据加密与解密算法，且与服务器配置统一。

### 客服消息之发送客服消息

1、发送消息格式固定为JSON格式。<br>
2、支持4种消息类型：**文本**，**图片**，**图文链接**，**小程序卡片**。<br>
3、touser等于发送消息的FromUsername，即微信用户的openid。<br>
4、发送消息时需要accesstoken，则针对小程序APPID和APPSECRET成对使用，且缓存accesstoken 在服务器，随用随取。此处需要注意，同一个小程序APPID多处需要accesstoken时，最好可以统一调用同一个缓存数据。

### 注意事项
1、注意客服消息的收发是异步的，调用不同接口，并无直接关系。

2、若使用加密方式，开发者在接收消息和事件时，都需要进行消息加解密（某些事件可能需要回复，回复时也需要先进行加密）。但是，通过API主动调用接口（包括调用客服消息接口发消息）时，不需要进行加密。

### 参考资料

1、[接入微信小程序消息服务](https://developers.weixin.qq.com/miniprogram/dev/api/custommsg/callback_help.html)

2、[接收消息和事件](https://developers.weixin.qq.com/miniprogram/dev/api/custommsg/receive.html)

3、[发送客服消息](https://developers.weixin.qq.com/miniprogram/dev/api/custommsg/conversation.html)

