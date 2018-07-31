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