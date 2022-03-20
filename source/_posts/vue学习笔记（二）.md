---
title: vue学习笔记（二）
date: 2018-08-24 16:41:15
tags: ['vue', '前端']
---
最近公司赶项目，一直也没时间看 vue，之前看下的都快忘得差不多了。哈哈哈，来一起回顾一下[vue 学习笔记（一）](#)后，继续向下看嘛。  
#### #表单输入绑定
**基础用法**  

1. v-model 会忽略所有表单元素的 value、checked、selected 特性的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 data 选项中声明初始值。  
2. 对于需要使用输入法 (如中文、日文、韩文等) 的语言，你会发现 v-model 不会在输入法组合文字过程中得到更新。如果你也想处理这个过程，请使用 input 事件。（手敲一个“中文”，在输入法中有“zhongwen”的拼音，并且有“中文”等选项可以选择，从输入拼音，到选择结束，这个过程就叫做 “输入法组合文字过程”）  

譬如：  
```html
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

---

在文本区域插值 (`<textarea></textarea>`) 并不会生效，应用 v-model 来代替。

顺便了解一下 white-space 的属性值：
- `pre` 按给的文字格式显示
- `pre-line` 合并多个空白符，但可正常换行
- `pre-wrap` 保留多个空白符，可以正常换行
- `no-wrap` 不换行
- `inherit` 从符元素继承 white-space 属性值
- `normal` 默认。空白会被浏览器忽略

---

当有多个同类型表单元素时，一般都加上 value。譬如：   
```html
// 单个复选框，绑定到布尔值
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>

// 多个复选框，绑定到同一个数组
<div id='example-3'>
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames }}</span>
</div>
```

技巧说明：  
```html
<div id="example-5">
  <select v-model="selected">
    <option disabled value="">请选择</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>
```
```js
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})
```
_如果 v-model 表达式的初始值未能匹配任何选项，`<select>` 元素将被渲染为“未选中”状态。在 iOS 中，这会使用户无法选择第一个选项。因为这样的情况下，iOS 不会触发 change 事件。因此，更推荐像上面这样提供一个值为空的禁用选项。_

**值绑定**  

对于单选按钮，复选框及选择框的选项，v-model 绑定的值通常是静态字符串 (对于复选框也可以是布尔值)，但有时我们可能想把值绑定到 Vue 实例的一个动态属性上，这时可以用 v-bind 实现，并且这个属性的值可以不是字符串。譬如：  

```html
// 复选
<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no"
>

// 单选
<input type="radio" v-model="pick" v-bind:value="a">

// 下拉列表
<select v-model="selected">
    <!-- 内联对象字面量 -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
```
```js
// 当选中时
vm.toggle === 'yes'
// 当没有选中时
vm.toggle === 'no'

// 当选中时
vm.pick === vm.a

// 当选中时
typeof vm.selected // => 'object'
vm.selected.number // => 123
```

---

这里的 true-value 和 false-value 特性并不会影响输入控件的 value 特性，因为浏览器在提交表单时并不会包含未被选中的复选框。如果要确保表单中这两个值中的一个能够被提交，(比如“yes”或“no”)，请换用单选按钮。

并没有理解到 true-value 和 false-value 有什么用？ // todo

---

**修饰符**

1. `.lazy` 在默认情况下，v-model 在每次 input 事件触发后将输入框的值与数据进行同步。你可以添加 lazy 修饰符，从而转变为使用 change 事件进行同步.(有丢丢没理解上 // todo)譬如：  
```html
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" >
```
2. `.number` 想自动将用户的输入值转为数值类型，可以给 v-model 添加 number 修饰符;即使在 type="number" 时，HTML 输入元素的值也总会返回字符串。  
3. `.trim` 过滤用户输入的首尾空白字符  



## 组件基础
#### #组件

因为组件是可复用的 Vue 实例，所以它们与 new Vue 接收相同的选项，例如 data、computed、watch、methods 以及生命周期钩子等。**_仅有的例外是像 el 这样根实例特有的选项。_**  

一个组件的 data 选项必须是一个函数，因此每个实例可以维护一份被返回对象的独立的拷贝。否则，点击某一个按钮，会影响其他实例。

```js
data: function () {
  return {
    count: 0
  }
}
```

组件注册分为 全局注册 和 局部注册 两种。
定义组件名的方式有两种：kebab-case 和 PascalCase。
譬如： 

```js
Vue.component('my-component-name', { /* ... */ })
Vue.component('MyComponentName', { /* ... */ })
```

当使用 PascalCase (驼峰式命名) 定义一个组件时，你在引用这个自定义元素时两种命名法都可以使用。也就是说 `<my-component-name>` 和 `<MyComponentName>` 都是可接受的。注意，尽管如此，直接在 DOM (即非字符串的模板) 中使用时只有 kebab-case 是有效的。

全局注册，在注册之后可以用在任何新创建的 Vue 根实例 (new Vue) 的模板中,在子组件各自内部也都可以相互使用。


**局部组件**  

全局注册往往是不够理想的。比如，如果你使用一个像 webpack 这样的构建系统，全局注册所有的组件意味着即便你已经不再使用一个组件了，它仍然会被包含在你最终的构建结果中。这造成了用户下载的 JavaScript 的无谓的增加。  

对于 components 对象中的每个属性来说，其属性名就是自定义元素的名字，其属性值就是这个组件的选项对象。

_**局部注册的组件在其子组件中不可用**_

```js
// 传统声明组件方式
var ComponentA = { /* ... */ }

