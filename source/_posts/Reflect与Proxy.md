---
title: Reflect与Proxy
date: 2021-07-07 09:14:34
tags: [es6,]
categories: JS
---

很早就听说Vue3的数据劫持是基于Proxy实现的，这是es6的新特性，现在就让我们去看看它的真面目吧。
<!--more-->

# 概述
Proxy与Reflect是ES6为了操作对象引入的API。
Proxy可以对目标对象的读取、函数调用等操作进行拦截，然后进行操作处理。它不直接操作对象，而是像代理模式，通过对象的代理对象进行操作。在进行这些操作时，可以添加一些需要的额外操作。
Reflect可以用于获取目标对象的行为，它与Object类似，但是更易读，为操作对象提供了一种更优雅的方式，它的方法与Proxy是对应的。


# 基本用法

### 术语

handler
    包含捕捉器（trap）的占位符对象，可译为处理器对象。

tarps
   提供属性访问方法。这类似于操作系统中捕获器的概念。

target
    被Proxy代理虚拟化的对象。它常被作为代理的储存后端。

### 语法

~~~js
const handler = {
    ...tarps
}
const p = new Proxy (target,handler)
~~~

## 实例方法

>#### handler.get
~~~js
get(target,propKey,receiver){
    //do something
}
~~~

target：代理的目标对象
propKey：属性名
receiver：代理对象实例

~~~js
let exam ={
    name: "Tom",
    age: 24
}
let proxy = new Proxy(exam, {
  get(target, propKey, receiver) {
    console.log('Getting ' + propKey);
    return target[propKey];
  }
})
proxy.name 
// Getting name
// "Tom"
~~~
get()方法可以继承

~~~js
let proxy = new Proxy({}, {
  get(target, propKey, receiver) {
      // 实现私有属性读取保护
      if(propKey[0] === '_'){
          throw new Erro(`Invalid attempt to get private     "${propKey}"`);
      }
      console.log('Getting ' + propKey);
      return target[propKey];
  }
});
 
let obj = Object.create(proxy);
obj.name  // Getting name
~~~

>#### handler.set 

~~~js
set(target,propKey,value,receiver){
    //do something
}
~~~

target：代理的目标对象
propKey：属性名
value: 新的属性值
receiver：代理对象实例

~~~js
let validator = {
    set: function(obj, prop, value) {
        if (prop === 'age') {
            if (!Number.isInteger(value)) {
                throw new TypeError('The age is not an integer');
            }
            if (value > 200) {
                throw new RangeError('The age seems invalid');
            }
        }
        // 对于满足条件的 age 属性以及其他属性，直接保存
        obj[prop] = value;
    }
};
let proxy= new Proxy({}, validator)
proxy.age = 100;
proxy.age           // 100
proxy.age = 'oppps' // 报错
proxy.age = 300     // 报错
~~~

receiver表示原始操作行为所在对象，一般是Proxy实例本身

~~~js
const handler = {
    set: function(obj, prop, value, receiver) {
        obj[prop] = receiver;
    }
};
const proxy = new Proxy({}, handler);
proxy.name= 'Tom';
proxy.name=== proxy // true
 
const exam = {}
Object.setPrototypeOf(exam, proxy)
exam.name = "Tom"
exam.name === exam // true
~~~
注意，严格模式下，set代理如果没有返回true，就会报错。
>#### handler.apply 

~~~js
apply(target,ctx,args){
    //do something
}
~~~

target：代理的目标对象
ctx:表示目标对象上下文
args表示目标对象的参数数组

~~~js
function sub(a, b){
    return a - b;
}
let handler = {
    apply: function(target, ctx, args){
        console.log('handle apply');
        return Reflect.apply(...arguments);
    }
}
let proxy = new Proxy(sub, handler)
proxy(2, 1) 
// handle apply
// 1
~~~
>#### handler.has

~~~js
has(target,propKey){
    //do something
}
~~~

target：代理的目标对象
propKey:属性名

用于拦截HasProperty操作，即判断target对象是否存在proKey属性时，会被这个方法拦截。此方法不判断一个属性是对象自身的属性，还是继承的属性。
~~~js
let  handler = {
    has: function(target, propKey){
        console.log("handle has");
        return propKey in target;
    }
}
let exam = {name: "Tom"}
let proxy = new Proxy(exam, handler)
'name' in proxy
// handle has
// true
~~~

注意，此方法不拦截for...in循环。

>#### handler.construct

~~~js
construct(target,args){
    //do something
}
~~~

target：代理的目标对象
args:参数

~~~js
let handler = {
    construct: function (target, args, newTarget) {
        console.log('handle construct')
        return Reflect.construct(target, args, newTarget)  
    }
}
class Exam { 
    constructor (name) {  
        this.name = name 
    }
}
let ExamProxy = new Proxy(Exam, handler)
let proxyObj = new ExamProxy('Tom')
console.log(proxyObj)
// handle construct
// exam {name: "Tom"}
~~~

