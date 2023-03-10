`readlink -f`：读取最终链接的文件？

```bash
sudo apt install vim
```

两种模式：

- 普通模式
- 插入模式

按 `i`  进入插入模式。

| 按键     | 作用                                                      |
| -------- | --------------------------------------------------------- |
| h        | 左 ←                                                      |
| j        | 下↓                                                       |
| k        | 上↑                                                       |
| l        | 右 →                                                      |
| Ctrl + F | Page Down，向下翻页                                       |
| Ctrl + B | Page Up，向上翻页                                         |
| G        | 移到最后一行                                              |
| num + G  | 移动到第 num 行，3G → 移动到第三行                        |
| gg       | 移动到第一行                                              |
| x        | 删除所在位置字符                                          |
| dd       | 删除所在行                                                |
| num + dd | 删除下面 num 行（包括当前行），5dd → 删除 5 行            |
| dw       | 删除所在位置单词                                          |
| d$       | 删除所在位置至行尾的内容                                  |
| u        | **撤销**                                                  |
| r char   | 用 char 替换当前位置字符                                  |
| R text   | 用 text 覆盖当前位置数据，直至按下 ESC                    |
| J        | 删除行尾换行符（拼接行）                                  |
| y        | 复制，3yy → 向下复制 3 行，y$ → 复制到行尾，yw → 复制单词 |
| p        | 粘贴                                                      |
| dd + p   | 剪切                                                      |
| y + p    | 复制                                                      |

命令模式：在普通模式下按下 `:` 键

- `q`：未修改数据退出
- `q!`：修改了数据，但不保存退出（强制退出）
- `w filename`：另存为 filename
- `wq`：保存并退出

可视模式：移动光标时高亮显示文本，可以看到复制的内容。移动光标到要复制的位置，按下` v` 键进入可视模式，移动光标覆盖要复制的文本，按下 `y` 键进行复制，按下 `p` 键进行粘贴。

- 查找：普通模式下按下 `/要查找的字符`，按 `n(next)` 表示下一个
- 替换：
  - `:s/old/new/g`
  - `n,ms/old/new/g`：指定范围，`[n, m]` 行
