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
        return <h1>Hello {this.props.name}</h1>;
    }
    }
});

ReactDOM.render(
    <HelloMessage name="John" />,
    document.getElementById('example');
);
```

上面代码中，变量 `HelloMessage` 就是一个组件类。模板插入 <HelloMessage /> 时，会自动生成 HelloMessage 的一个实例（下文的"组件"都指组件类的实例）。所有组件类都**必须**有自己的 `render` 方法，用于输出组件。
在组件中可以任意加入属性，组件的属性可以在组件类的 `this.props`对象上获取，如上例 `name` 属性就可以通过 `this.props.name` 读取。
> 组件类的第一个字母必须大写，否则会报错，比如 `HelloMessage` 不能写成 `helloMessage`。另外，组件类只能包含一个顶层标签，否则也会报错。添加组件属性， `class` 属性要写成 `className`， `for` 属性要写成 `htmlFor` ， 这是因为 `class` 和 `for` 是 `Javascript` 的保留字。

## `this.props.children`
`this.props` 对象的属性与组件的属性一一对应，除了 `this.props.children`属性。它表示组件的所有子节点。
```
var NotesList = React.crateClass({
    render: function() {
        <ol>
            {
                React.Children.map(this.props.children, child => {
                    return <li>{child}</li>;
                })
            }
        </ol>
    }
});

ReactDOM.render(
    <NotesList>
        <span>hello</span>
        <span>world</span>
    </NodesList>,
    document.body
);
```
> `this.props.children` 的值有三种可能：当前组件没有子节点，它就是 `undefined`; 如果有一个子节点，数据类型是 `object`; 如果有多个子节点，数据类型就是 `array`。

React提供一个工具方法 `React.Children` 来处理 `this.props.children`。我们可以用 `React.Children.map` 来遍历子节点，而不用担心 `this.props.children`的数据类型是 `undefined` 还是 `object` 。

## `Prop.Types`
组件的属性可以接受任意值，字符串、对象、函数等等都可以。
组件类的 `PropTypes` 属性，就是用来验证组件实例的属性是否符合要求。
```
var MyTitle = React.createClass({
    propTypes: {
        title: React.PropTypes.string.isRequired
    },

    render: function() {
        return <h1> {this.props.title} </h1>;
    }
});

var data = 123;

ReactDOM.render(
    <MyTitle title={data} />,
    document.body
);
```

如上，title 属性通不过验证，控制台显示一行错误信息。
```
Warning: Failed propType: Invalid prop `title` of type `number` supplied to `MyTitle`, expected `string`.
```

此外， `getDefaultProps` 方法可以用来设置组件属性的默认值。
```
var MyTitle = React.createClass ({
    getDefaultProps: function () {
        return {
            title: 'Hello World'
        };
    },

    render: funciton () {
        return <h1> {this.props.title} </h1>;
    }
});

ReactDOM.render(
    <MyTitle />,
    document.body
);
```
## 获取真实的DOM节点
组件并不是真实的DOM节点，而是存在于内存之中的一种数据结构，叫做虚拟DOM(virtual DOM)。只有当它插入文档以后，才会变成真实的 DOM 。根据 React 的设计，所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实 DOM上，这种算法叫做 [DOM diff](http://calendar.perfplanet.com/2013/diff/) ，它可以极大提高网页的性能表现。
当需要从组件获取真实的DOM节点时，使用 `ref`属性。
```
var MyComponent = React.createClass({
    handleClick: function() {
        this.refs.myTextInput.focus();
    },

    render: function() {
        return (
            <div>
                <input type="text" ref="myTextInput" />
                <input type="button" value="Focus the text input" onClick={this.handleClick} />
            </div>
        );
    }
});

ReactDOM.render(
    <MyComponent />,
    document.getElementById("example")
);
```
上面代码中，组件 `MyComponent` 的子节点有一个文本输入框，用于获取用户的输入。这时就必须获取真实的 DOM 节点，虚拟 DOM 是拿不到用户输入的。为了做到这一点，文本输入框必须有一个 `ref` 属性，然后 `this.refs.[refName]` 就会返回这个真实的 DOM 节点。

需要注意的是，由于 `this.refs.[refName]` 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。上面代码中，通过为组件指定 `Click` 事件的回调函数，确保了只有等到真实 DOM 发生 `Click` 事件之后，才会读取 `this.refs.[refName]` 属性。

React组件支持很多事件，除了 `Click` 事件以外，还有 `KeyDown`、 `Copy`、 `Scroll`等。


## `this.state`
组件免不了要与用户互动，React 的一大创新，就是将组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染 UI 。
```
var LikeButton = React.createClass({
    getInitialState: function() {
        return {liked: false};
    },

    handleClick: function(event) {
        this.setState({liked: !this.state.liked});
    },

    render: function() {
        var text = this.state.liked ? 'like' : 'haven\'t liked';
        return (
            <p onClick={this.handleClick}>
                You {text} this. Click to toggle.
            </p>
        );
    }
});

