# CAT-组件

您可能会认为CAT组件是胡须，爪子，甚至是尾巴，但并非总是如此。

CAT组件是[Web组件](http://webcomponents.org/)的通用实现，Web组件是Web组件规范中声明的功能和特定于Catberry框架的功能的混合，设计用于服务器和客户端，并且可以作为 [NPM dependency](https://docs.npmjs.com/files/package.json#dependencies)发布。

默认情况下，每个`cat-component`都使用`cat-component.json`文件声明为目录。但是您可以通过配置更改此设置。当`Catberry`初始化应用程序时，它将以项目根目录递归地搜索此类目录，然后通过`./node_modules`目录中的一级依赖项进行搜索。

`cat-component.json`文件包含以下内容：

+ `name`  – 组件的名称（可选）。默认情况下，这是目录的名称。
+ `description`  – 有关CAT组件的一些其他信息（可选）。虽然目前尚未使用，但已保留此属性的潜在将来功能。
+ `template`  – 组件模板的相对路径（必需）。
+ `errorTemplate`  – 发生错误时将呈现的组件错误模板的相对路径（可选）。
+ `logic` – 导出组件逻辑对象的类或构造函数的文件的相对路径（可选）。默认情况下，将使用`index.js`。

**注意**：组件名称始终以`cat-`开头，并且不区分大小写。

`cat-component.json`示例如下所示：

```
{
	"name": "cool",
	"description": "Some awesome and cool cat-component",
	"template": "./template.hbs",
	"errorTemplate": "./errorTemplate.hbs",
	"logic": "./Cool.js"
}
```

在上面的示例中，组件的标签名称是`cat-cool`，可以按以下方式使用：

```
<cat-cool cat-store="group/store1" some-additional="value" ></cat-cool>
```

重要注意事项：

+ 设置`cat-store`属性会将`store`连接到`cat-component`，这意味着随着`store`的更改，`cat-component`将自动重新呈现。
+ 您可以设置所需的任何其他属性，并在组件内部使用它。
+ 您必须始终使用开始和结束标签（即，不得使用自闭合标签）。大多数浏览器不正确支持自关闭自定义标签。
+ 支持嵌套组件（在组件模板内使用`cat-component`）

有两种保留的组件名称，它们以不寻常的方式使用：

+ `document`  – 整个应用程序的根模板（doctype，html，body等）。它不能依赖于任何储存，因此`cat-store`属性将被忽略。
+ `head` – 文档中HEAD元素的组件。它始终使用特殊的差异/合并模式进行渲染，否则，每次都会重新加载所有样式和脚本。它可以依赖商店，并且除了渲染方法外，还可以像通常的CAT组件一样工作。

## CAT组件的界面

CAT组件的逻辑文件应导出用于创建其实例的类或构造函数。Catberry为页面上的每个自定义标签创建一个单独的实例。另外，您可以选择在类/原型中定义以下方法：

+ `render()` – 创建并返回组件模板的数据或Promise。每当组件的自定义标签出现在页面上或需要用新数据更新时，都会调用此方法。在服务器和浏览器环境中都调用该方法。
+ `bind()`  – 创建并返回带有事件绑定或Promise的对象。每次更改页面并重新安装组件时，或者在初始文档加载时，仅在浏览器中调用此方法。请参见下面的示例。
+ `unbind()`  – 此方法的行为类似于析构函数。如果要手动删除某些事件侦听器或自己清理后使用此方法。仅在浏览器环境中以及每次从DOM中删除组件时，才调用该方法。

这是`bind()`方法可以返回的对象的示例。

```
bind() {
	return {
		click: {
			'.clickable': this._clickHandler,
			'#some-div': this._someDivHandler
		},
		hover: {
			'.clickable': this._clickableHoverHandler
		}
	};
}
```

每个事件处理程序都会自动绑定到组件的当前实例，您不需要自己使用[`.bind(this)`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)。此外，您可以在`bind`方法中手动添加事件处理程序，然后在`unbind`方法中手动将其删除。例如，处理window对象中的一些全局事件。

从DOM中删除该组件后，所有事件侦听器都将被删除，然后，如果已声明该组件，则将调用`unbind`方法。

**注意**：组件构造函数和渲染方法在服务器和浏览器上均执行。因此，您不能使用特定于环境的全局对象或函数，例如窗口，进程或DOM方法。

## CAT组件的上下关系

每个组件的`$ context`都具有以下属性和方法：

+ `this.$context.getStoreData()`  – 获得组件绑定存储的数据的承诺。如果该储存不存在，则承诺将被拒绝。
+ `this.$context.sendAction('name', object)` – 将操作发送到绑定存储，并返回处理程序结果的承诺。如果商店没有用于此操作的处理程序，则结果将为`null`。
+ `this.$context.element` – 表示当前组件的当前DOM元素。
+ `this.$context.attributes` – 组件的DOM元素当前具有的属性集。.
+ `this.$context.getComponentById('id')` – 通过其元素的ID获取另一个组件*对象*。
+ `this.$context.getComponentByElement(domElement)` – 通过其DOM元素获取另一个组件的对象。
+ `this.$context.getComponentsByTagName('tag-name', parentComponent)` – 通过标签名称获取组件*对象*列表。`parentComponent` – 一个父组件 *对象*，仅用于在嵌套组件之间查找（可选）。
+ `this.$context.queryComponentSelector('selector', parentComponent)` – 通过选择器获取组件*对象*。`parentComponent` –一个父组件*对象*，仅用于在嵌套组件之间查找（可选）。
+ `this.$context.queryComponentSelectorAll('selector', parentComponent)` – 通过选择器获取组件*对象*列表。`parentComponent `– 一个父组件对象，仅用于在嵌套组件之间查找（可选）。
+ `this.$context.createComponent('tagName', attributesObject)` – 创建一个新组件的实例并返回其DOM元素的承诺。
+ `this.$context.collectGarbage()` – 收集所有使用`createComponent（'tagName'，attributeObjectObject）`方法创建的组件，这些组件目前未附加到DOM。

## 代码示例

这是一个示例，组件的逻辑文件如下所示：

```
'use strict';

class Some {

	/**
	 * Creates a new instance of the "some" component.
	 * @param {ServiceLocator} locator The service locator for resolving dependencies.
	 */
	constructor(locator) {
		// you can resolve any dependency from the locator.
	}

	/**
	 * Gets data for the template.
	 * This method is optional.
	 * @returns {Promise<Object>|Object|null|undefined} Data for the template.
	 */
	render() {
		return this.$context.getStoreData();
	}

	/**
	 * Returns event binding settings for the component.
	 * This method is optional.
	 * @returns {Promise<Object>|Object|null|undefined} Binding settings.
	 */
	bind() {
		return {
			'.clickable': () => window.alert('Ouch!');
		}
	}

	/**
	 * Cleans up everything that has NOT been set by .bind() method.
	 * This method is optional.
	 * @returns {Promise|undefined} Promise of nothing.
	 */
	unbind() {
		// nothing to do here since we used bind properly
	}
}

module.exports = Some;
```







