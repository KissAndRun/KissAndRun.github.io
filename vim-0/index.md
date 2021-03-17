# 🔧第 N 次的 Vim 入门记录


**不学个一个月就什么都学不会**

本着这样的理念，决定推出`每日学习计划`每项学习任务的周期为一个月，坚持每日学习，每日码字。实验小白鼠为早就想学的 vim。

**不积跬步无以至千里**

本次计划遵循到红哥那偷零食吃的原则：少量多次

## Day One

### 在 vim 中打开文件

-   `:e <filename>`打开名为 filename 的文件，若不存在则创建
-   `:Ex` 在 vim 中打开目录树，光标选中后回车打开对应文件，`-`进入上级目录

记忆：大概是 explorer 的意思

### 查找

-   `/<search>` 向后查找指定的字符串
-   `?<search>` 向前查找指定的字符串
-   `n` 继续查找下一个
-   `N` 继续查找上一个

> 匹配查找： 可以用`%`对 (),\[],{}进行匹配查找

### 移动光标

-   `e` 向右移动到单词结尾 记忆：end
-   `b` 向左移动到单词开头 记忆：back
-   `0` 行首
-   `$` 行尾
-   `<N>gg` 跳转到第 N 行，gg 为到第一行
-   `Ctrl-d` 向下移动半页
-   `Ctrl-u` 向上移动半页 记忆：down and up

## Day Two

### 替换

**:[addr]s/ 源字符串 / 目的字符串 /[option]** , 其中 [addr][option] 是可以缺省不填的

-   `[addr]` 代表检索范围，常用的有`%`代表整个文件，`1,10`代表从 1 到 10 行，缺省为当前行
-   `[option]` 代表操作类型，缺省只对第一个匹配的字符替换，`g` 代表全局替换，`c` 代表替换时确认

几个例子：

```shell
:s/aa/bb/g       将所在行出现的所有 aa 替换为 bb
:%s/aa/bb/g      将文档中所有的包含 aa 的字符串替换为 bb
:12,25s/aa/bb/g  将 12 行到 25 行中所有包含 aa 的字符替换为 bb
:%s/^/#/         全文的行首添加#字符
:%s= *$==        所有行尾多余的空格去除
:g/^$/d          删除所有空行 markdown 慎用
```

> 中间穿插点东西，作为一个 copy 党不和系统复制粘贴使用 vim 实在是太困难了，所以 google 了解决方法

-   复制到系统剪贴板   **"+y**
-   系统剪贴板中复制   **"+p**

### 批量编辑文件

    :argdo 『加命令』

例如：`:argdo %s/xxx/yyy/g` 对所有打开的文件进行查找替换

最后编辑完之后保存和关闭所有打开的文件

    :wa
    :qa

## Day Three

### 自动补全

vim 自带的自动补全功能并不好用所以使用第三方的 YouCompleteMe 来完成自动补全的工作。YouCompleteMe 并不好装，折腾了一上午终于装完了

YouCompleteMe 默认只能自动你安装时候用的 Python Interpret, 如果要添加 Conda 或者 Virtualenv 中的 Python 环境，解决方法是在`.ycm_extra_conf.py`文件的最后写入以下内容

    def Settings( **kwargs ):
        return {
            'interpreter_path': '/path/to/virtual/environment/python'
        }

YouCompleteMe 采用 Tab 键接受补全，一直按 Tab 键则会循环所有的匹配补全项，操作逻辑和 zsh 相似

