---
layout: default
title: Managing relationships
permalink: /managing-relationships/
redirect_from:
  - /managing_relationships.html
sitemap:
    priority: 0.7
    lastmod: 2016-03-26T18:40:00-00:00
---

# <i class="fa fa-sitemap"></i> 管理实体间映射关系

如果你在使用JPA，可以通过 [entity sub-generator]({{ site.url }}/creating-an-entity/) 创建实体之间映射关系。
 

## 介绍 
注意只有当你使用了 JPA 才可以在实体间生成映射关系。如果你选择了 [Cassandra]({{ site.url }}/using-cassandra/) 或者 [MongoDB]({{ site.url }}/using-mongodb/)，则不会起作用。

映射关系只在两个实体间起作用，JHipster 将生成如下的代码：

- 使用 JPA 管理生成实体的映射关系的代码。
- 创建 Liquibase 的修改日志，以便在数据库中体现出它们映射关系。
- 生成 AngularJS 前端代码，以便在用户界面中以图形方式管理这种映射关系。


## JHipster UML 和 JDL Studio
这一章节描述如何使用标准的命令行界面创建实体映射关系。不过如果你想创建大量的实体和映射关系，你可能会喜欢通过图形工具来实现。

针对这种情况，有两个可供选择的工具：

- [JHipster UML]({{ site.url }}/jhipster-uml/)，可以让你使用 UML 编辑器。

