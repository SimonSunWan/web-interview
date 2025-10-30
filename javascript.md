1. var,let,const的区别
* 作用域
* 变量提升
* 暂时性死区
* 重新声明
* 重新赋值

2. 7个原始类型,1个对象类型
* string
* number
* boolean
* null
* undefined
* symbol
* bigint
* object

3. typeof
* typeof '1' === 'string'
* typeof 1 === 'number'
* typeof true === 'boolean'
* typeof undefined === 'undefined'
* typeof Symbol('id') === 'symbol'
* typeof 123n === 'bigint'
* typeof null === 'object'
* typeof [] === 'object'
* typeof new Date() === 'object'
* typeof () => {} === 'function'

```
判断null和array:
const a = null;
if (a === null) {
    console.log(true)
};

Array.isArray([]) === true;

最精确的类型检查方法:
Object.prototype.toString.call(null) === '[object Null]';
Object.prototype.toString.call([]) === '[object Array]';
```

4. 值类型和引用类型
* 值类型直接存储值,引用类型存储内存地址
* javascript中所有参数传递都是按值传递;对于引用类型来说:传递的是引用的副本

5. 深拷贝和浅拷贝
```
浅拷贝只复制一层,共享引用;深拷贝递归复制所有,完全独立

浅拷贝:
const obj = {name: '张三'};
Object.assign({}, obj);

const obj2 = {a: 1, b: {c: 2}};
const obj3 = {...obj2};

深拷贝:
JSON.parse(JSON.stringify()); // 不推荐
structuredClone(); // 推荐
lodash.cloneDeep(); // 推荐
手写深拷贝:
function deepClone(obj, hash = new WeakMap()) {
  if (obj === null || typeof obj !== 'object') return obj;

  if (hash.has(obj)) return hash.get(obj); // 处理循环引用

  let cloneObj = Array.isArray(obj) ? [] : {};
  hash.set(obj, cloneObj); // 存入哈希表

  for (let key in obj) {
    if (Object.prototype.hasOwnProperty.call(obj, key)) {
      cloneObj[key] = deepClone(obj[key], hash); // 递归
    }
  }

  return cloneObj;
}
```

6. 数组的常用方法

## 修改原数组的方法
* push() - 末尾添加元素，返回新长度
* pop() - 删除末尾元素，返回被删除元素
* unshift() - 开头添加元素，返回新长度
* shift() - 删除开头元素，返回被删除元素
* splice() - 删除/插入/替换元素，返回被删除元素数组
* reverse() - 反转数组
* sort() - 排序数组

## 不修改原数组的方法
* concat() - 连接数组，返回新数组
* slice() - 截取数组片段，返回新数组
* join() - 数组转字符串
* indexOf() - 查找元素索引，找不到返回-1
* includes() - 判断是否包含元素，返回布尔值

## 遍历方法
* forEach() - 遍历数组，无返回值
* map() - 遍历并返回新数组
* filter() - 过滤元素，返回新数组
* find() - 查找第一个满足条件的元素
* findIndex() - 查找第一个满足条件的元素索引
* some() - 判断是否有元素满足条件
* every() - 判断是否所有元素都满足条件
* reduce() - 累加器，返回单个值

7. 字符串的常用方法

## 查找和匹配
* indexOf() - 查找子字符串位置，找不到返回-1
* includes() - 判断是否包含子字符串，返回布尔值
* startsWith() - 判断是否以指定字符串开头
* endsWith() - 判断是否以指定字符串结尾
* match() - 正则匹配，返回匹配结果数组
* search() - 正则搜索，返回匹配位置

## 截取和分割
* slice() - 截取字符串片段，支持负数索引
* substring() - 截取字符串片段，不支持负数
* split() - 按分隔符分割字符串，返回数组

## 大小写转换
* toUpperCase() - 转大写
* toLowerCase() - 转小写

## 去除空白
* trim() - 去除首尾空白字符
* trimStart() - 去除开头空白字符
* trimEnd() - 去除结尾空白字符

