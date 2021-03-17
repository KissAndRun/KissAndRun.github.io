# 👨‍💻Windows 下优秀软件推荐

`macOS`下有很多优秀的软件，极大的提升了生产力。人们提到`Windows`系统下的软件，第一感觉可能是：多而乱，良莠不齐。而且由于不少国产软件的荼毒，`Windows`下经常出现弹窗，广告等等。但是，大人，时代变了。`Windwos`下其实有很多优秀的软件，而且只要避免使用垃圾国产软件，`Windwos`的纯净程度不低于`macOS`。

## AutoHotKey 自动化神器
AutoHotKey 是 Windows 下的一款自动化神器。第一次接触这款软件是为了实现设置快捷键的需求，后来又了解了一些这款软件的其他功能。可以说，目前为止，我都没有发挥这款软件的最大功效，但已经帮我解决了一些问题。
### `Windows`下虚拟桌面的切换问题。
系统自带的虚拟桌面切换快捷键十分难按，而且只能在左右两个虚拟桌面之间切换，也就是说如果你要从第一个虚拟桌面切换至第三个虚拟桌面就要按两次快捷键。这是十分不方便的。使用 AutoHotKey 可以解决这个问题。
```
; Globals
DesktopCount = 2 ; Windows starts with 2 desktops at boot
CurrentDesktop = 1 ; Desktop count is 1-indexed (Microsoft numbers them this way)
;
; This function examines the registry to build an accurate list of the current virtual desktops and which one we're currently on.
; Current desktop UUID appears to be in HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\SessionInfo\1\VirtualDesktops
; List of desktops appears to be in HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\VirtualDesktops
;
mapDesktopsFromRegistry() {
 global CurrentDesktop, DesktopCount
 ; Get the current desktop UUID. Length should be 32 always, but there's no guarantee this couldn't change in a later Windows release so we check.
 IdLength := 32
 SessionId := getSessionId()
 if (SessionId) {
 RegRead, CurrentDesktopId, HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\SessionInfo\%SessionId%\VirtualDesktops, CurrentVirtualDesktop
 if (CurrentDesktopId) {
 IdLength := StrLen(CurrentDesktopId)
 }
 ; Get a list of the UUIDs for all virtual desktops on the system
 RegRead, DesktopList, HKEY_CURRENT_USER, SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\VirtualDesktops, VirtualDesktopIDs
 if (DesktopList) {
 DesktopListLength := StrLen(DesktopList)
 ; Figure out how many virtual desktops there are
 DesktopCount := DesktopListLength / IdLength
 }
 else {
 DesktopCount := 1
 }
 ; Parse the REG_DATA string that stores the array of UUID's for virtual desktops in the registry.
 i := 0
 while (CurrentDesktopId and i < DesktopCount) {
 StartPos := (i * IdLength) + 1
 DesktopIter := SubStr(DesktopList, StartPos, IdLength)
 OutputDebug, The iterator is pointing at %DesktopIter% and count is %i%.
 ; Break out if we find a match in the list. If we didn't find anything, keep the
 ; old guess and pray we're still correct :-D.
 if (DesktopIter = CurrentDesktopId) {
 CurrentDesktop := i + 1
 OutputDebug, Current desktop number is %CurrentDesktop% with an ID of %DesktopIter%.
 break
 }
 i++
 }
}
;
; This functions finds out ID of current session.
;
getSessionId()
{
 ProcessId := DllCall("GetCurrentProcessId", "UInt")
 if ErrorLevel {
 OutputDebug, Error getting current process id: %ErrorLevel%
 return
 }
 OutputDebug, Current Process Id: %ProcessId%
 DllCall("ProcessIdToSessionId", "UInt", ProcessId, "UInt*", SessionId)
 if ErrorLevel {
 OutputDebug, Error getting session id: %ErrorLevel%
 return
 }
 OutputDebug, Current Session Id: %SessionId%
 return SessionId
}
;
; This function switches to the desktop number provided.
;
switchDesktopByNumber(targetDesktop)
{
 global CurrentDesktop, DesktopCount
 ; Re-generate the list of desktops and where we fit in that. We do this because
 ; the user may have switched desktops via some other means than the script.
 mapDesktopsFromRegistry()
 ; Don't attempt to switch to an invalid desktop
 if (targetDesktop > DesktopCount || targetDesktop < 1) {
 OutputDebug, [invalid] target: %targetDesktop% current: %CurrentDesktop%
 return
 }
 ; Go right until we reach the desktop we want
 while(CurrentDesktop < targetDesktop) {
send {LWin down}{LCtrl down}{Right}{LCtrl up}{LWin up}
 CurrentDesktop++
 OutputDebug, [right] target: %targetDesktop% current: %CurrentDesktop%
 }
 ; Go left until we reach the desktop we want
 while(CurrentDesktop > targetDesktop) {
 send {LWin down}{LCtrl down}{Left}{LCtrl up}{LWin up}
 CurrentDesktop--
 OutputDebug, [left] target: %targetDesktop% current: %CurrentDesktop%
 }
ToolTip, %CurrentDesktop%%CurrentDesktop%%CurrentDesktop%%CurrentDesktop%%CurrentDesktop%%CurrentDesktop%`n%CurrentDesktop%%CurrentDesktop%%CurrentDesktop%%CurrentDesktop%%CurrentDesktop%%CurrentDesktop%
SetTimer, RemoveToolTip, -2000
return
}
RemoveToolTip:
ToolTip
return
; Main
SetKeyDelay, 75
mapDesktopsFromRegistry()
OutputDebug, [loading] desktops: %DesktopCount% current: %CurrentDesktop%
; User config!
; This section binds the key combo to the switch/create/delete actions
!1::switchDesktopByNumber(1)
!2::switchDesktopByNumber(2)
!3::switchDesktopByNumber(3)
!4::switchDesktopByNumber(4)
!5::switchDesktopByNumber(5)
!6::switchDesktopByNumber(6)
!7::switchDesktopByNumber(7)
!8::switchDesktopByNumber(8)
!9::switchDesktopByNumber(9)
```
我设置的是`Alt+Num`切换至对应的虚拟桌面，十分的方便。

### 快捷键快速启动应用
在`Linux`系统中，可以使用快捷键直接打开终端，很方便。但是在`Windows`中如果设置启动快捷键，只能使用`Ctrl`开头，和我习惯的`Alt+Enter`不符。可以使用 AutoHotKey 设置启动快捷键。
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

### 禁用 Win 键
我常常勿按 Win 键启动开始菜单，而且我认为用 Win 键启动开始在启动应用的方式十分的慢，不如使用 utools 启动。所以我直接禁用了 Win 键。实现方法也是 AutoHotKey 完成的。值得注意的是，只是禁用了 Win 键启动开始菜单的功能，而其作为修饰键的功能并不受影响。
```
LWin & vk07::return
LWin::return
RWin & vk07::return
RWin::return
```

### 窗口管理
包括窗口最大化，最小化，关闭。Windows 当然自带了窗口管理的快捷键。如果没记错好像是 Win 键+方向键。但是使用方向键双手就会离开主键盘区，而且我的键盘没有方向键。所以我重新设置了快捷键。
```#IfWinActive
!q::
SendInput {Alt Down}{F4}{Alt Up}
Return
#IfWinActive ahk_class Chrome_WidgetWin_1
!q::Send ^w

