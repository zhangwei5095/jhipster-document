---
layout: default
title: Using AngularJS
permalink: /using-angularjs/
redirect_from:
  - /using_angularjs.html
sitemap:
    priority: 0.7
    lastmod: 2015-01-29T23:41:00-00:00
---

# <i class="fa fa-html5"></i> Using AngularJS

## Project Structure

Jhipster前端的代码在`src/main/webapp`目录下,前端代码严格遵守[John Papa AngularJS 1 风格指南](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md).请先阅读这份引导,如果有关于项目结构,文件名称,JavaScript规范的疑问.

这套风格指南是AngularJS团队所认可的,并且保证了升级到AngularJS2 的一个明确路线.

AngularJS的路由我们遵循DASH下的命名约定,以便URLs是干净的和一致的.
当你生成一个实体的路由名称时,路由的URLs和REST API端点URLs会依据这个约定自动生成,同时实体名称自动复数化(如实体为Foo变为Foos)。

这里是主项目的结构:

    webapp
    ├── app                               - Your application
    │   ├── account                        - User account management UI
    │   ├── admin                         - Administration UI
    │   ├── blocks                        - Common building blocks like configuration and interceptors
    │   ├── components                    - Common components like alerting and form validation
    │   ├── entities                      - Generated entities (more information below)
    │   ├── home                          - Home page
    │   ├── layouts                       - Common page layouts like navigation bar and error pages
    │   ├── services                      - Common services like authentication and user management
    │   ├── app.constants.js              - Application constants
    │   ├── app.module.js                 - Application modules configuration
    │   ├── app.state.js                  - Main application router
    ├── bower_components                  - Dependencies installed by Bower
    ├── content                           - Static content
    │   ├── images                        - Images
    │   ├── styles                        - CSS stylesheets
    │   ├── fonts                         - Font files will be copied here
    ├── i18n                              - Translation files
    ├── scss                              - Sass style sheet files will be here if you choose the option
    ├── swagger-ui                        - Swagger UI front-end
    ├── 404.html                          - 404 page
    ├── favicon.ico                       - Fav icon
    ├── index.html                        - Index page
    ├── robots.txt                        - Configuration for bots and Web crawlers

使用[entity sub-generator]({{ site.url }}/creating-an-entity/)来创建一个新的实体名为`Foo`在`src/main/webapp`下生成前端文件:

    webapp
    ├── app
    │   ├── entities
    │       ├── foo                                    - CRUD front-end for the Foo entity
    │           ├── foo.controller.js                  - Controller for the list page
    │           ├── foo.service.js                     - Service which access the Foo REST resource
    │           ├── foo.state.js                       - AngularUI router, which manages routes for this entity
    │           ├── foo-delete-dialog.controller.js    - Controller for the delete dialog pop-up
    │           ├── foo-delete-dialog.html             - View for the delete dialog pop-up
    │           ├── foo-detail.controller.js           - Controller for the entity details page
    │           ├── foo-detail.html                    - View for the entity details page
    │           ├── foo-dialog.controller.js           - Controller for the create/update dialog pop-up
    │           ├── foo-dialog.html                    - View for the create/update dialog pop-up
    │           ├── foos.html                          - View for the list page
    ├── i18n                                           - Translation files
    │   ├── en                                         - English translations
    │   │   ├── foo.json                               - English translation of Foo name, fields, ...
    │   ├── fr                                         - French translations
    │   │   ├── foo.json                               - French translation of Foo name, fields, ...

注意,默认语言的翻译是基于你生成项目的时候选择.'en'和'fr'为项目演示而用.

## 授权

JHipster使用 [angular-ui-router](http://angular-ui.github.io/ui-router/) 来组织应用程序客户端的不同模块(路由).

For each state, the required authorities are listed in the state's data, and when the authority list is empty it means that the state can be accessed anonymously.

The authority names are defined in server's class `AuthoritiesConstants.java`.

In the example below, the 'sessions' state can be accessed only by authenticated users who have `ROLE_USER` authority:

    (function() {
        'use strict';

        angular
            .module('tmpApp')
            .config(stateConfig);

        stateConfig.$inject = ['$stateProvider'];

        function stateConfig($stateProvider) {
            $stateProvider.state('sessions', {
                parent: 'account',
                url: '/sessions',
                data: {
                    authorities: ['ROLE_USER'],
                    pageTitle: 'global.menu.account.sessions'
                },
                views: {
                    'content@': {
                        templateUrl: 'app/account/sessions/sessions.html',
                        controller: 'SessionsController',
                        controllerAs: 'vm'
                    }
                },
                resolve: {
                    translatePartialLoader: ['$translate', '$translatePartialLoader', function ($translate, $translatePartialLoader) {
                        $translatePartialLoader.addPart('sessions');
                        return $translate.refresh();
                    }]
                }
            });
        }
    })();

## Notification System

JHipster uses [ui-bootstrap alerts](https://angular-ui.github.io/bootstrap/#/alert) for the notification system, and has an i18n-capable `AlertService` which can be used throughout the generated applications.

By default JHipster will show success notifications whenever an entity is created/updated/deleted and error notifications when there is an error caught from the response.

To show a custom notification or alert, use the below methods after injecting the `AlertService` to your controller, directive or service.

The shorthand methods `success`, `info`, `warning` and `error` will have a timeout of 5 seconds, for other configurations use the `add` method:

    (function() {
        'use strict';

        angular
            .module('tmpApp')
            .controller('FooController', FooController);

        FooController.$inject = ['$scope', '$state', 'Foo', 'AlertService'];

        function FooController ($scope, $state, Foo, AlertService) {

            AlertService.success("This is a success message, it is green");

            AlertService.info("This is an info message, it is blue");

            AlertService.warning("This is a warning message, it is amber");

            AlertService.error("This is an error message, it is red");

            AlertService.success("i.will.be.translated");
            // where key i.will.be.translated needs to be in global.json

            AlertService.success("i.will.be.translated", {param: someParam});
            // where key i.will.be.translated needs to be in global.json and can have a { param } which will be replaced by `someParam`

            AlertService.add({
                type: "success", // can be success, info, warning and error
                msg: msg,
                params: params, // parameters to pass for translation
                timeout: timeout // how long to show the alert in milliseconds
            });

            AlertService.clear();  // clear all alerts

            AlertService.get(); // get all open alerts

        }
    })();


If you would like the notifications to look like toasts you can enable that by setting `AlertServiceProvider.showAsToast(true)` in `src/main/webapp/app/blocks/config/alert.config.js`. By default this will be set to false `AlertServiceProvider.showAsToast(false)`.
