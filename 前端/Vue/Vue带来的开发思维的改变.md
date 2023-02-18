---
tags: [Vue]
---

假设有这么一个要求，要我们根据一个数组渲染出一个列表，对于下面的数组

```javascript
const courses = ['语文', '数学', '英语'];
```

要求渲染出下面的 HTML 结构

```html
<ul>
    <li>语文</li>
    <li>数学</li>
    <li>英语</li>
</ul>
```

所以我们会写出这样的代码

```javascript
const courses = ['语文', '数学', '英语'];

const ulEle = document.createElement('ul');
courses.forEach(item => {
    const liEle = document.createElement('li');
    liEle.innerText = item;
    ulEle.append(liEle);
})

document.body.append(ulEle);
```

如果数组的内容发生改变，我们还需要**手动**更新上述的 HTML 结构。所以传统的开发方式为更新数据，根据数据操作 DOM 树。

那么使用 Vue 会有什么不同，来看一个使用 Vue 的方式来实现上述的功能

```html
<div id="app">
    <ul>
        <li v-for="course in courses" :key="course">{{course}}</li>
    </ul>
</div>
<script src="vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data() {
            return {
                courses: ['语文', '数学', '英语']
            }
        },
    })
</script>
```

上面的程序可能看不懂，毕竟还没有开始学，所以不必担心。从代码量上看，二者似乎相差不大，可能还有点多，但是当我们更新数组时，我们不必操作 DOM 树来更新页面了，当数据发生变化时，Vue 自动地帮我们更新页面，这种模式我们称为 MVVM，其中的 V 表示 View，表示的视图，而 M 表示 Model，表示的是数据，而 `Vue` 充当的角色是 VM，它将数据与视图进行了绑定，当数据发生变化时，视图也会相应的**自动**更新，如下图

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210325225532.png" style="zoom:50%;" />

所以 Vue 给我们带来开发思维上的改变就是，**我们只需要操作数据即可，更新页面的工作 Vue 会自动的帮我们完成。**

