# Catberry 服务

在Catberry中，每个框架的组件/模块（例如Logger或“通用HTTP（S）请求”）都称为“服务”。

整个Catberry的体系结构都是使用 [Service Locator](http://en.wikipedia.org/wiki/Service_locator_pattern)模式构建的。服务定位器是Catberry的核心组件，用于存储有关每个Catberry组件（服务）的所有信息。在某些其他平台上，它类似于[IoC](http://en.wikipedia.org/wiki/Inversion_of_control)容器。因此，例如，如果任何组件依赖于另一个组件，则只需从其构造函数内部的Service Locator对其进行解析即可。如果该依赖关系依赖于另一个依赖关系，则它会执行相同的操作来解析整个依赖关系图。

例如，每个服务（包括用户定义的服务）都可以要求“记录器”服务来将消息记录到控制台：

```
class Some {
	constructor(locator) {
		this._logger = locator.resolve('logger');
	}
}
```

当Catberry初始化自身时，它会将其所有服务注册到Service Locator中，但是框架用户还可以注册自己的插件（服务），甚至覆盖现有Catberry服务的实现。

例如，您可以使用自己喜欢的记录器替换服务定位器中的记录器服务，只需编写一个与 [Catberry "logger" 服务](https://github.com/catberry/catberry/blob/master/docs/index.md#logger) 的接口匹配的适配器，然后按如下所示进行注册：

```
const cat = catberry.create();
cat.locator.registerInstance('logger', MyCoolLogger);
```

要注册自己的服务，请记住，您可能需要在服务器和浏览器环境中使用不同的服务实现。但是在某些情况下，这并不重要。

在下一部分中了解更多如何使用服务定位器的信息。

## 定位器服务

Catberry框架的整个架构都基于 [定位器模式服务](http://en.wikipedia.org/wiki/Service_locator_pattern).

## 注册自己的服务

Catberry应用程序中只有一个服务定位器（单例），并且所有Catberry服务都可以从该定位器解析。服务器上的`getMiddleware`方法调用或浏览器环境中的`startWhenReady`方法正在解决所有依赖项。

在此之前，请随时注册您自己的服务。

您的Catberry应用程序可能具有`./server.js`文件，如下所示：

```js
const catberry = require('catberry');
const RestApiClient = require('./lib/RestApiClient');
const express = require('express');
const config = require('./server-config');
const cat = catberry.create(config);
const app = express();

// when you have created an instance of the Catberry application
// you can register anything you want in the Service Locator.
// last "true" value means that the instance of your service is a singleton
cat.locator.register('restApiClient', RestApiClient, true);

// you can register services only before this method below
app.use(cat.getMiddleware());
// now Catberry already has initialized the whole infrastructure of services
app.use(connect.errorHandler());
http
	.createServer(app)
	.listen(config.server.port || 3000);
```

另外，对于浏览器环境，您的应用程序可能具有`./browser.js`文件，如下所示：

```js
const catberry = require('catberry');
const RestApiClient = require('./lib/RestApiClient');
const config = require('./browser-config');
const cat = catberry.create(config);

// when you have created an instance of the Catberry application
// you can register anything you want in the Service Locator.
// last "true" value means that the instance of your service is a singleton
cat.locator.register('restApiClient', RestApiClient, true);

// you can register services only before this method below:
cat.startWhenReady(); // returns promise
// now Catberry has already initialized the entire infrastructure of services
```

另外，您可以覆盖现有的服务注册，例如Logger：

```
cat.locator.register('logger', MyCoolLogger, true);
```

它注册了logger服务的另一个实现，并且Catberry始终使用每个服务的最后注册的实现。

您还可以使用`resolveAll（）`方法访问任何服务的所有注册实现。但是请注意，如果没有使用指定名称的实现，定位器将引发错误。

每个服务的构造函数都有唯一的参数–服务定位符本身，您可以在其中解析任何其他依赖关系。

*请记住，您必须避免循环依赖，它们会导致无限循环。*

另请阅读：

- [定位器界面服务](https://github.com/catberry/catberry-locator/blob/master/lib/ServiceLocator.js)

用户区服务：

- [记录仪](https://github.com/catberry/catberry/blob/master/docs/index.md#logger)
- [配置](https://github.com/catberry/catberry/blob/master/docs/index.md#config)
- [通用HTTP（S）请求](https://github.com/catberry/catberry/blob/master/docs/index.md#uhr-universal-https-request)

## 记录仪

Catberry的核心不包含记录器服务，但是具有用于实现记录器插件的API。如果为适配器创建适配器，让其订阅Catberry中的几个重要事件，则可以使用所需的任何日志记录器。

作为实现的示例，在所有CLI提供的项目模板中都有一个[stupid console logger](https://github.com/catberry/catberry-logger)。

请记住，如果您不将任何记录器与Catberry一起使用，则应自行处理[进程“ uncaughtException” ](https://nodejs.org/api/process.html#process_event_uncaughtexception)和 [window.onerror](https://developer.mozilla.org/en/docs/Web/API/GlobalEventHandlers/onerror)，否则应用程序将在服务器上退出，并在出现任何致命错误时停止在浏览器中执行发生。

如果您有一个由CLI生成的项目，则无需担心，所有致命错误都将由作为Catberry插件附带的默认控制台记录器处理。

## 配置

Catberry具有在 [服务定位器](https://github.com/catberry/catberry/blob/master/docs/index.md#service-locator)中注册为“配置”服务的配置对象。

如果需要，只需从服务定位器解析`配置`即可。

该服务只是传递给`catberry.create（）`方法的完整配置对象。

Catberry使用以下参数：

+ `componentsGlob` – 用于发现CAT组件的glob表达式可以是字符串数组，也可以只是字符串（默认为`['catberry_components / ** / cat-component.json'，'node_modules / * / cat-component.json']）`。
+ `storesGlob`  –  用于发现储存的glob表达式（默认情况下为`** / *s`）。
+ `storesDirectory` – 包含储存的目录的相对路径（默认为`./catberry_stores`）。
+ `publicDirectoryPath` – 公共目录的路径（默认情况下为`./public`）。
+ `appBundleFilename` – 应用程序的浏览器捆绑文件的名称（默认为`app.js`）。
+ `externalsBundleFilename`  – 外部库（`node_modules`目录中任何必需的库）的浏览器捆绑文件的名称（默认为`externals.js`）。