#IfWinActive
!j::
SendInput {LWin Down}{down}{LWin Up}
Return

#IfWinActive
!k::
SendInput {LWin Down}{up}{LWin Up}
Return

```
使用`Alt+j`最小化窗口，`Alt+k`最大化窗口，`Alt+q`关闭窗口。使用 Alt 修饰键实在是太方便了。
## utools + Everything
[utools](https://u.tools/) 是逛 v2ex 时被安利的，它是一个快速启动+N 多实用功能的软件。使用 utools 之前，也是用过 wox，Listary 等工具，但当使用了 utool 是之后就没有换过了。当然它不是完美的，它占用的内存略大，第一次加载`find`功能时过慢，但这都不妨碍它是一个优秀的软件。在使用过程中我常使用快速启动和 find 插件。
### 快速启动

![](/images/utools.png)

如图，是 utools 的最基本功能，快速启动。只要输入软件的名称就可以快速启动。如果软件是绿色软件，也可以将快捷方式放置一个特定的文件夹，然后在 utool 是中设置添加该路径，同样可以快速启动。
### find 查找文件
在启动栏中输入 find 加空格之后可以激活 find 插件，它可以使用 Everything 作为后端，对文件进行查找。

![](/images/find.png)

如图所示，它提供了更好的界面。

## Rime 小狼毫输入法
虽然微软的自带输入法，已经能够应对百分之九十的输入环境，但是其不支持自定义快捷键，不支持加第三方词库。而国产的搜狗输入法经常出现弹窗广告，严重影响使用。所以推荐使用 Rime 小狼毫输入法。Rime 是一款开源的输入法，在各个平台都有，而且可以灵活的设置。
### 为不同应用设置模式
很多软件我们希望进入的时候，使用英文输入，比如终端、utools、photoshop 等等。Rime 输入法可以实现这项功能。话说应该修改用户文件夹，我好像直接修改程序文件夹了，不过问题不大。修改程序文件夹下的`data/weasel.yaml` 文件，将一些程序设置为 ascii_mode。
```
app_options:
  pycharm64.exe:
    ascii_mode: true
  code.exe:
    ascii_mode: true
  explorer.exe:
    ascii_mode: true
  launchy.exe:
    ascii_mode: true
  photoshop.exe:
    ascii_mode: true
  lightroom.exe:
    ascii_mode: true
  windowsterminal.exe:
    ascii_mode: true
  utools.exe:
    ascii_mode: true
  mintty.exe:
    ascii_mode: true
  goldendict.exe:
    ascii_mode: true
