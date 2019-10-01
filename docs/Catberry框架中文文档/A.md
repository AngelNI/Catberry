# 开始

首先，您需要安装CLI：

```
npm install -g catberry-cli
```

接下来，创建一个新目录。

```
mkdir ~/new-project
cd ~/new-project
```

最后，使用以下Catberry模板中的一个初始化新项目：

- `example`-该项目与GitHub API配合使用，并演示了如何使用Catberry 框架实现这种同构/通用应用程序。
- `empty-handlebars`-使用[Handlebars](http://handlebarsjs.com/)模板引擎的空项目。
- `empty-dust`-使用[[Dust](https://github.com/catberry/catberry-dust)]模板引擎的空项目。
- `empty-pug`-使用 [Pug](https://pugjs.org/)模板引擎的空项目。

```
catberry init <template-name>
```

例如：

```
catberry init empty-handlebars
```

尽管已经创建了项目结构，但是仍然需要安装依赖项：

```
npm install --production
```

现在您可以启动应用程序，从以下选项中进行选择：

+ `debug` 模式——不减少代码，监视文件中的更改并重建/重新初始化。
+ `release` 模式——代码已最小化，无需监视更改，已准备就绪。

在`release`模式中启动：

```
npm start
```

在`debug`模式中启动：

```
npm run debug
```

或者

```
npm run debug-win
```

如果您使用的是windows系统，

该应用程序将在控制台上输出它正在监听的端口-默认情况下为http：// localhost：3000。

现在，您已经有了第一个Catberry应用程序，请继续创建自己的储存和和CAT组件。

CLI也可以很好地帮助您。

用于添加储存：

```
catberry addstore <store-name>
```

其中<store-name>是储存的名称，例如`some-group / Store`。

添加CAT组件：

```
catberry addcomp <component-name>
```

其中<component-name>是组件的名称，例如`hello-world`。

现在，您应该是设置 Catberry Framework 的专家，继续尝试一下吧！