---
layout: default
title: Using DTOs
permalink: /using-dtos/
redirect_from:
  - /using_dtos.html
sitemap:
    priority: 0.7
    lastmod: 2015-05-28T23:41:00-00:00
---

# <i class="fa fa-briefcase"></i> [BETA] 使用 DTOs

__注意!__ 这是一个新的特性，处在 <b>BETA</b> 阶段。 使用它你要自己承担风险！非常欢迎提供反馈意见！

## 介绍
默认情况下，JHipster 在 REST 中使用领域对象（domain 对象，普通的 JPA 实体类）来做数据载体。这样有很多好处，其中一点就是使得代码容易看懂、使用和继承。

对于复杂的情况，你可能想在 REST 接口处使用数据传输对象，也就是 DTOs。这些对象是在领域对象的基础上构建的，专门用作在 REST 接口处。它们最大的好处是可以整合多个领域对象。

## DTOs 在 JHipster 中的运行机制

当你生成 JHipster 实体时，你也可以选择生成一个基于此实体的 DTO 对象。如果你选择了这个选项：

- 一个 DTO 将会生成，并且映射到它底层的实体。
- 它会整合多对一的映射关系，在 AngularJS 中仅使用 ID 这一字段来展示。举个例子，一个多对一的映射关系，对于 `User` 实体，它会在 DTO 中添加 `userId` 和 `userLogin`  这两个字段。
 
- 针对非映射关系的持有方，它将忽略一对多、多对多的映射关系，它们采用匹配实体的方式来运行。（在这些字段上会有 `@JsonIgnore`注解）。

- 对于多对多的映射关系，针对关系的持有方，它将从另外一个实体中获取 DTOs，并在一个 Set 中调用它们。因此，这要求另外一个实体也使用 DTos，才能正常工作。

## 使用 MapStruct 来映射 DTOs 和 entities
因为 DTOs 看起来和 entities 很相似，所以它们经常被要求来相互映射。

JHipster 选择了 [MapStruct](http://mapstruct.org/) 来解决这种情况。它是一个注解标注，在 Java 编译前运行，它会自动生成映射。

我们发现它很干净和高效，并且喜欢它不需要使用反射 （如果大量使用映射，反射会在性能上回带来很大的损失）。

## 为你的 IDE 配置 MapStruct
MapStruct 是一个标注处理器，当你的 IDE 编译你的项目时，它可以自动运行。我们发现这种方式很难用，而更喜欢通过 `mvn compile` 来直接编译项目。

如果你想配置你的IDE来使用 MapStruct，你需要在  pom.xml 添加以下依赖：


    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct-processor</artifactId>
        <version>${mapstruct.version}</version>
    </dependency>

如果你找到了一个更好的方式来使用你的 IDE 和 Maven ，请毫不犹豫的反馈给我们。

## 高级 MapStruct 的用法
MapStruct 映射是需要配置 Spring 的 Beans，支持依赖注入。一个好的方法是你可以注入一个 Repository 到 mapper 中，由此你可以从 mapper 中通过 ID 获取到这个实体。

下面是一个示例代码，获取一个` User`实体：

    @Mapper
    public abstract class CarMapper {

        @Inject
        private UserRepository userRepository;

        @Mapping(source = "user.id", target = "userId")
        @Mapping(source = "user.login", target = "userLogin")
        public abstract CarDTO carToCarDTO(Car car);

        @Mapping(source = "userId", target = "user")
        public abstract Car carDTOToCar(CarDTO carDTO);

        public User userFromId(Long id) {
            if (id == null) {
                return null;
            }
            return userRepository.findOne(id);
        }
    }

## 未来计划

在未来，如果我们发现，MapStruct 不够用，我们将会添加 DTO 的映射。最简单的解决办法是通过 JHipster 自动生成映射，不使用任何第三方库。
