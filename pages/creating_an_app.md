---
layout: default
title: Creating an application
permalink: /creating-an-app/
redirect_from:
  - /creating_an_app.html
sitemap:
    priority: 0.7
    lastmod: 2014-06-02T00:00:00-00:00
---

# <i class="fa fa-rocket"></i> 创建一个应用

_**请查看我们的 [教学视频]({{ site.url }}/video-tutorial/) 以快速创建一个应用!** _


1. [Quick start](#1)
2. [Questions asked when generating an application](#2)
3. [Command-line options](#3)
4. [Tips](#4)

## <a name="1"></a> 快速开始
首先，创建你要存放应用的目录：

`mkdir myapplication`

进入目录：

`cd myapplication/`

生成应用：

`yo jhipster`

根据需求回答相应的问题，详细的问题在 [下面部分](#2) 会提到.

当应用生成后，你可以通过 Maven  (`./mvnw` on Linux/MacOS, `mvnw.cmd` on Windows) 或者 Gradle (`./gradlew` on Linux/MacOS, `gradelw.bat` on Windows) 启动应用。 你可以前往 [Using JHipster in development]({{ site.url }}/development/) 页获取更多信息。

你可以通过  [http://localhost:8080](http://localhost:8080) 访问你的应用。

## <a name="2"></a> 当生成应用时需要回答的问题

_一些问题的改变取决于你前面的选择。例如，如果你zhiq没有选择一个SQL数据库的话，你不需要配置一个 Hibernate 缓存。

### 你想创建什么类型的应用？

你需要选择的应用依赖于你是否想选择微服务作为你的架构。关于微服务的详细描述在 [available here]({{ site.url }}/microservices-architecture/)，如果你不确定，就选择默认的 "Monolithic application"。

你可以选择：

* 一体化应用：这是一个典型的，通用的应用。它容易使用和开发，是我们默认推荐的。
* 微服务应用：采用微服务的架构，这是其中一个服务实例。
* 微服务网关：采用微服务的架构，这里一个为微服务实例提供路由以及安全保障的应用。

### 你应用的名称？

如题，输入你应用的名称。


### 你的 Java 包名 ?
你的 java 应用程序将使用此作为它的根包。这个值被存储在 Yeoman 在，下次运行生成器，它将成为默认值。当然，你可以通过提供一个新的值来覆盖它。

### 你选择哪种身份认证方式？

你可以选择：

* 基于经典的会话认证机制， 做 java web 都知道（这也是许多人使用 [Spring Security](http://docs.spring.io/spring-security/site/index.html) 的方式）。你也可以通过用这种方式来使用 Spring Social。Spring Social 可以让你使用社会化登陆（比如 Google，Facebook，Twitter）, 这个配置项由 Spring boot 提供。

*   基于 OAuth 2.0 的认证机制。（JHipster 会提供生成必要的 OAuth2 服务端代码和数据库表）。

*   采用 [JSON Web Token (JWT)](https://jwt.io/) 的认证机制。

OAuth 2.0 和 JWT 可以让你构建无状态的应用架构（他们不依赖 HTTP Session）。你可以在这里查看更多信息 information on our [securing your application]({{ site.url }}/security/) 。

### 你想使用哪种数据库？

你可以选择：

- 不使用数据 (仅支持 [微服务应用]({{ site.url }}/microservices-architecture/))
- SQL 数据库 （H2, MySQL, MariaDB, PostgreSQL, Oracle），默认 使用Spring Data JPA 来访问。
- [MongoDB]({{ site.url }}/using-mongodb/)
- [Cassandra]({{ site.url }}/using-cassandra/)

### 你要在生产环境下采用哪种数据库？
这个选项的回答会被写入生产环境下的配置文件。你可以在 `src/main/resources/config/application-prod.yml` 文件中配置他。.

如果你想使用 Oracle，你需要查看 [install the Oracle JDBC driver manually]({{ site.url }}/using-oracle/).

### 你要在开发环境下采用哪种数据库？

这个选项的回答会被写入开发环境下的配置文件。
你也可以选择：

* 在内存中运行的 H2。这种方式很简单使用，但是你的数据会丢失当你重新启动服务器。
* 数据存储在磁盘上的 H2。目前处在 BETA 测试状态（不支持 Windows），但这将最终是一个比在内存中运行更好的选择，因为你不会失去你的数据后，当应用程序重新启动后。

*   和生产环节一样的数据库，这搭建的时候会有点麻烦。但是你最好在编码完成后，在和生产环境一样的数据库上跑一下程序 。这里最好也采用 liquibase-hibernate，在  [the development guide]({{ site.url }}/development/)中有描述。

你可以在 `src/main/resources/config/application-dev.yml` 文件中配置它。

### 你想使用 Hibernate 二级缓存吗?

[Hibernate](http://hibernate.org/) 是 JHipster 提供的 JPA 的实现。处于性能考虑，我们强烈建议你是有缓存，你可以根据你的应用来调整它。你可以选择使用  [ehcache](http://ehcache.org/) (本地缓存) 或者 [Hazelcast](http://www.hazelcast.com/) (分布式缓存，用于集群环境)。

### 你想为你的应用提高搜索引擎吗？

[Elasticsearch](https://github.com/elastic/elasticsearch) 将配置实用 Spring Data Elasticsearch。你可以通过 [Elasticsearch guide]({{ site.url }}/using-elasticsearch/) 查看更多信息。

### 你想使用 HTTP sessions 集群吗？
默认情况下，JHipster 使用 HTTP session 存储 [Spring Security](http://docs.spring.io/spring-security/site/index.html) 的认证与授权信息，你可以选择存储更多的信息在 HTTP sessions 中。

如果你部署了一个集群，使用 HTTP session 将会出现一些问题，尤其是你如果不对粘滞会话(Sticky Sessions) 进行负载均衡。
如果你想在集群中复制你的 sessions，可以选择配置 [Hazelcast](http://www.hazelcast.com/)。

### 你想使用 WebSockets 吗？

你可以通过 Spring Websocket 来使用 Websockets.我们还提供了一个完整的示例来展示如何有效地使用这个框架。

### 你想使用 Maven 还是 Gradle ?
你可以通过   [Maven](http://maven.apache.org/) 或者 [Gradle](http://www.gradle.org/) 来构建你的应用。Maven 相对 Gradle 更稳定、更成熟。而 Gradle 更灵活，更容易扩展。


### 你想使用 LibSass 来预处理你的 CSS 嘛？

[Node-sass](https://www.npmjs.com/package/node-sass) 是处理 CSS 的一个很好的方案，你需要运行一个 [Gulp](http://www.gulpjs.com) 服务来自动配置它。 

### 你想通过 Angular Translate 来支持多语言嘛？
默认情况下 JHipster 提供l 优秀的国际化的支持，无论是在客户端与 [Angular Translate](https://angular-translate.github.io/) 和在服务器端。但是国际化增加系统开销，管理起来会有些复杂，所以你可以选择不安装这个功能。

### 你想使用那个测试框架？
默认情况下 JHipster 提供 java 单元/集成测试（使用Spring JUnit支持）和 JavaScript 单元测试（使用Karma.js）。这也是一个可选项。 

*   使用 Gatling 进行性能测试。
*   使用 Cucumber 模拟用户行为
*   使用 Protractor 对 AngularJS 进行集成测试

你可以在这里看到更多信息  ["Running tests" guide]({{ site.url }}/running-tests/)。

## <a name="3"></a> 命令选项

你也可以用一些可选的命令来运行 JHipster。这些选项可参考  `JHipster --help` :


以下是可选项：

* `--help` - 查看生成器的选项和用法 。
* `--skip-cache` - 不记住你之前的回答 （默认: false）。
* `--skip-install` - 不自动安装依赖 （默认: false）。
* `--skip-client` - 不生成客户端代码 （默认: false）。这和运行 `yo jhipster:server` 效果一样。
* `--skip-server` - 不生成服务端代码 （默认: false）。这和运行 `yo jhipster:client` 效果一样。
* `--skip-user-management` - 跳过生成前后端用户管理相关代码 （默认: false）
* `--i18n` - 禁用或允许客户端 i18n 国际化支持。（默认: true）。
* `--with-entities` - 重新生成现有的实体如果他们已经存在。（using their configuration in the `.jhipster` folder） （默认: false）
* `--check-install` - 检查你的安装是否正确（默认: true）。

## <a name="4"></a> Tips
如果你是一个高级用户，你可以运行我们的客户端和服务器子生成器 `yo jhipster:client --[options]` 和 `yo jhipster:server --[options]`。你可以通过在加上 `--help` 来查看所有选项。

你也可以使用 Yeoman 的命令行选项，比如 `--force` 去自动覆盖已有文件。如果你想重新生成整个应用，包括它的实体，你可以运行 `yo jhipster --force --with-entities`。