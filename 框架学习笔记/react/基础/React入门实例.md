> 本教程来源于阮一峰的[《React入门实例教程》](http://www.ruanyifeng.com/blog/2015/03/react.html)

## HTML模板
大致结构：
```
<!DOCTYPE html>
<html>
<head>
    <script src="react.js"></script>
    <script src="react-dom.js"></script>
    <script src="browser.js"></script>
</head>
<body>
    <div id="example"></div>
    <script type="text/babel">
        // Code runs here.
    </script>
</boby>
</html>
```

- `<script>`标签的`type`属性为 `text/babel`. 从而能够使用 `JSX`语法。
- 三个库： `react.js` 、 `react-dom.js`和 `browser.js`, 它们必须首先加载。其中， `react.js`是 `React`的核心库， `react-dom.js`是提供与`DOM`相关的功能，`browser.js`的作用时将 `JSX`语法转为 `javascript`语法，这一步很耗时间，实际上线的时候，应该将它放到服务器完成。

```
$babel src --out-dir build
```
上命令可以将`src`子目录的`js`文件进行语法转换，转码后的文件全部放在`build`子目录。

## `ReactDOM.render()`
`ReactDOM.render`是React的最基本的方法，用于将模板转化为`HTML`语言，并插入指定的DOM节点。
```
ReactDOM.render(
    <h1>Hello,world! </h1>,
    document.getElementById('example')
);
```
效果： 将一个 `h1`标题，插入`example`节点。

## JSX语法
将HTML语言直接写在JavaScript语言之中，不加任何引号。JSX允许 `HTML` 与 `JavaScript`的混写。
```
var names = ['Alice', 'Emily', 'Kate'];

ReactDOM.render(
    <div>
        {
            names.map(function(name) {
                return <div>Hello, {name}!</div>
            })
        }
    </div>,
    document.getElementById('example')
);
```
如上，JSX的基本语法规则: 遇到HTML标签(以 `<` 开头)，就用HTML规则解析；遇到代码块(以 `{`开头)， 就用 `JavaScript`规则解析。

JSX允许直接在模板插入 `JavaScript` 变量。如果这个变量是一个数组，则会展开这个数组的所有成员。

## 组件
React 允许将代码封装成组件(component), 然后像插入普通HTML标签一样，在网页中插入这个组件。 `React.createClass`方法就用于生成一个组件类。
```
var HelloMessage = React.createClass({
    render: function () {
        return <h1></h1>;
    }
    }
});