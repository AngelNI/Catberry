# 模板引擎

Catberry支持任何具有“字符串预编译”功能的模板引擎。

官方支持：

+ [Dust](https://github.com/catberry/catberry-dust)
+ [Handlebars](https://github.com/catberry/catberry-handlebars)
+ [Pug](https://github.com/catberry/catberry-pug)

您也可以为任何模板引擎创建自己的适配器，只需看一下现有的[Handlebars](https://github.com/catberry/catberry-handlebars)适配器即可。

要设置模板引擎，您只需要注册模板提供者，如下所示：

```js
const handlebars = require('catberry-handlebars');
const cat = catberry.create(config);
handlebars.register(cat.locator);
```

实际上， [Catberry CLI](https://github.com/catberry/catberry/blob/master/docs/index.md#cli) 为您做到了，请参阅CLI。

