# slots

看到这个题目，大家会自然的想起[Vue 中的插槽](https://cn.vuejs.org/v2/api/#v-slot)，在`shadow Dom`和`vue`使用效果基本一致
`slots`可以说是`shadow Dom`中的占位符，在组件开发中中，经常会遇到自定义组件中某块区域的内容；

将 `shadow DOM` 树与用户提供的`DOM`元素组合在一起。就可以通过`<slot>`元素完成的，该元素基本上是`shadow DOM`中的占位符，用户提供的`DOM`元素将在`shadow DOM`指定的位置中呈现。用户提供的 `DOM`元素 称为 `light DOM`。将`light DOM` 和`Shadow DOM` 组合到一个新的 `DOM` 树中。

例如之前在[CustomeModal](webcomponent/web-components-my-modal.md)中的弹窗内容中，

```html
<div class="wheat-modal-content">
  <slot name="content"> content </slot>
</div>
<div class="wheat-modal-footer">
  <slot name="wheat-modal-footer">
    <wheat-button class="wheat-modal-footer-cancel" type="cancel">取消</wheat-button>
    <wheat-button class="wheat-modal-footer-confirm" type="confirm">确定</wheat-button>
  </slot>
</div>

<wheat-modal title="弹窗" visiable="false" maskCloseable="true">
  <div slot="content">自定义弹框内容</div>
  <div slot="wheat-modal-footer">
    <wheat-button>自定义按钮</wheat-button>
  </div>
</wheat-modal>
```

最终渲染结果是这样的：

```js
<wheat-modal title='弹窗' visiable='false' maskCloseable='true'>
  <div slot='content'>自定义弹框内容</div>
  <div slot='wheat-modal-footer'>
    <wheat-button>自定义按钮</wheat-button>
  </div>
</wheat-modal>
```

如图：

![image](https://user-images.githubusercontent.com/24740506/100024896-a52eb700-2e22-11eb-93aa-98446bcee243.png)

在使用`slot`属性时候，不同的标签可以多次指定同一个`slot`

```html
<wheat-modal title="弹窗" visiable="false" maskCloseable="true">
  <div slot="content">自定义弹框内容</div>
  <div slot="content">自定义弹框内容2</div>
  <div slot="wheat-modal-footer">
    <wheat-button>自定义按钮</wheat-button>
  </div>
</wheat-modal>
```

结果：

```html
<wheat-modal title="弹窗" visiable="true" maskcloseable="true">
  <div slot="content">自定义弹框内容</div>
  <div slot="content">自定义弹框内容2</div>
  <div slot="wheat-modal-footer">
    <wheat-button>自定义按钮</wheat-button>
  </div>
</wheat-modal>
```

带有 `name` 属性的 `slot` 被称为具名 `slot`，但是这个属性不是必须的。它仅用于需要将内容呈现在特定位置时使用。当一个或多个 `slot` 没有 `name` 属性时，将按照用户提供内容的顺序在其中展示。当用户提供的内容少于 `slot` 时，`slot` 可以提供默认的展示。
