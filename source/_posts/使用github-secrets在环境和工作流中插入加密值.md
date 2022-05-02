---
title: 使用github的secrets在环境和工作流中插入Gitalk的ClientSecret
date: 2022-05-02 13:45:56
tags: [Hexo, git, gitalk]
cover: https://s3.bmp.ovh/imgs/2022/05/02/5ab3bb62500e0bc9.png
---

## 前言 
为Github page上的Hexo站点的 [gitalk][gitalk] 设定非公开的ClientSecret。
Gitalk 可以为Hexo站点的post增加评论功能，评论自动推到github issue，挺好用的。
[gitalk][gitalk] 的配置需要ClientSecret，如直接明文写在_config.yml文件中，**不妥!**
Github提供了[Encrypted secrets][secrets]功能满足这个需求。

## 为[gitalk][gitalk]注册一个OAth应用 
1. 登录你的Github账户
2. [注册OAuth apps](https://github.com/settings/developers) --> [New OAthApp](https://github.com/settings/applications/new)
   
    > Application name: 随意
    > Homepage URL: Github page 的域名，如果时custom domin，就用它
    > Application description: 随意
    > Authorization callback URL: 同Homepage URL
    > Enable Device Flow: 不选

在[OAuth apps](https://github.com/settings/developers)点击进入你注册好的App, 如下创建secret。至此，你拿到了 **ClientID** 和 **ClientSecret**
{% asset_img OauthApps.png 创建OAuth secret %}



## 创建仓库的Secret
{% tabs create-repository-secret %}
<!-- tab 从Environment -->
{% asset_img createSecret1.png 创建环境 %}
{% asset_img createSecret2.png 创建secret %}
{% asset_img createSecret3.png 粘贴上一步拿到的 **ClientSecret** %}
{% asset_img createSecret4.png 创建secret %}
<!-- endtab -->

<!-- tab 直接创建Secret-->
{% asset_img createSecret5.png 直接创建secret %}
{% asset_img createSecret3.png 粘贴上一步拿到的 **ClientSecret** %}
<!-- endtab -->

{% endtabs %}

## 配置Gitalk
进入你的hexo本地工程 
编辑主题配置文件 _config.butterfly.yml (此处以[hexo-theme-butterfly][hexo-theme-butterfly]主题为例）
``` yaml
gitalk: 
  client_id: "面申请的ClientID" 
  # client_secret:  # 这个项不要，你会从github secrets自动插入
  repo: "你的Github page域名"
  owner: "你的Github 用户名"
  admin: "你的Github 用户名"
  option:
```
{% note info  %}
详细设置请阅读 [gitalk][gitalk]*
{% endnote %}

{% note warning %}
**注意：别忘了选上选上Gitalk**
``` yaml
comments:
  use: Gitalk
```
参考[教程](https://butterfly.js.org/posts/ceeb73f/)
{% endnote %}

## 为工作流添加环境变量
新建一个 `你的hexo本地工程目录/source/env.yml` 添加环境变量
``` yaml
env:
  gitalk: 
    client_secret: ${{ gitalk.CLIENT_SECRET }} 
```
{% asset_img env.yml.png env.yml的解释 %}
github自动部署的Action流程中会把这个项隐式插入到网页静态文件中。

##  <i class="fa fa-champagne-glasses"></i> 祝贺 
`hexo deploy` 让你的代码自动完成部署，等待Github Action完成，如果一切顺利，可以在post下方以Github账号留言了。

## 更多阅读
- [Gitalk项目][gitalk]
- [Github 官方文档： Encrypted secrets][secrets]
- [hexo-theme-butterfly][hexo-theme-butterfly]

[gitalk]: https://github.com/gitalk/gitalk/blob/master/readme-cn.md 
[secrets]: https://docs.github.com/cn/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-an-environment 
[hexo-theme-butterfly]: https://github.com/jerryc127/hexo-theme-butterfly 