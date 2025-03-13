---
title: 用Hexo和Github搭建博客（4）：近几年更新汇总
tags: [Hexo,github]
categories: Web构建
date: 2025-03-09 05:20
---


用Hexo和Github搭建博客（4）：近几年更新汇总

### 一、nodejs新版本

已经重新安装

原来版本：node-v5.7.0-x64

新安装版本：node-v12.22.12-x86

因为是在Win7系统中，所以还不能安装更加新的版本；否则，可能运行不了。

安装后重新配置node与npm的关键目录，与设置环境变量。

### 二、Hexo的远程部署

问题：
> hexo d的错误remote: Invalid username or password.
fatal: Authentication failed for

修改Hexo 根目录下的_config.yml文件

在文件的最后，代码修改为：

    deploy:

    	type: git

    	repo: git@github.com:isunman/isunman.github.com.git 

    	branch: master

主要改动是repo这一行。

##### HTTPS 切换为 SSH 协议

既能解决HTTPS访问网络不稳定的问题，也能解决认证容易因密码变更或 2FA 导致失败等问题。

关于SSH怎么使用，SSH keys怎么生成等问题可以查看官方说明：
[https://docs.github.com/en/authentication/connecting-to-github-with-ssh](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

已亲测可用。


### 三、GitHub的分支master与main区别

GitHub 中的 master 和 main 分支本质上是同一概念的不同命名，主要区别源于命名习惯的调整和包容性考量。以下是两者的核心差异和关联：

##### 1、名称差异与历史背景
master：
这是 Git 早期默认的主分支名称，广泛用于旧项目中。其名称源自版本控制系统的传统术语，但因其与奴隶制（"master-slave"）的潜在关联，逐渐被认为不够包容。

main：
自 2020 年 10 月起，GitHub 将新仓库的默认主分支名称改为 main，以推动更具包容性的术语。其他平台（如 GitLab）也逐步采用这一变更。

##### 2、功能与用途

功能一致性：
无论是 master 还是 main，其功能完全相同，均为项目的主分支，用于存放经过测试的稳定代码，并作为其他开发分支（如 develop、feature）的合并目标。

使用场景：

master：常见于旧项目或未主动更名的仓库。

main：新项目的默认选择，尤其适用于注重包容性的团队或组织。

##### 3、底层原理

分支本质：
在 Git 中，分支仅是指向某个提交的指针，名称变更不影响其底层逻辑。无论是 master 还是 main，均通过相同机制管理代码提交、合并与推送514。

默认分支配置：

Git 允许通过 .git/config 或命令（如 git symbolic-ref）自定义默认分支名称。

GitHub 提供仓库设置界面，支持修改默认分支。

##### 4、操作差异

重命名分支：
若需将旧项目的 master 改为 main，可执行以下步骤：

切换到本地 master：git checkout master

重命名分支：git branch -m main

推送新分支：git push -u origin main

删除远程旧分支（可选）：git push origin --delete master

在 GitHub 仓库设置中更新默认分支513。

兼容性注意：
若协作项目仍使用 master，需确保本地与远程分支名称一致，避免推送冲突。

##### 5、社区与平台支持

GitHub 的默认规则：

2020 年 10 月后创建的仓库默认使用 main，旧仓库不受影响。

用户可手动修改默认分支名称，但需同步更新 CI/CD 等依赖分支名的配置813。

其他平台：
GitLab 等平台也支持类似调整，但具体实现可能略有差异。


##### 6、总结

若需统一分支命名或启动新项目，推荐使用 main 以符合现代实践。对于既有项目，可根据团队需求决定是否调整

### 四、更新博客源文件source的错误

    git push -u origin master

执行后提示

    fatal: unable to access

`'https://github.com/xxx/aaaa.git/': Empty reply from server`，这通常表示 网络通信异常 或 Git 服务器未响应。以下是分步排查和解决方案：

##### 1、检查网络连通性

测试 GitHub 访问：若返回 HTTP/2 200，说明网络正常；若超时或无响应，可能是网络被阻断。

排查代理或防火墙：如果使用代理，确保 Git 配置了正确的代理；临时关闭 VPN 或防火墙测试。

##### 2、验证仓库 URL 是否正确

检查远程仓库地址是否存在拼写错误；若 URL 错误，修正后重试。

##### 3、尝试切换协议（HTTPS ----> SSH）

改用 SSH 协议（避免 HTTPS 端口或代理问题）：

    git remote set-url origin git@github.com:用户名/仓库名.git
    git push -u origin master

若 SSH 失败，检查密钥配置：

    ssh -T git@github.com  # 验证 SSH 连接

(实际测试时，到这一步成功了）

##### 4. 清除 Git 缓存和凭据
##### 5. 检查 GitHub 服务状态
##### 6.  调整 Git 配置（临时测试）
##### 7. 检查本地 Git 版本
##### 8.  检查仓库权限和认证方式
##### 9. 使用调试模式定位问题

##### 10、 终极方案：重置远程仓库

如果仓库可重建，尝试重新初始化并推送：

    rm -rf .git
    git init
    git remote add origin https://github.com/用户名/仓库名.git
    git add .
    git commit -m "Initial commit"
    git push -u origin master

这个方案暂时不用。


### 五、以上总结

用像Hexo做分享创作的问题，需要解决后，可以流畅操作。

现在已经进入到了AI大模型时代，需要尽快学习，然后思考接入使用，总结表达分享自己的思考结果。

————————————

相关文章：

1、[用Hexo和Github搭建博客（1）：安装环境](http://isunman.com/2016/02/25/blog-by-hexo-github-step1/ "用Hexo和Github搭建博客（1）：安装环境")

2、[用Hexo和Github搭建博客（2）：部署到Github](http://isunman.com/2016/02/25/blog-by-hexo-github-step2/ "用Hexo和Github搭建博客（2）：部署到Github")

3、[用Hexo和github搭建博客（3）：Hexo介绍](http://isunman.com/2016/02/26/blog-by-hexo-github-step3/ "用Hexo和github搭建博客（3）：Hexo介绍")