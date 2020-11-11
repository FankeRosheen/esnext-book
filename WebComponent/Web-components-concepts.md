# Web Components

`Web Components` 得到越来越多的关注。而且微软的 Edge 开发团队在 2019 年就宣布，在 `Edge` 上开始支持 `Custom Elements` 和 `Shadow Dom`，所有的主流浏览器已经开始支持 `Web Components`。

另外，国外一些公司，像 `Github、 Netflix、 Youtube` 和 `ING` 这样的公司甚至已经在生产环境中使用 `Web Components`。

## 🙋 What are web components?

    Web components 是一套标准，允许开发者创建新的自定义、可重用、封装的 HTML 元素，以便在 Web 页面和 Web 应用程序中使用。
    自定义组件和小部件建立在 Web components 标准之上，可以跨现代浏览器工作，并且可以与**任何 JavaScript 库或框架**一起使用。
    Web components 基于现有的 Web 标准。支持 Web components 的特性目前正被添加到 HTML 和 DOM 规范中，
    让 web 开发人员可以通过封装样式和自定义行为轻松扩展 HTML。
    来源：https://www.webcomponents.org/introduction

    Web Components 是一套不同的技术，允许您创建可重用的定制元素（它们的功能封装在您的代码之外）并且在您的web应用中使用它们
    来源： https://developer.mozilla.org/zh-CN/docs/Web/Web_Components

### ✨ 划重点

首先: Web components 是一套标准，允许我们编写模块化、可重用和封装的 HTML 元素。

它最大的**优点**:
由于它们是基于 web 标准的，我们不需要安装任何框架或库就可以开始使用它们。也就是可以通过普通的 javascript 编写 web 组件了！

### 📂 使用

作为开发者，我们都知道尽可能多的重用代码是一个好主意。这对于自定义标记结构来说通常不是那么容易 — 想想复杂的 HTML（以及相关的样式和脚本），有时您不得不写代码来呈现自定义 UI 控件，并且如果您不小心的话，多次使用它们会使您的页面变得一团糟。

Web Components 旨在解决这些问题 — 它由三项主要技术组成，它们可以一起使用来创建封装功能的定制元素，可以在你喜欢的任何地方重用，不必担心代码冲突。

基本概念：

- **Custom elements（自定义元素）**：一组 JavaScript API，允许您定义 custom elements 及其行为，然后可以在您的用户界面中按照需要使用它们。
- **Shadow DOM（影子 DOM）**：一组 JavaScript API，用于将封装的"影子"DOM 树附加到元素（与主文档 DOM 分开呈现）并控制其关联的功能。通过这种方式，您可以保持元素的功能私有，这样它们就可以被脚本化和样式化，而不用担心与文档的其他部分发生冲突。
- **HTML templates（HTML 模板）**： <template> 和 <slot> 元素使您可以编写不在呈现页面中显示的标记模板。然后它们可以作为自定义元素结构的基础被多次重用。

就像之前的 JQ 一样，当 document.querySelector 开始得到浏览器的广泛支持后，使得 JQ 的使用频率开始变低。这样的事也有可能发生在 Angular 、React、Vue 这些框架上

## 浏览器的支持情况

![image](https://user-images.githubusercontent.com/24740506/98744397-9856a000-23ec-11eb-9d63-80797ffaa36a.png)
