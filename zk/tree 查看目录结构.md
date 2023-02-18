---
title: 使用 tree 命令查看目录结构
created: 2023/2/12 12:27:56
updated: 2023/2/12 12:28:02
tags: [Linux, tree, 命令, 目录]
---

我们可以通过 `ls -R` 来查看一个目录的结构，它可以显示该目录包含的所有文件以及子目录，以及子目录下包含的文件和目录，但是通过 `ls -R` 显示的信息有点难以阅读，在讲解其它命令之前，这里介绍一个 `tree` 的工具，它可以以树状的形式显示目录结构。下面给出两种方案显示目录结构，可以看到 `tree` 命令的输出可读性更强。

```antd
const { Tabs } = antd;
const { TabPane } = Tabs;
const { ThemeProvider } = components;
const ThemeTabs = ThemeProvider(Tabs);

const code = `\`\`\`bash
~ » ls -R
.:
dst  src

./dst:
hello  hello.c

./src:
hello.c  hello.md  hello2.md
\`\`\``

const code2 = `\`\`\`bash
~ » tree
.
├── dst
│   ├── hello
│   └── hello.c
└── src
    ├── hello.c
    ├── hello.md
    └── hello2.md

2 directories, 5 files
\`\`\``


const html = await renderMarkdown(code)
const html2 = await renderMarkdown(code2)

const CodeTab = () => (
 <ThemeTabs defaultActiveKey="1">
    <TabPane tab="ls -R" key="1">
      <div dangerouslySetInnerHTML={{ __html: html }} />
    </TabPane>
    <TabPane tab="tree" key="2">
      <div dangerouslySetInnerHTML={{ __html: html2 }} />
    </TabPane>
  </ThemeTabs>
);
const root = ReactDOM.createRoot(el)
root.render(<CodeTab />)
```


## 安装

`tree` 并不是系统内置的命令，在使用之前需要进行安装，安装命令如下

```antd
const { Tabs } = antd;
const { TabPane } = Tabs;
const { ThemeProvider } = components;
const ThemeTabs = ThemeProvider(Tabs);

const code = `\`\`\`bash
sudo apt install tree
\`\`\``

const code2 = `\`\`\`bash
yum install tree
\`\`\``


const html = await renderMarkdown(code)
const html2 = await renderMarkdown(code2)

const CodeTab = () => (
 <ThemeTabs defaultActiveKey="1">
    <TabPane tab="Ubuntu" key="1">
      <div dangerouslySetInnerHTML={{ __html: html }} />
    </TabPane>
    <TabPane tab="CentOS" key="2">
      <div dangerouslySetInnerHTML={{ __html: html2 }} />
    </TabPane>
  </ThemeTabs>
);
const root = ReactDOM.createRoot(el)
root.render(<CodeTab />)
```


在输入密码之后就会进行下载，然后就可以愉快的使用了。

## 基本用法

`tree` 可以接收一个路径参数，来查看该路径的目录结构，默认会列出该目录下所有的内容

```bash
# 列出 /tmp 的目录结构
~ » tree /tmp
/tmp
├── math
│   ├── add
│   └── add.c
├── tmpscfytyzt
└── tmux-1000
    └── default

2 directories, 4 files
```

当没有为 `tree` 命令指定参数时，默认会列出当前所在路径的目录结构

```bash
# 当前所在路径为家目录，所以 tree 会列出家目录的目录结构
~ » tree
.
├── dst
│   ├── hello
│   └── hello.c
└── src
    ├── hello.c
    ├── hello.md
    └── hello2.md

2 directories, 5 files
```

### -L

有的时候我们根本不需要查看该目录下的所有层级内容，我们可以通过 `-L` 选项指定查看的层级深度

```antd
const { Tabs } = antd;
const { TabPane } = Tabs;
const { ThemeProvider } = components;
const ThemeTabs = ThemeProvider(Tabs);

const code = `\`\`\`bash
~ » tree ~
/home/xt
├── dst
│   ├── hello
│   └── hello.c
└── src
    ├── hello.c
    ├── hello.md
    └── hello2.md

2 directories, 5 files
\`\`\``

const code2 = `\`\`\`bash
# -L 1 查看一个层级的内容
~ » tree -L 1 ~
/home/xt
├── dst
└── src

2 directories, 0 files
\`\`\``


const html = await renderMarkdown(code)
const html2 = await renderMarkdown(code2)

const CodeTab = () => (
 <ThemeTabs defaultActiveKey="1">
    <TabPane tab="查看所有层级的内容" key="1">
      <div dangerouslySetInnerHTML={{ __html: html }} />
    </TabPane>
    <TabPane tab="查看一个层级的内容" key="2">
      <div dangerouslySetInnerHTML={{ __html: html2 }} />
    </TabPane>
  </ThemeTabs>
);
const root = ReactDOM.createRoot(el)
root.render(<CodeTab />)
```

### -s

通过添加 `-s` 选项可以显示文件的大小

```bash
~ » tree -s ~
/home/xt
├── [       4096]  dst
│   ├── [      16696]  hello
│   └── [         75]  hello.c
└── [       4096]  src
    ├── [         75]  hello.c
    ├── [          0]  hello.md
    └── [          0]  hello2.md

2 directories, 5 files
```

### -h

上述显示的文件大小信息的单位为字节，我们可以添加 `-h` 选项，以人类更加可读的形式显示文件大小

```bash
~ » tree -sh ~
/home/xt
├── [4.0K]  dst
│   ├── [ 16K]  hello
│   └── [  75]  hello.c
└── [4.0K]  src
    ├── [  75]  hello.c
    ├── [   0]  hello.md
    └── [   0]  hello2.md

2 directories, 5 files
```

上述文件大小的单位不在仅仅是字节，对于较大的文件会自动选择相应的单位显示。

### -o

`tree` 默认会将目录信息打印在显示器上，我们可以通过 `-o` 选项指定将目录信息输出到某个文件中

```bash
# 将家目录信息输出到 home.txt 中
~ » tree -o home.txt ~
# cat 命令用来查看文件内容，详情见查看文件章节
~ » cat home.txt
/home/xt
├── dst
│   ├── hello
│   └── hello.c
├── home.txt
└── src
    ├── hello.c
    ├── hello.md
    └── hello2.md

2 directories, 6 files
```

上面我们将 `tree` 命令的输出指定到了 `home.txt` 中，然后通过 `cat` 命令查看了 `home.txt` 的内容。

``` info
有关 `cat` 命令的使用，可参考[[查看文件夹内容]]章节。
```

``` note
`tree` 的选项有很多，这里只介绍最常用的几个，如果需要了解更多内容，请通过 `man tree` 了解更多信息。
```
