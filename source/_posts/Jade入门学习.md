---
title: Jade入门学习
date: 2018-05-17 20:09:31
tags: [jade, pug]
---
jade是超高性能的node JavaScript模板引擎，有着非常强大的API和大量杰出的特性。它主要针对node的服务端。由于商标的原因，改为Pug，哈巴狗。Pug有它本身的缺点——可移植性差，调试困难，性能并不出色，但使用它可以加快开发效率。  
## 标签嵌套
使用缩进来表示标签间的嵌套关系，这样可以构建一个 HTML 代码的 __树状结构__ 语法  
``` html
ul
    li item A
    li item B
```
为了节省空间， Pug 嵌套标签提供了一种 __内联式__ 语法
``` html
a: img
```   
Pug知道哪些元素是自闭合的，为了语法的完整性，也可以通过在标签后加上 / 来明确声明此标签是 __自闭合__ 的
``` html
img
img/
input 
input/
```
HTML5的 __DOCTYPE__ 书写如下
``` html
doctype html
```
当然，也可以自定义一个 doctype 字面值。
``` html
doctype html PUBLIC "-//W3C//DTD XHTML Basic 1.1//EN"
```
## 内容
Pug 提供了三种常用的方式来放置内容  
+ 【管道文本】  
  这是最简单的向模板添加纯文本的方法。只需要在每行前面加一个 | 字符，这个字符在类 Unix 系统下常用作“管道”功能，因此得名  
``` html
| 纯文本当然也可以包括 <strong>HTML</strong> 内容。
p
  | 但它必须单独起一行。
```
+ 【标签内文本】
这实际上是最常见的情况，文本只需要和标签名隔开一个空格即可.
``` html
p 纯文本 <strong>HTML</strong> 内容
```
+ 【嵌入大段文本】
有时可能想要写一个大段文本块。比如嵌入脚本或者样式。只需在标签后面接一个 .即可。 注意： 不能有空格  
``` jade
script.
    if (usingPug)
        console.log('请用Pug')
    else 
        console.log('傻不拉几')
```

## 属性
1. 标签属性和 HTML 语法非常相似，它们的值就是普通的 JavaScript 表达式。可以用逗号作为属性分隔符，也可以不加逗号
2. 如果有很多属性，可以把它们分几行写
3. 如果有一个很长的属性，并且JavaScript运行时引擎支持ES2015模板字符串，可以使用它来写属性值

``` jade
// 1. 
a(href='baidu.com') 百度
= '\n'
a(class='button' href='baidu.com') 百度
= '\n'
a(class='button', href='baidu.com') 百度

// 2. 
input(
  type='checkbox'
  name='agreement'
  checked
)

// 3. 
input(data-json=`
  {
    "非常": "长的",
    "数据": true
  }
`)

```
4. 默认情况下，所有的属性都经过转义（即把特殊字符转换成转义序列）来防止诸如跨站脚本攻击之类的攻击方式。如果要使用特殊符号，需要使用 != 而不是 =  

注意： 未经转义的缓存代码十分危险。必须正确处理和过滤用户的输入来避免跨站脚本攻击
``` jade
div(escaped="<code>")
div(unescaped!="<code>")
```
5. 在Pug中，布尔值属性是经过映射的，这样布尔值(true和false)就能接受了。没有指定值时，默认是true

``` jade
input(type='checkbox' checked)
= '\n'
input(type='checkbox' checked=true)
= '\n'
input(type='checkbox' checked=false)
= '\n'
input(type='checkbox' checked=true.toString())
```
6. style（样式）属性可以是一个字符串（就像其他普通的属性一样）还可以是一个对象

``` jade
a(style={color: 'red', background: 'green'})
```

7. 标签嵌入

#[标签名(标签属性)  标签内容]  
8. 空格调整  
Pug 默认会去除一个标签前后的所有空格，而标签嵌入功能可以在需要嵌入的位置上处理前后空格

``` jade
p
  | 如果我不用嵌入功能来书写，一些标签比如
  strong strong
  | 和
  em em
  | 可能会产生意外的结果。
p.
  如果用了嵌入，在 #[strong strong] 和 #[em em] 旁的空格就会让我舒服多了。
```
效果，前者strong前后无空格，后者有空格。（啦strong啦啦，啦 strong 啦）

9. 注释
``` jade 
// 注释
//- 注释不输出，即不会出现在结果中
// 
    块注释
    继续写块注释
```

ps： 所有以 < 开头的行都会被当作纯文本，因此直接写一个 HTML 风格的条件注释也是没问题的
``` jade
<!--[if IE 8]>
<html lang="en" class="lt-ie9">
<![endif]-->
<!--[if gt IE 8]><!-->
<html lang="en">
<!--<![endif]-->
```
10. 使用=或#{}来进行变量的真实值替换  
在 #{ 和 } 里面的代码也会被求值、转义，并最终嵌入到模板的渲染输出中.Pug 足够聪明来分辨到底哪里才是嵌入表达式的结束，所以不用担心表达式中有 }，也不需要额外的转义;使用!{}嵌入没有转义的文本进入模板中

## 变量
变量来源有三种，分别是pug文件内部、命令行参数和外部JSON文件。
``` jade
// 文件内部
-var val = "aaaa";
p= val

// 命令行
pug test.pug -P -w --obj '{val: "fldasj"}'

// 外部json文件
pug test.pug -P -w -O test.json
```

## include
包含（include）功能允许把另外的文件内容插入进来,被包含的如果不是 Pug 文件，那么就只会当作文本内容来引入 
``` jade
//- index.pug
doctype html
html
  include includes/head.pug
  body
    h1 我的网站
    p 欢迎来到我这简陋得不能再简陋的网站。
    include includes/foot.pug

//- includes/head.pug
head
  title 我的网站
  script(src='/javascripts/jquery.js')
  script(src='/javascripts/app.js')
  
//- includes/foot.pug
footer#footer
  p Copyright (c) foobar
```

## 继承
Pug 支持使用 block 和 extends 关键字进行模板的继承。一个称之为“块”（block）的代码块，可以被子模板覆盖、替换。这个过程是递归的。   
``` jade
复制代码
//- layout.pug
html
  head
　　 meta(charset="UTF-8")
    title 我的站点 - #{title}
    block scripts
      script(src='/jquery.js')
  body
    block content
    block foot
      #footer
        p 一些页脚的内容

//- page-a.pug
extends layout.pug

block scripts
  script(src='/jquery.js')
  script(src='/pets.js')

block content
  h1= title
  - var pets = ['猫', '狗']
  each petName in pets
    include pet.pug

//- pet.pug
p= petName  // 或者 p #{petName}
```  

值得注意的是，因为这里的 foot 块 没有 被重定义，所以会依然输出“一些页脚的内容”  

## 扩展
Pug 允许去替换（默认的行为）、prepend（向头部添加内容），或者 append（向尾部添加内容）一个块。










**`参考文献：`**   [jade](https://www.cnblogs.com/xiaohuochai/p/7222227.html)