```
### 设置习惯的快捷键
首先是用`Shift`临时切换中英模式，这个功能我很不喜欢，因为 shift 作为修饰键经常要按，但绝大多数的输入法都自带，Rime 也一样，但它可以通过设置去除。
编辑`/data/default.yaml`。修改如下：
```
ascii_composer:
  good_old_caps_lock: true
  switch_key:
    Shift_L: noop
    Shift_R: noop
    Control_L: noop
    Control_R: noop
    Caps_Lock: clear
    Eisu_toggle: clear
```
然后是翻页，和选择。我希望使用`[` `]`进行翻页。使用`tab`进行预选项的选择。同样是修改上面的文件如下：
```
key_binder:
  bindings:
    __patch:
      - key_bindings:/emacs_editing
      - key_bindings:/paging_with_brackets
      - key_bindings:/optimized_mode_switch
```
然后可以在，同文件夹的 key_bindings 文件具体修改每一项的快捷键，直到自己舒服为止。
## Windows Subsystem for Linux
[✔️WSL2 折腾日记和使用体验报告](https://kissandrun.github.io/wsl-0/) 中已经写过了，不再说了。
## 其他
懒癌犯了，剩下的一些大名顶顶，耳熟能详的软件直接放到其他里面了，包括：
- Visual Studio Code 虽然我是 Vim 党，但是中文输入真的不是 Vim 的擅长，推荐使用 VSC。
- Office 365 办公神器，自带 1T Onedrive 空间。
- 坚果云 OneDrive 同步有些不稳定，坚果云是我使用过的国内最好的，就是价格略贵。
- ~~Google Chrome 估计我会专门写一篇。~~ Microsoft Edge 真香
- Wallpaper Engine 动态壁纸，里面还有福利彩蛋。
- 火绒 自带的一些工具很好用
