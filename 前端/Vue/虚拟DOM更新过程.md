---
tags: [Vue, 虚拟DOM, 踩坑]
---

## 事发现场

事发当晚，我正在与小伙伴愉快的打王者荣耀，当时看到微信群里交流出事情了，我看到了重复的数据出现了，我当时觉得是服务那边的问题，影响不到我，所以就没理。然后游戏打到一半，师兄一个电话打过来，出线上 `bug`，切换不了 `Tab` 了，唉😮‍💨，只能挂机修 `bug` 去了。

首先看报错信息，发现错误跟 `key` 有关，跟 `key` 有关的地方只有一个，是这段代码

```html
<div class="card-list">
  <Card v-for="item in lists" :key="item.tid"
    <!-- xxx -->
  />
</div>
```

我使用的是 `tid` 作为 `key`，但是因为服务那边数据出了问题，导致有相同的数据发了过来，所以导致 `tid` 是重复，也就导致 `key` 有重复。也就是说 `key` 重复会导致页面卡死，当时的处理方案将 `key` 设置为 `index`

```html
<div class="card-list">
  <Card v-for="(item, index) in lists" :key="index"
    <!-- xxx -->
  />
</div>
```

紧急修复了这个问题。

上面虽然修复了这个线上问题，但是 `key` 重复居然会导致页面卡死，我是第一次见到，当时和师兄聊也觉得 `Vue` 现在这个这么强势了吗？我当时也查了一些文章，有的人有和我一样的情况，但是却没有说明原因，在经过一番折腾查不到想要的结果以后，我只能从源码入手了，还是只能靠自己。

## VNode 初览

不过要深入搞明白这个问题，就要理解 Vue 是如何更新组件的，在 Vue 的内部会维护一个虚拟 DOM 树对应于当前页面的 DOM 树，当我们的数据更新时，Vue 会根据当前的数据生成一个新的虚拟 DOM 树，这个虚拟 DOM 树是使用了该数据的**组件的虚拟 DOM 树**，然后与该组件的当前虚拟 DOM 树进行比较，然后对真实的 DOM 进行更新(删除、添加、移动)，这个过程就是 DOM Diff 算法。

在 Vue 中，虚拟 DOM 的定义为 VNode 类，如下

```js
export default class VNode {
  tag: string | void;
  data: VNodeData | void;
  children: ?Array<VNode>;
  text: string | void;
  elm: Node | void;
  ns: string | void;
  context: Component | void; // rendered in this component's scope
  key: string | number | void;
  componentOptions: VNodeComponentOptions | void;
  componentInstance: Component | void; // component instance
  parent: VNode | void; // component placeholder node

  // strictly internal
  raw: boolean; // contains raw HTML? (server only)
  isStatic: boolean; // hoisted static node
  isRootInsert: boolean; // necessary for enter transition check
  isComment: boolean; // empty comment placeholder?
  isCloned: boolean; // is a cloned node?
  isOnce: boolean; // is a v-once node?
  asyncFactory: Function | void; // async component factory function
  asyncMeta: Object | void;
  isAsyncPlaceholder: boolean;
  ssrContext: Object | void;
  fnContext: Component | void; // real context vm for functional nodes
  fnOptions: ?ComponentOptions; // for SSR caching
  devtoolsMeta: ?Object; // used to store functional render context for devtools
  fnScopeId: ?string; // functional scope id support

	constructor(...) {
    // ... ...
  }
}
```

主要关注几个属性：

- `tag: string`：元素、组件标签
- `data: VNodeData`：各种数据信息，包括 `class`, `style`, `attrs`, `props`, `listeners`, `hook` 等等
- `children: Array<VNode>`：元素的子节点
- `text: string | void`：文本节点或注释节点的内容
- `elm: Node | void`：虚拟 DOM 对应的真实的 DOM
- `key: string | number | void`：`v-for` 传入的键值

我们把当前的虚拟 DOM 树称为 `oldVNode`，根据新数据创建的虚拟 DOM 树称为 `newVNode`，更新的过程就是比较 `newVNode` 和 `oldVNode` 然后更新 DOM，注意，是在原有的基础中进行更新，而不是根据 `newVNode` 生成一个 DOM 直接去替换，这样可以复用原有的 DOM 节点。

