1. setState 不会立刻改变 React 组件中 state 的值。
可以感受下: 
```
function incrementMultiple() {
  this.setState({count: this.state.count + 1});
  this.setState({count: this.state.count + 1});
  this.setState({count: this.state.count + 1});
}
```
上面的例子，体现在下一周期的state值，state.count只增加了1而不是3.

setState() 的目的是为了驱动组件更新，如果直接修改 this.state的值是没有意义的。那么，什么时候会更新 state的值呢？

运行过程中，setState引发的生命周期函数依次是：
- `shouldComponentUpdate`
- `componentWillUpdate`
- `render`
- `componentDidUpdate`

当shouldComponentUpdate函数被调用的时候，this.state没有得到更新。

当componentWillUpdate函数被调用的时候，this.state依然没有得到更新。

直到render函数被调用的时候，this.state才得到更新。

(或者，当shouldComponentUpdate函数返回false，这时候更新过程就被中断了，render函数也不会被调用了，这时候React不会放弃掉对this.state的更新的，所以虽然不调用render，依然会更新this.state。）

2. 某个函数触发 setState 与 父组件方法  

基于上面一个原因，曾经踩过一个坑。
假设在一个需要分页的模块中，一个分页状态如下
```
this.state = {
    pagination: {
        pageSize: 10,   // 每页几条数据
        current: 1, // 查看第几页
        total: 0,    // 数据总量
        size: 'small',  // 分页样式
    }
}
```
在`handleChangeTable`中，调用了setState方法，更新了分页状态，同时调用父组件传递的方法 `getData`。
```
handleChangeTable(pagination) {
    this.setState({
        pagination: {
            ...this.state.pagination,
            pageSize: pagination.pageSize,
            current: pagination.current,
        }
    });
    
    this.props.getData(pagination);
}
```
上面会有什么问题呢？没什么问题。。。那问题出现在哪里？看下面的代码
```
componentWillReceiveProps(nextProps) {
    // 拉取数据后进入了这里
    this.setState({
        pagination: {
            ...this.state.pagination,
            total: nextProps.data.total
        }
    });
    
    // ...
}
```
没错，问题就出现在这里，componentWillReceiveProps 中取到的this.state.pagination 是尚未更新的~所以，最终的结果就是，列表数据更新了，分页状态却还是原来的。

怎么解决呢？直接修改 `this.state`对象就不说了。setState 方法有第二个参数，是一个回调。 React V16.0 更新日志：
```
setState:
- Calling setState with null no longer triggers an update. This allows you to decide in an updater function if you want to re-render.
- Calling setState directly in render always causes an update. This was not previously the case. Regardless, you should not be calling setState from render.
- setState callbacks (second argument) now fire immediately after componentDidMount / componentDidUpdate instead of after all components have rendered.
```
回调会在此 setSate 触发更新 componentDidUpdate 结束后立马调用。此时this.state的值已经是更新过的了。所以解决办法可以这样做: 
```
handleChangeTable(pagination) {
    this.setState({
        pagination: {
            ...this.state.pagination,
            pageSize: pagination.pageSize,
            current: pagination.current,
        }
    }, () => this.props.getData(pagination));
    
}
```
但是~ 这样就多了一次生命周期。还有个解决办法是，handleChangeTable 不调用 setState 了，把需要更新的值传递给props.getData, 再回传回来，在 componentWillReceiveProps 里再一并更新。但是当需要更新的字段多了后，传着传着就丢了，代码也变得不清晰。

所以，我也求教另外的解决办法~

3. 多次setState函数调用产生的效果会合并
```
function updateName() {
  this.setState({FirstName: 'Morgan'});
  this.setState({LastName: 'Cheng'});
}

// 效果同

function updateName() {
  this.setState({FirstName: 'Morgan', LastName: 'Cheng'});
}
```

4. 函数式 setState 用法

这个我还没用过~~~
具体的请查看程墨大大的《setState：这个API设计到底怎么样》

## Reference
整理自:
- [setState：这个API设计到底怎么样](https://zhuanlan.zhihu.com/p/25954470)