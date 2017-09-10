## What
布尔值，JavaScript 基本数据类型之一。

JavaScript 基本数据类型：
- number 数字
- string 字符串
- boolean 布尔值
- undefined
- null
- symbols
- object

值有两个：true 、 false

## Tips
### 条件判断
最多用的是在条件判断（`if`, `? :`, `while` 等），对非布尔值，解释引擎会先进行隐式转换为布尔值。

对于大部分值在转换为布尔值时都是 `true`， 以下 falsy 值被转为`false` :
- 空串 ""
- null
- undefined
- 数字 0
- 数字 NaN
- 布尔值 false

这些值通常由某些操作返回，如：
- 逻辑类操作, `!`、`&&`、`||`
- 比较类操作，`===`、`!=`、`>` and so on
- 可以转换为布尔类型的值或变量

常用小技巧：
- `!!`
`!!` 转成 boolean
```JavaScript
let stringTest = 'this is a string';

true === !!stringTest // true
```

一些容易出错的点
```
null == undefined   // true

null === undefined  // false

NaN == NaN  // false
```

### 惰性求值
有两个小技巧：

- `||`
```
let stringTest = 'this is a test';

// 已经定义， 保留原有值
let testA = stringTest || 'default';

testA   // 'this is a test'
```

- `&&`
```
let objectTest = {a1: {a2: 'a2'}};

const a2 = objectTest.a1 && objectTest.a1.a2 || 'default';

a2  // 'a2'
```

在使用上面方式进行赋值或者条件判断时，务必要考虑 falsy 的情况。如下面的代码可能会成为一个隐患
```
const warningNumber = 0;

// 数字 0 被当成了 false 处理
const notWant = warningNumber || 10;

notWant;    // 10
```