## patchVNode

通过属性可以观察出 VNode 的节点分为 6 类：

- 注释节点
- 文本节点
- 元素节点
- 克隆节点
- 组件节点
- 函数式组件节点

上面的节点可以分为两大类，元素节点和文本节点，像组件节点其实就是在元素节点的基础上添加了一些另外的信息，例如组件对应的 Vue 实例等等，组件的更新我就简要以这两大类进行分析。

- 如果 `newVNode` 是元素节点，那么
  - 如果二者都有 `children` 属性，那么比较它们的 `children` 进行更新，这个是重头戏，后面细说
    <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-1.drawio2022-04-22-16-25-13.svg" style="zoom:50%"/>

  - 否则如果 `newVNode` 有 `children`，而 `oldVNode` 没有，说明是新增内容，这个时候如果 `oldVNode` 是文本节点，那么先清空 `oldVNode` 中的内容，接着将 `newVNode` 的 `children` 添加到 `oldVNode` 中，并更新 DOM

    <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-2.drawio2022-04-22-16-26-45.svg" style="zoom:50%"/>

  - 否则如果 `newVNode` 没有 `children` 属性，但是 `oldVNode` 有 `children`，这表示删除掉了内容，这个时候就直接删除 `oldVNode` 中 `children` 中的所有 `VNode`，并在页面中删除对应的 DOM 元素

    <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-3.drawio2022-04-22-16-28-16.svg" style="zoom:50%"/>

  - 否则如果 `oldVNode` 有文本内容，那么清空文本内容

- 如果 `newVNode` 是文本节点

  - 如果 `oldVNode` 的文本内容与 `newVNode` 文本内容不同，直接设置对应的 DOM 元素的内容为 `newVNode.text`

下图是上面过程的流程框图：

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-4.drawio2022-04-22-16-32-26.svg" style="zoom:50%"/>

写一下代码(源自于源码，为了突出重点进行了适当的省略)：

```js
function patchVNode(oldVNode, newVNode) {
  if (oldVNode === newVNode) {
    return
  }

  let elm = newVNode.elm = oldVNode.elm

  let oldCh = oldVNode.children
  let newCh = newVNode.children

  if (isUnDef(newVNode.text)) {
    if (idDef(newCh) && isDef(oldCh)) {
      updateChildren(oldCh, newCh)
    } else if (isDef(newCh)) {
      if (isDef(oldVNode.text)) {
        nodeOps.setTextContext(elm, '')
      }
      addVNodes(elm, newCh)
    } else if (isDef(oldCh)) {
      removeVNodes(oldCh)
    } else if (isDef(oldVNode.text)) {
      nodeOps.setTextContext(elm, '')
    }
  } else if (oldVNode.text !== newVNode.text) {
    nodeOps.setTextContext(elm, newVNode.text)
  }
}
```

## updateChildren

接下来我们比较关心的是在两个都有 `children` 的情况下如何更新，这种情况还是非常常见的，比如 `v-for` 遍历一个列表显示数据的时候，如果修改了列表数据，这个时候就会根据新的数据渲染 `newVNode`，这时 `newVnode` 与 `oldVNode` 都有 `children` 属性，但是 `children` 不同，这个时候就会进入 `updaeChidlren` 的流程。

`updateChildren` 目的是尽可能的复用 `oldVNode.children(oldCh)` 中的元素，所以这个过程就一个查找的过程，在 `oldCh` 中查找 `newVNode.children(newCh)` 中的元素，就是一个很简单的算法题

```js
const updateChildren(oldCh, newCh) {
  for (let i = 0; i < newCh.length; i++) {
    for (let j = 0; i < old.length; j++) {
      if (sameVNode(newCh[i], oldCh[j])) {
        // 找到了，移动节点到相应位置
        break;
      }
    }
    // 没找到，就创建新节点插入到元素中
  }
}
```

