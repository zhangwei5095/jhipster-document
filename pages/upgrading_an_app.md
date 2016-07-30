---
layout: default
title: Updating an application
permalink: /upgrading-an-application/
sitemap:
    priority: 0.7
    lastmod: 2014-06-02T00:00:00-00:00
gitgraph: http://jsfiddle.net/lordlothar99/tqp9gyu3
---

# <i class="fa fa-refresh"></i> 升级应用

当新版本的 JHipster 发布时，JHipster 的 upgrade 生成器可以帮助我们把已有的应用升级到最新版本，并且不会改变原有的代码。 

这一点对我们很有帮助：

- 在已有的应用上使用最新的 JHipster 特性。

- 修复已经发现的 bug 或者获取最新的安全更新。 

- 在原有的代码的基础上，很容易的合并最新生成的代码。

_在做升级之前请仔细阅读此页，了解升级过程是如何工作的_

## 要求

对于这个子生成器，你需要安装 git ，从 [http://git-scm.com](http://git-scm.com/) 可以下载。

## 运行 upgrade 子生成器

进入应用的根目录：

`cd myapplication/`

运行下面的命令以更新：

`yo jhipster:upgrade`

如果你想即使没有新的 JHipster版本，也可以运行该子生成器，运行：

`yo jhipster:upgrade --force`

## 升级过程的图形视图

这是升级过程的一个图形视图（下面部分会有文本解释）

![GitGraph]({{ site.url }}/images/upgrade_gitgraph.png)

(这张图片来自[JSFiddle](http://jsfiddle.net/lordlothar99/tqp9gyu3/) )

请注意 `jhipster_upgrade`  分支将会被独立的创建，上面得到图片可能没有展示到这一点。

## 一步步讲解更新过程

下面的是 up-to-date 这个子生成器的运行步骤：

1. 检查是否有新的 JHipster 版本可用。(如果你加上了 `--force` 选项，则这条会跳过)。

2. 检查你的应用是否有被初始化为一个 `git` 仓库，没有的话，则会初始化，并且提交所有的变更到  master 分支。 

3. 检查并确保你的应用没有需要 commit 的修改，如果有的话这个过程会被终止。

4. 检测 `jhipster_upgrade` 分支是否存在。如果没有，则创建之，详情可以查看下面 “第一次升级执行的具体步骤” 这一小节。

5. 切换到 `jhipster_upgrade` 分支。

6. 升级到最新版本的 JHipster。

7. 清空当前项目目录。

8. 重新生成应用，通过 `yo jhipster --force --with-entities` 命令。

9. 提交生成的代码到 `jhipster_upgrade` 分支。

10. 合并 `jhipster_upgrade` 分支到原本的分支，也就是你运行 `yo jhipster:upgrade` 时候的地方。

11. 后面你就只需要解决冲突就好。

完成上面步骤，你就可以庆祝升级完成了。

## 第一次升级执行的具体步骤

在 JHipster 的 upgrade 子生成器第一执行时，为了避免删除你所修改的代码，需要执行下面的步骤：

1. 一个 `jhipster_upgrade` 分支将会独立创建。（它没有 parent）

2. 使用当前的 JHipster 版本，重新生成一个应用（注意，不是新版本，是你当前的版本）。

3. 在 `master` 分支会有一个 block-merge commit，你 `master` 分支上的代码库不会有任何修改，这仅仅是为了在 git 上记录，你的 `master` 分支更新到了当前的 JHipster 版本。

### 注意事项

不要再在 `jhipster_upgrade` 分支上提交任何东西。这个分支是专门为升级应用做准备的，当你使用 upgrade 子生成器的时，这个分支才会有新的 commit。

