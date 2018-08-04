# this
> this

# 绑定规则
关于 this 的误解：this指向函数自身、this指向函数作用域
> 【this 是在运行时绑定的，完全取决于函数的调用位置】  
>  并不是在编写时绑定的，它的上下文取决于函数调用时的各种条件。

## 默认绑定
> 函数直接使用不带任何修饰的函数引用进行调用，则绑定到全局对象或 undefined 上
```
function foo(){
  console.log(this.a);
}
var a = 2;
foo();//2
```

## 隐式绑定
> 隐式绑定规则会把函数调用中的 this 绑定到这个上下文对象

注意：对象属性引用链中只有上一层或者说最后一层在调用位置中起作用
```
function foo(){
  console.log(this.a);
}
var obj2 = {
  a:42,
  foo:foo
};
var obj1 = {
  a:2,
  obj2:obj2
}
obj1.obj2.foo();//42
```

### 隐式丢失
被隐式绑定的函数会丢失绑定对象，就是说会默认绑定，从而把 this 绑定到全局对象或 undefined 上
```
function foo(){
  console.log(this.a);
}
var obj = {
  a:42,
  foo:foo
};
var bar = obj.foo;
bar();//"undefined"
```
```
//传入回调函数时
function foo(){
  console.log(this.a);
}
var obj = {
  a:42,
  foo:foo
};
function doFoo(fn){
  fn();
}
doFoo(obj.foo);//undefined
```
回调函数丢失 this 绑定非常常见（setTimeout()也会丢失 this 绑定）

## 显式绑定
call(),apply()
```
function foo(){
  console.log(this.a);
}
var obj = {
  a:2
};
foo.call(obj);//2
```
区别在于第二个参数，call（）是依次输入参数，而apply（）是一个参数数组

### 硬绑定
典型的应用场景就是创建一个包裹函数，负责接收参数并返回值
```
function foo(something){
  console.log(this.a,something);
  return this.a+something;
}
var bar = function(){  
  return foo.apply(obj,arguments);  
};  
var obj = {
  a:2
}
var bar = foo.bind(obj);  
var b = bar(3); 
console.log(b);//5
```

## new绑定
实际上并不存在所谓的“构造函数”，只有对于函数的“构造调用”。

1.创建一个全新的对象  
2.这个新对象会被执行 [[Prototype]] 连接  
3.这个新对象会绑定到函数调用的 this  
4.如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象 
```js
知识点重温：
//this指向调用的对象，当用了call后，能够改变this的指向，也就是指向传进来的对象，这是关键
//shift()函数是将数组的第一个值删除，并返回,这里是得到arr
[].shift.call(arguments)

因为 arguments 是类数组对象，构造如下，第一个是长度
{length:2,0:'first',1:'second'};

```
[Array.prototype.slice.call(arguments)](https://www.cnblogs.com/littledu/archive/2012/05/19/2508672.html)
```
function create() {
    // 创建一个空的对象
    let obj = new Object()
    // 获得构造函数
    let Con = [].shift.call(arguments)
    // 链接到原型
    obj.__proto__ = Con.prototype
    // 绑定 this，执行构造函数
    let result = Con.apply(obj, arguments)
    // 确保 new 出来的是个对象
    return typeof result === 'object' ? result : obj
}
```
 
```
function foo(a){
  this.a = a;
}
var bar = new foo(2);
console.log(bar.a);//2
```

## 优先级
new绑定>显示绑定>隐式绑定>默认绑定
 
## 绑定例外 
应用的是默认绑定规则  
call（null/undefined）、apply(null,[2,3])、bind（null，2）

## this 词法
ES6 中的箭头函数不会使用四条绑定规则，而是根据当前的词法作用域来决定 this。

箭头函数**继承**外层函数调用的 this 绑定，这其实和 ES6 之前代码中的 self = this 机制一样

```
function foo(){
  return (a) => {
    console.log(this.a);
  }
}
var obj1 = {
  a:2
};
var obj2 = {
  a:3
};
var bar = foo.call(obj1);
bar.call(obj2);//3,
```

箭头函数其实是没有 this 的，这个函数中的 this 只取决于他外面的第一个不是箭头函数的函数的 this。在下面这个例子中，因为调用 a 符合前面代码中的第一个情况，所以 this 是 window。并且 this 一旦绑定了上下文，就不会被任何代码改变。

```
function a() {
    return () => {
        return () => {
        	console.log(this)
        }
    }
}
console.log(a()()())
```

## call, apply, bind 区别
call 和 apply 都是为了解决改变 this 的指向。作用都是相同的，只是传参的方式不同。

除了第一个参数外，call 可以接收一个参数列表，apply 只接受一个参数数组。
```
let a = {
    value: 1
}
function getValue(name, age) {
    console.log(name)
    console.log(age)
    console.log(this.value)
}
getValue.call(a, 'yck', '24')
getValue.apply(a, ['yck', '24'])
```

### 模拟实现 call 和 apply
可以从以下几点来考虑如何实现

* 不传入第一个参数，那么默认为 window
* 改变了 this 指向，让新的对象可以执行该函数。那么思路是否可以变成给新的对象添加一个函数，然后在执行完以后删除？

```
Function.prototype.myCall = function (context) {
  var context = context || window
  // 给 context 添加一个属性
  // getValue.call(a, 'yck', '24') => a.fn = getValue
  context.fn = this
  // 将 context 后面的参数取出来
  var args = [...arguments].slice(1)
  // getValue.call(a, 'yck', '24') => a.fn('yck', '24')
  var result = context.fn(...args)
  // 删除 fn
  delete context.fn
  return result
}
```
以上就是 call 的思路，apply 的实现也类似
```
Function.prototype.myApply = function (context) {
  var context = context || window
  context.fn = this

  var result
  // 需要判断是否存储第二个参数
  // 如果存在，就将第二个参数展开
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }

  delete context.fn
  return result
}
```
bind 和其他两个方法作用也是一致的，只是该方法会返回一个函数。并且我们可以通过 bind 实现柯里化。

同样的，也来模拟实现下 bind
```
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
```