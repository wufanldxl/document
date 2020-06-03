[TOC]

# 关于对象

## 面向对象
### 1、如何限制只能创建一个实例

```javascript
const singletonify = (className) => {
  return new Proxy(className.prototype.constructor, {
    instance: null,
    construct: (target, argumentsList) => {
      if (!this.instance)
        this.instance = new target(...argumentsList);
      return this.instance;
    }
  });
}
```

> EX

```javascript
class MyClass {
  constructor(msg) {
    this.msg = msg;
  }

  printMsg() {
    console.log(this.msg);
  }
}

MySingletonClass = singletonify(MyClass);

const myObj = new MySingletonClass('first');
myObj.printMsg();           // 'first'
const myObj2 = new MySingletonClass('second');
myObj2.printMsg();           // 'first'
```



## 操作对象-增、删、改、查

### 深度查找对象

> 诸如获取a.b.c的值

```javascript
Object.defineProperty(Object.prototype,'depth',{
  value(from, ...selectors){
    const sel=[...selectors];
    if(!sel.length){throw new Error('selectors is null')}
    const selVal=s=>s
    .replace(/\[([^\[\]]*)\]/g, '.$1.')
    .split('.')
    .filter(t => t !== '')
    .reduce((prev, cur) => prev && prev[cur], from)
    return sel.length<=1?
      selVal(sel[0]):
      [...selectors].map(s =>
        selVal(s)
      );
  }
})


const obj = { selector: { to: { val: 'val to select' } }, target: [1, 2, { a: 'test' }] };
console.log(obj.depth(obj,'selector.to.val','target[0]'));
```



# 日常

## 常用

### url to obj的ES6的方案

```javascript
const getURLParameters = url =>
  (url.match(/([^?=&]+)(=([^&]*))/g) || []).reduce(
    (a, v) => ((a[v.slice(0, v.indexOf('='))] = v.slice(v.indexOf('=') + 1)), a),
    {}
);
```



### 一个简洁版的eventBus

```javascript
class eventBus {
  constructor() {
    this._hub={}
  }

  emit(event, data) {
    (this._hub[event] || []).forEach(handler => handler(data));
  }

  on(event, handler) {
    if (!this._hub[event]) this._hub[event] = [];
    this._hub[event].push(handler);
  }

  off(event, handler) {
    const i = (this._hub[event] || []).findIndex(h => h === handler);
    if (i > -1) this._hub[event].splice(i, 1);
    if (this._hub[event].length === 0) delete this._hub[event];
  }
}
```

