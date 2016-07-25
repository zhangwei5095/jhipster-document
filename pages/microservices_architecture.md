---
layout: default
title: Doing microservices with JHipster
permalink: /microservices-architecture/
sitemap:
    priority: 0.7
    lastmod: 2016-03-10T00:00:00-00:00
---

# <i class="fa fa-sitemap"></i> 使用JHipster开发微服务

* 点此查看 [原文地址]({{ site.url }}/microservices-architecture/#production)

## 摘要

1. [微服务架构 vs 一体化架构](#microservices_vs_monolithic)
2. [概览](#overview)
3. [JHipster 的API 网关](#gateway)
  * [使用网关进行HTTP路由](#http_routing)
  * [安全](#security)
  * [自动生成文档](#documentation)
  * [请求速率限制](#rate_limiting)
  * [访问控制策略](#acl)
4. [JHipster 的注册中心](#registry)
  * [JHipster 注册中心概览](#registry_overview)
  * [JHipster 注册中心的安全保障](#securing_registry)
  * [在JHipster 上注册你的应用](#registry_app_configuration)
5. [创建微服务应用](#microservices)
  * [为微服务应用生成实体对象](#generating_entities)
  * [使用HazelCast做分布式缓存](#hazelcast)
  * [不带数据库的微服务应用](#no_database)
6. [使用 Docker Compose](#docker_compose)
7. [使用JHispter Console 和ELK技术栈来监控服务](#monitoring)
8. [生产环境](#production)
  * [部署到 Docker Swarm](#docker_swarm)
  * [部署到 CloudFoundry](#cloudfoundry)
  * [部署到 Heroku](#heroku)

## <a name="microservices_vs_monolithic"></a> 微服务架构 vs 一体化架构

使用 JHipster 生成应用时，第一个问题就是让你选择你要的生成的应用(目前有4个选项)，但实际上你是在两种架构风格里面做选择：

- 一体化架构，用来创建单独的一个应用，包含前端 AngularJS 代码和后端 spring boot 相关代码，项目中所有代码都在一个应用中。
- 微服务架构，进行了前后端分离，优点是它可以让你很容易的控制单个应用的规模，并处理好这些应用中一些简单细小的问题。

相对来说，一体化架构是比较容易上手，官网默认推荐这个，如果是刚接触 JHipstert，建议从这个入手，熟悉后，如果项目有要求，则再选择微服务架构应用。


_下面部分则主要讲解下使用JHipster进行微服务架构。_

## <a name="overview"></a> 概览

JHipster 微服务架构的工作方式如下：

 
* [JHipster gateway](#gateway) ，是一个可以通过生成器直接生成的(在第一个问题里面选择 microservice gateway)完整应用，包含来服务端和前端，用来处理web请求。一个微服务架构里面可以同时有几个网关，如果你遵循[ Backends for Frontends pattern](https://www.thoughtworks.com/cn/insights/blog/bff-soundcloud)这种模式，当然这不是强制性的。



* [JHipster Registry](#registry) JHipster的注册中心，可以在github上 [获取](https://github.com/jhipster/jhipster-registry)，所有的微服务应用和网关都是从注册中心获取配置，所以它必须要先运行起来。
 
* JHipster的微服务应用实例，可以通过生成器直接生成(在第一个问题里面选择microservice application)，它们是提供服务的具体实现。它们是无状态的。可以同时并行运行好几个相同的实例，来处理海量的请求。

* [JHipster Console](https://github.com/jhipster/jhipster-console)，JHipster的控制台，提供了服务监控和警报的功能，基于ELK 技术栈。

在下图中，绿色的组件代表你的特定应用，蓝色组件代表的底层基础架构：

![Diagram]({{ site.url }}/images/microservices_architecture_1.png)

## <a name="gateway"></a> JHipster 的API 网关

JHipster 可以生成API gateway。 这是一个普通的JHipster 应用,在使用yo jhipster时第一个问题可以选择生成，你可以像开发一个普通应用来开发它。它在微服务架构中扮演一个入口的角色，提供了http 路由，负载均衡，api 文档、保障服务质量和安全的功能。

### <a name="http_routing"></a> 使用网关进行HTTP路由

JHipster 的网关和服务应用启动之前，需要先启动 [JHipster register](https://github.com/jhipster/jhipster-registry) 项目作为注册中心。应用在配置注册中心地址时候，只需要修改 `eureka.client.serviceUrl.defaultZone `key 对应的值 （`在 src/main/resources/config/application.yml` 文件中）。

网关将自动代理所有请求的服务，并沿用应用程序的名称，例如：当微服务应用`APP1`在注册中心注册后，可以通过`/APP1`网址来访问。

再举个例子，如果你的网关运行在`localhost:8080`，你可以通过 [http://localhost:8080/app1/rest/foos](http://localhost:8080/app1/rest/foos) 获得通过微服务APP1服务提供的的`foos`资源。

另外，JHipster中REST接口资源都是受保护的，在通过浏览器访问这些接口时，你需要带上正确JWT(在请求头的header上，下面安全章节的时候还会在提到)，或在`MicroserviceSecurityConfiguration`类中注释掉相关代码。

如果有多个相同的服务实例在同时运行，网关可以从JHipster的注册中心获取这些实例，并且：

* 使用 [Netflix Ribbon](https://github.com/Netflix/ribbon) 进行负载均衡。

* 采用 [Netflix Hystrix](https://github.com/Netflix/hystrix) 提供的熔断机制，这样可以将挂的运行实例快速安全地移除。

每个网关应用都提供了http 路由和服务实例监控的功能，在后台"管理员>网关"菜单中可以看到。 

### <a name="security"></a> 安全

#### JWT (JSON Web Token)

JWT（JSON网络令牌）是现在的一个业界标准，简单易用，可以为微服务应用提供安全保障。

JHispter使用 [JJWT library](https://github.com/jwtk/jjwt) 库，由Stormpath提供，用来实现具体的jwt。


所有的toekn都由网关生成，并传送给底层微服务应用。因为它们共享一个公共的密钥，所以微服务应用能够验证该令牌，并且使用该令牌认证用户。

这些令牌是自给自足的：它们具有认证和授权的信息，所以微服务应用不需要查询数据库或外部系统。这是点保证了微服务的可扩展性，所以很重要。

为了保证服务的安全运行，一个token必须在所有应用程序之间共享：

* 对于每个应用，其默认的token是独一无二的，并通过JHipster产生，被存储在 `.yo - rc.json` 文件

* 令牌的值可以通过修改在 `src /mian/resources/config/application.yml` 文件中的 `jhipster.security.authentication.jwt.secret` 的值来进行配置

* 一个好的实践是在生产环境和开发环境采用不同的token

#### OAuth2

此功能目前处于 **BETA** 阶段，因此它的文档还没有完成。

JHipster提供生成一个基于Srping security 的"UAA(用户账号和认证信息)"的服务 。这项服务采用Oauth2 token机制，来保证服务网关的安全。

在这基础上，服务网关利用Spring Security对jwt的支持来传递token给其它底层的微服务应用。
 
### <a name="documentation"></a> 自动生成文档

JHipster 的网关整合了swagger API，所以我们可以很方便的使用 `Swagger UI` 和 `swagger-codegen`。在管理后台的"admin> API"的菜单中，可以看到网关的API，以及所有注册了微服务的API。

通过下拉列表，可以查看有Swagger 生成的API文档，这些API都可以在线测试。测试的时候token会自动添加到Swagger UI 的接口中去，所以所有的请求都是在沙盒外进行。
 

### <a name="rate_limiting"></a> 请求速率限制

这是一个高级功能，需要建立一个Cassandra 集群（通过Docker Compose configuration 可以比较容易的搭建起来）。

网关提供限速的功能，所以REST请求的次数可以被限制：

* 通过IP地址（匿名用户）

* 用户登录（登录用户）

JHipster将使用Cassandra 集群存储的请求数据，并且对超出限制请求将发送HTTP 429 
（太多请求）错误。每个用户的默认限速为每小时10万API调用。

这是一个重要的功能，可以保护微服务不被一些特殊的的用户请求拖垮服务器。

JHipster register 作为一个管理 REST 接口资源的关卡，它对用户的安全信息拥有绝对控制权，因此它可以很容易扩展，以提供根据用户角色来进行特定速率限制。

为开启速率限制，需要在 `application-dev.yml` 或 `application-prod.yml` 中进行配置

```
jhipster:
    gateway:
        rate-limiting:
            enabled: true
```

当然 Cassandra 集群也需要搭建并配置好. 如果采用 JHipster’s Docker Compose 的配置(在 `src/main/docker` 中)，则可以正常运行。想自己手动设置群集，这里有些步骤要注意下：

* 首先要有一个可用的Cassandra集群。

* 需要配置好JHipster特定的限速表，相关的 `create_keyspace.cql` 和 `create_tables.cql` 脚本，在 `src/main/resources/config/cql` 目录下。

* 该群集必须在 `application-*.yml`文件中进行配置，其对应的键为`spring.data.cassandra`（已生成了默认配置）
* 
如果你想添加更多的规则，或修改现有规则，你需要修改RateLimitingFilter类。比如说，如果你要进行以下情况的限制：

* 降低HTTP调用的限制

* 添加每分钟或每日限制

* 删除了"管理员"用户的所有限制

### <a name="acl"></a> 访问控制策略

默认情况下所有已注册的微服务都可以通过网关访问。如果你想从通过网关排除特定的API ，你可以使用网关的访问控制过滤器来对具体的访问进行控制。在 `application-*.yml` 文件中对 `jhipster.gateway.authorized-microservices-endpoints` 进行配置，默认配置为：

```
jhipster:
    gateway:
        authorized-microservices-endpoints: # Access Control Policy, if left empty for a route, all endpoints will be accessible
            app1: /api,/v2/api-docs # recommended dev configuration
```

如果你只想微服务bar的 `/api/foo` 接口可以被访问，那么你只需要进行如下配置：


    jhipster:
        gateway:
            authorized-microservices-endpoints:
                bar: /api/foo

## <a name="registry"></a> JHipster 的注册中心

### <a name="registry_overview"></a> JHipster 注册中心概览

JHipster Registry 是一个可运行的应用，由JHipster 的团队开发。和JHipster generator一样，也是开源的，遵循Apache 2-licensed，也放在github上，[点此查看](https://github.com/jhipster/jhipster-registry)。

可以在github上clone或者下载Registry的源码，如果使用了JHipster generator，则建议使用使用和它相同tag的Registry，它的运行方式和其它的应用一样：

- 开发环境下，直接运行 `./mvnw`，它默认采用开发环境下的配置文件，Eureka Registry 将可以通过 [http://127.0.0.1:8761/](http://127.0.0.1:8761/) 进行访问。
 
- 生产环境下，使用./mvnw -Pprod打包生成可执行WAR文件。


如果想通过docker镜像来运行JHipster Registry，Docker Hub 中也有提供，在[JHipster Registry](https://hub.docker.com/r/jhipster/jhipster-registry/)，当然这个镜像是预先配置好。

* 运行 `docker-compose -f src/main/docker/jhipster-registry.yml up` 来启动JHipster Registry. 然后 Eureka Registry 就会监听你8761 端口 , 由此便可以通过 [http://127.0.0.1:8761/](http://127.0.0.1:8761/) 来访问

更多关于docker和JHipster Registry的问题，可以参见[docker Compose documentation ]({{ site.url }}/docker-compose/)


### <a name="securing_registry"></a> JHipster 注册中心的安全保障

JHipster Registry 默认是受保护的，需要使用账户和密码来登陆，默认账号密码为"admin/admin"。

微服务应用当然也是以admin的角色在Registry上进行注册，但是是通过HTTP Basic 认证。所以如果你的微服务应用不能连接到注册中心，你会收到"401 authentication error"的错误信息，因为你配置错了某些东西。


为了保障JHipster Registry 的安全：

* 你必须修改admin的密码。此密码可以在Spring boot 的 `application-*.yml` 文件中通过修改 `security.user.password` 对应的值来实现，或者你也可以新建一个  `SECURITY_USER_PASSWORD` 环境变量。在[Docker Compose sub-generator]({{ site.url }}/docker-compose/)中，用到了这个变量。

* 因为你的应用程序是通过HTTP连接到Registry，所以保障连接通道的安全性很重要，其中一个比较简单的方式是采用HTTPS。


### <a name="registry_app_configuration"></a> 在JHipster 上注册你的应用

JHipster Registry 是采用了 [Netflix Eureka server](https://github.com/Netflix/eureka) 和 [Spring Config Server](http://cloud.spring.io/spring-cloud-config/spring-cloud-config.html)。
当微服务应用和或者微服务网关启动的时候，它们会首先连接到JHipster Registry去获取配置信息。

这些配置是指spring boot 的配置，也就是 `application-*.yml`  文件。但它们存储在中央服务器上，易于管理。

当整个服务启动时，微服务应用或者网关会从Registry上获取服务的配置信息并且覆盖它们原来存储在本地的配置文件。

以下两种配置信息是可以用的：

* 开发环境下的本地配置文件（使用 dev profile），使用本地的文件系统。

* git 配置信息，用在生产环境下（使用 JHipster prod profile），存储在git 服务中。使用git可以建立tag、分支，或者回滚配置信息，这是生产环境下非常实用。

为了集中管理微服务的配置，你需要按照 `application-*.yml` 的格式创建配置文件，并放在Registry 的config 文件下。要求 **appname** 和 **profile** 你的微服务应用同名和profile对应。

举个例子，添加了一个 `gateway-prod.yml` 文件将对所有的名为gateway的应用采用生产环境下的配置文件。此外，定义在 `application[-dev|prod].yml` 配置将对所有的应用产生效果。

上面提到网关路由是通过Spring boot 来配置，它们其实也可以通过Spring Config Server 来管理。举个例子，你可以在  `v1` 分支上，将应用 `app1-v1` 路由到 `/appl` 的url上，而在 `v2` 分支，将 `app1-v2` 路由到 `/app1` 的url上。这是一种无缝升级微服务的好方式，以达到不需要停止服务就可以升级的效果。

## <a name="microservices"></a> 创建微服务应用

微服务应用实例是可以通过 JHipster 生成的，没有前端（生成的微服务网关会有前端，用到了angularJS ），它要配合着 JHipster Registry 才能正常运行。

### <a name="generating_entities"></a> 为微服务应用生成实体对象

在用 [entity sub-generator]({{ site.url }}/creating-an-entity/)为微服务应用中生成entity时，和在一体化架构应用中生成entity的方式有点不一样，因为微服务实现了前后端分离，微服务应用后台只需要提供接口，不需要前端代码，因此它也就不需要生成前端代码。

首先，在微服务的应用中生成实体，可以采用在一体化应用中生成实体对象的方法，也可以使用 [JHipster UML]({{ site.url }}/jhipster-uml/) 或者 [JDL Studio]({{ site.url }}/jdl-studio/) 来生成更加复杂的实体以及关系。当然这不会生成AngularJS代码。

然后，在微服务网关应用中，再次运行[entity sub-generator]({{ site.url }}/creating-an-entity/)，会在开始时多出一个问题，这是专门针对网关应用的：

* 有两个选项：要么像在一体化架构应用中生成实体方式那样，正常生成一个新的实体（微服务网关应用本身是一个完整的 JHipster 应用，这点上它有点像是个一体化应用），要么是利用已有的 JHipster 配置信息来生成。

* 如果选择后者，你需要输入微服务应用的路径，然后 JHipster 会产生网关上的前端代码（相当于是在网关应用中通过页面来管理微服务应用实体）。

### <a name="hazelcast"></a> 使用HazelCast做分布式缓存

如果你的应用采用了 SQL 的数据库，JHipster 针对微服务 给出了不同的，支持二级缓存的解决方案：

- JHipster 的默认微服务缓存解决方案是采用 Hazelcast
- 你也可以采用 Ehcache(一体化应用默认支持的方案) ，或者干脆不适用缓存。

默认的 Hazelcast 解决方案是支持微服务的，它可以很好的支持你拓展服务：


- 使用本地缓存，你是单个服务没有一个可以同步的缓存，可能导致数据不一致。
 
- 不使用任何缓存，随着服务的拓展增加，系统的负担都放到来数据库，这也不是个很好的方案。
 
采用 Hazelcast做缓存，需要做些特别的配置：

- 在启动时，应用程序会连接到 JHipster 注册服务中，找到和他相同的服务实例。
 - 如果使用了 `dev` profile，JHipster 将在本地主机上 `127.0.0.1` 上创建这些实例的群集 ，使用每个实例不同的端口。默认情况下， Hazelcast端口是 `你的应用程序的端口+ 5701` (所以如果你的应用程序的端口是 `8081`，Hazelcast将使用端口 `13782`)
 - 如果使用了 `prod` profile，JHipster 用它找到的所有其他节点来构建一个群集，使用Hazelcast默认的端口 (`5701`)
 
### <a name="no_database"></a> 不带数据库的微服务应用

只有微服务应用可以创建无数据库的程序。这是因为微服务应用可以很小，可以没有用户管理的代码。

一个没有数据库的微服务应用很小，可用于连接到一个遗留的后端系统。

## <a name="docker_compose"></a> 使用 Docker Compose 开发和部署

开发微服务系统，意味着你可能需要在几个不同的 services 和 databases 上同时工作，而 Docker Compose 则是一个针对此情况，管理开发，测试和生产环境的绝佳工具。

为此我们有一篇专门的文档来介绍 [Docker Compose documentation]({{ site.url }}/docker-compose#microservices) ，我们强烈建议在开发微服务架构的系统前阅读这篇文章，熟悉这方面的知识。

## <a name="monitoring"></a>使用JHispter Console 和ELK技术栈来监控服务
当你使用 docker-Compose sub-generator 的时候，会询问你是否需要为你的应用添加监控。如果选择是，它将会在你的 `docker-compose.yml` 文件下添加 JHipster-Console。当你启动系统后，就可以有通过访问 [http://localhost:5601](http://localhost:5601) 来获取系统应的日志和各种指标。更多关于监控的东西，可以参见 [monitoring documentation](http://jhipster.github.io/monitoring/)


对比一体化应用，网关和微服务监视器提供了一些额外的功能，可以帮助你有效地监控微服务集群。例如查看日志，它可以具体到日志对于的应用程序的名称，主机，端口和Eureka ServiceId ，这样可以让你追踪到具体的service。此外，JHipster Console带有默认的仪表板，让你在同一时间查看所有的服务。


## <a name="production"></a> 生产环境

### <a name="docker_swarm"></a> 部署到 Docker Swarm

Docker Swarm (Docker 集群管理工具) 底层使用的API 和Docker Machine (Docker管理工具) 是一样的，所以使用Docker Swarm 发布微服务应用和在你本机上发布是一样的。更多关于Docker和Docker Swarm的文档请参考 [Docker Compose documentation ]({{ site.url }}/docker-compose/)。

### <a name="cloudfoundry"></a> 部署到 CloudFoundry

使用 [CloudFoundry sub-generator]({{ site.url }}/cloudfoundry/) 搭建为微服务的应用原理和之前是一样的，只是你需要部署更多的应用：

* 使 sub-generator 发布你的 JHipster Registry

* 拿到JHipster Registry 的url,你需要在你的其它应用里面配置这个地址：

  * 在 `bootstrap-prod.yml` 文件中，设置 `spring.cloud.config.uri` 值为 `http://<your_jhipster_registry_url>/config/`

  * 在 `application-prod.yml` 文件中，设置 `eureka.client.serviceUrl.defaultZone` 值为 `http://<your_jhipster_registry_url>/eureka/`

* 发布你的网关应用和微服务应用

* 拓展你的应用

一个重要的点是 JHipster Registry 默认是不受保护的，而微服务应用也不应该直接通过外网访被访问到，用户只有通过网关才能访问到你的服务。针对此问题有两种解决方案：

* 通过使用特定的路由来保护你的的 Cloud Foundry。


* 全部应用都使用 Https，并通过使用 Spring Security 的 basic authentication 来保护你的 JHipster Registry。

### <a name="heroku"></a>部署到 Heroku

使用 [Heroku sub-generator]({{ site.url }}/cloudfoundry/) 的原理和之前也是是一样的，只是你需要部署更多的应用：


通过下面的按钮可以在 Heroku 上一键部署 JHipster Registry：

[![Deploy to Heroku](https://camo.githubusercontent.com/c0824806f5221ebb7d25e559568582dd39dd1170/68747470733a2f2f7777772e6865726f6b7563646e2e636f6d2f6465706c6f792f627574746f6e2e706e67)](https://dashboard-preview.heroku.com/new?&template=https%3A%2F%2Fgithub.com%2Fjhipster%2Fjhipster-registry)

为了保障注册中心的安全，请参考 [Heroku sub-generator documentation]({{ site.url }}/heroku/) 

在获取注册中心的地址后，你的全部应用都要在 application-prod.yml 中配置这个地址：

```
eureka:
    instance:
        hostname: <your_jhipster_registry_url>.herokuapp.com
        non-secure-port: 80
        prefer-ip-address: false
```

配置好后你就可以部署你的网关应用和微服务应用。使用 Heroku sub-generator 会询问你 JHipster Registry 的地址，这个可以让你的应用程序直接到 Spring Cloud Config server 上获取它们的配置。


**注意** 以上的配置是通过http协议，但是在生产环境上，建议使用HTTPS来连接到你的JHipster Registry。因为管理员的密码是通过HTTP来进行传输的，所以极力建议通过HTTPS来加密通信通道。