---
layout: post
title: "Git Pages构建个人博客"
author: "Yitong Huang"
---

使用Git pages + jekyll构建个人博客；使用gitment添加评论功能；关联个人域名。
<!--more-->

## 一、创建git pages

1. 新建repo，仓库名为` <username>.github.io `，其中username必须与github用户名完全相同。

2. 本地clone仓库，创建index.html。` echo "hello world" > index.html `

3. 将修改push到残酷仓库。

4. 浏览器中输入` <username>.github.io `验证。

## 二、关联个人域名

1. 申请个人域名，个人在万网上申请。

2. 在仓库根目录创建文件CNAME，内容为个人域名。` echo "xxx.com > CNAME" `

3. 将修改push到远程仓库。

4. 配置dns解析，增加CNAME记录，记录值为` <username>.github.io `。

    ![dns_cname](/img/dns_cname.png)

5. 过几分钟后浏览器中输入` xxx.com `验证.

## 三、配置jekyll

1. mac安装jekyll：参考[官方文档](https://jekyllrb.com/docs/installation/)

    ```
    $ gem install bundle
    $ gem install jekyll
    ```
2. jekyll目录结构

    ```
    .
    +-- _config.yml
    +-- _drafts
    +-- _includes
    |   +-- footer.html
    |   +-- header.html
    +-- _layouts
    |   +-- default.html
    |   +-- post.html
    +-- _posts
    |   +-- 2018-03-16-gitpages-setup.md
    +-- _data
    |   +--members.yml
    +-- _site
    +-- index.html
    ```

3. 添加文章

    项目根目录下有\_post文件夹，所有博客文件就保存在该文件夹下，命名方式为YYYY-MM-DD-NAME.md，使用markdown语法编写。

4. 本地查看

    在项目根目录下执行``` bundle exec jekyll serve ```，jekyll根据配置文件及\_post内容生成html文件，保存在\_site目录下。同时本地启动jekyll服务器，只要在浏览器中输入``` http://127.0.0.1:4000/ ```即可访问项目首页。

## 四、使用gitment评论

1. 注册OAuth Application

    点击[https://github.com/settings/applications/new](https://github.com/settings/applications/new)注册新的应用，其中Authorization callback URL填自己的网站url：``` http://yitong0768.cc/ ```，记下Client ID和Client Secret。
    
    ![gitment_app](/img/gitment_app.png)

2. 在博文中添加评论控件

    通常我们要为所有博文添加评论功能，可以在``` _layout/post.html ```中添加以下代码：
    ```html
    <div id="gitmentContainer"></div>
    <link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
    <script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
    <script>
    var gitment = new Gitment({
        owner: 'Your GitHub username',
        repo: 'The repo to store comments',
        oauth: {
            client_id: 'Your client ID',
            client_secret: 'Your client secret',
        },
    });
    gitment.render('gitmentContainer');
    </script>
    ```
    按实际情况修改owner、repo、oauth。
    
3. 为博文初始化评论系统

    Gitment的原理是为每篇博文以其url为标识创建issue，对该博客的评论就是对该issue的评论。因此，我们需要为每篇博文初始化一下评论系统，即创建对应的issue：
    
    3-1. 第2步代码添加成功并上传后，在你的博文页出现一个评论框，并且出现以下错误信息``` Error: Comments Not Initialized ```，提示该博文的评论系统未初始化。
    
    3-2. 点击``` Login with GitHub ```，使用自己的github账号登录后，3-1中错误信息处出现Initialize Comments的按钮。

    3-3. 点击``` Initialize Comments ```按钮，完成该博文评论系统初始化。
    
    3-4. 该操作只是针对一篇博文，每上传一篇博文都需要针对该篇文章初始化一次评论。
