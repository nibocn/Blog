---
title: 玩转 Vim 笔记
date: 2019-06-25 17:00
permalink: play-vim/
categories:
  - Vim
tags:
  - Vim
---

![Cover][]

*根据慕课网视频《玩转 Vim 从放弃到爱不释手》整理。地址：https://www.imooc.com/learn/1129*

## 初始 Vim
### Vim 插入模式小技巧
| 模式   | 命令    | 描述                           |
|--------|---------|--------------------------------|
| insert | `<C-h>` | 删除上一个字符                 |
| insert | `<C-w>` | 删除上一个单词                 |
| insert | `<C-u>` | 删除当前行                     |
| normal | `gi`    | 快速跳转到最后编辑的位置并插入 |

### Vim 快速移动大法
| 模式   | 命令            | 描述                           |
|--------|-----------------|--------------------------------|
| normal | `w`、`W`        | 移到下一个 word/WORD 开头      |
| normal | `e`、`E`        | 移到下一个 word/WORD 结尾      |
| normal | `b`、`B`        | 回到上一个 word/WORD 开头      |
| normal | `f{char}`       | 移动到当前行 char 字符上       |
| normal | `t{char}`       | 移动到当前行 char 的前一个字符 |
| normal | `0`             | 移动到行首第一个字符           |
| normal | `^`             | 移动到第一个非空白字符         |
| normal | `$`             | 移动到行尾                     |
| normal | `g_`            | 移动到行尾非空白字符           |
| normal | `(`、`)`        | 在句子间上下移动               |
| normal | `{`、`}`        | 在段落间上下移动               |
| normal | `gg`            | 移动到文件开头                 |
| normal | `G`             | 移动到文件结尾                 |
| normal | `Ctrl+o`        | 快速返回到上一个位置           |
| normal | `H`             | 跳转到屏幕开头（Head）         |
| normal | `M`             | 跳转到屏幕中间（ Middle）      |
| normal | `L`             | 跳转到屏幕结尾（Lower）        |
| normal | `<C-u>`         | 向上翻页（upword）             |
| normal | `<C-f>`         | 向下翻页（forward）            |
| normal | `zt`            | 把当前行放置到屏幕顶部         |
| normal | `zz`            | 把当前行放置到屏幕中间         |
| normal | `zb`            | 把当前行放置到屏幕底部         |
| normal | <code>`^</code> | 快速跳转到最后编辑的位置       |

*Tips:*

- word 指的是以非空白字符分割的单词，WORD 以空白字符分割的单词。
- `f{char}`、`f{char}` 可以用 `;`、`,` 继续搜索该行上的下一个/上一个。

<!-- more -->

### Vim 如何搜索替换
`:[rang] s[ubstitute]/{pattern}/{string}/[flags]`

- `rang`：表示范围，比如：`10,20` 表示 10-20 行，`%` 表示全部。
- `pattern`：表示要替换的模式（支持：正则）。
- `string`：替换后的文本。
- `flags`：替换标志位。`g(global)` 表示全局范围内执行；`c(confirm)` 表示确认，可以确认或拒绝修改；`n(number)` 报告匹配到的次数而不替换。

*Tips:*

- 单词精确匹配，可使用 `<` 和 `>`，如：`:% s/\<hello\>/world/g`，将所有的 hello 替换为 world。

### Vim 多文件操作
**Buffer 缓冲区**

| 模式    | 命令             | 描述                |
|---------|------------------|---------------------|
| command | `:ls`            | 列举当前缓冲区      |
| command | `:b n`           | 跳转到第 n 个缓冲区 |
| command | `:bpre`          | 上一个缓冲区        |
| command | `:bnext`         | 下一个缓冲区        |
| command | `:bfirst`        | 第一个缓冲区        |
| command | `:blast`         | 最后一个缓冲区      |
| command | `:b buffer_name` | 加上 Tab 补全来跳转 |

**Window 窗口**

| 模式    | 命令                          | 描述                      |
|---------|-------------------------------|---------------------------|
| normal  | `<C-w>s`                      | 水平分割                  |
| command | `:sp`                         | 水平分割                  |
| normal  | `<C-w>v`                      | 垂直分割                  |
| command | `:vs`                         | 垂直分割                  |
| normal  | `<C-w>w`                      | 在窗口间循环切换          |
| normal  | `<C-w>h`                      | 切换到左边的窗口          |
| normal  | `<C-w>j`                      | 切换到下边的窗口          |
| normal  | `<C-w>k`                      | 切换到上边的窗口          |
| normal  | `<C-w>l`                      | 切换到右边的窗口          |
| normal  | `<C-w>H`                      | 将窗口移动到左边          |
| normal  | `<C-w>J`                      | 将窗口移动到下边          |
| normal  | `<C-w>K`                      | 将窗口移动到上边          |
| normal  | `<C-w>L`                      | 将窗口移动到右边          |
| normal  | `<C-w>=`                      | 使所有窗口等宽、等高      |
| normal  | `<C-w>_`                      | 最大化活动窗口的高度      |
| normal  | `<C-w>`<code>&#124;</code>    | 最大化活动窗口的宽度      |
| normal  | `[n]<C-w>_`                   | 把活动窗口的高度设为 n 行 |
| normal  | `[n]<C-w>`<code>&#124;</code> | 把活动窗口的宽度设为 n 行 |

### Vim 复制粘贴与寄存器
- 通过 `"{register}` 前缀可以指定寄存器，不指定默认用无名寄存器。如： `"ayiw` 复制一个单词到寄存器 `a` 中，`"bdd` 删除当前行到寄存器 `b` 中。
- 复制专用寄存器 `"0` 使用 `y` 复制文本同时会被 拷到复制寄存器 `0`。
- 系统剪贴板 `"+` 可以在复制前加上 `"+` 复制到系统剪贴板。*注：*需要支持 `:echo has('clipboard')` 返回结果为：1。
- 其它一些寄存器，如： `".` 上次插入的文本； `"%` 当前文件名。

