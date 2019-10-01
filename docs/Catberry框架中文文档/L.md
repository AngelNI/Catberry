# 事件总线和诊断

Catberry具有一组可用于诊断目的的事件（例如，通过浏览器扩展）。Catberry使用完全相同的事件来记录所有消息。

监听Catberry事件的方式有两种：

+ 像这样直接使用Catberry应用程序实例对其进行监听：

```js
const catberry = require('catberry');
const cat = catberry.create();

cat.events.on('error', error => {
	// some action
});
```

+ 使用储存或CAT组件的`this.$ context`对象使用相同的`on`或`once`方法对其进行监听。

在浏览器中，您可以访问全局Catberry应用程序实例：

```
// catberry object is global because it is a property of the window
catberry.events.on('error', error => {
	// some action
});
```

实际上，`cat.events`属性具有类似于 [EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter)的接口。

## 事件名称和参数

以下是Catberry活动的列表：

| 事件                | 何时发生                     | 参数                                                         |
| ------------------- | ---------------------------- | ------------------------------------------------------------ |
| ready               | Catberry已完成初始化         | 无                                                           |
| trace               | 跟踪消息已发送               | `String`                                                     |
| debug               | 调试消息已发送               | `String`                                                     |
| info                | 信息消息已发送               | `String`                                                     |
| warn                | 警告消息已发送               | `String`                                                     |
| error               | 错误消息已发送               | `String`                                                     |
| fatal               | 致命错误消息已发送           | `String`                                                     |
| storeLoaded         | 每个储存都已加载             | `{name: String, path: String, constructor: function}`        |
| componentLoaded     | 每个组件都已加载             | `{name: String, properties: Object, constructor: function, template: Object, errorTemplate: Object}` |
| allStoresLoaded     | 所有储存都已加载             | 按名称加载储存                                               |
| allComponentsLoaded | 所有组件均已加载             | 按组件名称加载的组件                                         |
| componentRender     | Catberry开始渲染组件         | `{name: String, context: Object}`                            |
| compomentRendered   | Catberry完成了组件渲染       | `{name: String, context: Object, hrTime: [number, number], time: Number}` |
| storeDataLoad       | Catberry开始从储存加载数据   | `{name: String}`                                             |
| storeDataLoaded     | Catberry完成从储存加载数据   | `{name: String, data: Object, lifetime: Number}`             |
| actionSend          | Catberry将动作发送到储存     | `{storeName: String, actionName: String, args: Object}`      |
| actionSent          | 操作已发送到储存             | `{storeName: String, actionName: String, args: Object}`      |
| documentRendered    | Catberry完成了所有组件的渲染 | 带有位置，引荐来源网址，用户代理等的路由关系                 |
| storeChanged        | Catberry应用程序储存已更改   | 储存名称                                                     |
| stateChanged        | Catberry应用程序已更改状态   | `{oldState: Object, newState: Object}`                       |

仅服务器事件的列表：

| 事件                 | 何时发生                   | 参数                                                       |
| -------------------- | -------------------------- | ---------------------------------------------------------- |
| storeFound           | 找到每个储存               | {name: String, path: String}                               |
| componentFound       | 找到每个组件               | {name: String, path: String, properties: Object}           |
| appBundleBuilt       | 该应用的浏览器捆绑包已构建 | {hrTime: [number, number], time: Number, path: String}     |
| externalsBundleBuilt | 构建了用于外部的浏览器包   | {hrTime: [number, number], time: Number, path: String}     |
| appBundleChanged     | 浏览器套件已更改           | {changedFiles: ['path1', 'pathN'], path: String}           |
| appDefinitionsBuilt  | 应用程序定义文件已构建     | {hrTime: [number, number], time: Number, template: String} |

仅浏览器事件的列表：

| 事件             | 何时发生                   | 参数                             |
| ---------------- | -------------------------- | -------------------------------- |
| documentUpdated  | 存储已更改，组件已重新渲染 | `['store1', 'store2']`           |
| componentBound   | 每个组件都绑定             | `{element: Element, id: String}` |
| componentUnbound | 每个组件都是未绑定的       | `{element: Element, id: String}` |

这些事件可用于浏览器扩展，扩展的日志记录或通过组件/存储逻辑（在极少数情况下）使用，可随时随地使用它们，但请记住，如果事件的订阅过多，则可能导致性能下降，如果订阅服务器环境中的事件，您也必须取消订阅这些事件。