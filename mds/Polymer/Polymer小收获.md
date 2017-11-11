> Polymer 是一个 JavaScript 库,可帮助我们创建自定义的可重用 HTML 元素,并使用它们来构建高性能、可维护的 App。

# 自定义元素

Custom element names. By specification, the custom element's name must start with a lower-case ASCII letter and must contain a dash (-). There's also a short list of prohibited element names that match existing names. For details, see the Custom elements core concepts section in the HTML specification.

![polymer代码结构](https://cuiwanyue.github.io/static/images/polymer-custom%20element-1.jpg)


# 划分小组件

1. 最外面的入口index.html不用写太多只有一个主要的组件组成，例如：

```
<body>
    <my-app></my-app>
</body>
```
2. 主页面my-app.html则是由许多Shadow DOM组成。
3. 需要跳转的页面需要分开写，每个页面如果有相同的样式，可以写一个html只包含这几个相同样式的class，样式页面例子（polymer官网提供）：

![image](https://cuiwanyue.github.io/static/images/example1-2.jpg)

页面引入这个样式，先引入这个html，样式继承include="dom-module id"，页面直接引用样式定义好的class name。

![image](https://cuiwanyue.github.io/static/images/example1-3.jpg)

# 组件内部的值传到外面

1. 一种是简单的传到外面使用：

```
// 组件内部代码my-example.html：（如果是点击生效）
clickEvent() {
    this.dispatchEvent(new CustomEvent('_nowClick', {detail: {kicked: this.value}}));
}
// 外部引用index.html：
document.querySelector('my-example').addEventListener('_nowClick', function (e) {
    console.log(e.detail.kicked); //打印出来this.value
})
```

2. 另一种是实时变化的值传到外面使用：

```
// 组件内部代码my-example.html：
// dom：
value=""
// 事件：
static get observers() {
    return [
        'updateVal(val)'
    ]
}

updateVal(val) {
    this.value = this.val;//内部赋值给外部的value
    this.dispatchEvent(new CustomEvent('newValue', {
        detail: {
            kicked: this.value
        }
    }));
}

//　外部引用index.html：
// dom：
value=""
// 事件：
document.querySelector('my-example').addEventListener('newValue', function (e) {
    console.log(e.detail.kicked);
});
```

#  样式的选择:

比较规范的写法是css里面是纯样式，html中dom结构，js中是一些事件，例如给一个input框通过type配置

1. 在css里给不同的class写两个不同的input样式，代码如下：

```
.default {
    width: 120px;
    height: 56px;  
    border: none;
    border-radius: 3px;
    background-color: #fff;
    border-style: solid;
    border-width: 1px;
    border-color: #ccc;
    box-shadow: 0 0 2px #ccc;
    padding-right: 11px;
}

.active {
  width: 90px;
  height: 40px;
  border: none;
  border-radius: 3px;
  background-color: #fff;
  border-style: solid;
  border-width: 1px;
  border-color: #fc8e76;
  box-shadow: 0 0 2px #ccc;
  font-size: 14px;
  color: #EE5930;
  padding-right: 11px;
}
```

2. html中给一个input　ID，代码如下：

```
button id="basicIconButton" on-click="_nowClick"
```

3. js里通过ID获取input并修改其class，代码如下获取子组件也可以用（this.shadowRoot.querySelector('标签名称')或者document.querySelector('plus-function').shadowRoot.querySelectorAll('basic-icon-button')[4].setAttribute('class', 'authori');）：

```
ready() {
  super.ready();
  switch (this.type) {
    case '1':
    this.$.basicIconButton.className = 'active';
    break;
  default:
    this.$.basicIconButton.className = 'default';
  break;
  }
}
```
