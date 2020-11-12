# Web Components 的生命周期

上一篇中简单的介绍了一下一些关键名词概念：`Custom Elements`、`Shadow DOM`、`HTML templates`
本篇中就开始介绍，比较重要内容，关于自定义元素的 生命周期

## ♻️ 组件的生命周期

让我们看一下自定义元素的生命周期

```js
class MyElement extends HTMLElement {
  constructor() {
    // always call super() first
    super()
    console.log('constructed!')
  }

  connectedCallback() {
    console.log('connected!')
  }

  disconnectedCallback() {
    console.log('disconnected!')
  }

  attributeChangedCallback(name, oldVal, newVal) {
    console.log(`Attribute: ${name} changed!`)
  }

  adoptedCallback() {
    console.log('adopted!')
  }
}

window.customElements.define('my-element', MyElement)
```

## constructor() 初始化

`constructor()`方法是元素挂载之前运行的，一般会在这个函数中设置一些元素的**初始状态**，还有**事件的监听** 以及创建 `Shadow Dom`

> 类比 `Vue` 中的 `create()`，`React`中 `class` 组件中的 `constructor()`它们两个是一样的

## connectedCallback() 挂载完成

当元素插入到 DOM 中时，将调用 `connectedCallback`， 通常，组件的一些`样式`或者其他需要`获取元素的状态` 应该在 `connectedCallback` 进行设置，因为在这里才能确定元素的所有属性和子元素都是可用的。`constructor` 一般应该只用于初始化状态和设置`shadow DOM`。

### `constructor` 和 `connectedCallback` 之间的区别

创建元素时调用`constructor`，
已经插入到 `DOM` 元素后调用 `connectedCallback`

> 可以将其与`vue`的`mount`方法、`React`的`componentDidMount`方法进行比较

## disconnectedCallback 卸载元素

当`自定义元素`从`DOM`中移除的时候，就会触发该方法；同时也会在这个事件中删除调改元素添加的事件，比如全局的`键盘函数`以及`延时函数`，
disconnectedCallback 的对应函数是 connectedCallback，
另外有个注意点，当用户关闭浏览器或关闭该标签页时，将不会调用此方法。不过可以用 `window.unload beforeunload` 或者 `widow.close` 去触发在浏览器关闭是的回调

> `react` 中的 `componentWillUnmount` 的方法进行比较, `vue` 中的 `destory` 中是生命周期函数进行对比

## attributeChangedCallback 属性监听

使用这个函数需要和 `observedAttributes` 该方法配合使用，需要将你要监听的属性在该方法中注册；

```js
static get observedAttributes() {
    return ['my-attr']
}

```

上面的函数中，就是对`my-attr`属性进行了监听；当该属性的值发生改变的时候，就会触发`attributeChangedCallback`

```js
/**
   *
   * @param {*} name 属性名称
   * @param {*} oldVal 改变前的值
   * @param {*} newVal 改变后的值
   *
   */
attributeChangedCallback(name, oldVal, newVal) {
    console.log(`Attribute: ${name} changed!`)
}
```

**注意：**只有在 `observedAttributes` 监听的值才会触发 `attributeChangedCallback`

> `react` 中的 `static getDerivedStateFromProps(props, state)` 与这个有些类似，以及 `vue` 中的 `watch` 函数

## 执行顺序

`constructor -> attributeChangedCallback -> connectedCallback`；vue 以及 react 中的生命周期也是这样的一个顺序；

这个时候，可能会有些困惑了。

#### 为什么 `attributeChangedCallback` 要在 `connectedCallback` 之前执行 ?

回想一下上面每个函数的含义，属性需要在元素插入到 DOM 树中的时候，要保证能够被获取的；所以合理的话就是需要`connectedCallback`获取的属性是通过`attributeChangedCallback`处理过可使用的；

这意味着如果需要根据某些属性的值配置 `shadow DOM` 中的任何节点，需要在 `constructor` 中而不是在 `connectedCallback` 中再次操作`DOM`节点

例如，如果在组件中有一个`id="container"`的元素，当观察到的属性禁用更改时，您需要给这个元素一个灰色背景，在`constructor`中引用这个元素，保证它在 `attributeChangedCallback` 中可用

```js
constructor() {
  this.container = this.shadowRoot.querySelector('#container');
}
attributeChangedCallback(attr, oldVal, newVal) {
  if(attr === 'disabled') {
    if(this.hasAttribute('disabled') {
      this.container.style.background = '#808080';
    }
    else {
      this.container.style.background = '#ffffff';
    }
  }
}
```

## 代码备注

```js
class MyElementLifecycle extends HTMLElement {
  // 元素初始化的时候执行
  constructor() {
    // HTMLElement.prototype.constructor.call(this)
    super()
    console.log('constructed!')
  }
  /**
   * connectedCallback
   * 当元素插入到 DOM 中时，将调用 connectedCallback。
   * 这是运行安装代码的好地方，比如获取数据或设置默认属性。
   * 可以将其与React的componentDidMount方法进行比较
   * vue的mount方法作比较
   */
  connectedCallback() {
    console.log('connected!')
  }
  /**
   * disconnectedCallback
   * 只要从 DOM 中移除元素，就会调用 disconnectedCallback。清理时间到了！
   * 我们可以使用 disconnectedCallback 删除事件监听，或取消记时。
   * 但是请记住，当用户直接关闭浏览器或浏览器标签时，这个方法将不会被调用。
   *
   * 可以用window.unload beforeunload或者widow.close 去触发在浏览器关闭是的回调
   *
   * 可以与 react 中的 componentWillUnmount 的方法进行比较
   * vue 中的 destory中是生命周期函数进行对比
   */
  disconnectedCallback() {
    console.log('disconnected!')
  }

  static get observedAttributes() {
    return ['my-attr']
  }
  /**
   *
   * @param {*} name
   * @param {*} oldVal
   * @param {*} newVal
   *
   * 每当添加到observedAttributes数组的属性发生变化时，就会调用这个函数。使用属性的名称、旧值和新值调用该方法
   * react 中的 static getDerivedStateFromProps(props, state) 有些类似
   * 基本上和vue中的watch使用和observedAttributes + attributeChangedCallback使用雷同；
   */

  attributeChangedCallback(name, oldVal, newVal) {
    console.log(`Attribute: ${name} changed!`)
  }
  /**
   * 每次将自定义元素移动到新文档时，都会调用 adoptedCallback。只有当您的页面中有 < iframe > 元素时，您才会遇到这个用例。
   * 通过调用document.adoptnode (element)调用它，基本上用不上
   */
  adoptedCallback() {
    console.log('adopted!')
  }
  /**
   * 生命周期的执行顺序  挂载的时候 按照react 或者vue中的执行顺序是相同的
   * constructor -> attributeChangedCallback -> connectedCallback
   */
}
// 不是生命周期的API 但是肯定主要操作这一步的 注册
window.customElements.define('my-element-lifecycle', MyElementLifecycle)
```
