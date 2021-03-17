# WSL使用本地代理和git clone加速


平时科学上网时，一般的流程都是这样的，在一个客户端中配置你的**V2ray**，**Trojan**信息，然后客户端会设置一个本地的监听端口，常用的比如**1080**端口，**10808**端口等等。常用的协议比如**socks5**或者**http**。如果你没有设置全局代理，那么此时你是无法科学上网的，一些软件提供了监听这些端口的功能，这样这个软件就可以科学上网了。

以**V2rayN**这个客户端为例，

![UTOOLS_1606277244227.png](https://i.loli.net/2020/11/25/Ju9Q6ePs12XD7Vy.png)

可以看到他设置了**10808**端口为本地监听端口。

![UTOOLS_1606277027683.png](https://i.loli.net/2020/11/25/W8Nq3d6obnC9puH.png)

如上图就是**Telegram**使用本地**Socks5**的**10808**端口进行科学上网的设置。

## WSL使用本地代理

**Windows Subsystem for Linux** 和 **Windows** 是共享端口的，也就是**Windows**的**10808**端口和**WSL** 的**10808**端口是同一个，所以使用这个端口便可以给**WSL**进行科学上网和**Git**加速。

### **proxychains** 进行命令行加速

**proxychains**是一个可以让你的命令通过代理运行的linux命令。安装方法如下：

> sudo apt install proxychains

安装完之后需要对其进行配置，编辑 **/etc/proxychains.conf**文件。修改最后一行为如下：

```conf
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5 	127.0.0.1 10808
```

然后你只需要在平时的命令前加上**proxychains** 就可以了，比如

> proxychains git clone .........

### 设置ALL_PROXY变量进行加速

在 **\.zshrc** 或者 **\.bashrc** 中加入如下两行：

```bash
alias setproxy="export ALL_PROXY=socks5://127.0.0.1:10808" 
alias unsetproxy="unset ALL_PROXY"
```

然后当你要给终端命令科学上网时，只要先运行**setproxy**就行了。但这种方法只对走**http**协议的生效。比如**curl**，**wget**等，而对于走**SSH**协议的 **git clone git@\...** 是无效的。

## git clone下载加速

git是常用的版本控制的工具，也是从**github**，**gitlab**上下载代码的利器。每天几乎都会**git clone** 和 **git push**。但是由于**github**的服务器不在中国，上传下载十分缓慢，不得不进行科学上网。

### 加速SSH协议

**git clone git@xxxxxxxx** 走的其实就是**SSH**协议，所以只要对**SSH**进行配置就可以了。

编辑 **～/.ssh/config** 文件，如果不存在就新建。

加入如下代码

```
Host github.com
    HostName github.com
    User git
    ProxyCommand nc -X 5 -x 127.0.0.1:10808 %h %p
```

使用代理进行连接。这里使用到了工具**nc**如果没有安装需要提前安装，不过系统一般都会自带。

如果你 **git clone** 不是**github**，而是一个你自己搭建在国外服务器上的git服务器，也可以通过配置进行加速。在上面那个文件加入下面的代码，将**Host**改成服务器**ip**，就能起到加速作用。

```
Host 104.168.*.*
    ProxyCommand nc -X 5 -x 127.0.0.1:10808 %h %p
```

### 加速HTTP协议

在终端中输入下面两行命令就可以了。

```text
git config --global http.proxy 'socks5://127.0.0.1:10808' 
git config --global https.proxy 'socks5://127.0.0.1:10808'
```

## 最好的方法

最好的最优雅的方法当然是使用**软路由**。只要一个软路由，就可以全家科学上网，无需再各个设备上分别设置。
