---
title: >-
  论Object.keys(), Object.getOwnPropertyNames(), for in,
  Object.getOwnPropertySymbol()区别
date: 2018-06-12 15:37:26
tags: es6
---
前不久，一朋友求助，让我给解释一波Object.keys(), Object.getOwnPropertyNames(), for in的区别，面试中好几次呗问了。所以，抽了点时间看了看，大概把我看的过程中做的demo记录一下。  
看了网上一些关于介绍Object.keys(), Object.getOwnPropertyNames(), for in, Object.getOwnPropertySymbol()的区别，都介绍得不是很详细，不容易理解，今天抽空来说说这三者的区别。  
废话不多说，直接上代码。

defineProperty() =>  给对象添加单个属性及属性值的，并规定该属性是否可枚举，默认是不可枚举的；所以正常情况下，我们给对象添加单个属性时，只需要用.操作即可。亦，`obj.e = 2`。这种方式添加的属性默认是可枚举的。
我们通过设置 `enumerable` 的值来设置属性是否可枚举。
来，下面demo运行起来。
```js
var obj = { a: "1", b: "2"};
Object.prototype.protoName = "proto foobar";
var foo = Symbol("foo");
// 给obj 添加了一个键名为 c, foo 变量值
Object.defineProperty(obj, "c", {
    value: "3", 
    enumerable: false  // 定义是否可枚举，默认 false
})
Object.defineProperty(obj, "d", {
    value: "4", 
    enumerable: true  // 定义是否可枚举
})
Object.defineProperty(obj, foo, {
    value: "foobar",
    enumerable:false
});  
```

打印的结果为：  
```js
{a: "1", b: "2", d: "4", c: "3", Symbol(foo): "foobar"}
```
OK，接下来我们，我们输出日志看看...
```js
for (var i in obj) {
    console.log(" for in : ", i); // 输出 a b d protoName
    if (obj.hasOwnProperty(i)) {
        console.log("obj.hasOwnProperty(i): ", i); // a b d
    }
}
console.log("Object.keys(obj): ", Object.keys(obj));  // ["a", "b", "d"]
console.log("Object.getOwnPropertyNames(obj): ", Object.getOwnPropertyNames(obj));  // ["a", "b", "c", "d"]
console.log("Object.getOwnPropertySymbols(obj): ", Object.getOwnPropertySymbols(obj)); // [Symbol(foo)]
// JSON.stringify(obj) 只能将自身的属性转化成字符串
console.log("JSON.stringify(obj): ", JSON.stringify(obj));  // {"a": "1", "b": "2", "d": "4"}
```
就上面demo，简单总结一下：  
1. _keys() => 输出为一个数组，取到的是 obj 的所有的自身可枚举属性。其功能和for in + hasOwnProperty（）功能是一致的_
2. _getOwnPropertyNames() =>  输出为一个数组，取到的是 obj 的所有自身属性。（可枚举、不可枚举，但不包括symbol类型的属性）_
3. _getOwnPropertySymbol() => 输出为一个数组，取到的是 obj 的所有自身的Symbol属性_
4. _for in => 遍历 obj 的所有属性，包括自身属性和原型上的所有可枚举的属性_
5. _JSON.stringify() => 只能将自身的可枚举的属性转化成字符串_  


OK, 运行完上面demo后发现，问题是不是轻轻松松地解决了喂，然而，并没有。仔细对比一下上面的日志，然后，来，接着往下走。  
for in obj => 能遍历obj的所有属性，包括原型上的属性。（包括原型上的可枚举的属性和方法），有点蒙比了？
如果确实蒙比了，我们接着往下敲代码  
```js
Object.defineProperty(obj, "func1", {
    value: function() {console.log("func1")}
})
Object.defineProperty(obj, "func2", {
    value: function() {
        console.log("func2")
    },
    enumerable: true
})

// 来，我们在在原型上添加一个方法
Object.prototype.func3 = function() {
    console.log("func3")
}

for (var i in obj) {
    console.log(" for in : ", i); // 输出 a b d protoName func2 func3
    if (obj.hasOwnProperty(i)) {
        console.log("obj.hasOwnProperty(i): ", i); // a b d func2
    }
}
console.log("Object.keys(obj): ", Object.keys(obj));  // ["a", "b", "d", "func2"]
console.log("Object.getOwnPropertyNames(obj): ", Object.getOwnPropertyNames(obj));  // ["a", "b", "c", "d", "func1", "func2"]
console.log("Object.getOwnPropertySymbols(obj): ", Object.getOwnPropertySymbols(obj)); // [Symbol(foo)]
console.log("JSON.stringify(obj): ", JSON.stringify(obj));  //  {"a":"1","b":"2","d":"4"}
console.log("obj:  ", obj); // {a: "1", b : "2", d : "4", func2 : ƒ (), c : "3", func1 : ƒ (), Symbol(foo) : "foobar"}
```
对比上面输出日志，我不难发现：
1. _defineProperty() 和 prototype 添加的属性默认是不可枚举的。_
2. _defineProperty() 添加的属性时属于自身属性。_  

哈哈哈，能读到这儿，我想未来的我，可能没有晕吧。  
管他呢，简单提个醒吧，方便以后自己更快速地懂这些东西；  
1. _for in 没有 func1， 是因为defineProperty()定义的是自身属性，且默认不可枚举。protoName 是在原型 prototype 上，所以会有。_
2. _hasOwnProperty() 是只取自身属性，所以 原型上的 protoName， func3 都没有。_
3. _keys() 所有的自身可枚举属性， 所以 protoName 和 func3 首先排除了。c 和 func2 是不可枚举。_
4. _getOwnPropertyNames()  所有自身属性。所以原型上的protoName 和 func3 都被排除了。_
5. _JSON.stringify()  只要自身的，且可枚举的属性。认真看看，转字符串和直接输出的 obj 有多大区别。_

所以，网上关于深度克隆一个对象的方式 ---- `JSON.parse(JSON.stringify(obj))`，严格来说，是不正确的。  
然而此时，我就有问题要讲啦。  
实际项目中我们应该怎么选，如何规避上述的问题呢？  
还待高人指点迷津，  
抑或，以后自己去发现。  


---

今天被同事骗了，说 `撒拉黑` 是 `你好` 的意思。来了一个男同事，我就对他说了：  
-- 撒拉黑  
-- 滚犊子  
啊，留我一人在风中凌乱...  




       >|=|<