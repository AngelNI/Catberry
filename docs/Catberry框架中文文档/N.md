# API插件

整个Catberry的插件API均基于服务定位器，每个插件都只是在定位器中注册的服务。

因此，有几种插件。

## 储存转换API

您可以注册一个将某些转换应用于已加载的商店的插件。支持Promise作为返回值。插件可以注册为实例（`locator.registerInstance`）或类/构造函数（`locator.register`），如下所示：

```js
locator.registerInstance('storeTransform', {
	transform(store) {
		// store is loaded
		// you can replace values, wrap a constructor, or even build a new object
		return newStore;
	}
);
```

`store`参数将是如下的对象：

```
{
	name: 'some store name',
	constructor: class StoreClass { }
}
```

## 组件转换API

您可以注册一个将某些转换应用于加载的组件的插件。支持Promise作为返回值。插件可以注册为实例（`locator.registerInstance`）或类/构造函数（`locator.register`），如下所示：

```
class ComponentTransform {
	constructor(locator) {
		// …
	}
	transform(component) {
		// component is loaded
		// you can replace values, wrap a constructor, or even build a new object
		return Promise.resolve(newComponent);
	}
}

locator.registerInstance('componentTransform', ComponentTransform);
```

`component`参数将是如下的对象：

```
{
	name: 'nameOfTheComponent',
	constructor: class ComponentClass {},
	// the object from cat-component.json
	// you can store in these files whatever you want and use it in
	// transformations
	properties: {
		name: 'nameOfTheComponent',
		template: './template.hbs',
		logic: 'index.js'
	},
	templateSource: 'compiled template sources here',
	errorTemplateSource: 'compiled error template sources here or null'
}
```

## 发表-建立操作API

您可以注册一个在构建浏览器捆绑包后执行操作的插件。它可以是资产建设或文件的某些后处理。支持Promise作为返回值。可以将插件注册为实例（`locator.registerInstance`）或类/构造函数（`locator.register`），如下所示：

```
locator.registerInstance('postBuildAction', {
	action(storeFinder, componentFinder) {
		// you can get a list of found stores or a list of found components
		// using storeFinder.find() and componentFinder.find() respectively
		// every component object has "properties" field
		// that contains cat-component.json values
		return Promise.resolve();
	}
);
```

`find（）`方法返回带有存储名称的对象的Promise。

在这种情况下，每个`store`都是如下的对象：

```
{
	name: 'some store name',
	path: 'relative path to a store module'
}
```

在这种情况下，每个`component`都是如下的对象：

```
{
	name: 'nameOfTheComponent',
	path: 'path to a cat-component.json file',
	// the object from cat-component.json
	// you can store whatever you want in these files and use it in
	// transformations
	properties: {
		name: 'nameOfTheComponent',
		template: './template.hbs',
		logic: 'index.js'
	}
}
```

上面的这种类型的对象称为描述符。

每个查找器都是一个[EventEmitter](https://nodejs.org/api/events.html#events_class_events_eventemitter) ，并具有以下事件：

存储查找器

+ add–已将新储存添加到应用程序
+ change–储存的来源已更改
+ unlink-储存已从应用程序中删除
+ error–发生监察错误

组件查找器

+ add – 已将新组件添加到应用程序
+ change – 组件的文件夹已更改（任何嵌套文件）
+ changeLogic – 组件的逻辑文件已更改
+ changeTemplates – 组件的模板或错误模板已更改
+ unlink – 该组件已从应用程序中删除
+ error – 发生监察错误

除`change`事件外，每个事件处理程序都将组件描述符作为第一个参数，但是`change`事件处理程序将接收具有附加数据的对象，如下所示：

```
{
	filename: 'filename of changed file',
	// component descriptor
	component: {
		name: 'nameOfTheComponent',
		path: 'path to a cat-component.json file',
		properties: {
			name: 'nameOfTheComponent',
			template: './template.hbs',
			logic: 'index.js'
		}
	}
}
```

## Browserify转换和插件API

它们都可以注册为实例（`locator.registerInstance`）或注册为类/构造函数（`locator.register`）：

转换示例：

```
locator.registerInstance('browserifyTransformation', {
  transform(filePath, options) {
	return transformStream;
  },
  options: { /* the transform options will be passed to the browserify */ }
);
```

插件示例：

```
locator.registerInstance('browserifyPlugin', {
  plugin(bundlerObject, options) {
	return transformStream;
  },
  options: { /* the plugin options will be passed to the browserify */ }
);
```