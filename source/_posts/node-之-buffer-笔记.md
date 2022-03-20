---
title: node 之 buffer 笔记
date: 2018-12-03 10:16:56
tags: [node, buffer]
---
**1. Buffer 相关**  
- js 最初是被设计来处理 html的，因此它不善于处理二进制数据，只有数值和字符串类型。而 node 是基于 js 的，因为 node 需要实现一些譬如数据库通信、操作图像及文件上传等，只是用字符串来操作完成这些任务是相当困难的，且费资源，速度缓慢，还不安全。所以，node 就引入了一个二进制缓冲区 Buffer 来实现。

- Buffer 类的另一个特别之处是数据占用的内存并不是分配在 js VM 内存堆中，这些对象不会被垃圾收集算法处理，它会占据一个不被修改的永久内存地址，这样就避免了因缓冲区内容的内存复制所造成的 cpu 浪费。
- 当创建一个已经被初始化的缓冲区时，缓冲区中包含的数据并非是 0，而是一些随机值。
```
var buf = new Buffer('1234asdf');
console.log(buf[i])  // i ∈ [0, 7] eg: buf[1] 可能为任意随机值 
```
- 如果某个位置设置为一个大于 255 的数，那么将会用 256 对概述取模，最终取模结果赋给该位置;设置为小数，则会取整；给超出边界的位置赋值，会失败，_缓冲区不会发生变化_
![在这里插入图片描述](/images/201812/20181108175706218.png)
- 可以从父缓冲区切分一块更小的缓冲区。子缓冲区延用父缓冲区的内存区域；父变子可能变；这种方式，父缓冲区在操作结束后依然继续保留，并不会并垃圾收集器回收，处理不慎很容易内存泄漏。可用 copy 方式来代替 slice 方式。
```
var buffer = new Buffer("My name is Mobro Zhu");
var childBuffer = buffer.slice(11);
console.log(childBuffer)
console.log(childBuffer.toString())
console.log(buffer.toString())	// 	缓冲区解码

// copy 方式
var childBuffer2 = new Buffer(11);
buffer.copy(childBuffer2, 0, 11)
// === buffer.copy(childBuffer2, 0, 11, 19)
console.log(childBuffer2.toString());	// Mobro Zhu
```

**2. Buffer API** (参考地址： https://nodejs.org/dist/latest-v10.x/docs/api/buffer.html)
`Class Method: Buffer.alloc(size[, fill[, encoding]])`   

声明一个 Buffer; size 是必须参数。这里的 fill 可以是数字，字符串，但好像不能是数组，对象；而且，"12" 和 12 的结果是不相同的；

与此类似的有：  
`Buffer.allocUnsafe(size)`  Buffer.alloc(size, fill) 永远不会使用这个内部的 Buffer 池，但如果 size 小于或等于 Buffer.poolSize 的一半， Buffer.allocUnsafe(size).fill(fill) 会使用这个内部的 Buffer 池.
`Buffer.allocUnsafeSlow(size)` 这个方法通过像大多数持久对象一样消除追踪与清理的需求，改善了性能与内存使用。
```
const buf = Buffer.alloc(10, "abc", "utf-8");
console.log(buf.toString()) 	// abc
//////
buf = Buffer.alloc(2)
buf.fill("12", "base64") 	// buf.fill(fill, encoding)
console.log(buf.toString("base64"))	// 为什么不是 12，而是 19c=

// base64 的声明方式
const buf = Buffer.alloc(11, 'aGVsbG8gd29ybGQ=', 'base64');
console.log(buf);
```
常见的 API:
`Buffer.byteLength(string[, encoding])`  字符串的实际字节长度。
`Buffer.from()`	
`Buffer.compare(buf1, buf2)`  类似 Array.compare()
`Buffer.concat(list[, totalLength])`	
`Buffer.from(array)`
`Buffer.from(arrayBuffer[, byteOffset[, length]])`
`Buffer.from(buffer)`
`Buffer.from(string[, encoding])`
`Buffer.from(object[, offsetOrEncoding[, length]])`
`Buffer.isBuffer(obj)`
`Buffer.isEncoding(encoding)`
`Buffer.poolSize` 缓冲池
```
Buffer.concat([buf1, buf2, buf3], totalLength)
```
常见的实例 API：
`buf.buffer`	指向创建该 Buffer 的底层的 ArrayBuffer 对象

```
// buf.buffer
const arrayBuffer = new ArrayBuffer(16);
const buffer = Buffer.from(arrayBuffer);
console.log(buffer.buffer === arrayBuffer);
```


---
沾沾人气听听热闹，人才不会固步自封，被憋死在孤独的绝壁里。 
人间烟火是好东西，琐碎而温暖。
一一 俗念亲 《每次跳楼，都看见那厮在铺救生气垫》

