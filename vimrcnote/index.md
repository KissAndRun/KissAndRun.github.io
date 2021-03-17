# 我的 Vimrc 快捷键备忘


我的 Vim 配置文件放置在我的 [Github](https://github.com/KissAndRun/dotfiles/blob/master/.vimrc) 上，如有兴趣可以查阅

在尝试了很多集成性很高的 Vim 配置（比如 [SpaceVim](https://github.com/SpaceVim/SpaceVim) 和 [rafi 大神的配置](https://github.com/rafi/vim-config)）之后，我决定重新使用和打磨自己的配置。原因是因为这些集成性很高的配置有太多我不需要的东西，或者是太多我不知道如何使用的东西，造成启动速度比较慢。当我需要某项功能，我自己去寻找解决的方法，也能让我迅速熟悉。

又因为 Vim 本身就有很多快捷键，添加了诸多插件后，又增加了成吨的快捷键，所以我决定写一个笔记。

## Vim 本身加强

Vim 本身加强指的是一些 vim 没有的功能通过插件或者设置实现的。

- <leader\>w 保存修改；:W 使用`sudo`保存文件
- <leader\>q 新建一个 buffer
- J norm 模式下快速向下 20 行
- K norm 模式下快速向上 20 行
- H 键用于定位到当前行第一个非空白字符，
- L 键定位到当前行最后一个字符
- <leader\>cd 将当前目录切换至 buffer 所在目录
- 增强 f 键 normal 模式下，再次按`f`可以将光标移到下一个 find 的字母
- search 后改变了 n 和 N 的方向，让 n 永远向前，N 永远向后
- \<leader>af 用于 autoformat
- [e 将当前行上移，]e 将当前行下移
- [<space\> 在当前行之上添加空白行，]<space\> 在当前行之下添加空白行
- `[b`和 `]b` 左右切换 buffer

## 跳转窗口
跳转窗口部分和`tmux`共享快捷键。下面的`M`指的是`Alt`键
- <M-C-j\> <M-C-k\> <M-C-h\> <M-C-l\> 在窗口之间上下左右切换
- <M-C-n\> 跳转到上一个窗口

## Coc 快捷键
`Coc`指的是 [neoclide/coc.nvim](https://github.com/neoclide/coc.nvim) 是新一代的补全框架，同时包括了很多额外的功能。它也可以安装 extension，也会产生很多的快捷键。
- `Tab` 键补全列表往下 `Shift+Tab`补全列表往上
- `Enter` 选中 Snippet 后 确认补全
- \<space>a 打开当前文件错误的列表
- `[g` `]g` 错误直接跳转
- \<space>f 打开文件模糊搜索列表
- \<space>e 打开 extension 列表
- \<space>w 查找光标下的单词
- \<space>m 打开最近文件列表
- \<space>p 打开最近的一个列表
- `Esc`退出列表
- `U` 查看光标下的单词的文档
- `gd`跳转到定义

## Defx 目录树快捷键
使用 [Defx](https://github.com/Shougo/defx.nvim) 作为目录树插件。
- <leader\>e 打开和关闭目录树。下面的快捷键都是在 defx 下的
- `Enter`键或者`o`键 打开文件，如果在目录上摁，那就是展开目录
- `x` 关闭目录
- `l`和`h` 打开和关闭目录
- `yy` 复制目录或者文件地址
- `c` 复制文件
- `p` 粘贴文件
- `.` 显示隐藏文件