如果对算法复杂度比较了解的话，两层循环嵌套，所以算法复杂度为 $O(mn)$，其中 $m$ 为 `oldCh` 的长度，$n$​ 为 `newCh` 的长度。但是 Vue 做了一个小小的优化，使用 `key` 来对 `oldCh` 中的元素进行索引，复杂度可以降低到 $O(m + n)$。

在 `oldCh` 中查找有两种方法，一种是建立 `key` 和 `index` 的对应关系，通过 `key` 进行查找

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-5.drawio2022-04-22-16-33-41.svg" style="zoom:50%"/>

然后通过 `key` 在 `oldCh` 中查找相应元素

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-6.drawio2022-04-22-16-39-03.svg" style="zoom:50%"/>

上面的算法只有两次循环，一次是遍历 `oldVNode` 构建 `keyToIndex` 这张表，另一次是遍历 `newVNode`，根据表查找相应元素，由于两次循环是独立的，而不是嵌套的，总时间为二者之和而不是二者之积，算法复杂度为 $O(m + n)$​​。

那如果没有 `key` 呢？那就不能构建 `keyToIndex` 这张表来帮助我们加速，我们只能通过遍历 `oldVNode` 来查找元素

```js
function findIndexInOld(node, oldCh, start, end) {
  for (let i = start; i <= end; i++) {
    if (isDef(oldCh[i]) && sameVNode(oldCh[i], node)) {
      return i
    }
  }
}
```

也就是相当于两层循环，时间的复杂度还是 $O(mn)$。

所以在这里大家是不是明白了，为什么 ***Vue*** 推荐我们在使用 `v-for` 的时候加上 `key`，可以帮助我们将算法的时间复杂度从 $O(mn)$ 降低到 $O(m + n)$​​​​​​​​，数据量越大，性能提升的越明显。那为什么只需要在 `v-for` 的时候加上 `key` 呢，既然加上 `key` 有这等好处，我们应该在每个元素上面都加上 `key`。

原因**我认为**有两个：

1. 除了在 `v-for` 的时候，会导致 `children` 的长度较长，其他的场景并不会有很长的 `children`

   ```html
   <div>
     <div>Hello World</div>
     <div>
       <span>QQ</span>
       <span>WeChat</span>
     </div>
     <div>
       <span>UC</span>
       <span>DingDing</span>
       <span>GaoDe</span>
     </div>
   </div>
   ```

   `children` 的大小也就为 3，那么 $3 \times 3$ 与 $3 + 3$ 结果的差距很小，对于数据量较小的情况下，$O(mn)$ 与 $O(m + n)$​ 差距不明显，如果为了这么点效率就要求每个元素都加上 `key`，势必会增加开发者的负担，随之使用的开发者就会更少，不利于框架推广。

2. 除了 `v-for` 循环产生的列表，因为列表发生改变导致 DOM 结构产生较大的变化，其他情况都不会导致 DOM 结构产生较大变化，这就使得 `newCh` 和 `oldCh` 中的结构几乎一致，大概率只是文本的变化，我们查找起来根本不需要深层次的遍历，可能在开头就已经找到了，大概率算法复杂度只有 $O(max(m, n))$​。

   <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-7.drawio2022-04-22-16-47-11.svg" style="zoom:50%"/>

## key 不是一一绑定

在更新的过程中，我们是根据 `key` 来找到对应的节点的，所以我们是希望 `key` 和节点是一一绑定的关系，`key` 是唯一的标识，如果 `key` 不是唯一绑定的关系，会发生什么，我将其分为两种情况

- 使用 `index` 作为 `key`，此时节点和 `key` 并不是绑定的关系，相同的 `key` 可以对应于不同的节点
-  `key` 重复的问题，多个节点拥有同样的 `key`

### index 作为 key

