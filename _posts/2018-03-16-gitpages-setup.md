---
layout: post
title: "Git Pages构建个人主页"
author: "Yitong Huang"
---

### 创建git pages

1. 新建repo，仓库名为` <username>.github.io `，其中username必须与github用户名完全相同。

2. 本地clone仓库，创建index.html。` echo "hello world" > index.html `

3. 将修改push到残酷仓库。

4. 浏览器中输入` <username>.github.io `验证。

### 关联个人域名

1. 申请个人域名，个人在万网上申请。

2. 在仓库根目录创建文件CNAME，内容为个人域名。` echo "xxx.com > CNAME" `

3. 将修改push到远程仓库。

4. 配置dns解析，增加CNAME记录，记录值为` <username>.github.io `。

 ![Alt text](/img/dns_cname.png)

5. 过几分钟后浏览器中输入` xxx.com `验证.

### 配置jekyll

### 使用gitment评论



