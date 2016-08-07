---
layout: default
title: Configuring HTML 5 mode
sitemap:
priority: 0.5
lastmod: 2016-03-07T23:23:00-00:00
---

# 配置HTML5模式的URL

你可能注意到,AngularJS在URLs里使用了"#".HTML5Mode的AngularJS将"#"从URLs里去掉了.

## 激活HTML5 URLs模式

打开 `app.js` 文件在 `config`方法里增加下面这行:

    $locationProvider.html5Mode({ enabled: true, requireBase: true });

然后打开 `index.html` 在 `head` 里增加这行标签:

    <base href="/">

## 重定向过滤器     


现在为了让所有相关的链接都正常工作(例如:激活链接,激活后发送给用户邮件)我们将创建一个  `controller` 将转发定位到index.html

    @Controller
    public class AngularJsForwardController {
        @RequestMapping(value = "/{[path:[^\\.]*}")
        public String redirect() {
            return "forward:/";
        }
    }

请注意这可能会导致和 [Spring actuators URLs](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-endpoints.html)的冲突.

这就是为什么你需要先更改 `metric.js` 和 `health.js`,打开 `webapp\app\admin\health\health.js` 然后更改:

    url: '/health' -> url: '/apphealth'

`webapp\app\admin\metrics\metrics.js`同上操作:

    url: '/metrics' -> url: '/appmetrics'

最后为了让在导航上的首页链接正常工作,打开`webapp\app\layouts\navbar\navbar.html` 然后更改:

    <a class="navbar-brand" href="#/"> -> <a class="navbar-brand" href="/">