## 替换和填充
* replace() - 替换第一个匹配的子字符串
* replaceAll() - 替换所有匹配的子字符串
* padStart() - 开头填充到指定长度
* padEnd() - 结尾填充到指定长度

8. 对象遍历方式对比

## 遍历方法对比
| 方法 | 遍历内容 | 返回值 | 是否包含原型链 | 适用场景 |
|------|----------|--------|----------------|----------|
| for...in | 可枚举属性名 | key | ✅ 是 | 遍历所有属性 |
| Object.keys() | 自身可枚举属性名 | key数组 | ❌ 否 | 只要自身属性 |
| Object.values() | 自身可枚举属性值 | value数组 | ❌ 否 | 只要属性值 |
| Object.entries() | 自身可枚举键值对 | [key,value]数组 | ❌ 否 | 键值对操作 |
| Object.getOwnPropertyNames() | 自身所有属性名 | key数组 | ❌ 否 | 包含不可枚举 |
| Object.getOwnPropertySymbols() | 自身Symbol属性 | Symbol数组 | ❌ 否 | 包含不可枚举 |

9. 执行上下文和作用域

## 执行上下文 (Execution Context)
* **全局执行上下文** - 代码开始执行时创建，只有一个
* **函数执行上下文** - 每次调用函数时创建

## 执行上下文包含
* **变量对象** - 存储变量和函数声明
* **作用域链** - 当前作用域到全局作用域的链
* **this指向** - 当前执行环境的this值

## 作用域类型
* **全局作用域** - 全局变量，任何地方都能访问
* **函数作用域** - 函数内部变量，外部无法访问
* **块级作用域** - let/const在{}内创建的作用域

## 作用域链查找规则
* 从当前作用域开始查找
* 找不到就向上一级作用域查找
* 直到全局作用域，找不到返回undefined

10. this指向

## this指向规则
* **全局环境** - this指向全局对象(window/global)
* **函数调用** - this指向全局对象(严格模式下为undefined)
* **方法调用** - this指向调用该方法的对象
* **构造函数** - this指向新创建的实例对象
* **箭头函数** - this指向定义时的外层作用域
* **call/apply/bind** - this指向指定的对象

## this指向优先级
1. **new绑定** - new Person() > this指向新实例
2. **显式绑定** - call/apply/bind > this指向指定对象
3. **隐式绑定** - obj.method() > this指向obj
4. **默认绑定** - 函数调用 > this指向全局对象

11. 闭包

## 闭包定义
* 函数能够访问外部作用域的变量
* 即使外部函数执行完毕，内部函数仍能访问外部变量
* 闭包 = 函数 + 外部作用域

## 闭包形成条件
* 函数嵌套
* 内部函数引用外部函数的变量
* 内部函数被返回或传递

## 闭包优缺点
### 优点
* 实现数据私有化
* 保持变量状态
* 避免全局污染
* 实现模块化

### 缺点
* 内存占用增加
* 可能导致内存泄漏
* 调试困难
* 性能影响

12. 高阶函数和柯里化

## 高阶函数 (Higher-Order Function)
* 接受函数作为参数
* 返回一个函数
* 或者两者兼有

## 常见高阶函数
* **map()** - 映射，返回新数组
* **filter()** - 过滤，返回满足条件的元素
* **reduce()** - 归约，返回单个值
* **forEach()** - 遍历，无返回值
* **find()** - 查找，返回第一个满足条件的元素
* **some()** - 判断，是否有元素满足条件
* **every()** - 判断，是否所有元素都满足条件

## 柯里化 (Currying)
* 将多参数函数转换为单参数函数序列
* 每次只接受一个参数
* 返回接受下一个参数的函数

13. 防抖和节流

## 防抖 (Debounce)
* 延迟执行，重复调用时重新计时
* 在事件停止触发后延迟执行
* 适用于搜索框输入、窗口resize等