var ComponentB = {
  components: {
    'component-a': ComponentA
  },
  // ...
}

// 通过 Babel 和 webpack 使用 ES2015 模块
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  },
  // ...
}
```

使用 require.context 只全局注册这些非常通用的基础组件。

#### #prop
当你使用 DOM 中的模板时，camelCase (驼峰命名法) 的 prop 名需要使用其等价的 kebab-case (短横线分隔命名) 命名。但是，重申一次，如果你使用字符串模板，那么这个限制就不存在了。譬如：  
```js
Vue.component('blog-post', {
  // 在 JavaScript 中是 camelCase 的
  props: ['postTitle'],  // 也可以是 json 对象格式
  template: '<h3>{{ postTitle }}</h3>'
})
```

```html
<blog-post post-title="hello"></blog-post>

<!-- 动态赋予一个变量的值 -->
<blog-post v-bind:title="post.title"></blog-post>

<!-- 动态赋予一个复杂表达式的值 -->
<blog-post v-bind:title="post.title + ' by ' + post.author.name"></blog-post>

<!-- 即便 `42` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:likes="42"></blog-post>

<!-- 包含该 prop 没有值的情况在内，都意味着 `true`。-->
<blog-post is-published></blog-post>

<!-- 即便 `false` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:is-published="false"></blog-post>
```

即便 `42` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue 这是一个 JavaScript 表达式而不是一个字符串。传入数组，对象都是一样的。[详情见官网](https://cn.vuejs.org/v2/guide/components-props.html#%E4%BC%A0%E5%85%A5%E4%B8%80%E4%B8%AA%E6%95%B0%E7%BB%84)

所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定：父级 prop 的更新会向下流动到子组件中，这样可以防止子组件意外改变父组件的状态。不应该在一个子组件内部改变 prop。常见的两种试图改变 prop 情形：  
1. **_这个 prop 用来传递一个初始值；这个子组件接下来希望将其作为一个本地的 prop 数据来使用。_**这种情况下，会定义一个data 属性来存储这个 prop 值。
```js
props: ['initialCounter'],
data: function () {
  return {
    counter: this.initialCounter
  }
}
```
2. _**这个 prop 以一种原始的值传入且需要进行转换。**_在这种情况下，最好使用这个 prop 的值来定义一个计算属性： 
```js
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```


**注意在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变这个对象或数组本身将会影响到父组件的状态。**  

为了定制 prop 的验证方式，你可以为 props 中的值提供一个带有验证需求的对象，而不是一个字符串数组。譬如：  
```js
Vue.component('my-component', {
  props: {
    // 基础的类型检查 (`null` 匹配任何类型)
    propA: Number,
    // 多个可能的类型
    propB: [String, Number],
    // 必填的字符串
    propC: {
      type: String,
      required: true
    },
    // 带有默认值的数字
    propD: {
      type: Number,
      default: 100
    },
    // 带有默认值的对象
    propE: {
      type: Object,
      // 对象或数组且一定会从一个工厂函数返回默认值
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
})
```

注意那些 prop 会在一个组件实例创建之前进行验证，所以实例的属性 (如 data、computed 等) 在 default 或 validator 函数中是不可用的。

type 可以是下列原生构造函数中的一个：
+ String
+ Number
+ Boolean
+ Array
+ Object
+ Date
+ Function
+ Symbol

额外的，type 还可以是一个自定义的构造函数，并且通过 instanceof 来进行检查确认。例如，给定下列现成的构造函数：

```js
function Person (firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}

Vue.component('blog-post', {
    props: {
        author: Person
    }
})
```
*来验证 author prop 的值是否是通过 new Person 创建的。*

#### #自定义事件

事件名不会被用作一个 JavaScript 变量名或属性名，所以就没有理由使用 camelCase 或 PascalCase 了。并且 v-on 事件监听器在 DOM 模板中会被自动转换为全小写 (因为 HTML 是大小写不敏感的)，所以 v-on:myEvent 将会变成 v-on:myevent——导致 myEvent 不可能被监听到。

一个组件上的 v-model 默认会利用名为 value 的 prop 和名为 input 的事件，但是像单选框、复选框等类型的输入控件可能会将 value 特性用于不同的目的。model 选项可以用来避免这样的冲突：

```js
Vue.component('base-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="$emit('change', $event.target.checked)"
    >
  `
})
```

```html
<base-checkbox v-model="lovingVue"></base-checkbox>
```
这里的 lovingVue 的值将会传入这个名为 checked 的 prop。同时当 `<base-checkbox>` 触发一个 change 事件并附带一个新的值的时候，这个 lovingVue 的属性将会被更新。  

__PS: 注意你仍然需要在组件的 props 选项里声明 checked 这个 prop。__


---

---
羡慕和追逐强者，是男人生来融入血脉的天性。        一一 俗念亲 《每次跳楼，都看见那厮在铺救生气垫》

天气转凉，请预防感冒！这不是广告，是忠告。






