## 关于状态管理
> 为什么需要状态管理？
- 因为层次深，很不爽

> 为什么是`mobx`,而不是`redux`或者直接`context`
- redux 用好久了，尝试下新的，样板代码太多了，很不爽
- context 不推荐不是？
- 什么函数式，个人见智
- 听说`mobx`跟`vuex`一个道理，我试试
- mobx 代码好少，哇，可以提前下班了，爽

## mobx 介绍
这个不用介绍了吧，自己google

## next 介绍
这个我也不管， ssr、同构嘛，也就那回事。

## next 引入mobx
### 配置啥的(for decorators)
- babel 6
```
npm i --save-dev babel-plugin-transform-decorators-legacy
```
.babelrc
```
{
    "presets": ["es2015", "stage-1"],
  "plugins": ["transform-decorators-legacy"]
}
```

- babel 7
```
// .babelrc
{
  "presets": [
    "react",
    "es2015",
    "stage-1"
  ],
  "plugins":[
      "transform-decorators",
      ["transform-class-properties", { "loose": true}]
  ]
}

// package.json
{
    "babel-core": "7.0.0-beta.3",
    "babel-preset-es2015": "7.0.0-beta.3",
    "babel-preset-react": "7.0.0-beta.3",
    "babel-preset-stage-1": "7.0.0-beta.3",
    "babel-plugin-transform-decorators": "^6.24.1"
}
```

### 两个方案
- 统一管理多个 store
```
import A from './mobx/A'
import B from './mobx/B'

const aStore = new A();
const bStore = new B();

const stores = {
    aStore,
    bStore,
}

class App extends Component {
    render() {
        return (
            <Provider {...stores}>
                <Home/>
            </Provider>
        );
    }
}

export default App;
```
如上，简单的一种引用方式。next 改造同方案二。

- 单 page 单store

由于在我项目中，各个模块不耦合，路由间不想干，所以各自处理就ok。  
ssr 中要解决的一个很大的问题就是 server 和 client 之间状态同步的问题。如下:
```js
# with-mobx-state.js
import React from "react";
import { Provider } from "mobx-react";

export default (initializeStore, storeName, initState = {}) => App => {
  const isServer = typeof window === "undefined";

    // cache
  function getOrCreateStore(initialState = initState) {
    if (isServer) {
      return initializeStore(isServer, initialState);
    }

    if (!window[storeName]) {
      window[storeName] = initializeStore(isServer, initialState);
    }
    return window[storeName];
  }

  return class AppWithMobx extends React.Component {
    static async getInitialProps(ctx) {
      const mobxStore = getOrCreateStore();
      ctx.mobxStore = mobxStore;

      let appProps = {};
      if (typeof App.getInitialProps === "function") {
        appProps = await App.getInitialProps.call(App, ctx);
      }

      return {
        ...appProps,
        initialMobxState: mobxStore
      };
    }

    constructor(props) {
      super(props);
      // sync
      this.mobxStore = getOrCreateStore(props.initialMobxState);
    }

    render() {
      const store = { [storeName]: this.mobxStore };
      return (
        <Provider {...store}>
          <App {...this.props} {...store} />
        </Provider>
      );
    }
  };
};
# testStore.js
import { action, observable } from "mobx";

// 注意模块内变量的作用范围： 模块内是全局的，模块间是独立的
let store = null;

class Store {
  @observable lastUpdate;
  @observable light = false;

  constructor(isServer, lastUpdate) {
    this.lastUpdate = lastUpdate;
  }

  @action
  start = () => {
    this.timer = setInterval(() => {
      this.lastUpdate = Date.now();
      this.light = true;
    }, 1000);
  };

  stop = () => clearInterval(this.timer);
}

export default function initializeTestStore(
  isServer,
  { lastUpdate = Date.now() }
) {
  if (isServer) {
    return new Store(isServer, lastUpdate);
  } else {
    if (store === null) {
      store = new Store(isServer, lastUpdate);
    }
    return store;
  }
}

# test.js
import React, { Component } from "react";
import { Card, Button } from "antd";
import { inject, observer } from "mobx-react";
import moment from "moment";
import withMobxStore from "../../decorators/with-mobx-store";
import initializeTestStore from "./testStore";

@withMobxStore(initializeTestStore, "testStore")
class MobxTest extends Component {
  static async getInitialProps(ctx) {
    return {
      title: "mobx test",
      PageHeaderVisible: true
    };
  }

  render() {
    return (
      <Card title="I'm parent">
        <TestChild1 />
        <TestChild2 />
      </Card>
    );
  }
}
export default MobxTest;

@inject("testStore")
@observer
class TestChild1 extends Component {
  render() {
    const { testStore = {} } = this.props;
    return (
      <Card title="I'm child1">
        <code>{JSON.stringify(testStore)}</code>
        <br />
        <br />
        <Button onClick={testStore.start}>start</Button>
      </Card>
    );
  }
}

@inject("testStore")
@observer
class TestChild2 extends Component {
  render() {
    const { testStore } = this.props;
    console.log("testStore.lastUpdate", testStore.lastUpdate);
    return (
      <Card title="I'm child2" style={{ marginTop: 20 }}>
        {/*<code>{testStore.lastUpdate}</code>*/}
        <br />
        <br />
        <Button onClick={testStore.stop}>stop</Button>
        <p>{moment(testStore.lastUpdate, "x").format("YYYY-MM-DD HH:mm:ss")}</p>
      </Card>
    );
  }
}
```
留有问题：上边`initState`其实是没有意义的。  
解决方案: 没想到。求大佬指教。

## TL;DR
- 存在即合理
- 对症下药。别再说啥啥啥比啥啥啥好了。
- 能快速抓老鼠的都是好猫。