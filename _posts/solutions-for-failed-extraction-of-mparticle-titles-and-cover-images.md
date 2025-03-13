---
title: 微信公众号文章标题与封面图片提取失败的解决方案
tags: [HTTP,API,爬虫,大模型]
categories: Web构建
date: 2025-03-13 16:05
---


原来写的算法【对微信公众号文章的链接直接提取标题与封面图片URL】突然失效了，就在今天2025年03月13日。
目前在我们产品中使用的场景还需要继续支持，需要修复。

第一反应：在我系统中算法未更改的情况下，微信公众号文章的html页面结构有变化，导致原来的正则匹配算法失败了，也可能是微信确实优化了他们的**反爬机制算法**，例如会通过JavaScript动态加载或验证请求头来限制数据抓取。

现在AI大模型时代，直接让Poe与Claude与DeepSeek给出优化建议。

对比与实测后，DeepSeek给的建议有效可用。

### DeepSeek的建议V1——仅标题

由于微信公众号的部分公开文章在浏览器未登录时可以直接访问，但实际页面内容会通过 JavaScript动态加载 或 验证请求头 来限制数据抓取。以下是优化后的 PHP代码，通过更精准的请求头模拟和HTML解析策略实现标题提取：

#### 最终可执行代码（已验证有效）

    <?php
	function extractWechatTitle($url) {
	    $ch = curl_init();
	    
	    // 关键：模拟完整浏览器请求头（包含移动端特征）
	    $headers = [
	        'User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1',
	        'Referer: https://mp.weixin.qq.com/',
	        'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8',
	        'Accept-Language: zh-CN,zh;q=0.9',
	        'Cookie: 请替换为实际浏览器Cookie（如需要）'
	    ];
	    
	    curl_setopt_array($ch, [
	        CURLOPT_URL => $url,
	        CURLOPT_RETURNTRANSFER => true,
	        CURLOPT_SSL_VERIFYPEER => false,
	        CURLOPT_HTTPHEADER => $headers,
	        CURLOPT_FOLLOWLOCATION => true,
	        CURLOPT_ENCODING => 'gzip, deflate'  // 处理压缩响应
	    ]);
	    
	    $html = curl_exec($ch);
	    curl_close($ch);
	
	    // 调试：保存HTML用于分析
	    // file_put_contents('debug.html', $html);
	
	    // 方案一：从JSON-LD结构化数据提取（微信部分文章适用）
	    preg_match('/<script type="application\/ld\+json">(.*?)<\/script>/is', $html, $jsonMatches);
	    if (!empty($jsonMatches[1])) {
	        $jsonData = json_decode($jsonMatches[1], true);
	        if (isset($jsonData['name'])) {
	            return html_entity_decode($jsonData['name'], ENT_QUOTES);
	        }
	    }
	
	    // 方案二：从<meta>标签提取（需处理转义字符）
	    preg_match('/<meta\s+property="og:title"\s+content="([^"]+)/i', $html, $titleMatches);
	    $title = $titleMatches[1] ?? null;
	    
	    // 方案三：从<title>标签提取（兼容性更强）
	    if (empty($title)) {
	        preg_match('/<title>(.*?)<\/title>/i', $html, $titleTagMatches);
	        $title = $titleTagMatches[1] ?? '未找到标题';
	    }
	
	    return html_entity_decode($title, ENT_QUOTES);
	}
	
	// 测试URL
	$url = 'https://mp.weixin.qq.com/s/2h_Z_mwRrigGAwmc7yewKg';
	$title = extractWechatTitle($url);
	echo "提取到的标题：\n" . $title;
	?>

#### 代码关键改进点
##### 1、请求头深度伪装
- 使用 移动端User-Agent（如iPhone Safari）绕过微信的PC端反爬检测
- 添加完整的Accept和Accept-Language头模拟真实浏览器环境
- 支持Cookie注入（部分文章需要验证登录态）
##### 2、多层级标题提取策略
- 优先解析JSON-LD结构化数据（微信文章可能在此隐藏真实标题）
- 备用方案：依次尝试og:title元标签和<title>标签
- 自动处理HTML实体转义（如&amp;转回&）
##### 3、调试辅助
- 可取消注释file_put_contents保存原始HTML分析页面结构变化

#### 高频问题解决方案

##### 1、返回“请在微信客户端打开”

- 检查User-Agent是否为移动端格式
- 添加随机延迟（如sleep(rand(1,5))）

##### 2、提取到空白标题

- 检查Cookie是否有效（需手动从浏览器复制）
- 更新正则表达式匹配规则

##### 3、请求超时

- 设置CURLOPT_TIMEOUT为30秒
- 使用代理服务器（配置CURLOPT_PROXY）

总结：如需进一步优化，可结合 XPath解析器（如DOMDocument）替代正则表达式提升稳定性，或集成 IP代理池 规避访问限制。


### DeepSeek的建议V2——标题+封面图片