```html
<template>
  <div>
    <div v-for="(value, index) in list" :key="index">{{value}}</div>
    <button @click="swap">交换</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: [1, 2, 3]
    }
  },
  methods: {
    delete() {
      this.lisy.splice(0, 1)
    }
  }
}
</script>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-8.drawio2022-04-22-16-49-11.svg" style="zoom:50%"/>

`key` 相同，但是内容不同，无法进行复用，这个时候需要操作 DOM 更改内容，但是如果使用唯一标识作为 `key` 的话

```html
<template>
  <div>
    <div v-for="value in list" :key="value">{{value}}</div>
    <button @click="swap">交换</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: [1, 2, 3]
    }
  },
  methods: {
    delete() {
      this.lisy.splice(0, 1)
    }
  }
}
</script>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-9.drawio2022-04-22-16-50-01.svg" style="zoom:50%"/>

### key 重复

在更新 DOM 的时候，我们会根据 `oldVNode` 制作出一张 `keyToIndex` 的表，如果有 key 重复的情况下，相同的 `key` 返回的时后面 `VNode` 的下标

```html
<template>
  <div>
    <div v-for="value in list" :key="value">{{value}}</div>
    <button @click="swap">交换</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: [1, 1, 2, 3]
    }
  },
  methods: {
    delete() {
      this.lisy.splice(0, 1)
    }
  }
}
</script>
```

| key  | index |
| ---- | ----- |
| `1`  | `1`   |
| `2`  | `2`   |
| `3`  | `3`   |

这就使得前面的 `VNode` 得不到复用(***Vue*** 有做一个优化，前面的 `VNode` 还是有可能得到复用的)。

## 优化

针对我们对数组常见的操作，例如移动数组元素，删除元素，添加元素，***Vue*** 有做一个优化，会先进行四组比较

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-10.drawio2022-04-22-16-51-59.svg" style="zoom:50%"/>

## 源码

```js
function patchVnode (
  oldVnode,
  vnode,
  insertedVnodeQueue,
  ownerArray,
  index,
  removeOnly
) {
  if (oldVnode === vnode) {
    return
  }
  if (isDef(vnode.elm) && isDef(ownerArray)) {
    // clone reused vnode
    vnode = ownerArray[index] = cloneVNode(vnode);
  }
  var elm = vnode.elm = oldVnode.elm;
  if (isTrue(oldVnode.isAsyncPlaceholder)) {
    if (isDef(vnode.asyncFactory.resolved)) {
      hydrate(oldVnode.elm, vnode, insertedVnodeQueue);
    } else {
      vnode.isAsyncPlaceholder = true;
    }
    return
  }
  // reuse element for static trees.
  // note we only do this if the vnode is cloned -
  // if the new node is not cloned it means the render functions have been
  // reset by the hot-reload-api and we need to do a proper re-render.
  if (isTrue(vnode.isStatic) &&
    isTrue(oldVnode.isStatic) &&
    vnode.key === oldVnode.key &&
    (isTrue(vnode.isCloned) || isTrue(vnode.isOnce))
  ) {
    vnode.componentInstance = oldVnode.componentInstance;
    return
  }
  var i;
  var data = vnode.data;
  if (isDef(data) && isDef(i = data.hook) && isDef(i = i.prepatch)) {
    i(oldVnode, vnode);
  }
  var oldCh = oldVnode.children;
  var ch = vnode.children;
  if (isDef(data) && isPatchable(vnode)) {
    for (i = 0; i < cbs.update.length; ++i) { cbs.update[i](oldVnode, vnode); }
    if (isDef(i = data.hook) && isDef(i = i.update)) { i(oldVnode, vnode); }
  }
  if (isUndef(vnode.text)) {
    if (isDef(oldCh) && isDef(ch)) {
      if (oldCh !== ch) { updateChildren(elm, oldCh, ch, insertedVnodeQueue, removeOn
    } else if (isDef(ch)) {
      if (process.env.NODE_ENV !== 'production') {
        checkDuplicateKeys(ch);
      }
      if (isDef(oldVnode.text)) { nodeOps.setTextContent(elm, ''); }
      addVnodes(elm, null, ch, 0, ch.length - 1, insertedVnodeQueue);
    } else if (isDef(oldCh)) {
      removeVnodes(oldCh, 0, oldCh.length - 1);
    } else if (isDef(oldVnode.text)) {
      nodeOps.setTextContent(elm, '');
    }
  } else if (oldVnode.text !== vnode.text) {
    nodeOps.setTextContent(elm, vnode.text);
  }
  if (isDef(data)) {
    if (isDef(i = data.hook) && isDef(i = i.postpatch)) { i(oldVnode, vnode); }
  }
}
```