ReactDOM.render(
    <LikeButton />,
    document.getElementById('example')
)
```
上面代码是一个 `LikeButton` 组件，它的 `getInitialState` 方法用于定义初始状态，也就是一个对象，这个对象可以通过 `this.state` 属性读取。当用户点击组件，导致状态变化，`this.setState` 方法就修改状态值，每次修改以后，自动调用 `this.render` 方法，再次渲染组件。
由于 `this.props` 和 `this.state` 都用于描述组件的特性，可能会产生混淆。一个简单的区分方法是，`this.props` 表示那些一旦定义，就不再改变的特性，而 `this.state` 是会随着用户互动而产生变化的特性。

## 表单

用户在表单填入的内容，属于用户跟组件的互动，所以不能用 `this.props` 读取。
```
var Input = React.createClass({
    getInitialState: function() {
        return {value: 'Hello!'};
    },
    handleChange: function(event) {
        this.setState({value: event.target.value});
    },
    render: function() {
        var value = this.state.value;
        return (
            <div>
                <input type="text" value={value} onChange={this.handleChange} />
                <p>{value}</p>
            </div>
        );
    }
});

ReactDOM.render(<Input />, document.body);
```
上面代码中，文本输入框的值，不能用 `this.props.value` 读取，而要定义一个 `onChange` 事件的回调函数，通过 `event.target.value` 读取用户输入的值。`textarea` 元素、`select`元素、`radio`元素都属于这种情况

## 组件的生命周期
三个状态：
- Mounting:　已插入真实DOM
- Updating: 正在被重新渲染
- Unmounting: 已移出真实DOM

React为每个状态都提供了两种处理函数， `will` 函数在进入状态之前调用， `did`函数在进入状态之后调用，三种状态共计五种处理函数。
- componentWillMount()
- componentDidMount()
- componentWillUpdate(object nextProps, object nextState)
- componentDidUpdate(object prevProps, object prevState)
- componentWillUnmount()

此外，React还提供了两种特殊状态的处理函数。
- componentWillReceiveProps(object nextProps): 已加载组件收到新的参数时调用
- shouldComponentUpdate(object nextProps, object nextState): 组件判断是否重新渲染时调用

```
var Hello = React.createClass({
    getInitialState: function() {
        return {
            opacity: 1.0
        };
    },

    componentDidMount: function () {
        this.timer = setInterval(function () {
            var opacity = this.state.opacity;
            opacity -= .05;
            if (opacity < 0.1) {
                opacity = 1.0;
            }
            this.setState({
                opacity: opacity;
            });
        }.bind(this), 100);
    },

    render: function () {
        return (
            <div style={{opacity: this.state.opacity}}>
                Hello {this.props.name}
            </div>
        );
    };
});

ReactDOM.render(
    <Hello name="world" />,
    document.body
);
```
上面代码在`Hello`组件加载以后，通过 `componentDidMount` 方法设置一个定时器，每隔100毫秒，就重新设置组件的透明度，从而引发重新渲染。
另外，组件的`style`属性的设置方式也值得注意，不能写成
```
style="opacity:{this.state.opacity};"
```
而要写成
```
style={{opacity: this.state.opacity}}
```
这是因为 React 组件样式是一个对象，所以第一重大括号表示这是 JavaScript 语法，第二重大括号表示样式对象。

## Ajax
组件的数据来源，通常是通过 Ajax 请求从服务器获取，可以使用 `componentDidMount` 方法设置 Ajax 请求，等到请求成功，再用 `this.setState` 方法重新渲染 UI
```
var UserGist = React.createClass({
    getInitialState: function() {
        return {
            username: '',
            lastGistUrl: ''
        };
    },

    componentDidMount: function() {
        $.get(this.props.source, function(result) {
            var lastGist = result[0];
            if(this.isMounted()) {
                this.setState({
                    username: lastGist.owner.login,
                    lastGistUrl: lastGist.html_url
                });
            }
        }.bind(this));
    },

    render: function() {
        return (
            <div>
                {this.state.username}'s last git is
                <a href={this.state.lastGistUrl}> here </a>
            </div>
        );
    }
});

ReactDOM.render(
    <UserGist source="https://api.github.com/users/octocat/gists" />,
    document.body
);
```

上面代码使用 jQuery 完成 Ajax 请求，这是为了便于说明。React 本身没有任何依赖，完全可以不用jQuery，而使用其他库。

我们甚至可以把一个Promise对象传入组件.
```
ReactDOM.render(
  <RepoList
    promise={$.getJSON('https://api.github.com/search/repositories?q=javascript&sort=stars')}
  />,
  document.body
);
```
上面代码从Github的API抓取数据，然后将Promise对象作为属性，传给`RepoList`组件。
如果Promise对象正在抓取数据（pending状态），组件显示"正在加载"；如果Promise对象报错（rejected状态），组件显示报错信息；如果Promise对象抓取数据成功（fulfilled状态），组件显示获取的数据。
```
var RepoList = React.createClass({
    getInitialState: function() {
        return {
            loading: true, error: null, data: null
        };
    },

    componentDidMount() {
        this.props.promise.then(
            value => this.setState({loading: false, data: value}),
            error => this.setState({loading: false, error: error})
        );
    },

    render: function() {
        if(this.state.loading) {
            return <span>Loading...</span>;
        }
        else if(this.state.error !== null) {
            return <span>Error: {this.state.error.message}</span>;
        }
        else {
            var repos = this.state.data.items;
            var repoList = repos.map(function (repo) {
                return (
                    <li>
                        <a href={repo.html_url}>{repo.name}</a> ({repo.stargazers_count} stars) <br/> {repo.description}
                    </li>
                );
            });
            return (
                <main>
                    <h1>Most Popular JavaScript Projects in Github </h1>
                    <ol> {repolist}</ol>
                </main>
            );
        }
    }
});
```

> 以上实例基于`React v0.14.0`