---
title: Postman Interceptor 配置
date: 2015-12-06 21:28
permalink: postman-interceptor-setting
categories:
  - Tools
tags:
  - postman
  - postman-interceptor
---

> 当我们使用 Postman 发送一个请求时，可能会要求发送这个请求前先做用户认证（登录）。并且在具体请求某个 URL 时还会验证当前用户是否有权限请求这个 URL。在 Postman 中如何达到这一要求呢？

<!-- more -->

## 安装 Postman Interceptor 插件
> Interceptor 主要就是用来做请求的拦截。

* 访问 [chrome webstore][4]：  搜索 `Postman Interceptor` 然后进行安装。

    ![安装 interceptor][1]

* 配置 Interceptor

    ![setting-postman-interceptor.png-32.8kB][2]

    **注：**其中的`Filter requests`参数可以自己配置，如：`localhost:8081`表示只拦截本地的 8081 端口的请求。默认的配置`.*`配置拦截所有请求，可以使用默认配置不用修改。

## 登录系统
> Interceptor配置完后，在浏览器中正常登录被测试的系统(**这很重要**)。只需要按正常的流程登录即可。

## Postman 配置
在 Postman 中启用 Interceptor，如下图：

  ![setting-postman.png-50.6kB][3]

至此通过 Postman 发送需要登录或授权的 URL 请求时已配置完成。

  [1]: /uploads/postman-interceptor-setting/install-postman-interceptor.png
  [2]: /uploads/postman-interceptor-setting/setting-postman-interceptor.png
  [3]: /uploads/postman-interceptor-setting/setting-postman.png
  [4]: https://chrome.google.com/webstore/category/apps