参考：[Vim 自动补全插件 YouCompleteMe 安装与配置](http://howiefh.github.io/2015/05/22/vim-install-youcompleteme-plugin/)

### 自动匹配括号

自动匹配括号采用第三方插件'Raimondi/delimitMate'完成

## Day Four

### 多文件编辑

使用`NERDTree`插件结合 vim 的多文件编辑功能真的舒服的飞起

> 安装方法比较常规 在`.vimrc`文件中添加`scrooloose/nerdtree`

并且在`.vimrc`中添加如下的配置

    """""""""""""""NerdTree setting"""""""""""""""""""""""""""""""
    map <C-n> :NERDTreeToggle<CR>
    autocmd StdinReadPre * let s:std_in=1
    autocmd VimEnter * if argc() == 0 && !exists("s:std_in") | NERDTree | endif
    autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif

就能够更加舒服的使用，Ctrl-n 开关 Tree，当 Tree 是最后一个窗口的时候自动关闭 vim

其他快捷键有以下

-   选中目录 `o`打开目录
-   选中文件 `o`在当前窗口中直接打开文件
-   选中文件 `s`竖直分割窗口打开文件 `i`水平分割打开文件 `t`在新 tab 打开文件
-   `C`-- Change the tree root to the selected dir

> [NERDTREE 官方文档](https://github.com/scrooloose/nerdtree/blob/master/doc/NERDTree.txt)

在`.vimrc`中添加以下配置，切换 windows 更加快捷

    " Smart way to move between windows
    map <C-j> <C-W>j
    map <C-k> <C-W>k
    map <C-h> <C-W>h
    map <C-l> <C-W>l

### Buffer 运用

运用 Buffer 进行多文件管理能够更加高效（相比于 tab）；这好像是国外一群 vimer 交流讨论出来的结果

Buffer 缓冲区就是你用 vim 打开文件都会被写入缓冲区中，比如用`NerdTree`打开文件，不会加入 args 中，却会加入缓冲区；

重新 mapping 快捷键如下

    """"""""""""""buffer setting"""""""""""""""""""""""""""""""""""
    set hidden " 避免必须保存修改才可以跳转 buffer

    " buffer 快速导航
    nnoremap <Leader>b :bp<CR>
    nnoremap <Leader>f :bn<CR>

    " 通过索引快速跳转
    nnoremap <Leader>1 :1b<CR>
    nnoremap <Leader>2 :2b<CR>
    nnoremap <Leader>3 :3b<CR>
    nnoremap <Leader>4 :4b<CR>
    nnoremap <Leader>5 :5b<CR>
    nnoremap <Leader>6 :6b<CR>
    nnoremap <Leader>7 :7b<CR>
    nnoremap <Leader>8 :8b<CR>
    nnoremap <Leader>9 :9b<CR>
    nnoremap <Leader>0 :10b<CR>

当然 switch buffer 时还可以直接`:b [pattern]`直接切换，还可以用`Tab`补全

## Day Five

### 快速移动

个人比较倾向于多 windows 多 buffer 的操作，比如屏幕包含三列 windows（nerdtree 加两个打开 buffer 的 windows）

所以最后的结果是多个 windows 共用一个 buffer 列表；

在单个 windows 中快速跳转除了基本的移动光标（如`f` `t` 以及`hjkl`等等）的方法之外还有以下方法

1.  **CTRL-O** 和 **CTRL-I** 追踪光标的移动，可以回到光标以前的位置，甚至可以不是这个 buffer
2.  搜索跳转
3.  设置标志跳转（待学习

第一种使用** CTRL-O** 和 **CTRL-I **的方法其实是利用跳转表，`:jumps`可以查看。但是如果多 windows，因为各个 windows 跳转表不同，所以不能用这种方法在 window 之间切换；

> 一般，每次你执行一个会将光标移动到本行之外的命令，该移动即被称为一个 "跳转" 。这包括查找命令 "/" 和 "n" （无论跳转到多远的地方）。但不包括 "fx" 和 "tx" 这些行 内查找命令或者 "w" 和 "e" 等词移动命令。 ----vimdoc

同样也不包括"j" "k";

切换 window 参考上文；其中添加一个命令到`.vimrc`

    map <C-f> <C-W><C-P>

绑定快捷键 CTRL-F 用于切换到刚刚的那个 windows;

同一个 windows 中切换不同的 buffer 可以参考上面的内容；

## Day Six

### 可视模式

激活可视模式的方法有以下几种

| 命令       | 用途          |
| :------- | :---------- |
| v        | 激活面向字符的可视模式 |
| V        | 激活面向行的可视模式  |
| &lt;C-V> | 激活面向列的可视模式  |
| gv       | 激活上次的高亮区块   |

进入可视模式后，可以用普通模式下移动光标的方法选定区域；可以使用`A`或者`I`在区域前或者后面添加内容；

`viw` 可以快速选定一个 word; 之后可以对其进行`y` `x` `c`等操作

如果你在可视模式下选中了一些文字，然后你又发现你需要改变被选择的文字的另一端， 用 "o" 命令即可 （提示："o" 表示 other end)，光标会移动到被选中文字的另一端，现 在你可以移动光标去改变选中文字的开始点了。再按 "o" 光标还会回到另一端。

