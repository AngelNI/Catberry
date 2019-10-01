# 前后共享

Catberry将每个存储和CAT组件的每个实例的属性设置为`$ context`。

`$ context`至少具有以下属性和方法，并且专门针对存储和组件进行了扩展：

+ `this.$context.isBrowser`–`true` 如果代码正在浏览器中执行。
+ `this.$context.isServer` – `true` 如果代码正在服务器上执行。
+ `this.$context.userAgent` – 当前用户代理环境下的字符串。
+ `this.$context.cookie` – 当前的cookie包装器对象
+ `this.$context.location` – 包含当前位置的当前URI对象
+ `this.$context.referrer` – 包含当前引荐来源网址的当前URI对象。
+ `this.$context.locator` – 应用程序的服务定位器
+ `this.$context.getRouteURI('routeName', { parameter: 'value' })` – 生成URI字符串，将参数值粘贴到指定的路由表达式（必须具有名称）。如果将路由参数用作URI中的查询值，则可以使用Array作为其值。请参阅“路由”部分中的示例。
+ `this.$context.redirect('String')` – 重定向到指定的位置字符串。如果在呈现文档或标题组件时使用，则将使用服务器上的HTTP标头和状态代码来完成此操作，否则将通过内联< script >标签。此方法在服务器上使用HTTP状态代码302。
+ `this.$context.permRedirect('String')`– 与`this.$ context.redirect('url')`完全相同，但是使用HTTP状态代码301进行永久重定向。
+ `this.$context.notFound()` – 将请求处理移交给下一个 [转移/链接 中间件](http://expressjs.com/en/guide/using-middleware.html)。如果在呈现文档或标题组件时使用，则将使用服务器上的HTTP标头和状态代码来完成此操作，否则将通过内联< script >标签。

**注意**：路由器每次计算新的应用程序状态时，都会为每个存储区重新创建并重新分配上下文，*因此，请勿保存对`this.$ context`的引用。*

