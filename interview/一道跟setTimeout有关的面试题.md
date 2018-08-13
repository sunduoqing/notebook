# 一道跟 setTimeout 有关的面试题

#3 题目1
```js
function textlog() {
    for (var i = 0; i < 5; i++) {
        setTimeout(function timer() {
            console.log(i);
        }, i * 1000);
    }
}
textlog(); //55555
```
根据浏览器的事件循环机制
* 计时器线程
* js引擎线程
* 事件触发线程

事件触发线程维护一条**事件队列**，js引擎线程执行**同步栈**中的代码

当js引擎执行到 setTimeout 的时候，将其放到计时器函数中计时，js引擎继续执行同步栈中的同步代码（5次for循环）

当计时完成之后将回调函数放入事件队列等待空闲的js引擎执行，而此时全局中i的值是5

所以全部的输出都是5
### 利用闭包
闭包就是能够读取其他函数内部变量的函数

闭包的特点是变量会驻留在内存中
```js
function textlog() {
  for (var i = 0; i < 5; i++) {
    (function(i){
        setTimeout( function timer() {
              console.log(i);
          }, i * 1000 );
    })(i);
  }
}
textlog();//01234
// 上面的代码是标准答案，将变量i作为参数传到闭包中
// 我们也可以通过作用域在函数内部把变量隔离起来
// 其实，在闭包内部访问i的时候，i就是一个常量
```
```js
function textlog() {
  for (var i = 0; i < 5; i++) {
    (function(){
        var s = i;//把i赋值给另外一个变量
        setTimeout( function timer() {
              console.log(s);
          }, s * 1000 );
    })();
  }
}
textlog();
// 当然，也可以把 setTimeout 的回调函数做成一个闭包，同样能得到正确的结果。
```
### 利用块级作用域
```js
function textlog() {
  for (let i = 0; i < 5; i++) {
        setTimeout( function timer() {
              console.log(i);
          }, i * 1000 );
  }
}
textlog();
```
```js
function textlog() {
  for (let i = 0; i < 5; i++) {
    setTimeout(()=> {
      console.log(i);
    }, i * 1000 );
  }
}
textlog();
```

## 题目2
```js
var num = 0;
function Obj (){
    this.num = 1,
    this.getNum = function(){
        console.log(this.num);
    },
    this.getNumLater = function(){
        setTimeout(function(){
            console.log(this.num);
        }, 1000)
    }
}
var obj = new Obj(); 
obj.getNum();//1　　打印的是obj.num，值为1
obj.getNumLater();//0　　打印的是window.num，值为0
```
this 的隐式绑定中的隐式丢失

[this](https://github.com/sophiayang1997/notebook/blob/master/JavaScript/this.md)
### 将当前对象的this存为一个变量，定时器内的函数利用闭包来访问这个变量
```js
var num = 0;
function Obj (){
    var that = this;    //将this存为一个变量，此时的this指向obj
    this.num = 1,
    this.getNum = function(){
        console.log(this.num);
    },
    this.getNumLater = function(){
        setTimeout(function(){
            console.log(that.num);    //利用闭包访问that，that是一个指向obj的指针
        }, 1000)
    }
}
var obj = new Obj; 
obj.getNum();//1　　打印的是obj.num，值为1
obj.getNumLater();//1　　打印的是obj.num，值为1
```
### 利用bind()方法
```js
var num = 0;
function Obj (){
    this.num = 1,
    this.getNum = function(){
        console.log(this.num);
    },
    this.getNumLater = function(){
        setTimeout(function(){
            console.log(this.num);
        }.bind(this), 1000)    //利用bind()将this绑定到这个函数上
    }
}
var obj = new Obj; 
obj.getNum();//1　　打印的为obj.num，值为1
obj.getNumLater()//1　　打印的为obj.num，值为1
```
bind()方法是在Function.prototype上的一个方法，当被绑定函数执行时，bind方法会创建一个新函数，并将第一个参数作为新函数运行时的this。在这个例子中，在调用setTimeout中的函数时，bind方法创建了一个新的函数，并将this传进新的函数，执行的结果也就是正确的了。

### 箭头函数
```js
var num = 0;
function Obj (){
    this.num = 1,
    this.getNum = function(){
        console.log(this.num);
    },
    this.getNumLater = function(){
        setTimeout(() => {
            console.log(this.num);
        }, 1000)    //箭头函数中的this总是指向外层调用者，也就是Obj
    }
}
var obj = new Obj; 
obj.getNum();//1　　打印的是obj.num，值为1
obj.getNumLater()//1　　打印的是obj.num，值为1
```
ES6 中的箭头函数完全修复了 this 的指向，this 总是指向词法作用域，也就是外层调用者 obj，因此利用箭头函数就可以轻松解决这个问题。

以上三种方法都是比较常用的，当然如果使用 call 或 apply 方法来代替 bind 方法，得到的结果也是正确的，但是 call 方法会在调用之后立即执行，那样也就没有了延时的效果，定时器也就没有用了，所以推荐使用上述方法来将 this 传进 setTimeout 和 setInterval 中。 

## 参考
[详解setTimeOut面试题](https://segmentfault.com/a/1190000009017901)