---
title: 自己封装myLocalStorage，使其有有效期
date: 2018-07-20 16:30:02
tags: [localStorage, cookie]
description: 项目中遇见 cookie 值存不上，what fuck？什么情况，不知道。用$.cookie 和原生的 cookie 都不行，存上值，就被删了一样。找不见地方。考虑到项目比较大，去找得代价，还不如换种实现方式，于是就想起来用 localStorage 了。然后 localStorage 又不像 $.cookie 那样有过期时间。因此需要自己去封装一下，故而，有了这个一个博客。勿喷！
---
项目中遇见 cookie 值存不上，what fuck？什么情况，不知道。用$.cookie 和原生的 cookie 都不行，存上值，就被删了一样。找不见地方。考虑到项目比较大，去找得代价，还不如换种实现方式，于是就想起来用 localStorage 了。然后 localStorage 又不像 $.cookie 那样有过期时间。因此需要自己去封装一下，故而，有了这个一个博客。勿喷！  

## 调用方式
`setItem()`与 $.cookie() 类似，expires 支持传入一个时间对象（object），有效时间的数值（number， 单位：毫秒 或者 天）；  
当 expires 值类型为 number ，且值小于等于当年的总天数时，表示的为天，否则表示毫秒；  
光说，不行，看看下面：  
调用方式:
```js
var myLocalStorage = require('');
myLocalStorage.setItem("test1", "Mobro1", {expires: 1});
myLocalStorage.setItem(
    "test2", 
    "Mobro2", 
    {expires: new Date(new Date().getTime() + 1000 * 60 * 60 * 24)}
    );
myLocalStorage.setItem("test3", "Mobro3", {expires: 1000 * 60 * 10});
```
test1: 有效期为 1 天。  
test2: 有效期为 1 天。  
test3: 有效期为 10 分钟。  

`getItem()` 与 $.cookie() 也是类似的，取那个 localStorage 值，只需要传入一个 key 键名就好了。若没有找到 当前 key 对应的 localStorage 值，则返回 null 对象。  
`removeItem()` 删除一个 localStorage 值，删除成功，返回true，否则返回 false  

继续往下看：
```js
myLocalStorage.getItem("test1")  // Mobro1
myLocalStorage.removeItem("test1");  // 如果成功
myLocalStorage.getItem("test1")  // null
```

代码纯属手敲，逻辑上一定会有什么漏洞的，还望各位大牛不吝赐教。

----

只与同好争高下，不与傻瓜论短长。  -- 《欢乐颂》电视剧台词。  

最近看了一部日本的电影，《希望在别处》，感觉还是不错的，给推荐一下。    


四川老家大雨，火车拿去压桥了；   
读大学的地方兰州大雨，车子被冲跑了；  
工作地北京大雨，天天上班穿拖鞋，每天丢一把伞；   
哈哈哈哈  
    >oo<

----

## 完整源码  
```js
var myLocalStorage = {
    setItem: function(key, value, exp) {
        var obj = new Object(),
            expires = exp.expires || null;
        obj.value = value;
        obj.time = new Date().getTime();

        // expires 类型
        if (expires === null) {
            delete obj.expires;
        } else if (typeof expires === "object") {
            obj.expires = expires.getTime() - obj.time;
        } else if (typeof expires === "number") {
            if ((expires | 0) !== expires) {
                throw new Error("expires must be integer number!");
            }

            // expires 小于 365、366 则，按天算。否则按时间戳算
            if (expires <= getYearDays() && expires > 0) {
                obj.expires = expires * 1000 * 60 * 60 * 24;
            } else if (expires > getYearDays()) {
                obj.expires = expires;
            } else if (expires <= 0) {
                this.removeItem(key);
            }
        }
        localStorage.setItem(key, JSON.stringify(obj));
    },
    getItem: function(key) {
        var obj = JSON.parse(localStorage.getItem(key));
        if (obj === "null" || obj === null) return null;
        var expires = obj.expires, 
            now = new Date().getTime(), 
            time = obj.time;

        if (now - time >= expires ||
            now < time) {
            localStorage.removeItem(key);
            return null;
        } else {
            return obj.value;
        }
    },
    
    // 删除成功返回 true， 否则返回 false
    removeItem: function(key) { 
        if (this.getItem(key) !== null) {
            localStorage.removeItem(key);

            return this.getItem(key) === null ? true : false;
        }
        return true;
    },
    keys: function(key) {

    }
}
function getYearDays () {
    return new Date().getFullYear() % 4 === 0 ? 366 : 365;
}


module.exports = myLocalStorage;

```