## 节流 (Throttle)
* 限制执行频率，固定时间间隔执行一次
* 在指定时间内只执行一次
* 适用于滚动事件、鼠标移动等

## 实现对比
```javascript
// 防抖实现
function debounce(func, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// 节流实现
function throttle(func, limit) {
  let inThrottle;
  return function(...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}
```

14. 纯函数、副作用和函数式编程

## 纯函数 (Pure Function)
* 相同输入总是产生相同输出
* 不依赖外部状态
* 不产生副作用
* 可预测、可测试、可缓存

## 副作用 (Side Effects)
* 修改外部状态
* 修改参数
* 执行I/O操作
* 抛出异常
* 调用其他有副作用的函数

## 函数式编程原则
* **不可变性** - 不修改现有数据，创建新数据
* **纯函数** - 避免副作用
* **高阶函数** - 函数作为参数或返回值
* **函数组合** - 组合简单函数创建复杂功能

## 最佳实践
* 优先使用纯函数
* 避免修改外部状态
* 使用不可变数据结构
* 组合简单函数
* 分离副作用和纯逻辑

15. 事件循环

## 事件循环机制
* JavaScript是单线程语言
* 通过事件循环实现异步执行
* 基于调用栈、任务队列、微任务队列

## 执行顺序
1. **同步代码** - 立即执行
2. **微任务** - Promise.then
3. **宏任务** - setTimeout、setInterval、DOM事件

## 执行流程
1. 执行同步代码
2. 检查微任务队列，执行所有微任务
3. 检查宏任务队列，执行一个宏任务
4. 重复步骤2-3

16. Promise.all、allSettled、race、any 用法对比

## Promise.all()
* 所有Promise都成功时返回结果数组
* 任何一个失败立即返回失败原因
* 适用于需要所有操作都成功的场景

## Promise.allSettled()
* 等待所有Promise完成（无论成功失败）
* 返回每个Promise的状态和结果
* 适用于需要知道所有操作结果的场景

## Promise.race()
* 返回第一个完成的Promise结果
* 成功或失败都返回
* 适用于超时控制、竞速场景

## Promise.any()
* 返回第一个成功的Promise结果
* 只有所有都失败才返回失败
* 适用于容错处理、备用方案

## 示例代码
```javascript
const promise1 = Promise.resolve(1);
const promise2 = Promise.resolve(2);
const promise3 = Promise.reject('错误');

// Promise.all - 全部成功才成功
Promise.all([promise1, promise2])
  .then(results => console.log('all成功:', results)) // [1, 2]
  .catch(error => console.log('all失败:', error));

Promise.all([promise1, promise2, promise3])
  .then(results => console.log('all成功:', results))
  .catch(error => console.log('all失败:', error)); // '错误'

// Promise.allSettled - 等待所有完成
Promise.allSettled([promise1, promise2, promise3])
  .then(results => console.log('allSettled:', results));
  // [
  //   { status: 'fulfilled', value: 1 },
  //   { status: 'fulfilled', value: 2 },
  //   { status: 'rejected', reason: '错误' }
  // ]

// Promise.race - 第一个完成
Promise.race([promise1, promise2, promise3])
  .then(result => console.log('race成功:', result)) // 1
  .catch(error => console.log('race失败:', error));

// Promise.any - 第一个成功
Promise.any([promise1, promise2, promise3])
  .then(result => console.log('any成功:', result)) // 1
  .catch(error => console.log('any失败:', error));
```

17. 原型和原型链

## 原型 (Prototype)
* 每个函数都有一个prototype属性
* 指向一个对象，包含共享的属性和方法
* 实例对象通过__proto__访问原型

## 原型链 (Prototype Chain)
* 对象查找属性时的查找路径
* 从自身开始，沿原型链向上查找
* 直到找到属性或到达Object.prototype

## 示例代码
```javascript
// 构造函数
function Person(name) {
  this.name = name;
}

// 在原型上添加方法
Person.prototype.sayHello = function() {
  return `Hello, I'm ${this.name}`;
};

