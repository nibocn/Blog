---
title: 'Git 差异化配置'
date: 2020-09-18 10:31:02
permalink: git-difference-config/
categories:
  - Tools
tags:
  - git
---

## Git Config 配置范围

Git Config 通常有三个配置文件范围，系统级：`/etc/gitconfig`，用户级：`$HOME/.gitconfig`，项目级：`.git/config`。

它们的优先级分别是：`系统级 < 用户级 < 项目级`。

如果我们需要根据不同项目配置不同的 Git 配置信息（如：用户、邮箱），一般就会在项目级去添加配置信息，如：`git config user.name=xiaoming`（配置用户），`git config user.email=xiaoming@company.com`（配置邮箱）。

但这样随之带来的问题就是如果我们有多个项目那么都需要在每个项目下面去进行这样的配置，这样显然是不方便的。在 Git 较新的版本中提供了 `include`、`includeIf`、`gitdir` 这几个关键字对一个范围的目录文件夹进行统一配置。

## 指定 Git Config 配置范围

_注：_ `gitdir` 关键字的使用要求 Git 的版本必须 `>= v2.13.0`。

<!-- more -->

在 `系统级` 或 `用户级` 的 git config 文件中添加如下内容：

`$HOME/.gitconfig` 文件

```
# 通过 gitdir 指定单独的 git 配置（git >= v2.13.0）
[includeIf "gitdir:~/Work/xxx/"]
  path = ~/Work/xxx/.gitconfig
```

_注：_ 这里的 `gitdir` 值 `~/Work/xxx/` 必须以 `/` 结尾，不然不能正常匹配 `~/Work/xxx` 目录下的所有 Git 仓库。

这里的配置含义是： 在 `~/Work/xxx` 目录下的所有 Git 仓库配置会追加 `~/Work/xxx/.gitconfig` 文件中的配置信息（覆盖用户级和系统级相同的配置信息）。

其中 `gitdir`、`path` 值可以根据你的实际情况替换。

然后我们在上面的 `~/Work/xxx/.gitconfig` 文件中对 Git 的用户和邮箱进行单独的指定：

```
[user]
    name = xiaoming
    email = xiaoming@company.com
```

这样配置完成后只要是放置在 `~/Work/xxx` 文件夹下面的 Git 项目 commit 时使用的用户就会都是 xiaoming 邮箱都是 xiaoming@company.com，通过这种方式指定 Git Config 配置就比针对每一个 Git 项目去单独配置要方便得多了。

_注：_ 通过 `gitdir` 来指定 Git Config 配置的优先级别高于 `用户级` 配置，但低于 `项目级` 配置。

## 参考资料
- https://git-scm.com/docs/git-config#_includes
