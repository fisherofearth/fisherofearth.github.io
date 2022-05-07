---
title: 在github工作流中插入gitalk的clienSecret
date: 2022-05-02 13:45:56
tags: [Hexo, github, pages, gitalk, secret]
cover: https://s3.bmp.ovh/imgs/2022/05/02/5ab3bb62500e0bc9.png
---

## 前言 
美好的愿望：为Github page上的Hexo站点的 [gitalk][gitalk] 设定非公开的ClientSecret。
Gitalk 可以为Hexo站点的post增加评论功能，评论自动推到github issue，挺方便的。
[gitalk][gitalk] 的配置需要ClientSecret，如直接明文写在_config.yml文件中，**不妥!**。但是也没办法，参考[Gitalk明文使用secret安全吗](https://carl-zk.github.io/blog/2020/03/03/gitalk-%E8%BF%90%E4%BD%9C%E5%8E%9F%E7%90%86/)。
Github提供了[Encrypted secrets][secrets]功能，原以为能解决这个问题，但仍只能在静态文件上明文写出client secret，但起码主分支上看不到明文的secret了。

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
  client_id: "前面申请的ClientID" 
  client_secret: ${{ secret.CLIENT_SECRET }} # 假设你注册的secret名称是CLIENT_SECRET
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
在你的workflow配置文件`.github/workflows/deploy_pages.yml`中添加透传
``` yaml 
# .github/workflows/deploy_pages.yml.yml
jobs:
  pages:
    runs-on: ubuntu-latest
    environment: gitalk # 这是一个环境，如果你的secret 不在 github 项目的 Environment 下，则不需要配置它
    steps:
    ...
      - name: Parser secret # 透传你的secret给_config.butterfly.yml
        uses: jwsi/secret-parser@v1
        with:
          filename: _config.butterfly.yml
          secret-name: CLIENT_SECRET
          secret-value: ${{ secrets.CLIENT_SECRET }}
    ...
```

Github Action 会把 环境变量中的 `CLIENT_SECRET` 明文地传入 `_config.butterfly.yml`

##  <i class="fa fa-champagne-glasses"></i> 祝贺 
把你的site push到github上，完成部署，等待Github Action完成，如果一切顺利，可以在post下方以Github账号留言了。
如果去看page 项目的部署分支，在静态页面文件下，你将会看到明文的secret，虽然不合理，但应该“问题不大”。

## 更多阅读
- [Gitalk项目][gitalk]
- [Github 官方文档： Encrypted secrets][secrets]
- [hexo-theme-butterfly][hexo-theme-butterfly]

[gitalk]: https://github.com/gitalk/gitalk/blob/master/readme-cn.md 
[secrets]: https://docs.github.com/cn/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-an-environment 
[hexo-theme-butterfly]: https://github.com/jerryc127/hexo-theme-butterfly 