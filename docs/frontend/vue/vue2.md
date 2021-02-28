# vue2

## 安装

- 官方手册给出了多种安装方式，我这里列出两种：开发版和生产版；
```html
<!-- 开发环境版本，包含了有帮助的命令行警告-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<!-- 生产环境版本，优化了尺寸和速度-->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

!>我们可以在执行Vue 代码之前，执行一条命令，可以去掉警告

```html
<script>
    //去掉警告
    Vue.config.productionTip = false;

    const app = new Vue({
        el : '#app',
        data : {
            message : 'Hello, Vue!'
        }
    });
</script>
```

## 定义选项对象

1. 所有的前端框架基本都是为了简化：模版渲染、事件绑定和用户交互问题；
2. Vue 参考了MVVM 模型，即：视图(View)-视图模型(ViewModel)-模型(Model)；
3. 当我们new Vue()创建一个实例时，括号内传递的是一个选项对象(options)；
4. 这个选项对象一般包含：数据、模版、挂载元素、方法、生命周期钩子等等；
5. 当Vue 实例被创建时，options 对象属性都会被添加到Vue 的响应式系统中；
6. 所谓响应式系统，就是可以通过修改这些属性的值，让视图执行更新渲染；
7. 我们可以通过火狐F12 控制器来修改Vue 对象数据值，也可以使用Vue 调试；
8. 当然，我们也可以将数据对象设置为保护不得修改，具体代码如下：

```javascript
//数据对象
const dataObj = {
message : 'Hello, Vue!'
};
//数据冻结
Object.freeze(dataObj);
//创建一个Vue 实例
const app = new Vue({
el : '#app',
data : dataObj
});
```

### options

```html
//el 固定属性，挂载指定元素
el : '#app',
<!--渲染id="app"中的模版-->
<div id="app">
{{message}}
</div>
//data 固定属性，定义数据
//数据中的属性名和模板变量名一致
data : {
message : 'Hello, Vue!'
}

//methods 固定属性，执行方法
methods : {
alert : function () {
alert('点击');
}
}
<!--渲染id="app"中的模版-->
<div id="app">
<button v-on:click="alert">点击</button>
</div>
```

### [生命周期](https://cn.vuejs.org/v2/guide/instance.html)

- 钩子函数

  ```javascript
  //created 固定方法，生命周期钩子
  //这个方法在初始化后自动执行
  created : function () {
  console.log('初始化后：' + this.message);
  },
  //当修改并更新渲染后执行的钩子
  updated : function () {
  console.log('修改后：' + this.message);
  }
  ```

## 模板语法和指令

- 文本插值

  1 .数据绑定最常用的方式就是：“Mustache”语法，即双花括号进行文本插值；

```html
<div id="app">
{{message}}
</div>
```

2. 我们可以通过在html 设置一个指令：v-once，强制插值只能渲染一次，不可更新；

`<div id="app" v-once>`

3. 模板的插值解释为普通文本，如果想输出html 格式，则可以使用v-html 指令；

```html
<script>
const dataObj = {
//绝不要对用户的内容提供v-html 插值
//因为它容易造成XSS 攻击
html : '<strong style="color:red;">HTML5</strong>'
};
</script>
<div v-html="html"></div>
```

4. 模版插值不能作用域HTML 元素的属性上，我们可以使用v-bind 指令实现；

```html
<script>
const dataObj = {
color : 'red'
};
</script>
<!--通过更改color 值实现替换颜色的效果-->
<div v-bind:class="color">bind</div>
```

5. 在模板语法中，支持JavaScript 表达式，比如：运算、函数等；

```html
<!--注意：语句不支持，比如赋值语句，条件判断等-->
<div>{{num + 1}}</div>
<div>{{true ? '真' : '假'}}</div>
<div>{{message.split('')}}</div>
```

- 指令分析

1. 条件判断指令v-if，可以通过属性的布尔值来决定是否渲染显示html 内容；
```html
<div v-if="flag">为true 方可显示</div>
<script>
const dataObj = {
flag : true,
};
    </script>
```

2. 指令可以有参数，之前的`v-on:click `和`v-bind:class `中冒号后面就是参数；
3. on 表示触发事件，参数click 表示单击，所以参数的值必须是DOM 事件方法；
4. bind 表示绑定属性，参数class 表示HTML 的属性，添加HTML 属性并赋值；
5. 当然，2.6.0 新增了动态参数：`v-bind:[attrName]`，通过数据对象属性赋值；
6. 参数后面可以有修饰符，`v-on:submit.prevent`，后面遇到具体实例再分析；
7. `v-bind `和`v-on `属性较长，系统提供了缩写方法，具体如下：

```html
<!--v-bind 缩写-->
<div :class="color">bind</div>
<div :[key]="color">bind</div>
<!--v-on 缩写-->
<button @click="alert">点击</button>
<button @[event]="alert">点击</button>
```

## 第四页