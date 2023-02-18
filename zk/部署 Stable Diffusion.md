---
tags: [深度学习, 踩坑, 部署, Stable Diffusion]
---

近期公司要求部署一个 Stable Diffusion 给运营玩玩，我只是个前端啊，但是没办法。

我使用的 AutoDL 这个平台，首先租了一个机器，用的卡是 A5000，显存 24GB。

首先需要通过 ssh 生成公钥和密钥，然后将公钥添加到 Github 上，否则下载不了 Github 上的项目。这个如何配置参考 Github 的文档吧，我也是跟着文档一步步来的。

然后下载 Stable Diffusion 的[官方仓库](https://github.com/CompVis/stable-diffusion)，不要通过 https://github.com 的链接下载仓库，要通过 git@github.com，可能是国内环境的原因，不清楚。下载完之后进入项目目录

```shell
conda env create -f environment.yaml
```

但是每次我在这一步都失败，每次都是 Github 仓库下载不下来，我看了一下 `environment.yaml` 里面的内容，发现链接用的是 `git+https://github.com/xxx`，我将其改为了 `git+ssh://git@github.com/xxx`，然后就成功了。参考 [这个 Gist](https://gist.github.com/javrasya/e95ade856ff42e4649972f8a54368459)。

接着激活刚刚创建的环境

```shell
conda activate ldm
```

然后更新包为最新的

```shell
conda install pytorch torchvision -c pytorch
pip install transformers==4.19.2 diffusers invisible-watermark
pip install -e .
```

最后一步，下载训练好的模型参数，要去 [Hugging Face](https://huggingface.co/CompVis/stable-diffusion-v-1-4-original) 这个网站，需要注册一个账号，使用邮箱注册就行，然后下载，我下载的是 `sd-v1-4.ckpt`，这个文件有 4.7G，要下蛮久的，下好之后把它放在项目的 `models/ldm/stable-diffusion-v1` 这个目录下，并命名为 `model.ckpt`。

然后在项目的根目录，运行如下命令即可愉快的使用了

```shell
python scripts/txt2img.py --prompt "a photograph of an astronaut riding a horse" --plms 
```

命令包含的所有选项及含义可以参考 Github 文档。
