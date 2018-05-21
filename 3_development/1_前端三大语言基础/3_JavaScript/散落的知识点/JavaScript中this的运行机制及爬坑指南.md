在 JavaScript 中，this 这个特殊的变量是相对比较复杂的，因为 this 不仅仅用在面向对象环境中，在其他任何地方也是可用的。 本篇博文中会解释 this 是如何工作的以及使用中可能导致问题的地方，最后奉上最佳实践。



为了更好地理解 this，将 this 使用的场景分成三类：



- 在函数内部 this 一个额外的，通常是隐含的参数。

- 在函数外部（顶级作用域中）： 这指的是浏览器中的全局对象或者 Node.js 中一个模块的输出。

- 在传递给eval()的字符串中： eval() 或者获取 this 当前值值，或者将其设置为全局对象，取决于 this 是直接调用还是间接调用。


我们来看看每个类别。

## this 在函数中
这是最常用的 this 使用方式，函数通过扮演三种不同的角色来表示 JavaScript 中的所有可调用结构体：



- 普通函数（this 在非严格模式下为全局对象，在严格模式下为undefined）

- 构造函数（this 指向新创建的实例）

- 方法（this 是指方法调用的接收者）



在函数中，this 通常被认为是一个额外的，隐含的参数。

### this 在普通函数中
在普通函数中，this 的值取决于模式：



非严格模式： this 是指向全局对象 （在浏览器中为window对象）。
```
function sloppyFunc() {

    console.log(this === window); // true

}

sloppyFunc();
```
严格模式： this 的值为 undefined。
```
function strictFunc() {

    'use strict';

    console.log(this === undefined); // true

}

strictFunc();
```
也就是说，this 是一个设定了默认值（window或undefined）的隐式参数。 但是，可以通过 call() 或 apply() 进行函数调用，并明确指定this的值：
```
function func(arg1, arg2) {

    console.log(this); // a

    console.log(arg1); // b

    console.log(arg2); // c

}

func.call('a', 'b', 'c'); // (this, arg1, arg2)

func.apply('a', ['b', 'c']); // (this, arrayWithArgs)
```

### this 在构造函数中
如果通过new运算符调用函数，则函数将成为构造函数。 该运算符创建一个新的对象，并通过this传递给构造函数：
```
var savedThis;

function Constr() {

    savedThis = this;

}

var inst = new Constr();

console.log(savedThis === inst); // true
```
在JavaScript中实现，new运算符大致如下所示（更精确的实现稍微复杂一点）：
```
function newOperator(Constr, arrayWithArgs) {

    var thisValue = Object.create(Constr.prototype);

    Constr.apply(thisValue, arrayWithArgs);

    return thisValue;

}
```

### this 在方法中
在方法中，类似于传统的面向对象的语言：this指向接受者，方法被调用的对象。
```
var obj = {

    method: function () {

        console.log(this === obj); // true

    }

}

obj.method();
```

## this 在顶级作用域中
在浏览器中，顶层作用域是全局作用域，它指向 global object（如window）：
```
console.log(this === window); // true
```
在Node.js中，通常在模块中执行代码。 因此，顶级作用域是一个特殊的模块作用域：
```
// `global` (不是 `window`) 指全局对象:

console.log(Math === global.Math); // true



// `this` 不指向全局对象:

console.log(this !== global); // true

// `this` refers to a module’s exports:

console.log(this === module.exports); // true
```

## this 在 eval() 中
eval() 可以被直接（通过真正的函数调用）或间接（通过其他方式）调用。

如果间接调用evaleval() ，则this指向全局对象：
```
(0,eval)('this === window')

true
```
否则，如果直接调用eval() ，则this与eval()的环境中保持一致。 例如：
```
// 普通函数

function sloppyFunc() {

    console.log(eval('this') === window); // true

}

sloppyFunc();



function strictFunc() {

    'use strict';

    console.log(eval('this') === undefined); // true

}

strictFunc();



// 构造器

var savedThis;

function Constr() {

    savedThis = eval('this');

}

var inst = new Constr();

console.log(savedThis === inst); // true



// 方法

var obj = {

    method: function () {

        console.log(eval('this') === obj); // true

    }

}

obj.method();
```

## 与this相关的陷阱
有三个你需要知道的与this相关的陷阱。请注意，在各种情况下，严格模式更安全，因为this在普通函数中为undefined，并且会在出现问题时警告。

