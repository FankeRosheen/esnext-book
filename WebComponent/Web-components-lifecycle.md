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

## constructor() 构造函数

`constructor()`方法是元素挂载之前运行的，一般会在这个函数中设置一些元素的**初始状态**，还有**事件的监听** 以及创建 `Shadow Dom`

> （类比 `Vue` 中的 `create()`）

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
