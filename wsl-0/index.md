# ✔️WSL2 折腾日记和使用体验报告

疫情期间，迫于撰写毕业论文和大量的网络会议需求，我将自己的主力系统换到了 **Windows 10**，前几天又加入了预览者计划，更新到了最新的**20H2 **版本，该版本能够体验到最新的**WSL2**。**WSL2 **据说相对于**WSL1 **有了很大的性能提升，尤其是**I/O **性能。但对于我这个菜鸟使用者，估计是体验不出太大的差别。既然已经转移到了**Windows 10 **下面，那么体验一下**WSL2 **是必须的。
![wsl](/images/wsl.jpg)

## **WSL2** 的安装 
**WSL2 **的安装网上教程太多了，就不赘述了，给个链接吧：[https://docs.microsoft.com/en-us/windows/wsl/install-win10](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

有一点需要注意，网上很多教程都是安装完直接使用**root **账户进行使用的，这样做能够极大方便使用，但也非常的危险，一般还是推荐使用非**root **账户。至于 Linux 发行版的选择，我之前使用的是 [ArchWSL](https://github.com/yuk7/ArchWSL), 这是一个第三方维护的 Arch 版本，使用完全没有问题，也能使用 Pacman 和 Aur，后来我使用的是官方维护的**Ubuntu 20.04**，安装软件相比于 Arch 麻烦一点，但毕竟是官方维护，用着更放心。

## 终端的选择--**wsl-terminal**
至于终端的选择，我使用的是 wsl-terminal。至于为什么不使用微软新推出的 Windows Terminal，原因主要有以下几点：
- 渲染问题。使用 Windows Termianl 打开 Vim 时，四周会出现空白，和 Vim 的 Theme 不一致。而使用 wsl-terminal 则不会，wsl-terminal 具有和原本 Linux 系统相同的渲染。
- 当我使用 Windows Terminal 时，它还没有在该目录打开 Terminal 的功能，不过在最新的预览版中已经更新。
- wsl-terminal 可以右击使用 WSL2 中的 Vim 打开文本文件，十分方便。
  

![](/images/screenfetch.png)

可以设置打开 wsl-terminal 的快捷键，可以使用 Autohotkey 脚本实现。

```
RunOrActivate(Target, WinTitle = "", Parameters = "")
{
   ; Get the filename without a path
   SplitPath, Target, TargetNameOnly

   Process, Exist, %TargetNameOnly%
   If ErrorLevel > 0
      PID = %ErrorLevel%
   Else
      Run, %Target% "%Parameters%", , , PID

   ; At least one app (Seapine TestTrack wouldn't always become the active
   ; window after using Run), so we always force a window activate.
   ; Activate by title if given, otherwise use PID.
   If WinTitle <> 
   {
      SetTitleMatchMode, 2
      WinWait, %WinTitle%, , 3
      WinActivate, %WinTitle%
   }
   Else
   {
      WinWait, ahk_pid %PID%, , 3
      WinActivate, ahk_pid %PID%
   }
}

RunOrActivateMultiParam(Target, WinTitle = "", Param1 = "", Param2 = "", Param3 = "")
{
   ; Get the filename without a path
   SplitPath, Target, TargetNameOnly

   Process, Exist, %TargetNameOnly%
   If ErrorLevel > 0
      PID = %ErrorLevel%
   Else
      Run, %Target% "%Param1%" "%Param2%" "%Param3%", , , PID

   ; At least one app (Seapine TestTrack wouldn't always become the active
   ; window after using Run), so we always force a window activate.
   ; Activate by title if given, otherwise use PID.
   If WinTitle <> 
   {
      SetTitleMatchMode, 2
      WinWait, %WinTitle%, , 3
      WinActivate, %WinTitle%
   }
   Else
   {
      WinWait, ahk_pid %PID%, , 3
      WinActivate, ahk_pid %PID%
   }
}

!Enter::RunOrActivateMultiParam("C:\Program Files\WSL-Terminal\open-wsl.exe","","-C","~"," ")
```
该程序还可以设置其他程序的打开快捷键，十分便利。

## 使用**fish**作为 shell（我又换回去了，哈哈哈）

本次转移，还有一个重要的变化是，使用**fish **代替原本的**zsh + oh my zsh**。替换的主要原因是，使用后者的启动速度过慢。作为一个菜鸟使用者，**fish **并不兼容 Bash 的缺点根本无法感知，而**fish **集成的语法高亮，输入提示等能够完全替代**oh my zsh**。

但是，必要的设置还是需要的，fish 的设置文件在`~/.config/fish/config.fish`

```
set PATH "/home/kissandrun/node/bin:$PATH"
set PATH "/home/kissandrun/.local/bin:$PATH"
if test -f /home/kissandrun/.autojump/share/autojump/autojump.fish; . /home/kissandrun/.autojump/share/autojump/autojump.fish; end

alias vim=nvim
alias configfish="nvim /home/kissandrun/.config/fish/config.fish"
alias wcd="cd /mnt/c/Users/Run/"

# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
eval /home/kissandrun/anaconda3/bin/conda "shell.fish" "hook" $argv | source
# <<< conda initialize <<<
```
其中可以加入一些 **alias**，和环境配置。

## 个人使用的一些**Tips**
1. 使用 WSL 后，一台电脑就相当于有两个 home 了，一个是 Win10 的 home 目录，一个是 wsl 中帐号的 home 目录，这两个目录并不是同一个，可以将 Win10 目录中的 Downloads 和 Document 使用`ln -s`命令链接到 wsl 中的 home 以方便进入，也可以 alias 进入各个 Win10 目录的命令。
2. 可以在 windows 的 explorer 中将 home 目录放置在常用目录里面，方便进入。
3. **Visual Studio Code **和** JetBrains **已经很好的支持了 WSL。
4. 在 wsl 中可以直接使用 windows 中的程序。比如在命令中输入`explorer.exe .`可以直接打开当前目录。输入命令`code`可以打开** Visual Studio Code**。
5. 可以在 Vim 中设置，实现粘贴板的通信。
```
    let s:clip = '/mnt/c/Windows/System32/clip.exe' 
    if executable(s:clip)
        augroup WSLYank
            autocmd!
            autocmd TextYankPost * call system('echo '.shellescape(join(v:event.regcontents, "\<CR>")).' | '.s:clip)
        augroup END
    end
    " I thought this will be better :)
    noremap <leader>p :exe 'norm a'.system('/mnt/c/Windows/System32/WindowsPowerShell/v1.0/powershell.exe -Command Get-Clipboard')<CR>
```

## 使用的一些痛点
1. WSL 暂不支持`systemd`
2. 想用的 Arch 发行版，没有官方的支持
3. 粘贴板并不互通
4. 打开速度可以更快
