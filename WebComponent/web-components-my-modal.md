# CustomeModal

## 一、编写 自定义标签 wheat-modal

本文演示如何把这个弹层，写成 `Web Components` 组件，这里是最后的[完整代码](https://github.com/glean-wheat/wheat-ui/tree/master/modal)。

![image](https://user-images.githubusercontent.com/24740506/99131610-9be96180-264e-11eb-90ed-a5c8065209dc.png)

网页只要插入下面的代码，就可以正常使用弹层

```html
<wheat-modal></wheat-modal>
```

这种自定义的 HTML 标签，称为自定义元素（`custom element`）。根据规范，自定义元素的名称必须包含连词线，`<wheat-modal>`不能写成`<wheatmodal>`，这样做是为了避免元素名称冲突，用与区别原生的 `HTML` 元素。自定义元素也不能自动关闭，`HTML` 只允许少数元素自动关闭。 因为 `HTML` 解析对对安全方面有很高的要求。

## 二、customElements.define()

自定义元素需要使用 `JavaScript` 定义一个类，所有 `<wheate-modal>` 都会是这个类的实例。

```js
class WheatModal extends HTMLElement {
  constructor() {
    super()
  }
}
```

上面代码中，`WheatModal` 就是自定义元素的类。注意，这个类的父类是 `HTMLElement`，因此继承了 HTML 元素的特性。

接着，使用浏览器原生的 `customElements.define()`方法，告诉浏览器`<wheat-modal>`元素与这个类关联。

```js
window.customElements.define('wheat-modal', WheatModal)
```

## 三、自定义元素的内容

自定义元素<wheate-modal>目前还是空的，下面在类里面给出这个元素的内容。

```js
class WheatModal extends HTMLElement {
  constructor() {
    super()
    this.render()
  }

  render() {
    this.template = document.createElement('div')
    this.template.innerHTML = `
          <div class="wheat-modal">
          <div class="wheat-modal-mask"></div>
          <div class="wheat-modal-container">
            <div class="wheat-modal-wrapper">
              <div class="wheat-modal-header">
                <span class="wheat-modal-header-text"></span>
                <span class="wheat-modal-header-close">X</span>
              </div>
              <div class="wheat-modal-content">
                <slot name="content">
                  content
                </slot>
              </div>
              <div class="wheat-modal-footer">
                <slot name='wheat-modal-footer'>
                  <wheat-button class="wheat-modal-footer-cancel" type='cancel'>取消</wheat-button>
                  <wheat-button class="wheat-modal-footer-confirm" type='confirm'>确定</wheat-button>
                </slot>
              </div>
            </div>
          </div>
        </div>`
    this.append(this.template)
  }
}
```

上面代码`render()`函数的最后一行，`this.append()`的 `this` 表示自定义元素实例。

完成这一步以后，自定义元素内部的 `DOM` 结构就**已经生成**了。

## 四、`<template>标签`

使用 `JavaScript` 写上一节的 DOM 结构很麻烦，`Web Components API` 提供了`<template>`标签，可以在它里面使用 `HTML` 定义 `DOM` 以及 `css`

我们一步一步来。我们首先通过调用

```js
const template = document.createElement('template')
```

创建一个 `<template >` ，然后在其中设置一些 `HTML`。

```js
const WheatModaltemplate = document.createElement('template')
WheatModaltemplate.innerHTML = `
<div class="wheat-modal">
  <div class="wheat-modal-mask"></div>
  <div class="wheat-modal-container">
    <div class="wheat-modal-wrapper">
      <div class="wheat-modal-header">
        <span class="wheat-modal-header-text"></span>
        <span class="wheat-modal-header-close">X</span>
      </div>
      <div class="wheat-modal-content">
        <slot name="content">
          content
        </slot>
      </div>
      <div class="wheat-modal-footer">
        <slot name='wheat-modal-footer'>
          <wheat-button class="wheat-modal-footer-cancel" type='cancel'>取消</wheat-button>
          <wheat-button class="wheat-modal-footer-confirm" type='confirm'>确定</wheat-button>
        </slot>
      </div>
    </div>
  </div>
</div>
`
```

然后，改写一下自定义元素的类，为自定义元素加载`<template>`

```js
class WheatModal extends HTMLElement {
  constructor() {
    super()
    this.render()
  }

  render() {
    this._shadowRoot = this.attachShadow({ mode: 'open' })
    this._shadowRoot.appendChild(WheatModaltemplate.content.cloneNode(true))
  }
}
```

这里可以看到，`render` 方法发生了改变
上面代码中，获取`<template>`节点以后，`克隆`了它的所有子元素，这是因为可能有多个自定义元素的实例，这个模板还要留给其他实例使用，所以不能直接移动它的子元素。另外，我们只在模板上设置 `innerHTML` 一次。我们使用模板的原因是克隆模板比调用要容易，消耗性能较少

[完整代码](https://github.com/glean-wheat/wheat-ui/)

## 五、添加样式

自定义元素还没有样式，可以给它指定全局样式，比如下面这样。

```css
wheat-modal {
  /*....*/
}
```

但是，组件的样式应该与`代码封装`在一起，只对自定义元素生效，不影响外部的全局样式。所以，可以把样式写在`<template>`里面。

```js
const WheatModaltemplate = document.createElement('template')
WheatModaltemplate.innerHTML = `
<style>
:host{
  display: block;
}
.wheat-modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  height: 0;
  z-index: 1000;
  background: rgba(0, 0, 0, 0.45);
  transition: opacity 0.3s, height 0s 0.3s;
}
.wheat-modal-mask-show {
  opacity: 1;
  height: 100%;
  transition: opacity 0.3s;
}
.wheat-modal-container {
  z-index: 1000;
  top: 100px;
  left: 50%;
  transform: translateX(-50%);
  position: fixed;
}

.wheat-modal-header {
  border-bottom: 1px solid #e6e6e6;
  font-size: 16px;
  color: #333;
  font-weight: 600;
  height: 54px;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  padding: 0 24px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-shrink: 0;
}
.wheat-modal-wrapper {
  width: 600px;
  max-height: 600px;
  min-height: 240px;
  display: flex;
  flex-direction: column;
  background: #fff;
  border-radius: 2px;
  box-shadow: 0 5px 15px 0 rgba(0, 0, 0, 0.1);
}
.wheat-modal-wrapper-show {
  animation:scale 0.3s 1;
}
@keyframes scale
{
  from {transform: scale(0.4);opacity: 0;}
  to {opacity: 1;transform: scale(1);}
}
.wheat-modal-header-close {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em !important;
  overflow: hidden;
  cursor: pointer;
}
.wheat-modal-footer {
  border-top: 1px solid #e6e6e6;
  height: 54px;
  padding: 0 24px;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  flex-shrink: 0;
}
.wheat-modal-content {
  box-sizing: border-box;
  flex: 1;
  overflow: auto;
  padding: 24px;
}

</style>
<div class="wheat-modal">
  <div class="wheat-modal-mask"></div>
  <div class="wheat-modal-container">
    <div class="wheat-modal-wrapper">
      <div class="wheat-modal-header">
        <span class="wheat-modal-header-text"></span>
        <span class="wheat-modal-header-close">X</span>
      </div>
      <div class="wheat-modal-content">
        <slot name="content">
          content
        </slot>
      </div>
      <div class="wheat-modal-footer">
        <slot name='wheat-modal-footer'>
          <wheat-button class="wheat-modal-footer-cancel" type='cancel'>取消</wheat-button>
          <wheat-button class="wheat-modal-footer-confirm" type='confirm'>确定</wheat-button>
        </slot>
      </div>
    </div>
  </div>
</div>
`
```

上面代码中，`<template>`样式里面的`:host` 伪类，指代自定义元素**本身**。

## 六、自定义元素的参数

`<wheat-modal>`内容现在是在`<template>`里面设定的，为了方便使用，把它改成参数。

```html
<wheat-modal title="弹窗" visiable="false" maskCloseable="true">
  <div slot="content">弹框内容</div>
  <!-- <div slot="modal-footer">
        自定义footer
      </div> -->
</wheat-modal>
```

`wheat-modal`也要进行相应的改造

```js
class WheatModal extends HTMLElement {
  constructor() {
    super()
    this.data = {
      title: this.getAttribute('title') || '弹层组件',
      visiable: this.getAttribute('visiable'),
      closeable: this.getAttribute('closeable') || 'true',
      maskCloseable: this.getAttribute('maskCloseable') || 'true',
    }
    this.renderShadowDom()
    this.$modalRoot = this._shadowRoot.querySelector('.wheat-modal')
    this.$closeBtn = this._shadowRoot.querySelector('.wheat-modal-header-close')
    this.$wrapper = this._shadowRoot.querySelector('.wheat-modal-wrapper')
    console.log('this.data.visiable', this.data.visiable)
    this.$mask = this._shadowRoot.querySelector('.wheat-modal-mask')

    this.bindEvents()
  }
  static get observedAttributes() {
    return ['visiable', 'title']
  }
  attributeChangedCallback(name, oldVal, newVal) {
    this.$modalRoot.style.display = name === 'visiable' && newVal !== 'false' ? 'block' : 'none'
    if (name === 'visiable' && newVal !== 'false') {
      this.$mask.classList.add('wheat-modal-mask-show')
      this.$wrapper.classList.add('wheat-modal-wrapper-show')
    }
  }

  connectedCallback() {
    this._shadowRoot.querySelector('.wheat-modal-header-text').innerHTML = this.data.title
    this._shadowRoot.querySelector('.wheat-modal-content')
    console.log(this.data)
    this.$closeBtn.style.display = this.data.closeable ? 'display' : 'none'
  }
  bindEvents() {
    this.hide()
    this.show()
  }
  disconnectedCallback() {
    this.removeEventListener('keydown', this._onKeyDown)
    this.removeEventListener('click', this._onClick)
  }
  hide() {
    this.$cancelBtn = this._shadowRoot.querySelector('.wheat-modal-footer-cancel')

    // 添加自定义事件
    this.$cancelBtn.addEventListener('click', () => {
      this.dispatchEvent(
        // 自定义事件
        new CustomEvent('onCancel', {
          detail: { visiable: false },
        }),
      )
    })

    // 添加自定义事件
    this.$closeBtn.addEventListener('click', () => {
      this.dispatchEvent(
        // 自定义事件
        new CustomEvent('onCancel', {
          detail: { visiable: false },
        }),
      )
    })
    this.data.maskCloseable === 'true' &&
      this.$mask.addEventListener('click', () => {
        this.$modalRoot.style.display = 'none'
      })
  }
  show() {
    this.$confirmBtn = this._shadowRoot.querySelector('.wheat-modal-footer-confirm')
    // 添加自定义事件
    this.$confirmBtn.addEventListener('click', () => {
      this.dispatchEvent(
        // 自定义事件
        new CustomEvent('onConfirm', {
          detail: { isConfirmed: true },
        }),
      )
    })
  }
  renderShadowDom() {
    this._shadowRoot = this.attachShadow({ mode: 'open' })
    this._shadowRoot.appendChild(WheatModaltemplate.content.cloneNode(true))
  }
}
// this需要讲解
window.customElements.define('wheat-modal', WheatModal)
```

## 七、总结

在上面的代码中，涉及到了 `shadow Dom`、`slot`、`事件监听`，在接下来的章节中会详细讲解
