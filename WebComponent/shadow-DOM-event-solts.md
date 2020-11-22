# 关于 Shadow Dom 事件以及 solt

## 常规事件监听

以`<wheate-button></wheate-button>`为例，[源码地址](https://github.com/glean-wheat/wheat-ui/tree/master/src/button)

我们需要使我们的自定义元素工作时，单击它。首先，自定义元素可以注册一个事件侦听器来对用户的交互作出反应。例如，我们可以在按钮上添加一个事件监听器:

```js
class WheatButton extends HTMLElement {
  constructor() {
    super()

    this._shadowRoot = this.attachShadow({ mode: 'open' })
    this._shadowRoot.appendChild(template.content.cloneNode(true))
    this.$button = this._shadowRoot.querySelector('button')
    this.$button.addEventListener('click', () => {
      // do something
    })
  }
}
```

从元素外部添加这个监听器也是可以

```html
<wheat-button onclick="showModal()">打开弹框</wheat-button>
```

这样也是可以触发正常触发的，毕竟是从`HTMLElement`这个类上继承的，所以关于标签一些基础的属性和方法都是同时具备的；
而且不需要在自定元素中做任何的处理；但是，在定制元素内部定义它，可以更好地控制应该传递给外部注册的函数内容。

将外部用户自定的函数传递给自定义元素，有很多方法。React、Vue 中都对此进行了处理，按照框架定义标准即可，整个处理起来还是比较麻烦的。
不过我的想法是没有必要处理，而且我希望能尽可能的使用原生方法；

我们刚刚定义了一个 onClick 处理程序作为元素的函数。接下来，我们可以在自定义元素中的监听调用这个函数属性:

```js
class WheatButton extends HTMLElement {
  constructor() {
    super()

    this._shadowRoot = this.attachShadow({ mode: 'open' })
    this._shadowRoot.appendChild(template.content.cloneNode(true))
    this.$button = this._shadowRoot.querySelector('button')
    this.$button.addEventListener('click', () => {
      // do something
    })
    this.$button.addEventListener('click', () => {
      this.onClick('Hello from within the Custom Element')
    })
  }
}
```

在使用的时候

```js
<whate-button label="Click Me"></whate-button>

<script>
// 方式1
  document.querySelector('wheat-button').onClick = value => {
      console.log(value)
    }

// 方式2
document
    .querySelector('my-button')
    .addEventListener('click', value => console.log(value));
</script>
```

## CustomEvent

在组件中自定义事件作为组件的 API 是必不可少的；
可以使用 [CustomEvent](https://developer.mozilla.org/zh-CN/docs/Web/API/CustomEvent) 从自定义元素抛出任何事件

通过 `CustomEvent` 事件是由程序创建的，可以有任意自定义功能的事件。

```js
class MyElement extends HTMLElement {
  ...  connectedCallback() {
    this.dispatchEvent(new CustomEvent('custom', {
      detail: {message: 'a custom event'}
    }));
  }
}

// 在使用标签的地方

document.querySelector('my-element').addEventListener('custom', e => console.log('message from event:', e.detail.message));

```

整个内容比较简单，这样可以自定方法为组件自定义标签服务。同时也能方便传递参数。

# slots
