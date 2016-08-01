---
layout: default
title: Speed up the generator-jhipster
sitemap:
priority: 0.5
lastmod: 2016-05-15T22:22:00-00:00
---

# 快速生成JHipter

**警告!** 不能在NPM3+下正常工作,因为它使用symlink.

当使用JHipster生成器的时候,`npm install`命令可能需要花费数N分钟,这取决于你的网卡情况.

这个提示可以用于很多情况:

- 适用于JHipster示例,提升你的经验
- 对于团队,使用 `.yo-rc.json` 重新快速生成项目
- 适用于持续集成

## 为新项目创建一个 node_modules

创建一个包含了 `node_modules` 所有库的文件夹,然后进入文件夹:

```
mkdir jhipster-speedup
cd jhipster-speedup
```

创建 `node_modules` 文件夹:

```
mkdir -p node_modules
```

项目结构如下 :

    jhipster-speedup
    ├── node_modules


**警告!** 使用下面的命令仅当你做JHipster开发时.这将link到你的JHipster生成器项目分支:

```
npm link generator-jhipster
```

## 生成项目

创建一个包含新的JHipster项目的文件夹,然后进入文件夹:

```
mkdir jhipster
cd jhipster
```

为 `node_modules` 创建一个链接:

```
ln -s <your path>/jhipster-speedup/node_modules
```

创建一个项目然后回答JHipster的提问:

```
yo jhipster
```

第一次的时候将会话费数分钟时间.

下一次,它将会使用已经存在的 `node_modules` 文件夹,所以npm将不会去下载所有的依赖库.

**警告!** 如果你使用了特殊的依赖库,并更新了你的 `package.json` ,你需要从jhipster-speedup 复制 `node_modules` 到你的文件目录下,替代使用的链接.
