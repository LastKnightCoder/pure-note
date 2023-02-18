使用 React 之后，通过更新数据即可驱动视图发生变化，我们无需通过手动的操作 DOM 来更新视图，意味着我们无需与 DOM 打交道。但是当我们使用第三方的 DOM 库时，我们不得不要获得 DOM 元素，所以接下来的问题就是如果在 React 中获得 DOM 元素。

要获得 DOM 元素要通过 `ref` 这个属性，它可以有三种取值：

- 字符串
- 回调函数
- `React.createRef()`

```note
其中字符串形式的取值由于有性能上的问题，即将被淘汰，官方不建议使用，因此此部分不要求掌握，了解即可。
```

接下来我们使用 `chart.js` 绘制一个柱状图作为例子，例子如下

```html
<div id="container" style="width: 400px; height: 300px;">
  <canvas id="chart" width="400" height="300"></canvas>
</div>
<script src="https://cdn.jsdelivr.net/npm/chart.js@3.8.0/dist/chart.min.js"></script>

<script>
  const data = {
    labels: ["语文", "数学", "英语", "物理", "化学", "生物"],
    datasets: [
      {
        label: "各科成绩",
        data: [65, 59, 80, 81, 56, 55],
        backgroundColor: "rgb(75, 192, 192)",
      },
    ],
  };

  const ctx = document.getElementById("chart").getContext("2d");
  const chart = new Chart(ctx, {
    type: "bar",
    data: data,
  });
</script>
```

Chart 需要传入 `canvas` 的上下文对象，而上下文对象需要通过 `canvas` DOM 对象获取。要在 `React` 中使用 `chart.js`，就要解决如何获得 DOM 对象的问题。

效果如下

<iframe src="https://lastknightcoder.github.io/react-demos/react05.html" style="--width: 800px" class="extra-width-center" height="440"></iframe>

下面将上面的柱状图 封装为一个 `React` 组件，将 `width`、`height`以及 `data` 作为组件的参数传入。

## 字符串

要获得 JSX 中标签对应的 DOM 元素，需要在标签上设置一个 `ref` 属性，它可以设置三种不同的取值。第一种取值是一个字符串，在组件挂载之后，该字符串和与之对应的 DOM 对象会作为实例属性 `refs` 这个对象上一个键值对，我们便可以通过实例的 `refs` 属性获得 DOM 元素。

```jsx
class BarChart extends React.Component {
  componentDidMount() {
    const chart = this.refs.chart
    const ctx = chart.getContext('2d')
    new Chart(ctx, {
      type: 'bar',
      data: this.props.data,
    })
  }

  render() {
    return (
      <div>
        <canvas
          ref="chart"
          width={this.props.width}
          height={this.props.height}
        ></canvas>
      </div>
    )
  }
}
```

在如上代码中，我们为 `canvas` 标签添加了 `ref="chart"`，当组件挂载之后，`componentDidMount` 这个生命周期函数会被调用，我们在其中中通过 `this.refs.chart` 访问到 `canvas` 对应的 DOM 对象。

<iframe src="https://lastknightcoder.github.io/react-demos/react07.html?x=2"  style="--width: 600px" class="extra-width-center" height="400"></iframe>

## 回调函数

`ref` 的第二种取值为回调函数，当组件挂载到页面上时，会调用该回调函数，并将 DOM 对象作为参数传入。

```jsx
class BarChart extends React.Component {
  renderGraph = (chart) => {
    const ctx = chart.getContext('2d')
    new Chart(ctx, {
      type: 'bar',
      data: this.props.data,
    })
  }

  render() {
    return (
      <div>
        <canvas
          ref={this.renderGraph}
          width={this.props.width}
          height={this.props.height}
        ></canvas>
      </div>
    )
  }
}
```

上述代码中我们将 `ref` 设置为了 `this.renderGraph`，当页面挂载到元素上时，会调用该回调函数，并将对应的 DOM 元素传入，拿到 DOM 元素后便可以渲染图形了。

<iframe src="https://lastknightcoder.github.io/react-demos/react08.html" style="--width: 800px" class="extra-width-center"  height="420"></iframe>

````note
`ref` 的值也可以是内联的回调函数，如

```jsx
<canvas
  ref={(chart) => {
    const ctx = chart.getContext('2d')
    new Chart(ctx, {
      type: 'bar',
      data: this.props.data,
    })
  }}
></canvas>
```

不同的是，内联形式的函数在更新时会调用两次，第一次传入的是 `null`，第二次传入的才是对应的 DOM 元素，因此在使用时要先进行判空

```jsx
<canvas
  ref={(chart) => {
    if (chart) {
      const ctx = chart.getContext('2d')
      new Chart(ctx, {
        type: 'bar',
        data: this.props.data,
      })
    }
  }}
></canvas>
```
````

## React.createRef

使用 `React.createRef` 相当于先准备一个放置 DOM 元素的容器，当组件挂载之后，会将相应的 DOM 元素放置在容器中，然后我们便可以使用该容器的 `current` 属性来获取 DOM 元素。

```jsx
class BarChart extends React.Component {
  chart = React.createRef()
  componentDidMount() {
    const ctx = this.chart.current.getContext('2d')
    new Chart(ctx, {
      type: 'bar',
      data: this.props.data,
    })
  }

  render() {
    return (
      <div>
        <canvas
          ref={this.chart}
          width={this.props.width}
          height={this.props.height}
        ></canvas>
      </div>
    )
  }
}
```

如上，首先我们准备了一个容器

```jsx
chart = React.createRef()
```

然后将其作为 `ref` 属性的值

```jsx
<canvas
  ref={this.chart}
  width={this.props.width}
  height={this.props.height}
></canvas>
```

当组件挂载之后，我们在 `componentDidMount` 方法中通过容器的 `current` 属性拿到对应的 DOM 元素，然后渲染图形

```jsx
const ctx = this.chart.current.getContext('2d')
```

<iframe src="https://lastknightcoder.github.io/react-demos/react09.html" style="--width: 800px" class="extra-width-center" height="440"></iframe>


```antd
const { Nav } = components

const root = ReactDOM.createRoot(el)
root.render(<Nav prev="生命周期函数" />)
```