// 创建实例
const person = new Person('张三');

// 原型链查找
console.log(person.name);        // '张三' - 自身属性
console.log(person.sayHello());  // 'Hello, I'm 张三' - 原型方法
console.log(person.toString());  // '[object Object]' - Object原型方法

// 原型链关系
console.log(person.__proto__ === Person.prototype);           // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null);             // true
console.log(Object.prototype.constructor === Object);         // true
```

## 原型链查找规则
1. 查找对象自身属性
2. 查找对象原型属性
3. 查找原型链上层属性
4. 直到Object.prototype或null

18. 常见继承方式和class

## 原型链继承
* 子类原型指向父类实例
* 问题：引用类型共享、无法传参

## 构造函数继承
* 子类调用父类构造函数
* 问题：无法继承父类原型方法

## 组合继承
* 结合原型链和构造函数继承
* 问题：调用两次父类构造函数

## 寄生组合继承
* 最优解决方案
* 只调用一次父类构造函数

## ES6 Class继承
* 语法糖，底层仍是原型链
* 支持super、static、extends

## 示例代码
```javascript
// 1. 原型链继承
function Parent() {
  this.name = 'parent';
}
Parent.prototype.sayHello = function() {
  return `Hello from ${this.name}`;
};

function Child() {
  this.age = 10;
}
Child.prototype = new Parent(); // 原型链继承

// 2. 构造函数继承
function Child2() {
  Parent.call(this); // 构造函数继承
  this.age = 10;
}

// 3. 组合继承
function Child3() {
  Parent.call(this); // 构造函数继承
  this.age = 10;
}
Child3.prototype = new Parent(); // 原型链继承

// 4. 寄生组合继承（最优）
function Child4() {
  Parent.call(this);
  this.age = 10;
}
Child4.prototype = Object.create(Parent.prototype);
Child4.prototype.constructor = Child4;

// 5. ES6 Class继承
class ParentClass {
  constructor(name) {
    this.name = name;
  }
  
  sayHello() {
    return `Hello from ${this.name}`;
  }
  
  static getType() {
    return 'Parent';
  }
}

class ChildClass extends ParentClass {
  constructor(name, age) {
    super(name); // 调用父类构造函数
    this.age = age;
  }
  
  sayHello() {
    return `${super.sayHello()}, I'm ${this.age} years old`;
  }
}

const child = new ChildClass('张三', 25);
console.log(child.sayHello()); // "Hello from 张三, I'm 25 years old"
console.log(ChildClass.getType()); // "Parent"
```

## 继承方式对比
| 方式 | 优点 | 缺点 | 推荐度 |
|------|------|------|--------|
| 原型链继承 | 简单 | 引用类型共享、无法传参 | ❌ |
| 构造函数继承 | 可传参、不共享引用 | 无法继承原型方法 | ❌ |
| 组合继承 | 结合两者优点 | 调用两次父类构造函数 | ⚠️ |
| 寄生组合继承 | 最优解决方案 | 代码复杂 | ✅ |
| ES6 Class | 语法简洁、功能完整 | 需要ES6支持 | ✅ |

## Class特性
* **extends** - 继承父类
* **super** - 调用父类方法/构造函数
* **static** - 静态方法
* **constructor** - 构造函数
* **getter/setter** - 属性访问器

19. Proxy

## Proxy 定义
* ES6新增的元编程特性
* 创建对象的代理，拦截并自定义操作
* 在目标对象前设置"拦截器"

## 基本语法
```javascript
const proxy = new Proxy(target, handler);
```

## 常用拦截器
* **get** - 拦截属性读取
* **set** - 拦截属性设置
* **has** - 拦截 in 操作符
* **deleteProperty** - 拦截 delete 操作
* **ownKeys** - 拦截 Object.keys()

## 实际应用
```javascript
// 1. 数据验证
const user = new Proxy({}, {
  set(target, key, value) {
    if (key === 'age' && value < 0) {
      throw new Error('年龄不能为负数');
    }
    target[key] = value;
    return true;
  }
});