- [JDL Studio]({{ site.url }}/jdl-studio/)，我们的在线工具，通过使用我们的特定领域语言 [JDL](https://jhipster.github.io/jdl/) 来创建实体间映射关系。


你可以使用 `import-jdl` 子生成器，通过读取 JDL 文件，来生成实体和映射关系，命令为 `yo jhipster:import-jdl your-jdl-file.jh`。


## 可能的映射关系种类

因为我们使用来 JPA ，所以普通的一对多、多对一，多对多和一对一的映射关系是可以生成的：

1. [双向一对多的映射关系](#1)
2. [单向多对一的映射关系](#2)
3. [单向一对多的映射关系](#3)
4. [在两个实体间存在两个一对多的映射关系](#4)
5. [一个多对多的映射关系](#5)
6. [双向一对一的映射关系](#6)
7. [单向的一对一映射关系](#7)


_关于 `User` 实体的小提示_

注意 `User` 这个实体，JHipster 是经过特殊处理的。假设你要进行 `多对一` 的映射（例如 `Car` 和 `User` 可能存在多对一的映射关系）。这样在实体对应的 repository 中会生成一个特殊的查询（如：在CarRepository 中会生成 `List<Car> findByUserIsCurrentUser();`），这个查询仅筛选出当前用户拥有的 `Car`。在生成前端UI中，在 `Car` 对应的下拉选择项里面你可以选择一个 `User`。

## <a name="1"></a> 双向一对多映射关系
举个例子，`Owner` 和 `Car` 这个两个实体。一个车主可以有多辆车，而一辆车只有一个车主。
 
所以这是一个双向一对多映射关系，即：

    Owner (1) <-----> (*) Car

我们需要先创建 `Owner` 实体。下面仅仅列出 JHipster 将询问的映射关系的相关问题：

    yo jhipster:entity Owner
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Car
    ? What is the name of the relationship? car
    ? What is the type of the relationship? one-to-many
    ? What is the name of this relationship in the other entity? owner

注意这里我们选择默认映射关系名称和实体的一样。

现在我们生成实体 `Car`，同上仅列出与生成映射关系相关的问题：

    yo jhipster:entity Car
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Owner
    ? What is the name of the relationship? owner
    ? What is the type of the relationship? many-to-one
    ? When you display this relationship with AngularJS, which field from 'Owner' do you want to use? id

你同样可以通过使用下面的 JDL 来实现：

    entity Owner
    entity Car

    relationship OneToMany {
      Owner{car} to Car{owner}
    }

这样就完成了在两个实体间，生成双向一对多的映射关系。在新生成的 AngularJS 前端代码所对应的UI界面，会有一个 `Car` 的下拉选项让你选择 `Owner`。


## <a name="2"></a> 单向多对一关系 

在前面的例子描述了一个双向的关系，通过一个 `Car` 的实例，你可以找到 `Owner`，通过一个 `Owner` 的实例，你可以得到他所有的 `Car`。

而一个单向的多对一关系，意味着汽车知道他们的车主，但是反过来就不行。

    Owner (1) <----- (*) Car

你生成这样的映射关系，会有两个原因：

- 从业务的角度来看，你要用这种方式来使用你的实体类，是因为你并不想去生成一个没有意义的API。

- 从性能的角度来考虑，你这样使用 `Owner`，可以为系统带来性能上的提升。（因为它不必管理汽车的集合）。

在这个案例中，你同样需要先创建 `Owner` 实体，生成的关系如下：

    yo jhipster:entity Owner
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? No

然后是 `Car` 实体，和之前例子一样：

    yo jhipster:entity Car
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Owner
    ? What is the name of the relationship? owner
    ? What is the type of the relationship? many-to-one
    ? When you display this relationship with AngularJS, which field from 'Owner' do you want to use? id

这里的运行方式和前面的例子一样，但是你不能通过 `Owner` 对象来添加或者移除汽车。在新生成的 AngularJS 前端代码所对应的UI界面，会有一个 `Car` 的下拉选项让你选择 `Owner`。这是对应的 JDL :

    entity Owner
    entity Car

    relationship ManyToOne {
      Car{owner} to Owner
    }


## <a name="3"></a> 单向一对多的映射关系
单向的一对多映射关系意味着一个 `Owner` 实例有一个 car 集合，但反过来就不行，这和之前的例子相反：


    Owner (1) -----> (*) Car

JHipster 目前默认不提供这个映射关系，点击 [#1569](https://github.com/jhipster/generator-jhipster/issues/1569) 查看更多信息。

针对此情况，有两种解决方式：

- 做一个双向映射，并不修改代码，这是我们推荐的方法，因为它是简单得多
 
- 做一个双向映射，然后修改它，把它转化成一个单向映射：
    - 移除在 `@OneToMany` 注解上的 "mappedBy" 属性
    - 生成需要的关联表，你可以通过`mvn liquibase:diff` 来生成，详情请查看  [documentation about using Liquibase diff]({{ site.url }}/development/)

这个是不支持 JDL 。

## <a name="4"></a> 在两个实体间有两种一对多的关系 
在这个例子中，一个`Person` 实例可能是多辆车的所有者，它也可能只是多辆车的司机：

    Person (1) <---owns-----> (*) Car
    Person (1) <---drives---> (*) Car

在这种情况下我们需要自定义映射关系名称，在之前的例子我们都是使用默认的值。
首先我们生成 `Person` 实体类，它和 `Car` 间存在两种一对多的映射关系：

    yo jhipster:entity Person
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Car
    ? What is the name of the relationship? ownedCar
    ? What is the type of the relationship? one-to-many
    ? What is the name of this relationship in the other entity? owner
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Car
    ? What is the name of the relationship? drivedCar
    ? What is the type of the relationship? one-to-many
    ? What is the name of this relationship in the other entity? driver

然后我们生成 `Car` 实体类，其中映射关系名要和在 `Person` 中声明的要一致：

    yo jhipster:entity Car
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Person
    ? What is the name of the relationship? owner
    ? What is the type of the relationship? many-to-one
    ? When you display this relationship with AngularJS, which field from 'Person' do you want to use? id
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Person
    ? What is the name of the relationship? driver
    ? What is the type of the relationship? many-to-one
    ? When you display this relationship with AngularJS, which field from 'Person' do you want to use? id

当然他也可以通过 JDL 来实现：

    entity Person
    entity Car

    relationship OneToMany {
      Person{ownedCar} to Car{owner}
    }

    relationship OneToMany {
      Person{drivedCar} to Car{driver}
    }

现在 `Car` 实体有一个司机和一个所有者，他们都是 `Person` 类的实例。在生成的前端界面中，`Car` 对应的下拉菜单项中会有 `driver` 和 `ower` 两个选项。

## <a name="5"></a> 多对多的映射关系
一个 `Driver` 可以有多辆车，而 `Car` 也可以有多个司机。

    Driver (*) <-----> (*) Car


在数据库的层面上，这意味着在 `Driver` 表和 `Car` 表间有一个关联表。

对 JPA 来说，这两个实体都可以管理它们间的映射关系。在我们下面的这个案例中，用 `Car` 作为演示主体，可以添加或者移除它的 `driver` 。

我们先生成非映射关系持有者 `Driver`，使用多对多的映射关系：

    yo jhipster:entity Driver
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Car
    ? What is the name of the relationship? car
    ? What is the type of the relationship? many-to-many
    ? Is this entity the owner of the relationship? No
    ? What is the name of this relationship in the other entity? driver

然后生成 `Car` 作为映射关系持有方：

    yo jhipster:entity Car
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Driver
    ? What is the name of the relationship? driver
    ? What is the type of the relationship? many-to-many
    ? Is this entity the owner of the relationship? Yes
    ? When you display this relationship with AngularJS, which field from 'Driver' do you want to use? id

当然这也可以通过下面的 JDL 来实现:

    entity Driver
    entity Car

    relationship ManyToMany {
      Car{driver} to Driver{car}
    }

以上，你可以完成在两个实体多对多的映射关系。生成的 AngularJS 前端代码所对应的UI界面，会有一个多选择项的下拉框，让你通过 `Car` 选择多个 `Driver`，因为这里 `Car` 是关系的持有者。

## <a name="6"></a> 双向一对一的映射关系
按照我们这个例子，一个一对一的映射关系意味着一个 `Driver` 只能有一辆 `Car`，一个 `Car` 也只能有一个 `Driver`。

    Driver (1) <-----> (1) Car

让我们选择 `Driver` 作为非关系持有者：

    yo jhipster:entity Driver
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Car
    ? What is the name of the relationship? car
    ? What is the type of the relationship? one-to-one
    ? Is this entity the owner of the relationship? No
    ? What is the name of this relationship in the other entity? driver

`Car` 作为关系持有者：

    yo jhipster:entity Car
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Driver
    ? What is the name of the relationship? driver
    ? What is the type of the relationship? one-to-one
    ? Is this entity the owner of the relationship? Yes
    ? What is the name of this relationship in the other entity? car
    ? When you display this relationship with AngularJS, which field from 'Driver' do you want to use? id

同样上面的效果也可以通过下面的 JDL 来实现：

    entity Driver
    entity Car

    relationship OneToOne {
      Car{driver} to Driver{car}
    }

以上，你可以完成在两个实体一对一的映射关系。生成的前端UI，你可以通过 `Car` 选择 `Driver`，因为 `Car` 是关系持有者。


## <a name="7"></a> 单向的一对一映射关系
单向的一对一映射关系意味着通过 `citizen` 实例可以得到它的通行证 `passport`，但是通过 `passport` 实例无法得到 `citizen`

    Citizen (1) -----> (1) Passport

首先生成 `Passport` 实体作为非关系的持有者：

    yo jhipster:entity Passport
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? No

然后生成 `Citizen` 实体:

    yo jhipster:entity Citizen
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Passport
    ? What is the name of the relationship? passport
    ? What is the type of the relationship? one-to-one
    ? Is this entity the owner of the relationship? Yes
    ? What is the name of this relationship in the other entity? citizen
    ? When you display this relationship with AngularJS, which field from 'Passport' do you want to use? id

这样做，一个 `Citizen` 拥有一个通行证 `passport`，但并非一个通行证就对应了一个 `Citizen` 实例。在对应生成 AngularJS 的客户端界面中，你可以在 `Citizen` 下拉菜单选项中选择一个 `passport` ，因为 `Citizen` 是关系持有方。

另外这个不支持 JDL 。