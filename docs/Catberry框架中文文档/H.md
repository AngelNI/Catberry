# 路由

Catberry的路由系统的语法类似于Rails，但是还具有其他一些功能。每个路由定义都在URI中设置可能存在存储参数的位置。每个参数都绑定到储存列表，并且在更改它时（例如，通过单击链接），所有商店相关者会自动触发其更改事件，这会导致使用新数据重新渲染绑定到商店的所有CAT组件。

这些路由定义放置在文件./routes.js中。

路由定义是一个规则，用于描述Catberry处理哪些URI，Catberry可以从这些URI解析哪些参数以及哪些存储区将接收解析的参数。

## 字符串中用冒号标记的参数

默认定义语法如下：

```
/some/:id[store1,store2]/actions?someParameter=:parameter[store1]
```

所有参数都应在开头加上冒号，并可以选择在其后跟随将接收此参数值的储存名称列表。这些储存称为储存相关者。此列表也可以为空。

在前面的示例中，将`id`值设置为`store1`和`store2`；并且仅将参数值设置为商店`store1`。

请记住，路由定义中的参数名称必须满足正则表达式`[$ A-Z _] [\\ dA-Z _ $] *`，并且参数值应满足正则表达式` [^ \\\ /＆?=]*`。

路径段，查询名称或查询值中的任何参数都可以用文本和其他参数包围，如下所示：

```
/item-:id[store1,store2]/actions?some-:queryName1[store1]-:queryName2[store2]=cool-:parameter[store1]
```

## 命名路由和URI生成

您可以使用`this.$ context.getRouteURI（'routeName'，parameters）`方法为路由命名。此方法将参数值粘贴到路由表达式中，而不是“冒号标记的参数”语法中。因此，您拥有一个URI，可在模板内的链接中使用该URI来在应用内进行路由。

例如，您有一条路线定义：

```
{
  name: 'myRoute',
  expression: '/some/:id[store1,store2]/actions?someParameter=:parameter[store1]'
}
```

并且您使用以下参数调用该方法：

```
this.$context.getRouteURI('myRoute', {
  id: 'uniqueId',
  parameter: 'someValue'
});
```

它会返回如下结果：

```
/some/uniqueId/actions?someParameter=someValue
```

同样，如果像我们在此特定示例中那样使用参数作为查询字符串值`（：parameter [store1]）`，则可以将Array指定为这样的值：

```
this.$context.getRouteURI('myRoute', {
  id: 'uniqueId',
  parameter: ['first', 'second', 'third']
});
```

这将返回如下结果：

```
/some/uniqueId/actions?someParameter=first&someParameter=second&someParameter=third
```

## 带有冒号标记的参数以及其他映射功能

同样，您可以定义映射器对象，该映射器允许您在Catberry处理应用程序状态对象之前对其进行修改。

如果要使用映射功能，只需定义如下路径：

```
{
	expression: '/user/news/:category[news]',
	map(state) {
		state.news.pageType = 'userNews';
		return state;
	}
}
```

映射功能接收由路线定义字符串准备的状态。状态是一个对象，其中键是储存名称，值是每个储存的状态对象。您可以根据需要更改整个状态对象，然后从map函数返回它。

在上面的此示例中，商店`news` 将接收带有值`userNews`的附加状态参数`pageType`。

## 正则表达式

在极少数情况下，您可能仍需要使用正则表达式解析参数。在这些情况下，可以如下定义一个映射器对象：

```
{
	expression: /^\/orders\/\d+/i,
	map(uri) {
		const matches = uri.path.match(/^\/orders\/(\d+)/i);
		return {
			order:{
				orderId: Number(matches[1])
			}
		};
	}
}
```

在上面的示例中，商店`order`将接收参数`orderId`，其值与URL中的数字匹配。

## 带有任何查询参数的URL

如果路由定义包含任何查询参数，则它们始终是可选的。例如，如果您具有以下路由定义：

```
/some/:id[store1,store2]/actions?a=:p1[store1]&b=:p2[store1]&c=:p3[store1]
```

您尝试路由这样的URL：

```
/some/1/actions?b=123
```

您将收到状态：

```
{
	store1: {
		id: '1',
		p2: '123'
	},
	store2: {
		id: '1'
	}
}
```

参数`p1`和`p3`被跳过。完全没有任何查询参数的URL也将被路由。

```
/some/1/actions
```

并且您将收到以下状态：

```
{
	store1: {
		id: '1'
	},
	store2: {
		id: '1'
	}
}
```

## 文件示例

这是`./routes.js`文件的示例，其中包含所有3种情况：

```
module.exports = [
	// 1. simple string expression
	'/user/:id[user,menu,notifications]',
	// 2. the string expression with the 'map' function
	{
		expression: '/user/news/:category[news]',
		map(state) {
			state.news.pageType = 'userNews';
			return state;
		}
	},
	// 3. the regular expression with the 'map' function
	{
		expression: /^\/orders\/\d+/i,
		map(urlPath) {
			const matches = urlPath.match(/^\/orders\/(\d+)/i);
			return {
				orderId: Number(matches[1])
			};
		}
	}
];
```



































