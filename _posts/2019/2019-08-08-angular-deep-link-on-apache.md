---
layout:     post
title:      Apache上部署Angular的深链接问题
no-post-nav: true
category: it
tags: [it]
keywords: Angular, Apache, Deep link
excerpt: 今天解决了一个Angular在Apache上部署时的深链接问题
---

今天解决了一个Angular在Apache上部署时的深链接问题。

## 问题描述

Angular 8支持两种路由策略：HashLocationStrategy（例如http://www.example.org/#/video/1）和PathLocationStrategy（http://www.example.org/video/1）。当使用PathLocationStrategy时，用户从运行中的app导航到这个URL时一切正常，这是因为Angular router会把这个URL路由到正确的页面。但是，当从地址栏中输入或者手动刷新浏览器时，Angular router没机会插手，浏览器会直接向服务器请求那个URL。Server会在收到对 http://www.example.org/的请求时返回index.html，但是会拒绝对深链接（http://www.example.org/video/1）的请求，并返回一个404 - Not Found错误。

## 解决方案

在Apache的.htaccess文件中添加一个重写规则，代码如下：

```
RewriteEngine On
# If an existing asset or directory is requested go to it as it is
RewriteCond %{DOCUMENT_ROOT}%{REQUEST_URI} -f [OR]
RewriteCond %{DOCUMENT_ROOT}%{REQUEST_URI} -d
RewriteRule ^ - [L]

# If the requested resource doesn't exist, use index.html
RewriteRule ^ /index.html
```

## 参考出处

- [https://angular.io/guide/deployment#fallback-configuration-examples](https://angular.io/guide/deployment#fallback-configuration-examples)
- [http://codedumpster.nithinbiliya.com/deep-linking-issue-angular-application-tomcat-server/](http://codedumpster.nithinbiliya.com/deep-linking-issue-angular-application-tomcat-server/)