```js
function updateChildren (parentElm, oldCh, newCh, insertedVnodeQueue, removeOnly) {
  var oldStartIdx = 0;
  var newStartIdx = 0;
  var oldEndIdx = oldCh.length - 1;
  var oldStartVnode = oldCh[0];
  var oldEndVnode = oldCh[oldEndIdx];
  var newEndIdx = newCh.length - 1;
  var newStartVnode = newCh[0];
  var newEndVnode = newCh[newEndIdx];
  var oldKeyToIdx, idxInOld, vnodeToMove, refElm;

  // removeOnly is a special flag used only by <transition-group>
  // to ensure removed elements stay in correct relative positions
  // during leaving transitions
  var canMove = !removeOnly;

  if (process.env.NODE_ENV !== 'production') {
    checkDuplicateKeys(newCh);
  }

  while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
    if (isUndef(oldStartVnode)) {
      oldStartVnode = oldCh[++oldStartIdx]; // Vnode has been moved left
    } else if (isUndef(oldEndVnode)) {
      oldEndVnode = oldCh[--oldEndIdx];
    } else if (sameVnode(oldStartVnode, newStartVnode)) {
      patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx);
      oldStartVnode = oldCh[++oldStartIdx];
      newStartVnode = newCh[++newStartIdx];
    } else if (sameVnode(oldEndVnode, newEndVnode)) {
      patchVnode(oldEndVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx);
      oldEndVnode = oldCh[--oldEndIdx];
      newEndVnode = newCh[--newEndIdx];
    } else if (sameVnode(oldStartVnode, newEndVnode)) { // Vnode moved right
      patchVnode(oldStartVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx);
      canMove && nodeOps.insertBefore(parentElm, oldStartVnode.elm, nodeOps.nextSibling(oldEndVnode.elm));
      oldStartVnode = oldCh[++oldStartIdx];
      newEndVnode = newCh[--newEndIdx];
    } else if (sameVnode(oldEndVnode, newStartVnode)) { // Vnode moved left
      patchVnode(oldEndVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx);
      canMove && nodeOps.insertBefore(parentElm, oldEndVnode.elm, oldStartVnode.elm);
      oldEndVnode = oldCh[--oldEndIdx];
      newStartVnode = newCh[++newStartIdx];
    } else {
      if (isUndef(oldKeyToIdx)) { oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx); }
      idxInOld = isDef(newStartVnode.key)
        ? oldKeyToIdx[newStartVnode.key]
      : findIdxInOld(newStartVnode, oldCh, oldStartIdx, oldEndIdx);
      if (isUndef(idxInOld)) { // New element
        createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx);
      } else {
        vnodeToMove = oldCh[idxInOld];
        if (sameVnode(vnodeToMove, newStartVnode)) {
          patchVnode(vnodeToMove, newStartVnode, insertedVnodeQueue, newCh, newStartIdx);
          oldCh[idxInOld] = undefined;
          canMove && nodeOps.insertBefore(parentElm, vnodeToMove.elm, oldStartVnode.elm);
        } else {
          // same key but different element. treat as new element
          createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx);
        }
      }
      newStartVnode = newCh[++newStartIdx];
    }
  }
  if (oldStartIdx > oldEndIdx) {
    refElm = isUndef(newCh[newEndIdx + 1]) ? null : newCh[newEndIdx + 1].elm;
    addVnodes(parentElm, refElm, newCh, newStartIdx, newEndIdx, insertedVnodeQueue);
  } else if (newStartIdx > newEndIdx) {
    removeVnodes(oldCh, oldStartIdx, oldEndIdx);
  }
}
```

更新虚拟的DOM的过程其实就是一个深度优先遍历

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/虚拟DOM-Page-11.drawio2022-04-22-16-55-34.svg" style="zoom:50%"/>


