# 《学习JavaScript数据结构与算法》
> algorithm

* [二、数组](#二、数组)
* [三、栈](#三、栈)
* [](#)
* [](#)
* [](#)

## 二、数组
JavaScript 可以在数组中保存不同类型的值

### 2.2 创建和初始化数组
```js
let a = new Array()
let a = new Array(7)
let a = new Array('one','two')

let a = []
let a = ['one','two']

console.log(a.length)

for(let i=0 ; i<a.length ;i++){
  console.log(a[i])
}
```

### 2.3 添加元素
```js
// 1.插入到指定元素
nums[nums.length] = 10

// 2.push 方法
nums.push(10,11)

// 3.插入元素到数组首位
for(let i=nums.length ; i>=0 ; i--){
  nums[i] = nums[i-1]
}
nums[0] = -1

// 4.上面的unshift实现
nums.unshift(-1,-2)
```

### 2.4 删除元素
```js
// 1.删除数组里最后一个元素
nums.pop()

// 2.删除数组里的首位元素
for(let i=0 ; i<nums.length ; i++){
  nums[i] = nums[i+1]
}

3.上面的shift实现
nums.shift()
```

* push()和pop()模拟栈
* unshift()和pop()模拟队列

### 2.5 在任意位置添加或删除元素
splice()
* 第一个参数：想要删除或插入的元素的索引值
* 第二个参数：删除元素个数（不删除时为0）
* 第三个以及后面的参数： 添加到数组里面的值

```js
nums.splice(5,3) // 删除了从索引5开始的3个元素

nums.splice(5,0,2,3,4) // 从索引5开始添加3个元素
```

### 2.6 二维和多维数组
1. 迭代二维数组元素
2. 多维数组

嵌套 for 循环

### 2.7 JavaScript 的数组方法参考
1. **API**

| 方法名| 描述    |
| --------    | --------|
| concat        | 连接2个或更多数组，并返回结果      |
| every        | 对数组中的每一项运行给定函数，如果该函数对每一项都返回true，则返回true      |
| map        |  对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组      |
| filter       | 对数组中的每一项运行给定函数，返回该函数会返回true的项组成的数组      |
| reduce       | 接收一个函数作为参数，函数有四个参数，返回一个被叠加到累加器的值  |
| forEach      | 对数组中的每一项运行给定函数，这个方法没有返回值      |
| join        | 将所有的数组元素连成一个字符串      |
| indexOf     | 返回第一个与给定参数相等的数组元素的索引，没有找到则返回-1      |
| lastIndexOf | 返回在数组中搜索到的与给定参数相等的元素的索引里最大的值      |
| reverse        | 颠倒数组中元素的顺序，原先第一个元素现在变成最后一个，最后一个变成第一个      |
| slice        | 传入索引值，将数组里对应索引范围内的元素作为新数组返回      |
| some        | 对数组中的每一项运行给定的函数，如果任一项返回true，则返回true      |
| sort        | 按照字母顺序对数组排序，支持传入指定排序防范的函数作为参数      |
| toString        | 将数组作为字符串返回      |
| valueOf        | 和toString类似，将数组作为字符串返回      |

* splice():改变原数组,返回被删除的元素组成的数组(增加元素时返回[])。
* slice():不改变原数组,返回截取到的新数组
* split():Split是切割字符串的一种方法，该方法主要用于把一个字符串分割成字符串数组。返回新的字符串数组。

2. **举例**
```js
nums.reduce((previous,current,index,array) => previous + current)
```

3. **ES6和ES7**

| 方法名        | 描述    |
| --------    | -----   |
| @@iterator  | 返回一个包含数组键值对的迭代器对象，可以通过同步调用得到数组元素的键值对  |
| copyWithin  | 复制数组中一系列元素到统一数组指定的起始位置     |
| entries     | 返回包含数组所有键值对的@@iterator     |
| includes    | 如果数组中存在某个元素则返true，否则返回false，第一个参数为起始值，第二个为元素  |
| find        | 根据回调函数给定的条件从数组中查找元素，如果找到则返回该元素，否则返回undefined |
| findIndex   | 根据回调函数给定的条件从数组中查找元素，如果找到则返回该元素在数组中的索引，否则返回-1 |
| fill        | 用静态值填充数组 |
| from        | 根据已有数组创建一个新数组 |
| keys        | 返回包含数组所有索引的@@iterator      |
| of          | 根据传入的参数创建一个新数组   |
| values      | 返回包含数组中所有值的@@iterator     |

for...of...：数组迭代
```js
nums=[1,...]

let i = nums[Symbol.iterator]()
i.next().value // 1

let e = nums.entries()
e.next().value // [0,1]
	
let k = nums.keys()
k.next() // {value:0, done:false}

let k = nums.values()
k.next() // {value:1, done:false}
```
```js
let nums2 = Array.from(nums)
let evens = Array.from(nums,x => (x%2==0))
```
```js
let nums = Array.of(1,2,3)
let numCopy = Array.of(...nums)
```
```js
let nums = Array,of(1,2,3,4,5)
nums.fill(0) // 5个0

nums.fill(2,1) // 从1开始所有位置都是2

nums.fill(1,3,5) // 从3开始到5的位置上是1

// 数组的初始化
let ones = Array(6).fill(1)
```
```js
// 从位置3开始到位置5结束（不包括5）的元素复制到位置1
nums.copyWithin(1,3,5)
```

4. **排序元素**
```js
nums.reverse()

nums.sort() // 默认按照字典顺序排序（字符串，ASCII码值）

nums.sort((a,b) => a-b) // 顺序，a<b返回负数，a>b返回正数
```
5. **类型数组**

## 三、栈
后进先出（LIFO）
```js
export default class Stack {
  constructor() {
    this.count = 0;
    this.items = {};
  }
  push(element) {
    this.items[this.count] = element;
    this.count++;
  }
  pop() {
    if (this.isEmpty()) {
      return undefined;
    }
    this.count--;
    const result = this.items[this.count];
    delete this.items[this.count];
    return result;
  }
  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.count - 1];
  }
  isEmpty() {
    return this.count === 0;
  }
  size() {
    return this.count;
  }
  clear() {
    /* while (!this.isEmpty()) {
        this.pop();
      } */
    this.items = {};
    this.count = 0;
  }
  toString() {
    if (this.isEmpty()) {
      return '';
    }
    let objString = `${this.items[0]}`;
    for (let i = 1; i < this.count; i++) {
      objString = `${objString},${this.items[i]}`;
    }
    return objString;
  }
}
```