### Text-object

经常使用`ci(` `ci"`之类的命令，还有上面提到的`viw`命令；让我好奇 i 的含义

在 vim 中，指令的基本结构是 `<number><command><text-object or motion>`

文本对象操作涉及到范围和操作，范围主要是主要是 i(inner ) 和 a(around)，文本对象有 w (word), s (sentence), p (paragraph), 和各种 引号和括号。

如果是 word 的话，i 只作用于字母，而 a 作用于 word 及其后面的空格；

如果是各种引号或者括号，i 只作用于括号内的内容，而 a 包括括号本身；

> [一篇很棒的文章](https://blog.carbonfive.com/2011/10/17/vim-text-objects-the-definitive-guide/)

        @todo args

## Day Seven

### Ex 模式中的 `%`

normal 模式中`%`用于匹配括号等；在 Ex 模式中 `%`代表活动缓冲区的完整路径

`:edit %<TAB\>`; 按 TAB 键可以将路径完整显示；而`:edit %:h<TAB\>` :h 修饰符会去除文件名

`:!mkdir -p %:h` 就能够创造一个不存在的目录

## Day Eight

### 寄存器

Vim 提供了 10 类共 48 个寄存器

一般来讲，可以用`"{register}y`来拷贝到`{register}`中， 用`"{register}p`来粘贴`{register}`中的内容。例如： `"ayy`可以拷贝当前行到寄存器 a 中，而`"ap`则可以粘贴寄存器 a 中的内容。

共有十种类型的寄存器：

1.  无名寄存器 ""
2.  10 个编号寄存器 "0 到 "9
3.  行内删除寄存器 "-
4.  26 个命名的寄存器 "a 到 "z 或者 "A 到 "Z
5.  三个只读寄存器 ":、". 和 "%
6.  轮换缓冲区寄存器 "#
7.  表达式寄存器 "=
8.  选择和拖放寄存器 "\*、"+ 和 "~
9.  黑洞寄存器寄存器 "\_
10. 最近搜索模式寄存器 "/

其中 无名寄存器`“”`是缺省寄存器，编号寄存器`"0`是拷贝寄存器，其他数字寄存器是大于一行的删除寄存器；

## Day Nine

### Marks

设定一个标记，使用命令`m{a-zA-Z}`; 例如，命令`mt`在把当前光标位置设定为标记 t；命令`mT`把当前光标位置设定为标记 T。(:help m)

要跳转到指定的标记，使用命令`'{a-zA-Z}`或`'{a-zA-Z}`。例如，命令`'t`会跳转到标记 t；命令`'T`会跳转到标记 T。

大写的标记可以在不同 buffer 之前跳转

使用`:marks`命令可以查看现有的 marks; 使用命令`:delmarks`可以删除指定标记

同样内置了几个 marks

    .   	最近编辑的位置
    0-9  	最近使用的文件
    ∧   	最近插入的位置
    '   	上一次跳转前的位置
    "   	上一次退出文件时的位置
    [   	上一次修改的开始处
    ]   	上一次修改的结尾处

## 圣诞特辑

### 宏

很多时候使用 vim 重复一个命令，我们最先想到`.`命令，但点命令只能重复上一个命令如果要重复很多个命令就要使用宏录制 (macro)

录制宏，可以使用 q+[a-z] 26 个字母中的一个

> q[a-z]

开始录制宏，结束时再摁一下`q`。

对的没错他用的就是前文所讲的字母寄存器。所以可以用查看 reg 的方法查看录制的宏`:reg a`;

录制完宏后，再寄存器名前加`@`，可以执行宏。

怎么编辑宏呢？

-   :let @a=’
-   输入 Ctrl + r + a 来插入 a 中内容；
-   编辑内容然后以 ' 结束 Enter 退出

至此，基础的 vim 已经学的差不多了，或者说已经可以满足日常的使用了。所要做的就是实践和总结。

所以，本文就结束了，今后实践和总结所得将会写在新的文章中。祝自己圣诞快乐

