---
title: stylus入门学习笔记
date: 2018-09-06 17:35:28
tags: [stylus]
description: 学习到 vue， 有人推荐使用 stylus 这个 css 预处理器。而之前也只是停留在听说过 stylus，并没有实际操作过。现在正好抽空来学习一下呗；如果会 less，sass之类的 css 预编译器，学 stylus 也是 so easy！ 
---
学习到 vue， 有人推荐使用 stylus 这个 css 预处理器。而之前也只是停留在听说过 stylus，并没有实际操作过。现在正好抽空来学习一下呗；如果会 less，sass之类的 css 预编译器，学 stylus 也是 so easy！  
学习来源：[张鑫旭个人博客，这是个 css 界大牛哦](https://www.zhangxinxu.com/jq/stylus/)

话不多说，先来段代码比较一下吧。
```stylus
body
    color: white
    textarea, input
        border: 1px solid #333
    p
    li
    ul
        margin: 0
    .contain
        &:hover
            opacity: 0.8
    
```
等同于
```css
body {
    color: white;
}
body textarea,
body input {
    border: 1px solid #333;
}
body p,
body li,
body ul {
    margin: 0;
}
body .contain:hover {
    opacity: 0.8;
}
```
目前看来，stylus 就是将 sass 的花括号去掉，分号不要而已。按照之前的 sass、less 的语法写，就差不了太多。所以不做多解释，继续下面。


有 stylus 无法处理的属性值，可以使用 unquote() 
```stylus
filter
unquote('progid: DXImageTransform.Microsoft.BasicImage(rotation=1)')
```
转化成
```css
filter:progid:DXImageTransform.Microsoft.BasicImage(rotation=1)
```

### #声明变量
直接使用 '=' 来声明, 变量中可以带 `$` 符
```stylus
font-size = 14px
$color = #ddd
body {
    font-size: font-size
    background-color: $color
}
```

等同于

```css
body {
    font-size: 14px;
    background-color: #ddd;
}
```

### #插值
Stylus支持通过使用{}字符包围表达式来插入值，其会变成标识符的一部分。例如，-webkit-{'border' + '-radius'}等同于-webkit-border-radius.
```stylus
vendor(prop, args)
  -webkit-{prop} args
  -moz-{prop} args
  {prop} args

border-radius()
  vendor('border-radius', arguments)

box-shadow()
  vendor('box-shadow', arguments)

button
  border-radius 1px 2px / 3px 4px
```

转换成
```css
button {
  -webkit-border-radius: 1px 2px / 3px 4px;
  -moz-border-radius: 1px 2px / 3px 4px;
  border-radius: 1px 2px / 3px 4px;  
}
```
插值也可以在选择器上起作用。eg:
```stylus 
table
  for row in 1 2 3 
    tr:nth-child({row})
      height: 10px * row
```
等同于：
```css
table tr:nth-child(1) {
    height: 10px;
}
table tr:nth-child(2) {
    height: 20px;
}
table tr:nth-child(3) {
    height: 30px;
}
```

利用这个属性，我们可以封装很多的方法了。将浏览器兼容前缀写法封装成一个函数。
```stylus
add-attr-prefix(attr, args) {
    -webkit-{attr}: args
    -moz-{attr}: args
    -o-{attr}: args
    -ms-{attr}: args
    {attr}: args
}

// 封装一下 box-shadow 函数
box-shadow()
    add-attr-prefix('box-shadow', arguments)
body
    box-shadow: 8px 8px 20px red
```

等价于

```css
body {
    -webkit-box-shadow: 8px 8px 20px red;
    -moz-box-shadow: 8px 8px 20px red;
    -o-box-shadow: 8px 8px 20px red;
    -ms-box-shadow: 8px 8px 20px red;
    box-shadow: 8px 8px 20px red;
}
```

### #混合书写
混入和函数定义方法一致，但是应用却大相径庭。  
定义方式： 
```stylus
aaa ()
    语句块
```
譬如，
```stylus
border-radius(npx)
    -webkit-border-radius npx
    -moz-border-radius npx
    border-radius npx
.box
    border-radius(5px)
// 使用混入书写，你可以完全忽略括号，提供梦幻般私有属性的支持。
    border-radius 5px
```
也可以不传参数，用 arguments
```stylus
border-radius()
    -webkit-border-radius arguments
    -moz-border-radius arguments
    border-radius arguments
box-shadow(args...)
    -webkit-box-shadow args
    -moz-box-shadow args
    box-shadow args
box-shadow1()
    -webkit-box-shadow agruments
    -moz-box-shadow agruments
    box-shadow agruments
.box
    box-shadow 1px 1px 5px red, 1px 3px 5px green
.box1
    box-shadow 1px 1px 5px red, 1px 3px 5px green
```
需要注意的是，argument 和 args...，前者可以准确的将所有参数传递，包括逗号之类的一些特殊符号。
看一下输出结果：
```css
.box {
    -webkit-box-shadow: #ddd 1px 1px #eee 2px 2px;
    -moz-box-shadow: #ddd 1px 1px #eee 2px 2px;
    box-shadow: #ddd 1px 1px #eee 2px 2px;
}
.box1 {
    -webkit-box-shadow: #ddd 1px 1px, #eee 2px 2px;
    -moz-box-shadow: #ddd 1px 1px, #eee 2px 2px;
    box-shadow: #ddd 1px 1px, #eee 2px 2px;
}
```

同 less， sass 一样，引用父级也是用 &；利用 & 封装一个条纹表格
```stylus
stripe(even = #fff, odd = #eee)
    tr
        background-color odd
        &.even
        &:nth-child(even)
            background-color even
```

函数是可以返回值的。有时候返回值不是一个具体的值，而是表示符。
```stylus
swap(a, b)
    b a
```
这样的时候，写成
```stylus
swap(a, b)
    (b a)
swap(a, b)
    return b a
```
个人在实际项目中用到的方法少之又少；所以感觉，不做游戏开发的话，用到方法的少。看到这儿，就算是已经入门了。  
 
### #注释
Stylus支持三种注释，单行注释，多行注释，以及多行缓冲注释。   
前面三种就不多说了，和以前的写法完全一致。对于多行缓冲注释，跟多行注释类似，不同之处在于开始的时候，这里是/*!.这个相当于告诉Stylus压缩的时候这段无视直接输出。

### #条件
if， else if, else 和常见的条件是一样的。在 stylus 里，也想 ruby 一样，用了 unless (除非)条件。也很好理解，其基本上与 if 相反，本质上是 (!(expr)). eg：
```stylus
disable = true

unless disable 
    display none
```
另外，Stylus 支持后缀条件，这就意味着 if 和 unless 可以当作操作符；当右边表达式为真的时候执行左边的操作对象。
__一般适用于单行语句。__
譬如：
```stylus
negative(n)
  unless n is a 'unit'
    error('无效数值')
  if n < 0
    yes
  else
    no
// 就可以写成下面这种形式了
negative(n)
  error('无效数值') unless n is a 'unit'
  return yes if n < 0
  no
```

### #迭代
```stylus
for <val-name> [, <key-name>] in <expression>
```
感觉实际开发中用不到，暂时先放着，待后续补齐吧。

### #@import
任何 `.css` 扩展的文件名将作为字面量。例如，
```stylus
@import "reset.css"

// 渲染成 ===>>
@import "reset.css"
```
当使用 @import 没有 .css 扩展，会被认为是 Stylus 片段（如：@import"mixins/border-radius"）。  

@import 工作原理为：遍历目录队列，并检查任意目录中是否有该文件（类似 node 的 require.paths）。该队列默认为单一路径，从 filename 选项的 dirname 衍生而来。因此，如果你的文件名是 /tmp/testing/stylus/main.styl，导入将显现为 /tmp/testing/stylus/。

@import 也支持索引形式。这意味着当你 @import blueprint,则会理解成 blueprint.styl 或 blueprint/index.styl. 对于库而言，这很有用，既可以展示所有特征与功能，同时又能导入特征子集。

还有像 @media，@font-face，@keyframes 等和 css 中的差不了太多。

注意，@keyframes 会通过 vendors 变量，会自动添加私有前缀(webkit moz official)。如果我们只想有标准解析，很简单，修改 `vendors = official`.

### #@extend
看个例子感受。
```stylus
red = #E33E1E
yellow = #E2E21E

.message
  padding: 10px
  font: 14px Helvetica
  border: 1px solid #eee

.warning
  @extends .message
  border-color: yellow
  background: yellow + 70%

.error
  @extends .message
  border-color: red
  background: red + 70%

.fatal
  @extends .error
  font-weight: bold
  color: red
```
生成 css 如下
```css
.message,
.warning,
.error,
.fatal {
  padding: 10px;
  font: 14px Helvetica;
  border: 1px solid #eee;
}
.warning {
  border-color: #e2e21e;
  background: #f6f6bc;
}
.error,
.fatal {
  border-color: #e33e1e;
  background: #f7c5bc;
}
.fatal {
  font-weight: bold;
  color: #e33e1e;
}
```

ok, stylus 的学习，就简单到这儿了吧。后面有必要再深入学习一波。

---

---
那一夜我烧毁了所有的记忆,从此我的梦就透明了,那一天我扔掉了所有的昨天,从此我的脚步就轻盈了。  一一 泰戈尔(《飞鸟集》)