*Tips:*

- `:set clipboard=unnamed` 可以直接复制粘贴系统剪贴板的内容。

### Vim 宏
- 使用 `q` 来进行录制，同时使用 `q` 来结束录制。
- 使用 `q{register}` 选择要保存的寄存器，把录制的命令保存其中。
- 使用 `@{register}` 回放寄存器中保存的一系列命令，达到批量操作。

### Vim 补全
| 模式   | 命令         | 描述                  |
|--------|--------------|-----------------------|
| insert | `<C-n>`      | 普通关键字（*）       |
| insert | `<C-x><C-n>` | 当前缓冲区关键字      |
| insert | `<C-x><C-i>` | 包含文件关键字        |
| insert | `<C-x><C-]>` | 标签文件关键字        |
| insert | `<C-x><C-k>` | 字典查找              |
| insert | `<C-x><C-l>` | 整行补全              |
| insert | `<C-x><C-f>` | 文件名补全（*）       |
| insert | `<C-x><C-o>` | 全能（Omni）补全（*） |

## Vim 配置

*Tips:*

- Vim 键位映射时尽量使用 `*noremap` 非递归映射，而不要使用 `*map` 的递归映射。

## Vim 插件
`vim-plug` 插件管理。

- vim 主题：
  - `gruvbox`。
  - `vim-colors-solarized`。
  - `vim-hybird`。
- `vim-startify`： 打开 Vim 时显示最近编辑的文件列表（Vim 启动界面）。
- `vim-airline`： 状态栏美化。
- `indentLine`： 增加代码缩进线。
- `NERDTree`： 显示文件目录树
- `LeaderF`： 文件搜索。
- `vim-easymotion`： 光标快速跳转（墙裂推荐）。
- `vim-surround`： 成对内容操作（主要是符号）。
- `fzf.vim`： 模糊搜索。
- `far.vim`： 搜索替换。
- `vim-interestingwords`： 单词高亮。
- `coc.vim`： 代码补全。
- `vim-go`： 编写 Go 代码。
- `python-mode`： 编写 Python 代码。
- `tagbar`： 代码大纲。
- 代码格式化：
  - `neoformat`。
  - `vim-autoformat`。
- 代码静态检查：
  - `ale`。
  - `neomake`。
- `vim-commentary`： 代码注释。
- `vim-fugitive`： 在 vim 中使用 git 命令。
- `vim- gitgutter`： 在 vim 中显示 git 文件变动。

## Vim 学习资料
- 《Practical vim》（中文：《Vim 实用技巧》）。
- 《笨方法学 Vimscript》。


[Cover]: /uploads/play-vim/cover.png
