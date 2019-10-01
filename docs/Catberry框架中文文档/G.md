# 应用程序结构示例

通常，项目结构如下所示：

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
./catberry_components/
	document/
		index.js
		template.hbs
		cat-component.json
	component1/
		index.js
		template.hbs
		errorTemplate.hbs
		cat-component.json

 # directory for your own external (not Catberry) modules/services
./lib/
 # this directory is a default destination for browser bundle building
./public/
	app.js
	externals.js
 # entry script for the browser environment
./browser.js
 # route definitions
./routes.js
 # entry script for the server environment
./server.js
```

如果您希望以完成的应用程序为例，请转到示例目录。