### 陷阱：忘记new操作符
如果你调用一个构造函数时忘记了new操作符，那么你意外地将this用在一个普通的函数。this会没有正确的值。 在非严格模式下，this指向window对象，你将创建全局变量：
```
function Point(x, y) {

    this.x = x;

    this.y = y;

}

var p = Point(7, 5); // 忘记new!

console.log(p === undefined); // true


// 创建了全局变量：

console.log(x); // 7

console.log(y); // 5
```
幸运的是，在严格模式下会得到警告（this === undefined）：
```
function Point(x, y) {

    'use strict';

    this.x = x;

    this.y = y;

}

var p = Point(7, 5);

// TypeError: Cannot set property 'x' of undefined
```

### 陷阱：不正确地提取方法
如果获取方法的值（不是调用它），则可以将该方法转换为函数。 调用该值将导致函数调用，而不是方法调用。 当将方法作为函数或方法调用的参数传递时，可能会发生这种提取。 实际例子包括setTimeout()和事件注册处理程序。

我将使用函数callItt() 来模拟此用例：
```
/**类似setTimeout() 和 setImmediate() */

function callIt(func) {

    func();

}
```
如果在非严格模式下把一个方法作为函数来调用，那么this将指向全局对象并创建全局变量：
```
var counter = {

    count: 0,

    // Sloppy-mode method

    inc: function () {

        this.count++;

    }

}



callIt(counter.inc);



// Didn’t work:

console.log(counter.count); // 0



// Instead, a global variable has been created

// (NaN is result of applying ++ to undefined):

console.log(count); // NaN
```
如果在严格模式下把一个方法作为函数来调用，this为undefined。 同时会得到一个警告：
```
var counter = {

    count: 0,

    // Strict-mode method

    inc: function () {

        'use strict';

        this.count++;

    }

}



callIt(counter.inc);



// TypeError: Cannot read property 'count' of undefined

console.log(counter.count);
```
修正方法是使用bind()：
```
var counter = {

    count: 0,

    inc: function () {

        this.count++;

    }

}



callIt(counter.inc.bind(counter));



// 成功了!

console.log(counter.count); // 1
```
bind()创建了一个新的函数，它总是能得到一个指向counter的this。

### 陷阱：shadowing this
当在一个方法中使用普通函数时，很容易忘记前者具有其自己this（即使其不需要this）。 因此，你不能从前者引用该方法的this，因为该this会被遮蔽。 让我们看看出现问题的例子：
```
var obj = {

    name: 'Jane',

    friends: [ 'Tarzan', 'Cheeta' ],

    loop: function () {

        'use strict';

        this.friends.forEach(

            function (friend) {

                console.log(this.name+' knows '+friend);

            }

        );

    }

};

obj.loop();

// TypeError: Cannot read property 'name' of undefined
```
在前面的例子中，获取this.name失败，因为函数的this个是undefined，它与方法loop()的不同。 有三种方法可以修正this。



修正1：` that = this。` 将它分配给一个没有被遮蔽的变量（另一个流行名称是self）并使用该变量。
```
loop: function () {

    'use strict';

    var that = this;

    this.friends.forEach(function (friend) {

        console.log(that.name+' knows '+friend);

    });

}
```
修正2：` bind()`。 使用bind()来创建一个this总是指向正确值的函数（在下面的例子中该方法的this）。
```javascript
loop: function () {

    'use strict';

    this.friends.forEach(function (friend) {

        console.log(this.name+' knows '+friend);

    }.bind(this));

}
```
修正3： forEach的第二个参数。 此方法具有第二个参数，this值将作为此值传递给回调函数。
```javascript
loop: function () {

    'use strict';

    this.friends.forEach(function (friend) {

        console.log(this.name+' knows '+friend);

    }, this);

}
```

## 最佳实践
从概念上讲，我认为普通函数没有它自己的this，并且想到上述修复是为了保持这种想法。 ECMAScript 6通过箭头函数支持这种方法 - 没有它们自己的this。 在这样的函数里面，你可以自由使用this，因为不会被屏蔽：
```javascript
loop: function () {

    'use strict';

    // The parameter of forEach() is an arrow function

    this.friends.forEach(friend => {

        // `this` is loop’s `this`

        console.log(this.name+' knows '+friend);

    });

}
```
我不喜欢使用this作为普通函数的附加参数的API：
```javascript
beforeEach(function () {

    this.addMatchers({

        toBeInRange: function (start, end) {

            ...

        }

    });

});
```
将这样的隐含参数变成明确的参数使得事情更加明显，并且与箭头函数兼容。
```javascript
beforeEach(api => {

    api.addMatchers({

        toBeInRange(start, end) {

            ...

        }

    });

});
```