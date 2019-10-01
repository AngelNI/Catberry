# 储存

存储是使用路由参数从远程资源加载数据的模块。它还可以处理来自cat组件或其他存储的操作消息，并将请求发送到远程资源更改数据。每当它确定远程资源上的数据已更改并且应用程序应重新加载它时，它也可以发出更改事件。

默认情况下，所有存储都应放置在项目的`./catberry_stores`目录中。但是您可以通过`config`参数更改此目录。

当Catberry初始化时，它将在`./catberry_stores/`中进行递归搜索，并加载每个商店文件。商店名称将成为相对文件路径。

例如，如果您具有以下结构：

```
./catberry_stores/
	group1/
		store1.js
		store2.js
	group2/
		store1.js
		store2.js
	store1.js
	store2.js
```

那么您将获得储存列表：

```
group1/store1
group1/store2
group2/store1
group2/store2
store1
store2
```

**注意：**因为储存名称是相对路径，所以它们*区分大小写。*

## 存储界面

每个存储都必须导出一个类或构造函数以创建其实例。

（可选）您可以在类/原型中定义以下方法和属性。

+ `$lifetime` – 该值表示Catberry应该缓存从该存储加载的数据的时间（毫秒）。默认情况下，它设置为60000 ms。
+ `load()` – 从远程资源加载数据并返回它或一个Promise。
+ `handle<SomeActionNameHere>(args)` – 处理操作并返回结果或Promise。

处理程序可用于将数据提交到此处的远程资源，或仅更改存储中的状态-在这种情况下，您将必须通过`this.$ context.changed()`发出自己的更改事件。

例如，您可命名存储方法为`handleFormSubmit`，并且当某个组件或其他储存使用诸如`form-submit`，`form_submit`，`FORM_SUBMIT`，`formSubmit`或`FormSubmit`之类的名称向该储存发送操作时，它将触发该商店的`handleFormSubmit`方法。

请记住，储存是通用的，它们的源代码正在服务器和浏览器环境中执行。因此，您不能使用特定于环境的全局对象或函数，例如窗口，进程或其他DOM方法。

## 存储关系

储存的`$ context`具有以下属性和方法：

+ `this.$context.state` – 路由定义中的当前参数集。
+ `this.$context.getStoreData('storeName') ` – 得到另一个存储的数据的保证。如果`storeName`是当前商店的名称，则返回`null`。
+ `this.$context.sendAction('storeName', 'name', object)` –通过名称将操作发送到储存，并返回处理程序结果的Promise。如果商店没有用于此操作的处理程序，则结果将为`null`。
+ `this.$context.changed()` – 触发当前储存的更改事件。
+ `this.$context.setDependency('storeName')` – 设置对另一个储存数据的依赖性。每次储存相关性更改时，当前存储也会随着更改而触发。
+ `this.$context.unsetDependency('storeName')` –删除`this.$ context.setDependency`设置的依赖项。

 **注意：**使用其他储存中的数据时（例如通过`this.$ context.getStoreData`），必须将该商店设置为依赖项（通过`this.$ context.setDependency`），否则商店的依赖性将不会更新。

例如，假设您有两个商店`Country`和`CityList`，在`CityList.load（）`内部则调用`this.$ context.getStoreData（'Country'）`。在这种情况下，更改国家/地区后，`CityList`将永远不会更新。为了避免这种情况，请将`this.$ context.setDependency（'Country'）`添加到`CityList`的构造函数中。

## 代码示例

这是一个示例，储存的文件如下所示：

```
'use strict';

class Some {
	/**
	 * Creates a new instance of the "some" store.
	 * @param {ServiceLocator} locator The service locator for resolving dependencies.
	 */
	constructor(locator) {

		/**
		 * Current universal HTTP request for environment-independent requests.
		 * @type {UHR}
		 * @private
		 */
		this._uhr = locator.resolve('uhr');

		/**
		 * Current lifetime of data (in milliseconds) that is returned by this store.
		 * @type {number} Lifetime in milliseconds.
		 */
		this.$lifetime = 60000;
	}

	/**
	 * Loads data from a remote source.
	 * @returns {Promise<Object>|Object|null|undefined} Loaded data.
	 */
	load() {
		// Here you can perform HTTP requests using this._uhr.
		// For more info see: https://github.com/catberry/catberry-uhr.
	}

	/**
	 * Handles an action named "some-action" from any component or store.
	 * @returns {Promise<Object>|Object|null|undefined} Response to the component/store.
	 */
	handleSomeAction() {
		// Here you can call this.$context.changed() if you're sure
		// that the remote data on the server has been changed.
		// You can additionally have many more handler methods.
	};
}

module.exports = Some;
```

