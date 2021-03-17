# Zsh & Zinit 配置舒服的终端环境

### 为什么不用Bash和Fish

`Bash`是很多Linux发行版的默认shell，但是显然他不是一个最佳的选择，单颜值不够高一条就足以把它抛弃了。当我们厌烦了`Bash`的陈旧愚钝，自然是要去找更加智能更加顺手的工具。`Fish`和`Zsh`是两个热度很高的`Bash`替代者。`Fish`有个致命的缺点就是他不兼容`Bash`，这或多或少产生一些问题。经过了两个月试用`Fish`后，我果断抛弃了，重新回到了`Zsh`的怀抱。然而这次我没有选择`Oh-My-Zsh`这个非常火的高度集成`Zsh`配置，而是找到了`Zinit`。

### 为什么不用Oh-My-Zsh

当你在Google上搜索`Zsh`配置时，百分之九十的人都会让你一起装上`Oh-My-Zsh`，作为一个用了几年`OMZ`的人，我必须说它实在有点慢，使用`OMZ`打开终端竟然需要一两秒的时间。因为`OMZ`装了太多我不需要的插件了，他显得太臃肿。在我写这篇文章时，我又搜索了一下，看见这位博主对OMZ做了优化，提升了打开速度。[我就感觉到快 —— zsh 和 oh my zsh 冷启动速度优化 | Sukka's Blog (skk.moe)](https://blog.skk.moe/post/make-oh-my-zsh-fly/)感兴趣的也可以试试优化。

但我要说的不是优化，而是直接抛弃`OMZ`，使用`Zinit`。它是一个插件管理器，可以管理你的插件，只安装那些有用的插件。那速度不就快了吗。而且`Zinit`可以安装`OMZ`里面的一些插件，只把你里面好用的拿过来装，简直杀人猪心。

### Zinit安装及配置

` Zinit`的项目地址是:[https://github.com/zdharma/zinit](https://github.com/zdharma/zinit)

官方推荐的安装方法为：

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/zdharma/zinit/master/doc/install.sh)"
```

他会安装在你的`home`目录，生成一个`.zinit`的文件夹。

#### 我的配置

下面是我的配置，基本只留了一些我需要的插件。将其复制到`.zshrc`然后source一下就可以了。

```zsh
# 语法高亮
zinit ice lucid wait='0' atinit='zpcompinit'
zinit light zdharma/fast-syntax-highlighting

# 自动建议
zinit ice lucid wait="0" atload='_zsh_autosuggest_start'
zinit light zsh-users/zsh-autosuggestions

# 补全
zinit ice lucid wait='0'
zinit light zsh-users/zsh-completions

# 加载 OMZ 框架及部分插件
zinit snippet OMZ::lib/completion.zsh
zinit snippet OMZ::lib/history.zsh
zinit snippet OMZ::lib/theme-and-appearance.zsh
zinit snippet OMZ::plugins/autojump/autojump.plugin.zsh
zinit snippet OMZ::plugins/command-not-found/command-not-found.plugin.zsh

zinit load djui/alias-tips
```

这些插件基本是够用的，再配合一些快捷键就能高效进行终端操作了。常用的快捷键：`Ctrl-A`回到命令最开始。`Ctrl-E`回到命令末尾。`C-f`补全命令

#### 速度测试

![UTOOLS_1606219121570.png](https://i.loli.net/2020/11/24/yTYptPHO8ujmalZ.png)