>#### handler.deleteProperty

~~~js
deleteProperty(target,propKey){
    //do something
}
~~~

target：代理的目标对象
propKey:属性名

~~~js
let handler = {
            deleteProperty(target,propKey){
                console.log(target,propKey);
                delete target[propKey]
            }
        }
        let obj = {
            name:"jack",
            age:18
        }
       let p = new Proxy(obj,handler)
       delete p.age

       console.log(obj);
~~~
用于拦截delete操作

>#### handler.defineProperty

~~~js
defineProperty(target, propKey, propDesc){
    //do something
}
~~~

target：代理的目标对象
propKey:属性名
propDesc: 描述

该方法会拦截目标对象的以下操作
Object.defineProperty()
Reflect.defineProperty()
proxy.property='value'

>#### handler.getOwnPropertyDescriptor

~~~js
getOwnPropertyDescriptor(target, propKey){
    //do something
}
~~~

用于拦截Object.getOwnPropertyDescriptor操作,返回值为当前属性的配置


~~~js
let handler = {
            getOwnPropertyDescriptor: function (target, propKey) {
                return Reflect.getOwnPropertyDescriptor(target, propKey);
            }
        }
        let target = { name: "Tom" }
        let proxy = new Proxy(target, handler)
        console.log(Reflect.getOwnPropertyDescriptor(proxy, 'name'));
        
~~~

>#### handler.getPrototypeOf

~~~js
getPrototypeOf(target){
    //do something
}
~~~

主要用于拦截获取对象原型的操作。包括以下操作：

- Object.prototype._proto_
- Object.prototype.isPrototypeOf()
- Object.getPrototypeOf()
- Reflect.getPrototypeOf()
- instanceof

~~~js
let exam = {}
let proxy = new Proxy({},{
    getPrototypeOf: function(target){
        return exam;
    }
})
Object.getPrototypeOf(proxy) // {}
~~~


>#### handler.isExtensible

~~~js
isExtensible(target){
    //do something
}
~~~
用于拦截 Object.isExtensible 操作。

该方法只能返回布尔值，否则返回值会被自动转为布尔值。

>#### handler.ownKeys

~~~js
ownKeys(target){
    //do something
}
~~~

用于拦截对象自身属性的读取操作。主要包括以下操作：

- Object.getOwnPropertyNames()
- Object.getOwnPropertySymbols()
- Object.keys()
- for...in

~~~js
let proxy = new Proxy( {
  name: "Tom",
  age: 24
}, {
    ownKeys(target) {
        return ['name'];
    }
});
Object.keys(proxy)
// [ 'name' ]返回结果中，三类属性会被过滤：
//          - 目标对象上没有的属性
//          - 属性名为 Symbol 值的属性
//          - 不可遍历的属性
 
let target = {
  name: "Tom",
  [Symbol.for('age')]: 24,
};
// 添加不可遍历属性 'gender'
Object.defineProperty(target, 'gender', {
  enumerable: false,
  configurable: true,
  writable: true,
  value: 'male'
});
let handler = {
    ownKeys(target) {
        return ['name', 'parent', Symbol.for('age'), 'gender'];
    }
};
let proxy = new Proxy(target, handler);
Object.keys(proxy)
// ['name']
~~~
注意：返回结果中，三类属性会被过滤：
     - 目标对象上没有的属性
     - 属性名为 Symbol 值的属性
     - 不可遍历的属性

>#### handler.preventExtensions

~~~js
preventExtensions(target){
    //do something
}
~~~

拦截Object.preventExtensions 操作

该方法必须返回一个布尔值，否则会自动转为布尔值。

>#### handler.setPrototypeOf

~~~js
setPrototypeOf(target){
    //do something
}
~~~

主要用来拦截 Object.setPrototypeOf 方法。

返回值必须为布尔值，否则会被自动转为布尔值。

若目标对象不可扩展，setPrototypeOf 方法不得改变目标对象的原型。
~~~js
let proto = {}
let proxy = new Proxy(function () {}, {
    setPrototypeOf: function(target, proto) {
        console.log("setPrototypeOf");
        Reflect.setPrototypeOf(target, proto)
        return true;
    }
}
);
Object.setPrototypeOf(proxy, proto);
~~~


>#### Proxy.revocable()

用于返回一个可取消的Proxy实例

~~~js
let {proxy, revoke} = Proxy.revocable({}, {});
proxy.name = "Tom";
revoke();
proxy.name 
// TypeError: Cannot perform 'get' on a proxy that has been revoked
~~~

## Reflect

ES6 中将 Object 的一些明显属于语言内部的方法移植到了 Reflect 对象上（当前某些方法会同时存在于 Object 和 Reflect 对象上），未来的新方法会只部署在 Reflect 对象上。

Reflect 对象对某些方法的返回结果进行了修改，使其更合理。

Reflect 对象使用函数的方式实现了 Object 的命令式操作。