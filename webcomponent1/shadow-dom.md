# Shadow DOM

`Web components` 的一个重要属性是**封装**——可以将**标记结构**、**样式**和**行为**隐藏起来，并与页面上的其他代码相隔离，保证不同的部分不会混在一起，可使代码更加干净、整洁。其中，`Shadow DOM` 接口是关键所在，它可以将一个隐藏的、独立的 DOM 附加到一个元素上。

使用 `Shadow DOM`，自定义元素的 `HTML` 和 `CSS` 完全封装在组件内。这意味着元素将以单个的 `HTML` 标签出现在文档的 `DOM` 树种。其内部的结构将会放在`#shadow-root`。
![image](https://user-images.githubusercontent.com/24740506/99316220-ec56fe00-289e-11eb-9360-d9bd9d50d0b0.png)

`Shadow DOM` 使得`HTML` 和 `CSS` 与**主文档**的`DOM`保持分离。

> 备注： Firefox（从版本 63 开始），Chrome，Opera 和 Safari 默认支持 Shadow DOM。基于 Chromium 的新 Edge 也支持 Shadow DOM；而旧 Edge 未能撑到支持此特性。

## 为什么要把一些代码和网页上其他的代码分离？

原因之一是，大型站点若 `CSS` 没有良好的组织，导航的样式可能就『泄露』到本不应该去的地方，如主要内容区域，反之亦然。随着站点、应用的拓展，这样的事难以避免。

## 重新认识

实际上一些原生的 HTML 元素也使用了 Shadow DOM。例如你再一个网页中有一个`<video>`元素，它将会作为一个单独的标签展示，但它也将显示播放和暂停视频的控件，当你在浏览器开发工具中查看 `video` 标签，是看不到这些控件。

这些控件实际上就是 `video` 元素的 `Shadow DOM` 的一部分，因此默认情况下是隐藏的。要在 `Chrome` 中显示 `Shadow DOM`，进入开发者工具中的 Preferences 中，选中 Show user agent Shadow DOM。当你在开发者工具中再次查看 video 元素时，你就可以看到该元素的 Shadow DOM 了。

**详细的操作步骤如下：**

![image](https://user-images.githubusercontent.com/24740506/99316960-2ffe3780-28a0-11eb-8602-ba0eca657779.png)

![image](https://user-images.githubusercontent.com/24740506/99317037-50c68d00-28a0-11eb-90d3-9345a8be1ebc.png)

![image](https://user-images.githubusercontent.com/24740506/99316814-f4fc0400-289f-11eb-89b8-60de8d4e4d8c.png)

### 注意:

不管从哪个方面来看，`Shadow DOM` 都不是一个新事物——在过去的很长一段时间里，浏览器用它来封装一些元素的内部结构。以一个有着默认播放控制按钮的 `<video>` 元素为例。如上图所示，你所能看到的只是一个 `<video>` 标签，实际上，在它的 Shadow DOM 中，包含来一系列的按钮和其他控制器。Shadow DOM 标准允许你为你自己的元素（custom element）维护一组 Shadow DOM。

## 属性

`Shadow DOM` 允许将隐藏的 `DOM` 树附加到常规的 `DOM `树中——它以 `shadow root` 节点为起始根节点，在这个根节点的下方，可以是任意元素，和普通的 `DOM` 元素一样。
![image](https://mdn.mozillademos.org/files/15788/shadow-dom.png)

**这里，有一些 Shadow DOM 特有的术语需要我们了解：**

- **Shadow host**：一个常规 `DOM`节点，`Shadow DOM` 会被附加到这个节点上。
- **Shadow tree**：`Shadow DOM`内部的 DOM 树。
- **Shadow boundary**：`Shadow DOM`结束的地方，也是常规 `DOM`开始的地方。
- **Shadow root**: `Shadow tree`的根节点。

## 基础用法

可以使用 [Element.attachShadow()](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/attachShadow) 方法来将一个 `shadow root` 附加到任何一个元素上。它接受一个配置对象作为参数，该对象有一个 `mode` 属性，值可以是 `open` 或者 `closed`：

```js
let shadow = elementRef.attachShadow({ mode: 'open' })
let shadow = elementRef.attachShadow({ mode: 'closed' })
```

`open` 表示可以通过页面内的 `JavaScript` 方法来获取 `Shadow DOM`，例如使用 `Element.shadowRoot` 属性：

```js
let myShadowDom = myCustomElem.shadowRoot
```

如果你将一个 `Shadow root` 附加到一个 `Custom element` 上，并且将 `mode` 设置为 `closed`，那么就不可以从外部获取 `Shadow DOM` 了——`myCustomElem.shadowRoot` 将会返回 `null`。浏览器中的某些内置元素就是如此，例如`<video>`，包含了不可访问的 `Shadow DOM`

如果你想将一个 `Shadow DOM` 附加到 `custom element` 上，可以在 `custom element` 的构造函数中添加如下实现（目前，这是 `shadow DOM` 最实用的用法）：

```js
let shadow = this.attachShadow({ mode: 'open' })
```

将 `Shadow DOM` 附加到一个元素之后，就可以使用 `DOM APIs` 对它进行操作，就和处理常规 `DOM` 一样。

```js
var para = document.createElement('p');
shadow.appendChild(para);
etc.
```

## CSS

`Shadow DOM` 提供了局部作用域的 `CSS`。所有的 `CSS` 都只应用于组件本身。元素将只继承最小数量从组件外部定义的 `CSS`，甚至可以不从外部继承任何 `CSS`。不过你可以暴露这些 `CSS` 属性，以便用户对组件进行样式设置。这可以解决许多 `CSS` 问题，同时仍然允许自定义组件样式。 定义一个 `Shadow root`:

```js
const shadowRoot = this.attachShadow({ mode: 'open' })
shadowRoot.innerHTML = `<p>Hello world</p>`
```

这定义了一个带 `mode: open` 的 `Shadow root`，这意味着可以再开发者工具找到它并与之交互，配置暴露出的 `CSS` 属性，监听抛出的事件。同样也可以定义 `mode：closed`，会得到与之相反的表现。

你可以使用使用 `HTML` 字符串添加到 `innerHtml` 的 `property` 属性中，或者使用一个`<template>`去给 `Shadow root` 添加 `HTML`。一个 `HTML` 的 `template` 基本是惰性的 `HTML` 片段，你可以定义了延后使用。在实际插入 `DOM `前，它是不可见也不可解析的。这意味着定义在内部的任何资源都无法获取，任何内部定义的 `CSS` 和 `JavaScript` 只有当它被插入 `DOM` 中时，才会被执行。当组件的 HTML 根据其状态发生更改时，例如你可以定义多个`<template>`元素，然后根据组件的状态去插入这些元素，这样可以轻松的修改组件的 HTML 部分，并不需要修改单个 DOM 节点。

当 Shadow root 被创建之后，你可以使用 `document` 对象的所有 `DOM `方法，例如 `this.shadowRoot.querySelector` 去查找元素。组件的所有样式都被定义在 `style` 标签内，如果你想使用一个常规的`<link rel="stylesheet">`标签，你也可以获取外部样式。除此之外，还可以使用`:host` 选择器对组件本身进行样式设置。例如，自定义元素默认使用 `display: inline`，所以如果你想要将组件展示为款元素，你可以这样做：

```css
:host {
  display: block;
}
```

这还允许你进行上下文的样式化。例如你想要通过 `disabled` 的 `attribute` 来改变组件的背景是否为灰色：

```css
:host([disabled]) {
  opacity: 0.5;
}
```

默认情况下，自定义元素从周围的 `CSS` 中继承一些属性，例如颜色和字体等，如果你想清空组件的初始状态并且将组件内的所有 `CSS `都设置为默认的初始值，你可以使用：

```css
:host {
  all: initial;
}
```

非常重要，需要注意的一点是，从外部定义在组件本身的样式优先于使用:host 在 Shadow DOM 中定义的样式。如果你这样做

```css
my-element {
  display: inline-block;
}
```

它将会被覆盖

```css
:host {
  display: block;
}
```

不应该从外部去改变自定义元素的样式。如果你希望用户可以设置组件的部分样式，你可以暴露 `CSS` 变量去达到这个效果。例如你想让用户可以选择组件的背景颜色，可以暴露一个叫 `--background-color` 的 `CSS` 变量。 假设现在有一个 `Shadow DOM` 的根节点是 `<div id="container">`

```css
#container {
  background-color: var(--background-color);
}
```

现在用户可以在组件的外部设置它的背景颜色

```css
my-element {
  --background-color: #ff0000;
}
```

你还可以在组件内设置一个默认值，以防用户没有设置

```css
:host {
  --background-color: #ffffff;
}

#container {
  background-color: var(--background-color);
}
```

当然你还可以让用户设置任何的 `CSS` 变量，前提是这些变量的命名要以 **--开头**。

通过提供局部的 `CSS、HTML，Shadow DOM` 解决了全部 `CSS` 可能带来的一些问题，这样问题通常导致不断地添加样式表，其中包含了越来越多的选择器和覆盖。`Shadow DOM` 似的标记和样式捆绑到自己的组件内，而不需要任何工具和命名约定。你再也不用担心新的 `class` 或 `id` 会与现有的任何一个冲突。
