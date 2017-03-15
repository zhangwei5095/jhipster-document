---
layout: default
title: Using Oracle
permalink: /using-oracle/
redirect_from:
  - /using_oracle.html
sitemap:
    priority: 0.7
    lastmod: 2015-06-08T18:40:00-00:00
---

# <i class="fa fa-archive"></i> Using Oracle使用Oracle

使用JPA时，你可以选择使用Oracle数据库.

由于Oracle是有专利的数据库，因此我们无法为此提供和其他数据库同等的支持：

- 它的驱动也是专有的，因为我们无法把它和JHipster打包在一起。
- 我们也无法为此提供同等级别的测试，因为它无法在Mac OS X上使用（最终的JHipster的构建和测试都是在Macbook Pro上完成的）。
所以，当你在JHipster使用Oracle的时候，你需要手动的安装Oracle JDBC的驱动：
- 从Oracle官网下载驱动（ojdbc7.jar for Java 8）
- 把下载好的驱动（ojdbc7.jar）复制到你的应用下的 `lib/oracle/ojdbc/7/` 文件夹下。
- 你需要重命名一下驱动，把`ojdbc7.jar`重命名为`ojdbc-7.jar`，因为这样才能符合Maven的命名规则。

当你在JHipster使用Oracle的时候，会有以下要求：
- 实体的名称不能超过26个字符，这是因为Oracle对象的命名限制为30个字符的，还有，我们将预留剩下的4个字符，用于为已经生成的表创建主键的序列。
- 实体的属性名称也不能超过30个字符。
- Oracle的保留关键字不能用作实体和属性的名称。
