---
layout: default
title: Creating a module
permalink: /modules/creating-a-module/
redirect_from:
  - /creating_a_module.html
  - /modules/creating_a_module.html
sitemap:
    priority: 0.7
    lastmod: 2015-12-05T18:40:00-00:00
---

# <i class="fa fa-cube"></i> 创建一个模块（Creating a module）

A JHipster module is a Yeoman generator that is [composed](http://yeoman.io/authoring/composability.html) with a specific JHipster sub-generator to inherit some of the common functionality from JHipster. A JHipster module can also register itself to act as a hook from the JHipster generator.

JHipster模块是一个[Yeoman](http://yeoman.io/authoring/composability.html)生成器，是由一个继承了一些JHipster的公共函数特性的JHipster子生成器。

JHipster modules are listed on the [JHipster marketplace]({{ site.url }}/modules/marketplace/).

JHipster模块在[JHipster市场]({{ site.url }}/modules/marketplace/)的列表.

This allows to create third-party generators that have access to the JHipster variables and functions, and act like standard JHipster sub-generators.
The hook mechanism invokes third-party generators before and after app generation and entity generation.

这允许创建第三方生成器像标准JHipster子生成器那样访问JHipster变量和函数。第三方生成器通过Hook机制在执行应用和实体生成前后调用。

## 示例（Example）

The [JHipster Fortune module](https://github.com/jdubois/generator-jhipster-fortune) generates a "fortune cookie" page in a JHipster-generated application.

[JHipster Fortune module](https://github.com/jdubois/generator-jhipster-fortune)使用JHipster-generated应用生成了一个"fortune cookie"页。

It is our sample module that showcases how you can use JHipster's variables and functions in order to create your own generator.

这是一个展示如何在你自己的生成器中使用JHipster函数和变量场景的例子。

Or, you can use the [JHipster module generator](https://github.com/jhipster/generator-jhipster-module) to help you to initialize your module.

此外，你还可以使用[JHipster module generator](https://github.com/jhipster/generator-jhipster-module)来帮你初始化你的模块。

## JHipster模块的基本规则（Basic rules for a JHipster module）

A JHipster module:

一个JHipster模块包括：

- is an NPM package, and is a Yeoman generator.
- 是一个NPM包，也是一个Yeoman生成器。

- follows an extension of the Yeoman rules listed at [http://yeoman.io/generators/](http://yeoman.io/generators/) and can be installed, used and updated using the "yo" command. Instead of being prefixed by "generator-", it is prefixed by "generator-jhipster-", and instead of having just the "yeoman-generator" keyword, it must have 2 keywords, "yeoman-generator" and "jhipster-module".

- 遵循Yeoman的扩展规则列表[http://yeoman.io/generators/](http://yeoman.io/generators/)安装，使用“yo”命令更新，它必须包含两个关键词"yeoman-generator"和"jhipster-module"，而不是使用“generator-jhipster-”前缀代替"yeoman-generator"中的“generator-”前缀关键词.

- A JHipster module registering as a hook should not call `process.exit` in its generators being hooked.

- JHipster模块注册作为一个钩子，在生成器正在关联时不应调用`process.exit`。

## 兼容性(Composability)

A JHipster module uses the new "composability" feature from Yeoman, described at [http://yeoman.io/authoring/composability.html](http://yeoman.io/authoring/composability.html) to have access to JHipster's variables and functions.

JHipster模块使用了Yeoman的一个新的兼容性特性，这里[http://yeoman.io/authoring/composability.html](http://yeoman.io/authoring/composability.html)有访问JHipster变量和函数的描述。

For this, it composes with the "jhipster:modules" sub generator:

如下代码组成一个"jhipster:modules"的子生成器：

    compose: function() {
        this.composeWith('jhipster:modules', {
            options: {
                jhipsterVar: jhipsterVar,
                jhipsterFunc: jhipsterFunc
            }
        });
    },

## 钩子（Hooks）

JHipster will call certain hooks before and after some of its tasks, currently available and planned tasks are listed below.

JHipster将调用特定的钩子之前和之后的一些任务,现有的和计划中的任务如下。

- Post Entity creation hook
- Pre Entity creation hook [planned]
- Post App creation hook [planned]
- Pre App creation hook [planned]

- Post Entity creation hook
- 实体创建之后钩子
- Pre Entity creation hook [planned]
- 实体创建之前勾子 [计划中]
- Post App creation hook [planned]
- App创建之后钩子 [计划中]
- Pre App creation hook [planned]
- App创建之前钩子 [计划中]

[JHipster module generator](https://github.com/jhipster/generator-jhipster-module) now has option to generate this.
[JHipster module generator](https://github.com/jhipster/generator-jhipster-module) 现在有这些可选的生成器.

A JHipster module can register to act as a hook when its main generator is run by the end user. You need to call the `registerModule` method available in `jhipsterFunc` from your main (app) generator to register as hook, you need to pass the below parameters in the method as below
JHipster模块可以在主生成器运行时作为一个钩子注册。你需要在你的主生成器中调用`jhipsterFunc` 的 `registerModule` 方法注册一个钩子，你需要在方法中使用以下参数。
```javascript
jhipsterFunc.registerModule(npmPackageName, hookFor, hookType[, callbackSubGenerator[, description]])
```

- `npmPackageName` npm package name of the generator. e.g: `jhipster-generator-fortune`
- `hookFor` which Jhipster hook from above this should be registered to ( values must be `entity` or `app`)
- `hookType` where to hook this at the generator stage ( values must be `pre` or `post`)
- `callbackSubGenerator` [optional] sub generator to invoke, if this is not given the module's main (app) generator will be called, e.g: `bar` or `foo` generator
- `description` [optional] description of the generator, if this is not given we will generate a default based on the npm name given

- `npmPackageName` 生成器的npm包名. 例如: `jhipster-generator-fortune`
- `hookFor` 勾子注册的位置 ( 值必须是 `entity` 或 `app`)
- `hookType` 在生成器的那个阶段生效 ( 值必须是 `pre` 或 `post`)
- `callbackSubGenerator` [可选] 子生成器调用, 如果这个模块没有主生成器时使用, 例如: `bar` 或 `foo` 生成器
- `description` [可选] 生成器描述, 如果不指定则默认使用npm名称

### Variables available <small>(source <a href="https://github.com/jhipster/generator-jhipster/blob/master/generators/modules/index.js" target="_blank">here</a>)</small>

全局变量（Global variables）:

- `baseName`: the name of the application
- `packageName`: the Java package name
- `mainClassName`: the main Java class name
- `angularAppName`: the AngularJS application name
- `packageFolder`: the Java package folder
- `javaDir`: the directory for the Java application, including the package folders
- `resourceDir`: the directory containing the Java resources (always `src/main/resources`)
- `webappDir`: the directory containing the Web application (always `src/main/webapp`)
- `CONSTANTS`: the constants used by JHipster

- `baseName`: 应用名称
- `packageName`: Java package 名称
- `mainClassName`: main Java class 名称
- `angularAppName`: AngularJS 应用名称
- `packageFolder`: Java package 文件夹
- `javaDir`: Java 应用目录, 包含package文件夹
- `resourceDir`: Java资源目录 (通常是 `src/main/resources`)
- `webappDir`: Web应用程序目录 (通常是 `src/main/webapp`)
- `CONSTANTS`: JHipster使用的常量

And all the variables from the JHipster `.yo-rc.json` file:
从JHipster的`.yo-rc.json`文件添加的变量:

- `authenticationType`: the type of authentication
- `hibernateCache`: the Hibernate 2nd level cache
- `clusteredHttpSession`: whether a clustered HTTP session is used
- `websocket`: whether WebSockets are used
- `databaseType`: the type of database used
- `devDatabaseType`: the database used in "dev" mode
- `prodDatabaseType`: the database used in "prod" mode
- `searchEngine`: whether a search engine is used
- `useSass`: if Sass is used for CSS pre-processing
- `buildTool`: the Java build tool
- `enableTranslation`: if translations are enabled
- `nativeLanguage`: native language selected for i18n
- `languages`: additional languages selecetd for i18n
- `enableSocialSignIn`: if social login is enabled
- `testFrameworks`: an array of the test frameworks selected
- `jhiPrefix`: the prefix applied before services, controllers and states names of JHipster components
- `jhipsterVersion`: the JHipster version used to generate the application

- `authenticationType`: 认证类型
- `hibernateCache`: Hibernate 二级缓存
- `clusteredHttpSession`: 是否使用HTTP session集群
- `websocket`: 是否使用WebSockets
- `databaseType`: 使用的数据库类型
- `devDatabaseType`: 数据库使用开发"dev"模式
- `prodDatabaseType`: 数据库使用生产"prod"模式
- `searchEngine`: 是否使用全文检索
- `useSass`: 如果使用Sass是否启用CSS预处理
- `buildTool`: Java构建工具
- `enableTranslation`: 启用事务
- `nativeLanguage`: 选择国际化原生语言
- `languages`: 额外的国际化语言
- `enableSocialSignIn`: 启用社会化登录
- `testFrameworks`: 选择使用的测试框架，是一个数组
- `jhiPrefix`: services前缀, JHipster 组件的controllers和 states命名
- `jhipsterVersion`: 用来生成应用的JHipster版本

### 可用函数（Functions available） <small>(source <a href="https://github.com/jhipster/generator-jhipster/blob/master/generators/generator-base.js" target="_blank">here</a>)</small>

- `addSocialButton`: add a new social button in the login and register modules
- `addSocialConnectionFactory`: add a new social connection factory in the `SocialConfiguration.java` file
- `addSocialConfiguration`: add new social configuration in the `application.yml`
- `addMavenPlugin`: add a new maven plugin in the `pom.xml` file
- `addMavenDependency`: add a new maven dependency in the `pom.xml`file
- `addGradlePlugin`: add a new gradle plugin
- `addGradleDependency`: add a new gradle dependency
- `applyFromGradleScript`: apply script from another gradle file
- `addBowerrcParameter`: add a new parameter in the `.bowerrc`
- `addBowerDependency`: add a new package in the `bower.json` file
- `addBowerOverride`: add an override configuration in the `bower.json` file
- `addMainCSSStyle`: add a new style in the `main.css` file
- `addMainSCSSStyle`: add a new style in the `main.scss` file
- `addAngularJsModule`: add a new module in the `app.js` file
- `addAngularJsInterceptor`: register an AngularJS interceptor in the `app.js` file
- `addElementToMenu`: add an entry in the navigation menu
- `addElementToAdminMenu`: add an entry in the admin navigation sub-menu
- `addEntityToMenu`: add an entity in the entity navigation sub-menu
- `addElementTranslationKey`: add a new translation key in the `global.json` file
- `addAdminElementTranslationKey`: add a new translation key for an admin sub-menu in the `global.json` file
- `addEntityTranslationKey`: add a new translation key for an entity in the `global.json` file
- `addGlobalTranslationKey`: add a new translation key in the `global.json` file
- `addTranslationKeyToAllLanguages`: add a new translation key for all installed languages using methods `addElementTranslationKey`, `addEntityTranslationKey`, `addAdminElementTranslationKey`
- `isSupportedLanguage`: check if a language is supported by JHipster
- `getAllSupportedLanguages`: get the list of languages supported by Jhipster
- `getAllSupportedLanguageOptions`: get all the language options supported by JHipster
- `getAllInstalledLanguages`: get the list of languages installed by current application
- `addChangelogToLiquibase`: add a new changelog in the Liquibase `master.xml` file for a given needle
- `addConstraintsChangelogToLiquibase`: add a new constraints changelog to the Liquibase `master.xml` file
- `addLiquibaseChangelogToMaster`: add a new changelog to the Liquibase `master.xml` file
- `addColumnToLiquibaseEntityChangeset`: add new columns to the Liquibase changelog for an entity
- `dateFormatForLiquibase`: creates a new timestamp to be used by a Liquibase changelog
- `copyI18nFilesByName`: copy i18n files
- `copyTemplate`: copy a template from source to a destination after stripping any translation content when translation is disabled
- `copyHtml`: short hand method for `copyTemplate` which is defaulted to action `stripHtml`
- `copyJs`: short hand method for `copyTemplate` which is defaulted to action `stripJs`
- `rewriteFile`: add the given content above a specific custom needle in a file
- `replaceContent`: replace the given content for a specific pattern/regex in a file
- `registerModule`: register a JHipster module to act as a hook from app or entity generator
- `getModuleHooks`: get the array of all registered module hooks for the application
- `updateEntityConfig`: update the json configuration file for an entity with given key and value
- `getExistingEntities`: get sorted list of entities according to changelog date in the current application
- `isJhipsterVersionLessThan`: check if the JHipster version used to generate the project is less than a particular version 

- `addSocialButton`: 在登录和注册模块添加一个社会化登录按钮
- `addSocialConnectionFactory`: 添加一个社会化登录连接工厂到`SocialConfiguration.java`文件
- `addSocialConfiguration`: 添加一个社会化登录配置到`application.yml`
- `addMavenPlugin`: 添加一个新的maven插件到`pom.xml`文件
- `addMavenDependency`: 添加一个新的maven依赖到`pom.xml`文件
- `addGradlePlugin`: 添加一个新的gradle插件
- `addGradleDependency`: 添加一个新的gradle依赖
- `applyFromGradleScript`: 从另一个gradle文件应用一个脚本
- `addBowerrcParameter`: 添加一个新的参数到`.bowerrc`
- `addBowerDependency`: 添加一个新的package到`bower.json`文件
- `addBowerOverride`: 添加一个覆盖的配置到`bower.json`文件
- `addMainCSSStyle`: 添加一个新的风格到`main.css`文件
- `addMainSCSSStyle`: 添加一个新的风格到`main.scss`文件
- `addAngularJsModule`: 添加一个新的模块到`app.js`文件
- `addAngularJsInterceptor`: 注册一个AngularJS拦截器到`app.js`文件
- `addElementToMenu`: 添加一个菜单到导航条
- `addElementToAdminMenu`: 添加一个菜单到管理员导航条上的子菜单
- `addEntityToMenu`: 添加一个实体菜单到导航条数据子菜单
- `addElementTranslationKey`: 添加一个新的翻译key`global.json`文件
- `addAdminElementTranslationKey`: 添加一个新的翻译key的管理子菜单到`global.json`文件
- `addEntityTranslationKey`: 添加给实体一个新的翻译key到`global.json`文件
- `addGlobalTranslationKey`: 添加一个新的翻译key到`global.json`文件
- `addTranslationKeyToAllLanguages`: 添加一个新的翻译key到全部语言，使用方法`addElementTranslationKey`, `addEntityTranslationKey`, `addAdminElementTranslationKey`
- `isSupportedLanguage`: 检查JHipster是否支持该语言
- `getAllSupportedLanguages`: 获取Jhipster支持的语言列表
- `getAllSupportedLanguageOptions`: 获取JHipster支持的所有语言
- `getAllInstalledLanguages`: 获取当前应用安装的语言列表
- `addChangelogToLiquibase`: 添加一个新的修改记录在Liquibase `master.xml`文件 for a given needle
- `addConstraintsChangelogToLiquibase`: 添加一个新的constraints 修改记录to the Liquibase `master.xml`文件
- `addLiquibaseChangelogToMaster`: 添加一个新的修改记录到Liquibase `master.xml`文件
- `addColumnToLiquibaseEntityChangeset`: 添加一个实体类的新列修改纪录到Liquibase
- `dateFormatForLiquibase`: 使用Liquibase修改纪录创建一个新的时间戳
- `copyI18nFilesByName`: 拷贝国际化文件
- `copyTemplate`: 当翻译禁用时，在翻译剥离后拷贝一个模版
- `copyHtml`: `copyTemplate`函数的`stripHtml`行为缩写
- `copyJs`: `copyTemplate`函数的`stripJs`行为缩写
- `rewriteFile`: 在指定的needle文件添加内容
- `replaceContent`: 使用正则表达式替换文件内容
- `registerModule`: 从应用或实体生成器注册一个JHipster模块hook给act
- `getModuleHooks`: 从应用中获取所有注册的hooks，得到一个数组
- `updateEntityConfig`: 更新实体键值的json配置文件
- `getExistingEntities`: 按照changelog日期顺序获取当前应用实体列表
- `isJhipsterVersionLessThan`: 检查JHipster生成项目的生成器版本是否低于指定版本


## 注册一个模块到JHipster marketplace（Registering a module to the JHipster marketplace）

To have your module available in [the JHipster marketplace]({{ site.url }}/modules/marketplace/), you need to make sure you have the 2 keyword `yeoman-generator` and `jhipster-module` in your published npm `package.json`.
If you find any entry in the marketplace which is not a JHipster module, you can help to blacklist it by adding it to the `blacklistedModules` section of the [modules-config.json file](https://github.com/jhipster/jhipster.github.io/blob/master/modules/marketplace/data/modules-config.json) by doing a Pull Request to the [jhipster/jhipster.github.io project](https://github.com/jhipster/jhipster.github.io).

让你的模块出现在[the JHipster marketplace]({{ site.url }}/modules/marketplace/), 你需要确保在你的npm的`package.json`中有2个关键字`yeoman-generator`和`jhipster-module`。
如果你在marketplace找到了非JHipster模版, 你可以通过将其添加到[modules-config.json文件](https://github.com/jhipster/jhipster.github.io/blob/master/modules/marketplace/data/modules-config.json)`blacklistedModules`部分，并发送Pull Request到[jhipster/jhipster.github.io project](https://github.com/jhipster/jhipster.github.io)来帮我们将其加入黑名单.

Your module will become "verified" if the JHipster team verifies it.

如果JHipster团队验证了它，您的模块将成为“验证”状态。

Once you publish your module to NPM, your module will become available in our marketplace.

一旦你发布你的NPM模块,模块将出现在我们的marketplace里。
