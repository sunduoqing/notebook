# 《学习JavaScript数据结构与算法》
> algorithm

* [二、数组](#二、数组)
* [](#)

## 二、数组
JavaScript 可以在数组中保存不同类型的值

### 2.2 创建和初始化数组
```js
let a = new Array();
let a = new Array(7);
let a = new Array('one','two');

let a = [];
let a = ['one','two'];

console.log(a.length);

for(let i=0 ; i<a.length ;i++){
  console.log(a[i]);
}
```

### 2.3 添加元素
```js
// 1.插入到指定元素
nums[nums.length] = 10

// 2.push 方法
nums.push(10)
```