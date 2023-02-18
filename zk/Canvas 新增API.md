---
tags: [Canvas, 新特性]
---

## CSS

1.  圆角矩形
    
    ```js
    ctx.roundRect(upper, left, width, height, borderRadius);
    ```
    
2.  圆锥渐变
    
    ```js
    const grad = ctx.createConicGradient(0, 100, 100);
    
    grad.addColorStop(0, "red");
    grad.addColorStop(0.25, "orange");
    grad.addColorStop(0.5, "yellow");
    grad.addColorStop(0.75, "green");
    grad.addColorStop(1, "blue");
    ```
    
3.  **Text modifiers**
    
    -   `ctx.letterSpacing`
    -   `ctx.wordSpacing`
    -   `ctx.fontVariant`
    -   `ctx.fontKerning`
    -   `ctx.fontStretch`
    -   `ctx.textDecoration`
    -   `ctx.textUnderlinePosition`
    -   `ctx.textRendering`

## 友好代码

1.  `context.reset()`：重置画布
2.  **Filters**
    -   引用 SVG 画布
        
        ```html
        <svg>
            <defs>
             <filter id="svgFilter">
               <feGaussianBlur in="SourceGraphic" stdDeviation="5"/>
               <feConvolveMatrix kernelMatrix="-3 0 0 0 0.5 0 0 0 3"/>
               <feColorMatrix type="hueRotate" values="90"/>
             </filter>
            </defs>
         </svg>
        ```
        
        ```jsx
        ctx.filter = "url('#svgFilter')";
        ```
        
    -   纯 JavaScript
        
        ```js
        ctx.filter = new CanvasFilter([
            {filter: "gaussianBlur", stdDeviation: 5},
            {filter: "convolveMatrix",
              kernelMatrix: [
                [-3, 0, 0],
                [0, 0.5, 0],
                [0, 0, 3]]
            },
            {filter: "colorMatrix", type: "hueRotate", values: 90},
          ]);
        ```
        

## 性能改善

1.  **Will Read Frequently**
    
    告知这个 Canvas 会被频繁读，可以加快速度，提升性能
    
    ```js
    const ctx = canvas.getContext('2d', {'willReadFrequently': true});
    ```
    
2.  **Context loss**
    
    由于 GPU 耗尽或其他问题导致 Canvas 崩溃，可以通过该 API 进行处理，并且在必要时进行重绘