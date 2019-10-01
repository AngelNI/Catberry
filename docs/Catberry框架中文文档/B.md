# 同构/通用应用

TL; DR通过同构/通用应用解决的一些直接好处：

+ *Search-engine crawlable single page application* ——可以像在浏览器中一样容易地在服务器上呈现同一页面。
+ *D.R.Y templating & logic* ——由于通用模块仅用JavaScript编写一次，因此您不必重复自己或在两个单独的代码库中维护相同的逻辑。

构建同构/通用应用程序，可以在服务器/客户端上相同地使用给定的模块。

以用于呈现页面的代码为例。它可以首先在服务器上使用–用于SEO，共享链接，快速初始化等–然后在浏览器中或完全在浏览器中用作单页应用程序，而无需服务器呈现。

Airbnb上有一篇很棒的博客文章[链接](http://nerds.airbnb.com/isomorphic-javascript-future-web-apps/)，解释了同构JavaScript应用程序背后的思想及其组成。

另外，您可以查看Spike Brehm的JSConf 2014年的演讲: [Building Isomorphic Apps](http://www.youtube.com/watch?v=CH6icJbLhlI)。

Catberry是用于使用通用组件构建前端应用程序的同构框架。Catberry背后的想法是构建一个仅前端应用程序，该应用程序可以通过一组RESTful API获取其数据，并快速呈现结果页面。