// 2. 响应式数据
const reactive = (obj) => {
  return new Proxy(obj, {
    get(target, key) {
      console.log(`读取 ${key}`);
      return target[key];
    },
    set(target, key, value) {
      console.log(`设置 ${key} = ${value}`);
      target[key] = value;
      return true;
    }
  });
};

// 3. 属性访问控制
const secureObj = new Proxy({}, {
  get(target, key) {
    if (key.startsWith('_')) {
      throw new Error('私有属性不可访问');
    }
    return target[key];
  }
});
```

## Proxy vs Object.defineProperty
| 特性 | Proxy | Object.defineProperty |
|------|-------|----------------------|
| 拦截操作 | 13种拦截器 | 只能拦截get/set |
| 数组支持 | ✅ 完美支持 | ❌ 需要特殊处理 |
| 性能 | 相对较慢 | 相对较快 |
| 兼容性 | ES6+ | ES5+ |

20. 手写ajax请求

## XMLHttpRequest（经典写法）
```javascript
function ajax({ method = 'GET', url, data = null, headers = {} }) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open(method, url, true);
    Object.entries(headers).forEach(([k, v]) => xhr.setRequestHeader(k, v));
    xhr.onreadystatechange = () => {
      if (xhr.readyState !== 4) return;
      if (xhr.status >= 200 && xhr.status < 300) resolve(xhr.responseText);
      else reject(new Error(xhr.status));
    };
    xhr.onerror = () => reject(new Error('Network Error'));
    xhr.send(data);
  });
}
```

## fetch（更简洁）
```javascript
fetch('/api/user', { method: 'GET' })
  .then(r => r.json())
  .then(console.log)
  .catch(console.error);
```

21. 事件模型

## 捕获 → 目标 → 冒泡（三阶段）
* addEventListener(type, listener, options)
* options.capture: true 表示捕获阶段触发
* options.once: 一次性监听
* options.passive: 不调用 preventDefault

```javascript
div.addEventListener('click', handler, { capture: true }); // 捕获
div.addEventListener('click', handler); // 冒泡（默认）
```

22. 事件委托

## 原理
* 利用冒泡，把子元素事件委托到共同祖先处理
* 优点：减少监听器数量、支持动态元素

```javascript
ul.addEventListener('click', (e) => {
  const target = e.target;
  if (target && (target.tagName === 'LI' || target.matches('li'))) {
    console.log('点击了项：', target.dataset.id);
  }
});
```

23. dom操作

## 选择
* document.querySelector / querySelectorAll
* getElementById / getElementsByClassName

## 创建/插入/删除
```javascript
const el = document.createElement('div');
el.textContent = 'hello';
parent.appendChild(el);
// parent.insertBefore(el, ref);
// el.remove();
```

## 属性/样式/class
```javascript
el.setAttribute('role', 'button');
el.style.color = 'red';
el.classList.add('active');
```

24. bom对象

* window：全局对象，定时器 setTimeout/setInterval
* location：URL 信息与跳转（href、search、hash、reload）
* history：前进后退（back、forward、go）
* navigator：UA/平台信息
* screen：屏幕信息

```javascript
location.href = 'https://example.com';
history.back(); // 返回上一页
setTimeout(() => console.log('tick'), 1000);
```

25. 本地存储

## localStorage / sessionStorage
* 键值对字符串存储；localStorage 长期，sessionStorage 会话
```javascript
localStorage.setItem('token', 'abc');
localStorage.getItem('token');
localStorage.removeItem('token');
```

## Cookie（对比）
* 小容量、可设过期、可携带到服务器（同域请求）
* document.cookie 读写麻烦，通常用库封装
* 安全属性：Secure、HttpOnly（前端不可读）、SameSite、Domain/Path、Expires/Max-Age
