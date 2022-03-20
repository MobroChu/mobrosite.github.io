---
title: 使用scss为css样式自动添加浏览器前缀
date: 2018-06-15 11:55:21
tags: scss 
description: 当一个浏览器实现一个新的属性、值或者选择器，而这个特征还不是处于候选推荐标准状态的时候，这属性的前面会添加一个前缀以便于它的渲染引擎识别。浏览器使用前缀来尝试一些新属性、值和选择器，即使他们还没有最终定稿——这是一个好的测试方法，在必要时也可以对他们进行修正或者重新定义。如果浏览器一上来就直接使用标准属性，那他们就会被直接锁定在这个特征的实现上而不易变更。
---
_当一个浏览器实现一个新的属性、值或者选择器，而这个特征还不是处于候选推荐标准状态的时候，这属性的前面会添加一个前缀以便于它的渲染引擎识别。_  

浏览器使用前缀来尝试一些新属性、值和选择器，即使他们还没有最终定稿——这是一个好的测试方法，在必要时也可以对他们进行修正或者重新定义。如果浏览器一上来就直接使用标准属性，那他们就会被直接锁定在这个特征的实现上而不易变更。

开发这可能会立即使用无前缀的属性，而且也会一直期望它能够保持同样的表现不再变更。如果浏览器在之后对这个属性做了变更，不管是由于它的实现存在缺陷，或者是由于规范本身发生了变更，所有现存的使用了这个属性的网站都有可能面临出问题的风险。除了会出现这样的锁定问题外，这种方式也有可能强迫其他浏览器和W3C去适配它的实现。

##### 1. 使用 scss 来实现为样式添加浏览器前缀，废话不多说，直接上代码。

``` scss
@mixin prefix($stylename, $value, $options: webkit moz o ms) {
    #{$stylename}: $value;

    @each $option in $options {
        @if $option == webkit {
            -webkit-#{$stylename}: $value;
        }
        @else if $option == moz {
            -moz-#{$stylename}: #{$value};
        }
        @else if $option == o {
            -o-#{$stylename}: #{$value};
        }
        @else if $option == ms {
            -ms-#{$stylename}: #{$value};
        }
    }
}

.test {
    @include prefix(background, blue);
}
``` 

编译后的结果：

``` css
.test {
  background: blue;
  -webkit-background: blue;
  -moz-background: blue;
  -o-background: blue;
  -ms-background: blue; 
}
```

ps:  
1. 很明显，上述代码中 `$stylename` 要用 #{} 包起来的。  
2. 形参中 $options 可以是 [webkit moz o ms]，但是却不能写成数组的形式，这可能是 scss 语法的原因吧。 
3. $option 有默认值，所以可以不用传参，若要指定生成哪些浏览器前缀，可以使用 `@include prefix(background, blue, webkit moz)` 

有的样式值有兼容性该怎么写呢？或者是新增了属性值，而不是属性时，怎么来写呢？

比如 `background`, 当值为渐变色时，就存在兼容性，而且是 background 的值是新增的，所以应该写成下图所示的样子。
![](/images/201806/WX20180615-115151.png)  
那么此时就只需要：
``` scss
.test {
    // @include prefix(background, linear-gradient())
    皮一下，很开森。
    其实这种情况下，用这个封装的前缀就不太合适了，还是老老实实分开单独写吧，或者封装一个background linear-gradient的mixin。
    因为这里liner-gradient值是不一样的，并不只是前缀的问题。
}
```

写成 `left , to right` 这种形式各浏览器之间有兼容性问题，所以我们可以改写成 `90deg` 这种形式。
![](/images/201806/WX20180615-143708.png)

##### 2. 封装一个动画 prefix-frames
``` scss
@mixin prefix-keyframes($name, $options: [webkit moz o ms]) {
    @keyframes #{$name} {
        @content;
    }
    @each $option in $options {
        @if $option == webkit {
            @-webkit-keyframes #{$name} {
                @content;
            }
        }
        @else if $option == moz {
            @-moz-keyframes #{$name} {
                @content;
            }
        }
        @else if $option == o {
            @-o-keyframes #{$name} {
                @content;
            }
        }
        @else if $option == ms {
            @-ms-keyframes #{$name} {
                @content;
            }
        }
    }
}

@include prefix-keyframes(colorchange) {
    from {
        color: red;
    }
    to {
        color: black;
    }
};
```

解析后的css 
``` css
@keyframes colorchange {
  from {
    color: red; }
  to {
    color: black; } }

@-webkit-keyframes colorchange {
  from {
    color: red; }
  to {
    color: black; } }

@-moz-keyframes colorchange {
  from {
    color: red; }
  to {
    color: black; } }

@-o-keyframes colorchange {
  from {
    color: red; }
  to {
    color: black; } }

@-ms-keyframes colorchange {
  from {
    color: red; }
  to {
    color: black; } }
```

##### 3. 分享几个我常用的 mixin
``` scss
// 浮动
@mixin float($f: left) {
    float: $f;
}
.test {
    @include float();
    @include float(right);
}

// 清除浮动
@mixin clearfix() {
    zoom: 1;
    &:after {
        content: '';
        display: block;
        clear: both;
    }
}
.test {
    @include clearfix();
}

// 三角形
@mixin triangle($size, $dir, $color) {
    display: block;
    width: 0;
    height: 0;
    border: $size solid transparent;
    border-#{$dir}: $size solid #{$color};
}
.test {
    @include triangle(20px, left, red);
}

// 设置宽高
@mixin size($w, $h: auto) {
    width: $w;
    height: $h;
}
.test {
    @include size(200px);
    @include size(200px, 200px);
}
// 标题一行，多余用省略号
@mixin text-overflow {
    max-width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}
// 文字不换行
@mixin word-break {
    word-break: break-all;
    word-wrap: break-word;
    white-space: normal;
}
// 圆角
@mixin border-radius($radius) {
    -webkit-border-radius: $radius;
    -moz-border-radius: $radius;
    border-radius: $radius;
    // 这样写是不是就更完美了。
    // @include prefix(border-radius, $radius); 
}
```

---

世界上只有一种真正的英雄主义，那就是在认清生活真相之后依然热爱生活 —— 罗曼罗兰
