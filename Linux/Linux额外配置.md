# 1. VPN

> 参考资料：
>
> [1]https://l02hj41pak.feishu.cn/wiki/NaVbwISZoiMysYkwIFQcWt67nVf

如果使用WSL，那么默认在Windows端开启的VPN并不会代理WSL中的请求，需要进行一些额外配置。

## 1.0 测试VPN

在进行VPN的额外配置之前，最好先测试一下自身网络对外网的连通性，如果不连通再进行之后的配置。

国内一般可以裸连github，因此不要直接使用`ping www.github.com`在WSL中测试网络。

最好使用`curl www.google.com`来测试网络连通性，如果能够连通，应该显示以下内容：

![image-20240502000116764](imgs/image-20240502000116764.png)

如果网络不连通，那么回车后不会显示任何内容：

![image-20240502000214123](imgs/image-20240502000214123.png)

> [!WARNING]
>
> 无论连接还是不连接VPN，谷歌都不会回应`ping`命令。因此不要使用`ping www.google.com`来测试网络，而使用`curl www.google.com`来测试网络。

## 1.1 Clash额外配置

需要打开`设置`→`局域网连接`。并且记录下`端口设置`处的端口号，后续会在[1.2 Linux额外配置](#1.2 Linux额外配置)使用。

![image-20240501232238054](imgs/image-20240501232238054.png)

## 1.2 Linux额外配置

使用命令`echo $0`查看当前使用终端。如果使用的是bash终端，那么请在`~/.bashrc`中添加以下配置；如果使用的是zsh终端，那么请在`~/.zshrc`中添加以下内容，并且注意将端口号换成[1.1 Clash额外配置](#1.1 Clash额外配置)中`端口设置`处的端口号：

```bash
hostip=$(cat /etc/resolv.conf |grep -oP '(?<=nameserver\ ).*')
export https_proxy="http://${hostip}:7897" # 这里的7897需要换成1.1中Clash的端口设置的端口号
export http_proxy="http://${hostip}:7897" # 这里的7897需要换成1.1中Clash的端口设置的端口号
export all_proxy="socks5://${hostip}:7897" # 这里的7897需要换成1.1中Clash的端口设置的端口号
```

添加以后使用`source ~/.bashrc`（bash终端）或者`source ~/.zshrc`（zsh终端）命令，以重新执行文件中的命令。

## 1.3 Windows防火墙额外配置

如果进行上述配置之后可以连通外网，那么可以忽略本节。

如果进行完[1.2 Linux额外配置](#1.2 Linux额外配置)后还不能连接到外网，那么可能与Windows自带的防火墙有关。有两种方法可以解决——直接关闭防火墙或者配置防火墙规则，二者选其一即可。

### 1.3.1  关闭防火墙

1. 打开Windows安全中心

   可以在直接使用Windows搜索打开，也可以在右下角隐藏图标处打开：

   <img src="imgs/image-20240502001043973.png" alt="image-20240502001043973" style="zoom:67%;" />

2. 点击防火墙和网络操作

   <img src="imgs/image-20240502001151227.png" alt="image-20240502001151227" style="zoom:67%;" />

3. 关闭公用网络防火墙

   <img src="imgs/image-20240502001247189.png" alt="image-20240502001247189" style="zoom: 67%;" />

### 1.3.2 配置防火墙规则

1. 打开高级安全Windows Defender防火墙

   ![image-20240502001521297](imgs/image-20240502001521297.png)

2. 新建入站规则

   ![image-20240502001729427](imgs/image-20240502001729427.png)

   规则类型选择`自定义`：

   <img src="imgs/image-20240502001835649.png" alt="image-20240502001835649" style="zoom:67%;" />

   选择规则应用于`所有程序`：

   <img src="imgs/image-20240502001918762.png" alt="image-20240502001918762" style="zoom:67%;" />

   协议和端口默认即可：

   <img src="imgs/image-20240502002026386.png" alt="image-20240502002026386" style="zoom:67%;" />

   作用域选择`远程IP地址`→`下列IP地址`，添加以下IP地址，这里的作用是允许来自以下网络的地址访问本机网络和端口:

   ```bash
   172.16.0.0/12
   192.168.0.0/16
   ```

   <img src="imgs/image-20240502002620985.png" alt="image-20240502002620985" style="zoom:67%;" />

   操作选择允许连接：

   <img src="imgs/image-20240502002723597.png" alt="image-20240502002723597" style="zoom:67%;" />

   配置文件应用于所有网络：

   <img src="imgs/image-20240502002810955.png" alt="image-20240502002810955" style="zoom:67%;" />

   填写任意名称：

   <img src="imgs/image-20240502002912641.png" alt="image-20240502002912641" style="zoom:67%;" />

   完成之后，在`防火墙和网络保护`→`允许应用通过防火墙`中，确认是否已经启用上述配置的规则：

   <img src="imgs/image-20240502003258653.png" alt="image-20240502003258653" style="zoom:67%;" />

   点击`更改设置`，确认勾选专用和公用。

   ![image-20240502003445828](imgs/image-20240502003445828.png)

   所有操作完成之后，点击确定，至此WSL应该可以通过VPN访问外网了。
   
## 1.4 VMWare额外配置

> 参考资料：
>
> [1]https://www.zhihu.com/question/495148700/answer/2496921147?utm_id=0

在Windows中的终端输入`ipconfig /all`，保存其中VMnet8的IP地址，如下所示：

   ![image-20240507225122840](imgs/image-20240507225122840.png)

VMWare中，`编辑`→`虚拟网络编辑器`，点击`VMnet8`后确认其子网IP和子网掩码和上面的IP地址是同一个子网，如果不是需要改成同一个子网。

<img src="imgs/image-20240507225546893.png" alt="image-20240507225546893" style="zoom:67%;" />

进入`NAT`设置，确认网关IP也处于上面的子网，并且和Windows主机的IP==不同==。

<img src="imgs/image-20240507225643044.png" alt="image-20240507225643044" style="zoom:67%;" />

然后确认`DHCP设置`中的起始IP地址和结束IP地址范围也处于同一子网内，并且不能够包含网关IP以及Windows主机IP：

<img src="imgs/image-20240507230327544.png" alt="image-20240507230327544" style="zoom:67%;" />

以上设置完成后，进入Ubuntu设置，选择`Network`，配置`Network proxy`为`Manual`模式，配置代理IP地址为Windows主机的VMnet8地址，端口号为[Clash开放的端口号](#1.1 Clash额外配置)。

<img src="imgs/image-20240507230610210.png" alt="image-20240507230610210" style="zoom:67%;" />

 此时在终端中使用`curl www.google.com`进行测试，应该可以返回结果。

# 2. Windows Terminal

可以使用Windows Terminal来替换Windows控制台，作为终端的默认启动程序。

Windows 终端程序是一款新式、快速、高效、强大且高效的终端应用程序，适用于命令行工具和命令提示符，PowerShell和 WSL 等 Shell 用户。主要功能包括多个选项卡、窗格、Unicode、和 UTF-8 字符支持，GPU 加速文本渲染引擎以及自定义主题、样式和配置。

可以直接在微软商店（Microsoft Store）中搜索`windows terminal`下载，如下：

<img src="imgs/image-20240502005701260.png" alt="image-20240502005701260" style="zoom:67%;" />

安装之后启动启动，进入设置，选择`Windows Terminal`作为默认的终端应用程序，如下：

1. 进入设置界面，进入设置的操作如下：![image-20240502010349583](imgs/image-20240502010349583.png)

2. 选择`启动`→`默认终端应用程序`，选择Windows终端作为默认终端应用程序，如下：

   <img src="imgs/image-20240502010601000.png" alt="image-20240502010601000" style="zoom:67%;" />

可以自己在设置界面摸索自己所喜欢的外观或快捷键设置。个人的外观设置如下，在设置界面的底部有`打开JSON文件`，可以选择将JSON文件替换为以下内容：

```json
{
    "$help": "https://aka.ms/terminal-documentation",
    "$schema": "https://aka.ms/terminal-profiles-schema",
    "actions": 
    [
        {
            "command": 
            {
                "action": "copy",
                "singleLine": false
            },
            "keys": "ctrl+c"
        },
        {
            "command": "paste",
            "keys": "ctrl+v"
        },
        {
            "command": 
            {
                "action": "splitPane",
                "split": "auto",
                "splitMode": "duplicate"
            },
            "keys": "alt+shift+d"
        },
        {
            "command": "find",
            "keys": "ctrl+shift+f"
        }
    ],
    "alwaysShowNotificationIcon": false,
    "copyFormatting": "none",
    "copyOnSelect": false,
    "defaultProfile": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
    "newTabMenu": 
    [
        {
            "type": "remainingProfiles"
        }
    ],
    "profiles": 
    {
        "defaults": 
        {
            "adjustIndistinguishableColors": "never",
            "backgroundImage": null,
            "colorScheme": "One Half Dark",
            "font": 
            {
                "cellHeight": "1.4",
                "face": "Consolas",
                "weight": "light"
            },
            "opacity": 85,
            "padding": "15",
            "scrollbarState": "visible",
            "useAcrylic": true
        },
        "list": 
        [
            {
                "commandline": "%SystemRoot%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
                "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
                "hidden": false,
                "name": "Windows PowerShell"
            },
            {
                "commandline": "%SystemRoot%\\System32\\cmd.exe",
                "guid": "{0caa0dad-35be-5f56-a8ff-afceeeaa6101}",
                "hidden": false,
                "name": "\u547d\u4ee4\u63d0\u793a\u7b26"
            },
            {
                "guid": "{07b52e3e-de2c-5db4-bd2d-ba144ed6c273}",
                "hidden": true,
                "name": "Ubuntu-20.04",
                "source": "Windows.Terminal.Wsl"
            },
            {
                "guid": "{b453ae62-4e3d-5e58-b989-0a998ec441b8}",
                "hidden": false,
                "name": "Azure Cloud Shell",
                "source": "Windows.Terminal.Azure"
            },
            {
                "guid": "{ffb6c8a2-149d-5e00-a19e-cfd4180e8a23}",
                "hidden": false,
                "name": "Developer Command Prompt for VS 2022",
                "source": "Windows.Terminal.VisualStudio"
            },
            {
                "guid": "{3125d9a0-10c3-5bc4-bb09-8e580015cd2a}",
                "hidden": false,
                "name": "Developer PowerShell for VS 2022",
                "source": "Windows.Terminal.VisualStudio"
            },
            {
                "adjustIndistinguishableColors": "never",
                "bellStyle": "window",
                "cursorShape": "bar",
                "guid": "{4dd1e689-b517-5f39-947d-78e8a8bdf958}",
                "hidden": false,
                "name": "Ubuntu 20.04.6 LTS",
                "source": "CanonicalGroupLimited.Ubuntu20.04LTS_79rhkp1fndgsc"
            }
        ]
    },
    "schemes": 
    [
        {
            "background": "#0C0C0C",
            "black": "#0C0C0C",
            "blue": "#0037DA",
            "brightBlack": "#767676",
            "brightBlue": "#3B78FF",
            "brightCyan": "#61D6D6",
            "brightGreen": "#16C60C",
            "brightPurple": "#B4009E",
            "brightRed": "#E74856",
            "brightWhite": "#F2F2F2",
            "brightYellow": "#F9F1A5",
            "cursorColor": "#FFFFFF",
            "cyan": "#3A96DD",
            "foreground": "#CCCCCC",
            "green": "#13A10E",
            "name": "Campbell",
            "purple": "#881798",
            "red": "#C50F1F",
            "selectionBackground": "#FFFFFF",
            "white": "#CCCCCC",
            "yellow": "#C19C00"
        },
        {
            "background": "#012456",
            "black": "#0C0C0C",
            "blue": "#0037DA",
            "brightBlack": "#767676",
            "brightBlue": "#3B78FF",
            "brightCyan": "#61D6D6",
            "brightGreen": "#16C60C",
            "brightPurple": "#B4009E",
            "brightRed": "#E74856",
            "brightWhite": "#F2F2F2",
            "brightYellow": "#F9F1A5",
            "cursorColor": "#FFFFFF",
            "cyan": "#3A96DD",
            "foreground": "#CCCCCC",
            "green": "#13A10E",
            "name": "Campbell Powershell",
            "purple": "#881798",
            "red": "#C50F1F",
            "selectionBackground": "#FFFFFF",
            "white": "#CCCCCC",
            "yellow": "#C19C00"
        },
        {
            "background": "#282C34",
            "black": "#282C34",
            "blue": "#61AFEF",
            "brightBlack": "#5A6374",
            "brightBlue": "#61AFEF",
            "brightCyan": "#56B6C2",
            "brightGreen": "#98C379",
            "brightPurple": "#C678DD",
            "brightRed": "#E06C75",
            "brightWhite": "#DCDFE4",
            "brightYellow": "#E5C07B",
            "cursorColor": "#FFFFFF",
            "cyan": "#56B6C2",
            "foreground": "#DCDFE4",
            "green": "#98C379",
            "name": "One Half Dark",
            "purple": "#C678DD",
            "red": "#E06C75",
            "selectionBackground": "#FFFFFF",
            "white": "#DCDFE4",
            "yellow": "#E5C07B"
        },
        {
            "background": "#FAFAFA",
            "black": "#383A42",
            "blue": "#0184BC",
            "brightBlack": "#4F525D",
            "brightBlue": "#61AFEF",
            "brightCyan": "#56B5C1",
            "brightGreen": "#98C379",
            "brightPurple": "#C577DD",
            "brightRed": "#DF6C75",
            "brightWhite": "#FFFFFF",
            "brightYellow": "#E4C07A",
            "cursorColor": "#4F525D",
            "cyan": "#0997B3",
            "foreground": "#383A42",
            "green": "#50A14F",
            "name": "One Half Light",
            "purple": "#A626A4",
            "red": "#E45649",
            "selectionBackground": "#4F525D",
            "white": "#FAFAFA",
            "yellow": "#C18301"
        },
        {
            "background": "#002B36",
            "black": "#002B36",
            "blue": "#268BD2",
            "brightBlack": "#073642",
            "brightBlue": "#839496",
            "brightCyan": "#93A1A1",
            "brightGreen": "#586E75",
            "brightPurple": "#6C71C4",
            "brightRed": "#CB4B16",
            "brightWhite": "#FDF6E3",
            "brightYellow": "#657B83",
            "cursorColor": "#FFFFFF",
            "cyan": "#2AA198",
            "foreground": "#839496",
            "green": "#859900",
            "name": "Solarized Dark",
            "purple": "#D33682",
            "red": "#DC322F",
            "selectionBackground": "#FFFFFF",
            "white": "#EEE8D5",
            "yellow": "#B58900"
        },
        {
            "background": "#FDF6E3",
            "black": "#002B36",
            "blue": "#268BD2",
            "brightBlack": "#073642",
            "brightBlue": "#839496",
            "brightCyan": "#93A1A1",
            "brightGreen": "#586E75",
            "brightPurple": "#6C71C4",
            "brightRed": "#CB4B16",
            "brightWhite": "#FDF6E3",
            "brightYellow": "#657B83",
            "cursorColor": "#002B36",
            "cyan": "#2AA198",
            "foreground": "#657B83",
            "green": "#859900",
            "name": "Solarized Light",
            "purple": "#D33682",
            "red": "#DC322F",
            "selectionBackground": "#073642",
            "white": "#EEE8D5",
            "yellow": "#B58900"
        },
        {
            "background": "#000000",
            "black": "#000000",
            "blue": "#3465A4",
            "brightBlack": "#555753",
            "brightBlue": "#729FCF",
            "brightCyan": "#34E2E2",
            "brightGreen": "#8AE234",
            "brightPurple": "#AD7FA8",
            "brightRed": "#EF2929",
            "brightWhite": "#EEEEEC",
            "brightYellow": "#FCE94F",
            "cursorColor": "#FFFFFF",
            "cyan": "#06989A",
            "foreground": "#D3D7CF",
            "green": "#4E9A06",
            "name": "Tango Dark",
            "purple": "#75507B",
            "red": "#CC0000",
            "selectionBackground": "#FFFFFF",
            "white": "#D3D7CF",
            "yellow": "#C4A000"
        },
        {
            "background": "#FFFFFF",
            "black": "#000000",
            "blue": "#3465A4",
            "brightBlack": "#555753",
            "brightBlue": "#729FCF",
            "brightCyan": "#34E2E2",
            "brightGreen": "#8AE234",
            "brightPurple": "#AD7FA8",
            "brightRed": "#EF2929",
            "brightWhite": "#EEEEEC",
            "brightYellow": "#FCE94F",
            "cursorColor": "#000000",
            "cyan": "#06989A",
            "foreground": "#555753",
            "green": "#4E9A06",
            "name": "Tango Light",
            "purple": "#75507B",
            "red": "#CC0000",
            "selectionBackground": "#555753",
            "white": "#D3D7CF",
            "yellow": "#C4A000"
        },
        {
            "background": "#300A24",
            "black": "#171421",
            "blue": "#0037DA",
            "brightBlack": "#767676",
            "brightBlue": "#08458F",
            "brightCyan": "#2C9FB3",
            "brightGreen": "#26A269",
            "brightPurple": "#A347BA",
            "brightRed": "#C01C28",
            "brightWhite": "#F2F2F2",
            "brightYellow": "#A2734C",
            "cursorColor": "#FFFFFF",
            "cyan": "#3A96DD",
            "foreground": "#FFFFFF",
            "green": "#26A269",
            "name": "Ubuntu-20.04-ColorScheme",
            "purple": "#881798",
            "red": "#C21A23",
            "selectionBackground": "#FFFFFF",
            "white": "#CCCCCC",
            "yellow": "#A2734C"
        },
        {
            "background": "#000000",
            "black": "#000000",
            "blue": "#000080",
            "brightBlack": "#808080",
            "brightBlue": "#0000FF",
            "brightCyan": "#00FFFF",
            "brightGreen": "#00FF00",
            "brightPurple": "#FF00FF",
            "brightRed": "#FF0000",
            "brightWhite": "#FFFFFF",
            "brightYellow": "#FFFF00",
            "cursorColor": "#FFFFFF",
            "cyan": "#008080",
            "foreground": "#C0C0C0",
            "green": "#008000",
            "name": "Vintage",
            "purple": "#800080",
            "red": "#800000",
            "selectionBackground": "#FFFFFF",
            "white": "#C0C0C0",
            "yellow": "#808000"
        }
    ],
    "showTabsInTitlebar": true,
    "snapToGridOnResize": true,
    "tabSwitcherMode": "inOrder",
    "tabWidthMode": "equal",
    "theme": "dark",
    "themes": [],
    "trimPaste": true,
    "useAcrylicInTabRow": true
}
```

# 3. zsh

> 参考资料：
>
> [1]https://www.cnblogs.com/Flat-White/p/16462109.html
>
> [2]https://blog.csdn.net/Mancuojie/article/details/120074919

Zsh（Z Shell）是一种功能强大的命令行 shell，它是 Bash 的扩展版本，提供了更多的功能和定制选项。它支持各种高级特性，如自动补全、历史记录搜索、主题定制等，使得命令行操作更加高效和愉悦。

1. 安装zsh：`sudo apt-get install zsh`
2. 更改zsh为默认shell：`chsh -s /bin/zsh`
3. 查看当前默认终端是否为zsh：`rm ~/.zshrc`

## 3.1 oh-my-zsh

Oh My Zsh 则是一个社区驱动的 Zsh 配置管理框架，它提供了一套预配置的 Zsh 配置和主题，以及一些方便的插件，使得用户可以更轻松地定制和管理自己的命令行环境。它包含了许多有用的功能，比如语法高亮、自动补全插件、提示符定制等，让用户能够快速地搭建一个功能丰富、个性化的命令行环境。

> [!Note]
>
> 可以理解为 Zsh 是底层引擎，而 Oh My Zsh 则是在引擎之上提供了更多功能和便利性的一层封装。

以下是oh-my-zsh的安装步骤：

1. 开启VPN，进入网站复制内容：https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh

2. 新建文件`1.sh`，并将上述内容粘贴进文件中。

3. 执行安装脚本，安装之后可以把该脚本删除：

   ```bash
   chmod +x 1.sh
   ./1.sh
   ```

## 3.2 自动建议

使用以下命令安装配置`zsh-autosuggestions`：

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions
echo "source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh" >> ~/.zshrc
source ~/.zshrc
```

安装之后，可以显示命令提示（之前输入过该命令），按下`→`可以采用建议。

## 3.3 语法高亮

使用以下命令安装配置`zsh-syntax-highlighting`：

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.zsh/zsh-syntax-highlighting
echo "source ~/.zsh/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ~/.zshrc
source ~/.zshrc
```

配置之后错误命令会标红，正确命令会标绿。

# 4. tldr

**tldr-pages**项目是社区维护的命令行工具帮助页面的集合，旨在成为传统man手册页的更简单、更平易近人的补充。

使用以下命令安装`tldr-bash-client`：

```bash
L=/usr/local/bin/tldr  # elevated privileges needed for some locations
sudo wget -qO $L good4.eu/tldr
sudo chmod +x $L
tldr --update # 更新tldr本地页面
```

在`/usr/local/bin/tldr`中修改以下内容，使tldr页面显示中文：

```bash
: ${TLDR_LANG="zh"}
```

此后如果想要搜索某个命令`cmd`的用法，可以输入命令`tldr cmd`即可。

# 5. fd

`fd` 是一个在你文件系统中查找条目的程序。它是一个简单、快速、友好的 `find`替代品。虽然它的目标不是支持 `find` 的所有强大功能，但它为大多数的使用情况提供了合理的（有意见的）默认值。

使用`apt install fd-find`安装fd。

> [!Note]
>
> 建议在安装后，通过执行 `ln -s $(which fdfind) /usr/bin/fd` 命令来添加 `fd` 的链接，以便使用 `fd`命令，否则可能要使用`fdfind`命令。

## 5.1 简单搜索

如果只有一个参数，那么fd会递归搜索当前目录中任何包含该模式的条目。

## 5.2 指定根目录

如果我们想搜索一个特定的目录，可以把它作为fd的第二个参数：`fd passwd /etc`。

## 5.3 列出所有条目

如果没有参数，那么fd会列出当前目录下的所有条目。

## 5.4 搜索文件扩展名

可以使用`-e`选项搜索扩展名，如`fd -e md`会搜索所有md文件。

## 5.5 搜索指定文件名

要找到与所提供的搜索模式完全一致的文件，请使用 `-g`（或 `--glob`）选项：`fd -g libc.so /usr`。

## 5.6 搜索隐藏文件

通常情况下fd不搜索隐藏目录，也不会显示隐藏文件。如果要显示需要添加`-H`或者`--hidden`。

## 5.7 匹配完整路径

通常情况下fd只匹配文件名。如果要匹配路径，需要添加`-p`选项。

## 5.8 命令执行

- `-x`/`--exec` 选项为每个搜索结果运行一个外部命令（并行）。
- `-X`/`--exec-batch` 选项启动一次外部命令，将所有搜索结果作为参数。

# 6. fzf

fzf 是一个通用的命令行模糊查找器。它是一个交互式 Unix 命令行过滤器，可以与任何列表一起使用；文件、命令历史记录、进程、主机名、书签、git 提交等。

使用`apt install fzf`安装fzf。可以在命令后面添加管道`| fzf`，以启动fzf的模糊查询。

| 模式      | 匹配类型         | 描述                          |
| --------- | ---------------- | ----------------------------- |
| `sbtrkt`  | 模糊匹配         | 匹配的项目`sbtrkt`            |
| `'wild`   | 完全匹配（引用） | 项目包括`wild`                |
| `^music`  | 前缀精确匹配     | 以以下开头的项目`music`       |
| `.mp3$`   | 后缀精确匹配     | 以以下结尾的项目`.mp3`        |
| `!fire`   | 逆向精确匹配     | 不包括的项目`fire`            |
| `!^music` | 反向前缀精确匹配 | 不以以下内容开头的项目`music` |
| `!.mp3$`  | 反向后缀精确匹配 | 不以以下内容结尾的项目`.mp3`  |

# 7. autojump

可以使用`autojump`来查找最常用或者最近使用的文件和目录。

如果您经常访问`/home/user/files/cool_project`目录，对于autojump，则使用`j cool`代替即可。

安装步骤如下：

1. 使用`apt install autojump`安装

2. 修改`~/.zshrc`，如下所示：

   ```bash
   plugins=(git autojump)
   
   # autojump
   [[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && . ~/.autojump/etc/profile.d/autojump.sh
   ```

# 8. broot

broot用于目录导航。

## 8.1 安装

在网站https://dystroy.org/broot/install/下载对应OS版本的文件后，将其移动到`/usr/local/bin`目录下，然后添加执行权限：`chmod +x broot`。

`br`可以用于`alt+enter`跳转目录。默认下载完成`broot`后会提示下载`br`shell工具，输入`Y`进行下载，`source ~/.zshrc`命令后即可使用`br`命令。

<img src="imgs/image-20240505130318962.png" alt="image-20240505130318962" style="zoom:67%;" />

## 8.2 查找

使用`br -s`从当前目录启动`br`。通常的用法是直接输入目录名的某个关键词，键入`tab`选择另一个目录或者文件，然后键入`alt+enter`进入目录。`:q`会返回shell。

可以使用`c/内容`查找文件内容。

## 8.3 预览

键入`ctrl+→`可以预览文件内容。

## 8.4 编辑文件

键入`:e+enter`可以编辑光标所指文件。

# 9. ctrlp

Vim 的完整路径模糊**文件**、**缓冲区**、**mru**、**标签**、**...**查找器。

- 用纯 Vimscript 编写，适用于 MacVim、gVim 和 Vim 7.0+。
- 完全支持 Vim 的正则表达式作为搜索模式。
- 内置最近使用 (MRU) 文件监控。
- 内置项目的根查找器。
- 一次打开多个文件。
- 创建新文件和目录。
- 可扩展。

## 9.1 安装

```bash
mkdir -p ~/.vim/pack/plugins/start
git clone --depth=1 https://github.com/ctrlpvim/ctrlp.vim.git ~/.vim/pack/plugins/start/ctrlp
```

将以下内容加入`~/.vimrc`，以修改在Vim中启动`ctrlp`的快捷键为`CTRL+P`。

```bash
let g:ctrlp_map = '<c-p>'
let g:ctrlp_cmd = 'CtrlP'
```

## 9.2 基本用法

打开 CtrlP 后：

- 按下`<F5>`可清除当前目录的缓存以获取新文件、删除已删除的文件并应用新的忽略选项。
- 按`<c-f>`和`<c-b>`在模式之间循环。
- 按此键`<c-d>`切换到仅搜索文件名而不是完整路径。
- 按此键`<c-r>`切换到正则表达式模式。
- ==使用`<c-j>`、`<c-k>`或箭头键导航结果列表。==
- ==通常使用`<c-t>`开启新tab，使用`<c-x>`开启新窗口。==
- ==使用`<c-n>`,`<c-p>`选择提示历史记录中的下一个/上一个字符串。==
- ==用于`<c-y>`创建新文件及其父目录。==
- 用于`<c-z>`标记/取消标记多个文件并`<c-o>`打开它们。

# 10. Vim

在虚拟机里的Ubuntu默认是vi，所以需要使用`apt install vim`安装。

## 10.1 剪贴板配置

> 参考资料：
>
> [1]https://www.zhihu.com/question/19863631
>
> [2]https://www.cnblogs.com/shanql/p/6373642.html

要想支持从Vim中的剪贴板粘贴内容到Windows，输入`vim --version | grep clipboard`命令，如果`clipboard`为减号，则需要先安装`vim-gtk`：`sudo apt install vim-gtk`。

```bash
➜  04-Data Wrangling vim --version | grep clipboard
+clipboard         +keymap            +printer           +vertsplit
+emacs_tags        +mouse_gpm         -sun_workshop      +xterm_clipboard
```

- 用`"+y` 代替`y`将选中的内容复制到系统剪贴板，效果和`ctrl-c`一致。 
- 用`"+p`代替`p`将剪贴板内容复制到指定位置，也可以用`ctrl-v`。 

`d`，`x`，`c`，`s`也一样，用之前前面加`"+`。 

如果想偷懒用`y`直接把内容复制到系统剪贴板，需要到vim配置文件`.vimrc`里加一行属性：

```text
set clipboard=unnamedplus "高于7.3.34版本的vim需要配置unnamedplus，其他版本配置unnamed即可
```

现在你的`y`，`d`，`x`，`p`已经能和 `ctrl-c`和`ctrl-v` 一个效果，并且能互相混用。

## 10.2 Vim插件管理

> 参考资料：
>
> [1]https://github.com/junegunn/vim-plug

使用`vim-plug`作为默认的vim插件管理工具。将以下内容粘贴至`~/.vimrc`：

```bash
let data_dir = has('nvim') ? stdpath('data') . '/site' : '~/.vim'
if empty(glob(data_dir . '/autoload/plug.vim'))
  silent execute '!curl -fLo '.data_dir.'/autoload/plug.vim --create-dirs  https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
  autocmd VimEnter * PlugInstall --sync | source $MYVIMRC
endif
```

> [!CAUTION]
>
> 以上粘贴的内容要确保在`plug#begin()`的前面。

在`~/.vimrc`里，插件管理的内容如下：

```bash
call plug#begin()
Plug ...
Plug ...
Plug ...
call plug#end()
```

## 10.2.1 ale

ale用于异步检查Vim中的语法并且修复文件。

安装步骤如下：

1. 在`~/.vimrc`中添加以下内容：

   ```bash
   Plug 'w0rp/ale'
   ```

2. 在Vim中运行以下命令：

   ```bash
   :source %
   :PlugInstall
   ```



# 11. man中文版本

1. 安装中文版本`man` :`apt install manpages-zh`。

2. 在`~/.zshrc`中新增alias，以便使用`cman`命令启动中文版本的`man`：

```bash
alias cman='man -M /usr/share/man/zh_CN'
```

3. 保存退出，执行`source ~/.zshrc`使配置生效。
4. 执行`cman cmd`查看中文版本`cmd`命令的手册。

# 12. vsftpd

vsftpd 是“very secure FTP daemon”的缩写，是一个开源的FTP服务器软件。

## 12.1 Linux端

安装配置vsftpd的步骤如下：

1. 安装vsftpd：

   ```bash
   sudo apt update
   sudo apt install vsftpd
   ```

2. 编辑vsftpd配置文件`/etc/vsftpd.conf`，需要修改的内容如下：

   ```bash
   anonymous_enable=YES # 允许匿名用户登录
   write_enable=YES # 允许上传文件
   anon_upload_enable=YES # 允许匿名用户上传文件
   anon_mkdir_write_enable=YES # 允许匿名用户创建目录
   anon_umask=022 # 设置默认上传的文件权限为rw-r--r--
   ```

3. 重启vsftpd服务：

   ```bash
   sudo systemctl restart vsftpd
   ```

4. 确保防火墙允许外界访问ftp端口：

   ```bash
   sudo ufw allow 20/tcp
   sudo ufw allow 21/tcp
   sudo ufw status
   ```

配置完成后，外部匿名用户默认访问的根目录为：`/srv/ftp`。

## 12.2 Windows端

Windows端使用的FTP客户端软件为winscp，下载地址：https://winscp.net/eng/index.php

打开winscp后，选择新建站点，如下：

<img src="imgs/image-20240509192658556.png" alt="image-20240509192658556" style="zoom:67%;" />

- 文件协议：FTP
- 主机名：由Linux端执行`ifconfig`得到。
- 端口号：21
- 勾选匿名登录

# 13. tmux

> 参考资料：
>
> [1]https://hamvocke.com/blog/a-guide-to-customizing-your-tmux-conf/

tmux全局配置文件位于` /etc/tmux.conf`，用户配置文件位于`~/.tmux.conf`。

## 13.1 更改前缀键

tmux默认的前缀键是`ctrl+b`，可以重新映射为`ctrl+a`。如下所示：

```bash
# remap prefix from 'C-b' to 'C-a'
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix
```

> [!warning]
>
> bash默认会把`ctrl+a`作为转到行首的快捷键，更改之后需要按下两次`ctrl+a`才会转到hang'shou

## 13.2 更改窗格分割键

选择`|`和`-`分割窗格，如下所示：

```bash
# split panes using | and -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %
```

## 13.3 快速窗格切换

将窗格切换改成不用前缀键触发，直接使用`Alt+<方向键>`触发：

```bash
# switch panes using Alt-arrow without prefix
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D
```

## 13.4 鼠标模式

可以使用鼠标单击选择窗口和窗格，并且可以拖动边框调整窗格大小，如下所示：

```bash
# Enable mouse control (clickable windows, panes, resizable panes)
set -g mouse on
```

## 13.5 停止窗口自动重命名

默认情况下，tmux根据窗口最后执行的命令自动更新窗口名称，可以禁用该行为：

```bash
# don't rename windows automatically
set-option -g allow-rename off
```

## 13.6 改变tmux外观

```bash
# DESIGN TWEAKS

# don't do anything when a 'bell' rings
set -g visual-activity off
set -g visual-bell off
set -g visual-silence off
setw -g monitor-activity off
set -g bell-action none

# clock mode
setw -g clock-mode-colour colour1

# copy mode
setw -g mode-style 'fg=colour1 bg=colour18 bold'

# pane borders
set -g pane-border-style 'fg=colour1'
set -g pane-active-border-style 'fg=colour3'

# statusbar
set -g status-position bottom
set -g status-justify left
set -g status-style 'fg=colour1'
set -g status-left ''
set -g status-right '%Y-%m-%d %H:%M '
set -g status-right-length 50
set -g status-left-length 10

setw -g window-status-current-style 'fg=colour0 bg=colour1 bold'
setw -g window-status-current-format ' #I #W #F '

setw -g window-status-style 'fg=colour1 dim'
setw -g window-status-format ' #I #[fg=colour7]#W #[fg=colour1]#F '

setw -g window-status-bell-style 'fg=colour2 bg=colour1 bold'

# messages
set -g message-style 'fg=colour2 bg=colour0 bold'
```

## 13.7 更改默认终端

```bash
# config terminor
set -g default-terminal "xterm-256color"
```



# 14. chezmoi

chezmoi帮助您跨多台计算机管理个人配置文件。

## 14.1 安装

```bash
cd /
sh -c "$(curl -fsLS get.chezmoi.io)"
```

如果之前已经使用chezmoi并且在GihHub上有dotfiles仓库，可以使用以下命令：

```bash
cd /
sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply $GITHUB_USERNAME # 将$GitHub_USERNAME修改为GitHub上的用户名
```

如果GitHub上的dotfiles仓库是私有的，使用以下命令：

```bash
cd /
sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply git@github.com:$GITHUB_USERNAME/dotfiles.git # 将$GitHub_USERNAME修改为GitHub上的用户名
```

安装完成后，将会出现`/bin/chezmoi`。

## 14.2 在首台计算机使用

<img src="imgs/image-20240510142003235.png" alt="image-20240510142003235" style="zoom:67%;" />

### 14.2.1 初始化chezmoi

```bash
cd ~
chezmoi init
```

运行完命令之后会在`~/.local/share/chezmoi`创建一个git仓库，用于存储==源状态==，用于后续修改以及上传。

> [!Note]
>
> 后续如果要修改点文件，先[编辑源状态](#14.2.3 编辑源状态)，然后[应用更改](# 14.2.5 应用更改)。
>
> 如果直接修改点文件，源状态不会发生改变，直到再次执行`chezmoi apply`，此时会提示点文件发生改变，可以`chezmoi merge <点文件>`进行合并。

### 14.2.2 管理文件

使用命令`chezmoi add <点文件>`，以便chezmoi可以管理该点文件。

执行完上述命令后，chezmoi将会复制点文件到`~/.local/share/chezmoi/dot_<点文件>`。

### 14.2.3 编辑源状态

之后如果要编辑点文件，尽量使用以下命令：

```bash
chezmoi edit <点文件>
```

如要修改`~/.bashrc`，需要执行命令`chezmoi edit ~/.bashrc`。

### 14.2.4 查看改变

使用命令`chezmoi diff`，查看chezmoi源状态和系统原点文件之间的区别。

### 14.2.5 应用更改

使用`chezmoi -v apply`应用更改（将源状态内容复制到点文件），并且查看更改的具体内容。

### 14.2.6 向GitHub提交

在执行以下命令之前，如果没有dotfiles仓库，需要先在GitHub中创建名为dotfiles的仓库：https://github.com/new

```bash
chezmoi cd # 移动到chezmoi目录
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:$GITHUB_USERNAME/dotfiles.git
git branch -M main
git push -u origin main
exit # 返回原来的目录
```

- `-u`或`--set-upstream`:这个选项将设置本地的`main`分支跟踪远程的`main`分支。这意味着在以后的推送和拉取操作中，Git将自动将本地的`main`分支与远程的`main`分支进行同步。

> [!caution]
>
>  初次创建dotfiles的GitHub仓库后，需要先将`~/.local/share/chezmoi`的分支与GitHub上的分支合并才能push。
>
> 可以使用命令`git pull origin main --allow-unrelated-histories`进行合并，其中`--allow-unrelated-histories`允许两个毫无关联的分支进行合并。

## 14.3 其他机器使用

![image-20240510145727971](imgs/image-20240510145727971.png)

在其他机器上，使用以下命令进行chezmoi初始化：

```bash
chezmoi init https://github.com/$GITHUB_USERNAME/dotfiles.git
```

> [!note]
>
> 如果是私有库，需要是由这个命令`chezmoi init git@github.com:$GITHUB_USERNAME/dotfiles.git`

可以通过`chezmoi diff`命令查看主目录进行哪些更改，如果满意则应用：`chezmoi apply -v`，如果不满意可以进行修改：`chezmoi edit <点文件>`。

可以使用`chezmoi`从远端提取最新变化并且应用：`chezmoi update -v`。

如果不想进行修改，可以直接使用以下命令直接应用：

```bash
chezmoi init --apply https://github.com/$GITHUB_USERNAME/dotfiles.git
```

同样，其他机器一样可以向GitHub的dotfiles库进行push。

## 14.4 配置自动push

chezmoi 可以自动提交对源目录的更改并将其推送到您的存储库。要启用它，请将以下内容添加到您的配置文件`~/.config/chezmoi/chezmoi.toml`：

```bash
[git]
    autoCommit = true
    autoPush = true
```

# 15. git

## 15.1 修改提交使用的用户名和邮箱

输入命令`git config --global --edit`进行配置文件修改，添加以下内容：

```bash
[user]
# Please adapt and uncomment the following lines:
	name = <用户名>
	email = <邮箱>
```

## 15.2 GitHub公钥配置

1. 使用命令`ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519`生成一对SSH密钥。

   - `-o`：使用新的私钥文件格式。
   - `-a 100`：加密密钥迭代次数为100次，通常迭代次数越高越安全，但是生成时间也越多。
   - `-t ed25519`：使用ed25519加密算法。
   - `-f ~/.ssh/id_ed25519`：生成的密钥文件为`~/.ssh/id_ed25519`。

2. 打开`~/.ssh/id_ed25519.pub`，复制其中的ssh公钥到GitHub，如下所示：

   <img src="imgs/image-20240510135445854.png" alt="image-20240510135445854" style="zoom:67%;" />

   > [!caution]
   >
   > 使用命令后会生成两个文件：`id_ed25519`和`id_ed25519.pub`，前者是私钥，需要严格保密；后者是公钥，可以随意公开。

# 16. glances

**Glances**是一款开源系统跨平台监控工具。它可以实时监控系统的各个方面，如CPU、内存、磁盘、网络使用情况等。它还可以监控正在运行的进程、登录用户、温度、电压、风扇速度等。它还支持容器监控、支持Docker、LXC等不同的容器管理系统。该信息显示在易于阅读的仪表板中，也可用于通过 Web 界面或命令行界面远程监控系统。它易于安装和使用，并且可以自定义以仅显示您感兴趣的信息。

## 16.1 安装

先安装`pipx`，再使用`pipx`安装glances：

```bash
sudo apt install pipx
pipx install --force 'glances[all]'
```

## 16.2 启动

使用tmux分窗口，然后再启动：

```bash
glances
```

![image-20240511180140925](imgs/image-20240511180140925.png)



