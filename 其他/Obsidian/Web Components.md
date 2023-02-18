折腾了许久，终于找到 Obsidian 支持 Web Components 的方法，原来是 Obsidian 为了防止安全问题，对输入的内容进行了处理，使用自定义的标签会直接被忽略，在网上了解到 Obsidian 官方是基于 `DOMPurify` 处理的，所以只要在 Obsidian 解析文章之前，将我们的标签添加到白名单，就可以了

```js
DOMPurify.setConfig({
  ALLOW_UNKNOWN_PROTOCOLS: !0,
  RETURN_DOM_FRAGMENT: !0,
  FORBID_TAGS: ["style"],
  ADD_TAGS: ["xt-popover", "xt-artnav"],
  ADD_ATTR: ["content", "placement", "noPrev", "prev", "noNext", "next"]
});
```

这次添加了两个 Web Components 组件，`xt-popover` 和 `xt-artnav`，再也不用为了标记某句话需要写代码了，直接使用标签解决

```js
<xt-popover content="标记" placement="top">
  <span class="comments">被标记的内容</span>
</xt-popover>
```

<xt-popover content="标记" placement="top">
  <span class="comments">被标记的内容</span>
</xt-popover>

`xt-artnav` 用以实现页面的跳转，之前由 `Nav` 组件提供这个能力，现在也只要一个标签就可以，爽歪歪

```js
<xt-artnav prev="Styled Container" next="Notion Video" />
```

<xt-artnav prev="Styled Container" next="Notion Video" />