以下是整合标题与封面图提取的 完整PHP代码，基于微信公开文章的页面结构优化，并增加对图片URL的特殊处理（如解码&amp;转义字符）：

    <?php
	function extractWechatInfo($url) {
	    $ch = curl_init();
	    
	    // 模拟移动端浏览器请求头（关键）
	    $headers = [
	        'User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 15_0 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/15.0 Mobile/15E148 Safari/604.1',
	        'Referer: https://mp.weixin.qq.com/',
	        'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8',
	        'Accept-Language: zh-CN,zh;q=0.9',
	        'Accept-Encoding: gzip, deflate, br'
	    ];
	    
	    curl_setopt_array($ch, [
	        CURLOPT_URL => $url,
	        CURLOPT_RETURNTRANSFER => true,
	        CURLOPT_SSL_VERIFYPEER => false,
	        CURLOPT_HTTPHEADER => $headers,
	        CURLOPT_FOLLOWLOCATION => true,
	        CURLOPT_ENCODING => 'gzip, deflate'
	    ]);
	    
	    $html = curl_exec($ch);
	    $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
	    curl_close($ch);
	
	    // 调试：保存HTML（排查反爬问题）
	    // file_put_contents('wechat_page.html', $html);
	
	    $result = ['title' => '', 'cover' => ''];
	
	    // 提取标题 ---------------------------------------------------
	    // 方案1：从JSON-LD结构化数据提取
	    preg_match('/<script type="application\/ld\+json">(.*?)<\/script>/is', $html, $jsonMatches);
	    if (!empty($jsonMatches[1])) {
	        $jsonData = json_decode($jsonMatches[1], true);
	        if (isset($jsonData['name'])) {
	            $result['title'] = html_entity_decode($jsonData['name'], ENT_QUOTES);
	        }
	    }
	
	    // 方案2：从<meta>标签提取
	    if (empty($result['title'])) {
	        preg_match('/<meta\s+property="og:title"\s+content="([^"]+)/i', $html, $titleMatches);
	        $result['title'] = $titleMatches[1] ?? '';
	    }
	
	    // 方案3：从<title>标签提取
	    if (empty($result['title'])) {
	        preg_match('/<title>(.*?)<\/title>/i', $html, $titleTagMatches);
	        $result['title'] = $titleTagMatches[1] ?? '标题未找到';
	    }
	
	    // 提取封面图 -------------------------------------------------
	    // 方案1：从Open Graph协议获取
	    preg_match('/<meta\s+property="og:image"\s+content="([^"]+)/i', $html, $imageMatches);
	    $coverUrl = $imageMatches[1] ?? '';
	
	    // 方案2：从JavaScript变量获取（微信特有）
	    if (empty($coverUrl)) {
	        preg_match('/var msg_cdn_url\s*=\s*["\'](.*?)["\']/i', $html, $jsMatches);
	        $coverUrl = $jsMatches[1] ?? '';
	    }
	
	    // 处理微信图片URL转义问题（如 &amp; 转 &）
	    $coverUrl = str_replace(['&amp;', '\u0026'], '&', $coverUrl);
	    $result['cover'] = $coverUrl;
	
	    return $result;
	}
	
	// 使用示例
	$url = 'https://mp.weixin.qq.com/s/2h_Z_mwRrigGAwmc7yewKg';
	$data = extractWechatInfo($url);
	
	echo "文章标题：\n" . $data['title'] . "\n\n";
	echo "封面图地址：\n" . ($data['cover'] ?: '未找到封面图');
	?>

#### 代码增强特性说明
##### 封面图双保险提取策略

- 第一优先级：解析标准的og:image元标签
- 第二优先级：捕获微信特有的msg_cdn_url变量（常用于未登录场景）
- 特殊处理：修复微信返回的图片URL中转义字符问题（如&amp;转义符）

##### 移动端环境模拟

- 使用 iOS Safari 的User-Agent绕过PC端反爬检测
- 添加完整的Accept-*头模拟真实用户访问

##### 调试支持

- 可取消注释file_put_contents保存原始HTML分析反爬拦截情况
- 自动输出HTTP状态码辅助诊断网络问题

#### 高频问题处理指南

##### 返回默认微信Logo封面

- 检查User-Agent是否为最新移动端版本
- 尝试添加有效Cookie（需手动抓取）

##### 封面图URL无法访问

- 在代码中添加URL解码：urldecode($coverUrl)

##### HTTP 403/404错误

- 使用代理IP
- 降低请求频率（如添加sleep(1)）

##### 提取内容为空

- 检查wechat_page.html是否包含“请在微信客户端打开”提示，调整请求头策略

总结：如需处理 登录态限制，可通过浏览器开发者工具复制登录后的Cookie填入$headers数组（需定期更新）。

### 总结

1、对问题：需要更敏感，及时发现问题，及时解决问题。

同时把不同项目用到相同算法的部分都同同时更新到新的有效的算法。

2、处理问题时，需要多用AI大模型的支持。

简单的问题自己想好解决方案后，可以与大模型给到方案对比，选个更好的用；
复杂困难的问题，可以让大模型给提供具体的建议。