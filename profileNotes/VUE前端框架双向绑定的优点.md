### VUE前端框架双向绑定的优点

##### 双向绑定的原理及含义？

数据绑定简单来说，就是当数据发生变化时，相应的视图会进行更新，当视图更新时，数据也会跟着变化。

vue的数据双向绑定主要通过Object.defineProperty()方法来进行数据劫持以及发布者-订阅模式来实现的，vue实例话的时候会去遍历所有的属性，给这些属性添加get和set方法进行数据劫持；

双向绑定流程图

![img](https://img2018.cnblogs.com/blog/986768/201909/986768-20190909170245828-682677739.png)

双向绑定的实现步骤大致分为：

1.实现一个监听器Observer，用来劫持并监听所有属性，如果有变动的，就通知订阅者。

2.实现一个订阅者Watcher，可以收到属性的变化通知并执行相应的函数，从而更新视图。

3.实现一个解析器Compile，可以扫描和解析每个节点的相关指令，并根据初始化模板数据以及初始化相应的订阅器。

4、构造函数 （new MVue({})）

#### 个人对于双向绑定的看法

vue中实现双方绑定，是给某些元素的节点特性添加一个指令，如v_bind指令，它会在渲染的 DOM 上应用特殊的响应式行为。

```HTML
<template>
<div id="app-2">
  <span v-bind:title="message">
    鼠标悬停几秒钟查看此处动态绑定的提示信息！
  </span>
</div>
</template>
<script>
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: '页面加载于 ' + new Date().toLocaleString()
  }
})
</script>
```

而v_bind指令在这里表示‘将这个元素节点的 `title` 特性和 Vue 实例的 `message` 属性保持一致’

绑定之后，当vue中的属性值message发生变化，相应的，在HTML页面中，title特性也会随之改变。

双向数据绑定好处在于无需进行和单向数据绑定的那些CRUD操作，比如在表单提交中，当用户在前端页面完成输入后，不用任何操作，就可以拿到用户的数据存放到数据模型中。

可是，由于双向绑定各种数据相互依赖相互绑定，导致数据问题的源头难以被跟踪到，子组件修改父组件，兄弟组件互相修改有有违设计原则。这也算是它的一个缺点吧。