# SSH+Tmux+Vim: 简单高效 Geek 的工作流

作为一个炼丹术士，操作服务器是家常便饭，如何高效的操作服务器，管理环境，运行代码便成了头等大事。炼的好不好先不说，得先炼得舒服。

常用的方法无非以下几种：
1. 利用远程桌面（**teamviewer**，**VNC** 等工具）全程 **GUI** 操作
2. 利用本地端的 IDE（如 **Pycharm**）或者代码编辑器（如 VS Code 等）写代码，然后利用他们的 Remote Tools 进行代码上传等
3. SSH 到远程服务器，全程 CLI 操作

三种方法各有优劣，但如果兼顾效率与 Geek，那第三种方法真是 Awesome 到不行。

我的工作流是：
- SSH 到远程服务器
- 新建 **Tmux** 会话，或者 attach 之前的会话
- 使用 **Vim** 编辑代码，以及其他命令行工具
- 使用 **Anaconda** 虚拟环境中 python 进行炼丹

## SSH
如果你操作远程服务器，那你一定接触过 SSH，如果没有那一定是没到家。`windows`的`powershell`和几乎所有的`Linux`发行版都自带`SSH`，可以在终端中使用。
![](https://atulhost.com/wp-content/uploads/2020/04/ssh.png)
常用的命令是
```
ssh user@domain.com
```
`Windows`下也可以使用`PuTTY`等工具使用`SSH`。这里不得不再推荐一下`Windows Subsystem for Linux`因为服务器是`Linux`系统，统一一下环境岂不美哉。

更多 ssh 配置关键字 [‘ssh 免密登录’]
## Tmux 终端复用
单使用 SSH 是不够的，因为如果你要同时运行多个命令，就需要开多个终端，那是非常复杂难以管理的，Tmux 就因此而诞生了，它可以让你在一个终端里面模拟出多个终端。不仅如此，它还可以让你的程序始终处于运行状态，不管终端是否关闭，只要你的 Tmux 进程没有停止。

试想这样一个情形，你用 Pycharm 写了代码，并上传到了服务器，利用 Pycharm 的终端开始了训练，这时你不小心把 Pycharm 关了，或者突然断电了，或者 Win10 又蓝屏又重启更新了，那你的训练就强制停止了。

而如果你是在 Tmux 里的训练的，那没有任何问题，你只要重新 attach 回之前的那个 session 就行。
![tmux.png](https://i.loli.net/2020/11/22/XqCRiMG7oYpQyu9.png)
### 安装 Tmux
首先先确认一下是否安装 Tmux，直接在终端输入`tmux`如果提醒命令没找到就使用`sudo apt install tmux`安装 tmux。然后再在终端输入`tmux`就可以建立一个新的会话。详细的`tmux`教程可以看 [Tmux 使用教程](http://www.ruanyifeng.com/blog/2019/10/tmux.html)
### 舒服的配置
#### 修改 prefix 键
默认的 prefix 键是`Ctrl+b`键，也就是先按 prefix 键再加一个键组成完整的快捷键。`Ctrl+b`很难按的，即使是交换了 Ctrl 和 CapsLock 键之后依旧很难按。可以在配置文件`~/.tmux.conf`里加入下面这行
```
bind C-f send-prefix -2
```
添加新的 prefix 键`Ctrl-f`
#### 分屏
默认的快捷键我忘了，但下面这个配置你肯定看了就不会忘。
```
bind - splitw -v -c '#{pane_current_path}' # 垂直方向新增面板，默认进入当前目录
bind \ splitw -h -c '#{pane_current_path}' # 水平方向新增面板，默认进入当前目录
```
使用`prefix+-`上下分屏；使用`prefix+\左右分屏`，因为上下分屏就是加一条横线（-），左右分屏就是加一条竖线（|也就是、键）。这样就不会忘了。
#### 如何与 Vim 联动
tmux 可以分屏，Vim 也可以分出多个窗口，那么怎样在 tmux 和 vim 之间来回切换呢。

在 Google 中键入 vim 和 tmux 两个关键词出来的第一条就是 [christoomey/vim-tmux-navigator](https://github.com/christoomey/vim-tmux-navigator)。在 vim 中安装此插件，并在。vimrc 和。tmux.conf 中分别添加相应的设置即可（详见此插件的 README）。
按照 README 的配置完后可以使用`Ctrl`加`hjkl`进行窗口的切换。

另一个插件 [wellle/tmux-complete.vim](https://github.com/wellle/tmux-complete.vim) 可以让你补全 tmux 里面的内容。

## 总结
- 这套工作流可以全键盘操作，极其优雅 Geek
- 上手难度有点高，不过完全值得
- 无 GUI 没办法看图
