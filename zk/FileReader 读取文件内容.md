---
title: FileReader的使用
created: 2023/2/14 14:44:18
updated: 2023/2/14 19:26:21
tags: [undefined]
---

使用 FileReader 读取二进制文件内容，提供了不同的方法来解析提交的二进制内容

-  `readAsText`：将二进制内容解析为字符串，用以读取文本文件
- `readAsArrayBuffer`：将文件解析为 ArrayBuffer，一般用以处理二进制文件，比如 Word、PPT 等文件
- `readAsDataURL`：返回一个 Data URL，一般读取图片、音视频为此格式，然后作为 `src` 属性的值进行展示
- `readAsBinaryString`：用的很少，目前还不知道有什么用

读取文本：

```js
const reader = new FileReader();
reader.addEventListener('load', () => {
  const text = reader.result;
  console.log(result);
});
reader.readAsText(file);
```

读取图片并显示：

```js
const reader = new FileReader();
reader.addEventListener('load', () => {
  const imgUrl = reader.result;
  img.src = imgUrl;
});
reader.readAsDataURL(file);
```

读取图片并对其进行置灰，然后显示：

```js
const inout = document.getElementById('fileInput')
inout.addEventListener('change', (e) => {
  const file = e.target.files[0];
  const reader = new FileReader();
  reader.addEventListener('load', () => {
    const image = new Image();
    image.src = reader.result;
    // 使用 canvas 操作图片数据
    image.onload = () => {
      const canvas = document.createElement('canvas');
      const ctx = canvas.getContext('2d');
      canvas.width = image.width;
      canvas.height = image.height;
      // 将图片放在画布上
      ctx.drawImage(image, 0, 0, image.width, image.height);
      const imageData = ctx.getImageData(0, 0, image.width, image.height);
      const data = imageData.data;
      // 将图片置灰
      for (let i = 0; i < data.length; i += 4) {
        // data[i] -> red data[i + 1] -> green data[i + 2] -> blue data[i + 4] -> alpha
        const gray = 0.3 * data[i] + 0.59 * data[i + 1] + 0.11 * data[i + 2];
        data[i] = gray;
        data[i + 1] = gray;
        data[i + 2] = gray;
      }
      // 将处理后的图片数据放置在画布上
      ctx.putImageData(imageData, 0, 0);
      document.getElementById('img').src = canvas.toDataURL();
    }
  });
  
  reader.readAsDataURL(file);
}
```

