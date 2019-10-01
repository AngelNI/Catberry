# 流

Catberry使用流（Flux）（类似于体系结构），该体系结构定义了储存的使用（数据源）以及从储存获取和呈现数据的视图/组件。Catberry将CAT组件用于这些视图。这是一个健壮的高性能体系结构，可让您创建具有许多数据依赖性的大型以及复杂应用程序。

异步工作流程是通过使用 [Promises](https://www.promisejs.org/)控制的。Catberry在浏览器或Node.js（V8）中使用本机Promise。如果找不到全局类型Promise，则框架将使用 ["Bare bones Promises/A+ implementation"](https://www.npmjs.org/package/promise)为您定义它。

从高层次来看，您真正需要知道的是，这里有存储，CAT组件以及控制它们之间通信的商店调度程序。

存储处理来自CAT组件的操作消息，这些消息触发更改的事件，然后导致Catberry重新呈现依赖于更改的商店的每个组件。

存储调度程序的工作方式是，一旦调用了存储来装载数据，它将不会遵循连续的装载调用，直到第一个解决为止。