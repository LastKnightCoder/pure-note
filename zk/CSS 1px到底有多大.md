---
title: 1px到底有多大
created: 2023/2/15 10:15:35
updated: 2023/2/15 10:15:35
tags: [CSS, 思考, px]
---

现在有一个灵魂问题，`1px` 的大小是多大?

要回答这个问题，首先要区分两种像素，一种是物理像素(或者称为设备像素)，一种是逻辑像素(或者称为设备独立像素、CSS 像素)，我们讨论的是一个 CSS 像素有多大。

屏幕是通过一个个的发光体发出不同的颜色的光让我们看到画面的，这一个个的发光体的大小可以认为是一个物理像素大小。不同的机器像素的密度是不同的，一般我们衡量密度的方法为每英寸上有多少像素(pixel per inch, ppi)，以我的机器为例，我的屏幕分辨率为 $2560 \times 1600$，而我的屏幕尺寸为 13.3 英寸(对角线的长度)，所以我的屏幕的 ppi 为

$$
\dfrac{\sqrt{(2560^2 + 1600^2)}}{13.3} = 227 \text{ppi}
$$

所以在我的机器上一个物理像素的长度为 `1/ppi = 1/227 = 0.0044英寸`。

那 CSS 像素与物理像素之间有什么关系呢。在比较早以前，1 个 CSS 像素就是一个物理像素的大小，所以 `1px` 的计算公式为 `1px = 1 /ppi`。

但是随着苹果提出了 Retina 屏幕的概念，这种屏幕的特点就是高分辨率，在相同的尺寸下具有两倍甚至三倍的像素点，如果这时 `1px` 的计算方式不变的话，随着 `ppi` 的增大，`1px` 就越小，在相同尺寸的高分辨率的屏幕下显示的很小，这十分的不合理，因此我们需要提出一种与设备无关的像素单位，也就是 CSS 像素。

CSS 像素与物理像素之间的关系使用 **Device Pixel Ratio(dpr)** 来定义，它表示一个 CSS 像素长度相当于 dpr 个物理像素长度，dpr 可以通过在系统设置里面设置缩放定义（当缩放网页时，修改的也是 dpr 的值）

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3/202112041620112021-12-04-16-20-12.png" style="zoom:50%"/>

我设置的系统缩放为 `200%`，也就是说 `dpr = 2`，一个 CSS 像素长度相当于两个物理像素长度，也就是说在我的电脑上 CSS 的长度为 `2/227 = 0.0088 英寸`。

CSS 像素的长度在不同的电脑上依据 `ppi` 和 `dpr` 的值具有不同的大小，它的计算公式为

$$
1\text{px} = \dfrac{\text{dpr}}{\text{ppi}}
$$

[W3C CSS specification](https://www.w3.org/TR/CSS2/syndata.html#:~:text=The%20reference%20pixel%20is%20the%20visual%20angle%20of%20one%20pixel%20on%20a%20device%20with%20a%20pixel%20density%20of%2096dpi%20and%20a%20distance%20from%20the%20reader%20of%20an%20arm%27s%20length.) 从视觉的角度给出了一个参考长度

``` info
<p style="font-family: Merriweather;">The reference pixel is the visual angle of one pixel on a device with a pixel density of 96 DPI and a distance from the reader of an arm’s length.</p>

人在离屏幕一臂之遥的位置看去，一个像素的大小看上去是 1/96 英寸的长度。
```

但是我们靠计算机比较近，一般不会有一臂之长，为了使得人眼看的比较舒服，一个 CSS 像素长度会小于 `1/96 英寸`，我粗略统计不同计算机的 CSS 像素长度一般在 `[1/100, 1/130]英寸` 之间，举几个🌰：

1. 我的笔记本电脑的 `ppi` 为 `227`，`dpr` 我设置为 `2`，那么我的 CSS 像素长度为
  $$\dfrac{2}{227} = \dfrac{1}{113.5}$$
2. 我的显示器分辨率为 `2560 * 1440`，尺寸为 `27寸`，`dpr` 设置为 `1`，所以其 CSS 像素长度为为 
  $$\dfrac{1}{\sqrt{(2560^2 + 1440^2)} / (27)} \approx \dfrac{1}{109}$$
3. 实验室同学的电脑分辨率是 $1920 \times 1080$，屏幕大小为 `15.6寸`，推荐系统缩放比为 `125%`，也就是说 `dpr = 1.25`，此时它的 CSS 像素长度为
  $$\dfrac{1.25}{\sqrt{(1920^2 + 1080^2)} / (15.6)} \approx \dfrac{1}{113}$$
4. 最新款 MacBook Pro(2021 年)，官网上给出的 `ppi = 254`，它们的 `dpr = 2`，所以它们的 CSS 像素长度为
  $$\dfrac{2}{254} = \dfrac{1}{127}$$

当我们使用手机时，手机离我们的距离相比于电脑离我们的距离更加的近，所以手机上的 CSS 像素长度更短，这样我们才会看的更加的舒服，否则太大的话，靠的太近看到的都是一个个像素点，体验感不好。以苹果手机为例，它的 CSS 长度大约为 `1/163 英寸`，大部分的安卓手机是 `1 / 160 英寸`，相差不大。

可见在不同的机器上一个 CSS 像素长度是不一样的，不过它们都在一定的范围之内，以保证我们在合适的距离之内看的舒服。

``` info
如果你了解 JavaScript 的话，可以通过 `window.devicePixelRatio` 查看 `dpr` 的大小。
```

在早期，大家都默认一英寸等于 `96px`，为了延续传统，Web 中的 `1in` 是依据 `px` 定义的，始终定义为 `1in = 96px`，但是我们知道一般 `1px < 1/96 英寸`，因此实际上的 `1in < 1英寸`，对于 `cm` 这个单位也是一样，它定义为 `1in = 2.54cm`，同样在网页中 `1cm < 1厘米`，所以在实际中，我们非常少使用 `in` 或 `cm` 作为单位，因为它的数值不准确，会带来误解。

```antd
let { Tabs } = antd;
const { TabPane } = Tabs;
const { ThemeProvider } = components;
Tabs = ThemeProvider(Tabs);

const htmlCode = `\`\`\`html
<p style="font-size: 24px;">Hello World</p>
<p style="font-size: 0.25in;">Hello World</p>
\`\`\``


const html = await renderMarkdown(htmlCode)

const CodeTab = () => (
 <Tabs defaultActiveKey="1">
    <TabPane tab="HTML" key="1">
      <div dangerouslySetInnerHTML={{ __html: html }} />
    </TabPane>
    <TabPane tab="RESULT" key="2">
       <div class='displaybox'>
        <p
        ref={(node) => {
          if (node) 
            node.style.setProperty('font-size', '24px', 'important')
          }
        }>Hello World!</p>
        <p
        ref={(node) => {
          if (node) 
            node.style.setProperty('font-size', '0.25in', 'important')
          }
        }>Hello World!</p>
       </div>
    </TabPane>
  </Tabs>
);
ReactDOM.createRoot(el).render(<CodeTab />)
```


``` warning
在网页中，所有的单位最终都会被换算为 `px`，虽然我们设置的字体大小为 `0.25in`，但是在实际显示的时候已经被换算为了 `px`，也就是 `24px`，我们一般称换后的值为计算值。

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3/202112041838582021-12-04-18-38-59.png" style="zoom:50%"/>
```
