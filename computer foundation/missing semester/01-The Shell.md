# 1. Shell是什么？

# 2. 使用Shell

## 2.1 参数空格转义

通常来说Shell基于空格分隔命令进行解析，如果希望Shell命令传递的参数中包含空格，可以使用单引号或者双引号将参数包裹起来，或者使用转义符号`\`进行处理，如下：

```bash
cd "my photo" # 单引号或者双引号将参数包裹起来
# 或者
cd my\ photo # 使用转义符号`\`进行处理
```

## 2.2 程序搜索路径

如果要求Shell执行某个命令，但是该命令不是Shell所了解的关键字，那么Shell会咨询*环境变量*`$PATH`，它会指明Shell接受指令后进行程序搜索的路径。

> [!WARNING]
>
> WSL中的`$PATH`会包含Windows中配置的环境变量`PATH`

```bash
➜  ~ echo $PATH # WSL中的`$PATH`会包含Windows中配置的环境变量`PATH`
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/lib/wsl/lib:/mnt/c/Program Files/WindowsApps/CanonicalGroupLimited.Ubuntu20.04LTS_2004.6.16.0_x64__79rhkp1fndgsc:/mnt/d/tools/VMware/VMware Workstation/bin/:/mnt/c/Program Files (x86)/Common Files/Oracle/Java/javapath:/mnt/d/tools/gcc/bin:/mnt/d/tools/QEMU:/mnt/d/Rational Rose/Common:/mnt/c/Program Files (x86)/MySQL/MySQL Server 5.5/bin:/mnt/c/WINDOWS/system32:/mnt/c/WINDOWS:/mnt/c/WINDOWS/System32/Wbem:/mnt/c/WINDOWS/System32/WindowsPowerShell/v1.0/:/mnt/c/WINDOWS/System32/OpenSSH/:/mnt/d/tools/mingw64/8.1.0/x86_64-8.1.0-release-posix-seh-rt_v6-rev0/mingw64/bin:/mnt/d/tools/Microsoft VS Code/bin:/mnt/d/tools/Microsoft Visual Studio/2019/VC/Tools/MSVC/14.28.29910/lib/x64:/mnt/c/Program Files/NVIDIA Corporation/NVIDIA NvDLISR:/mnt/c/Program Files (x86)/NVIDIA Corporation/PhysX/Common:/mnt/d/tools/NetSarang/Xshell 7/:/mnt/c/Program Files/Intel/WiFi/bin/:/mnt/c/Program Files/Common Files/Intel/WirelessCommon/:/mnt/c/Program Files/Java/jdk1.8.0_201/bin:/mnt/d/tools/Git/cmd:/mnt/d/tools/Node.js/:/mnt/c/Users/霍宇佳/AppData/Roaming/npm:/mnt/c/Program Files/Python/Python37:/mnt/c/Program Files/dotnet/:/mnt/c/Program Files/usbipd-win/:/mnt/c/Users/霍宇佳/AppData/Local/Microsoft/WindowsApps:/mnt/d/python:/mnt/d/IntelliJ IDEA Community Edition 2019.2/bin:/mnt/d/tools/modelsim/win64:/mnt/c/Modeltech_pe_edu_10.2a/win32pe_edu:/mnt/d/tools/modelsim/win32pe_edu:/mnt/c/Modeltech_pe_edu_10.4a/win32pe_edu:/mnt/c/modeltech64_10.4/win64:/mnt/d/tools/apache-maven-3.6.3/bin:/mnt/d/tools/IntelliJ IDEA 2024.1/bin:/mnt/c/Users/霍宇佳/AppData/Local/Programs/Microsoft VS Code/bin:/opt/riscv/bin
```

可以通过`which`命令查看某个命令所对应的程序路径。

```bash
➜  ~ which gcc # 查看命令对应程序所在的路径
/usr/bin/gcc
```

可以使用`clear`命令清空屏幕，也可以使用`CTRL`+`L`快捷键快速清空屏幕。

# 3. 在Shell中导航

在Linux和macOS中，路径中的目录使用`/`进行分隔；在Windows中，路径的目录使用`\`进行分隔。

当我们运行程序时，如果没有指定运行路径，那么程序会在当前目录下执行。

```bash
➜  ~ which gcc # 查看命令对应程序所在的路径
/usr/bin/gcc
```

## 3.1 文件或目录权限

```bash
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
```

本行第一个字符 `d` 表示 `missing` 是一个目录。

然后接下来的九个字符，每三个字符构成一组。 它们分别代表了文件所有者（`missing`），用户组（`users`） 以及其他所有人具有的权限。其中 `-` 表示该用户不具备相应的权限。

从上面的信息来看，只有文件所有者可以修改（`w`）`missing` 文件夹 （例如，添加或删除文件夹中的文件）。为了进入某个文件夹，用户需要具备该文件夹以及其父文件夹的“搜索”权限（以“可执行”`x`权限表示）。为了列出它的包含的内容，用户必须对该文件夹具备读权限（`r`）。

注意，`/bin` 目录下的程序在最后一组，即表示所有人的用户组中，均包含 `x` 权限，也就是说任何人都可以执行这些程序。

# 4. 在程序间创建连接

可以使用`< file`将程序的输入重定向到文件`file`，可以使用`> file`将程序的输出重定向到文件`file`。

可以使用`>>`向文件追加内容。

`|`操作符会将一个程序的输出与另一个程序的输入连接在一起，管道会在数据清理章节更详细介绍。

> [!Note]
>
> - `|`用于命令间的数据流动，将一个命令的输出作为另一个命令的输入。
> - `>`用于将命令的输出重定向到文件中。
>
> `|`的两边一般都是命令， `>`或者`<`一边是命令一边是文件。

```bash
# --head是获取html文件的头部，--silent是不输出进度条，grep -i content-length是提取内容包含“content-length”的行，并且不区分大小写
➜  ~ curl --head --silent google.com | grep -i content-length
Content-Length: 219

# cut --delimiter=' ' -f2，表示以空格为分隔符，取第二个分隔内容
➜  ~ curl --head --silent google.com | grep -i content-length | cut --delimiter=' ' -f2
219
```



# 5. 一个功能全面又强大的工具

使用除了root用户之外的用户执行以下命令，会发生错误：

```bash
$ cd /sys/class/backlight/thinkpad_screen
$ sudo echo 3 > brightness
```

发生错误的原因是：`|`、`>`、`<`是通过shell执行而不是各个程序单独执行的，因此在Shell执行`sudo echo 3`命令前，shell的当前用户会先尝试打开`brightness`文件，此时shell不是root用户，因此打开失败，显示`open: Permission denied`。

正确的方式如下：

```bash
$ echo 3 | sudo tee brightness # tee用于从标准输入读取数据，并将其输出到标准输出以及一个或多个文件。
```

#  6. 课后练习

## 6.6 创建的目录或文件的默认权限

在Linux中，新创建的文件和目录的权限与`umask`值有关。root系统管理员的默认`umask`值为022，其他用户的默认`umask`值为002。

文件权限的计算方法如下：

| **文件的起始权限值** | **umask值**                 | **操作**                            | **计算后文件权限** |
| -------------------- | --------------------------- | ----------------------------------- | ------------------ |
| 666                  | 022 (每位如果都是偶数)      | 相减                                | 644                |
| 666                  | 033  (每位如果有奇数或偶数) | 相减（奇数位相减后在其原奇数位加1） | 644                |
| 666                  | 325(每位如果有奇数或偶数)   | 相减（奇数位相减后在其原奇数位加1） | 442                |

目录权限的计算方法如下：

| **文件的起始权限值** | **umask值** | **操作** | **计算后文件权限** |
| -------------------- | ----------- | -------- | ------------------ |
| 777                  | 022         | 相减     | 755                |
| 777                  | 033         | 相减     | 744                |
| 777                  | 325         | 相减     | 452                |

不更改umask值情况下，创建的文件或者目录的权限如下：

| 用户     | 文件           | 目录           |
| -------- | -------------- | -------------- |
| root     | `rw-r--r--`644 | `rwxr-xr-x`755 |
| 其他用户 | `rw-rw-r--`664 | `rwxrwxr-x`775 |

## 6.8 Shebang

> 参考资料：
>
> [1]https://zh.wikipedia.org/wiki/Shebang

**Shebang**（也称为**Hashbang**）是一个由井号和叹号构成的字符序列*`#!`*，其出现在文本文件的第一行的前两个字符。在开头字符之后，可以有一个或数个空白字符，后接解释器的绝对路径，用于调用解释器。

运行文件后的参数将会作为指令的参数。如`/usr/local/bin/foo`文件中的内容为：

```bash
#!/bin/sh -x
脚本语句
```

输入命令`foo bar`等价于`/bin/sh -x /usr/local/bin/foo bar`

## 6.9 重定向

```bash
# 使用 | 和 > ，将 semester 文件输出的最后更改日期信息，写入主目录下的 last-modified.txt 的文件中
./semester | grep -i date > /root/study/missing\ semester/last-modified.txt
```

## 6.10 find命令

find命令的一般用法如下：

```bash
find /path/to/search -name "filename" -type d
```

- `/path/to/search`：在指定的目录中查找，如果没有该参数则在当前目录下查找。
- `-name "filename"`：按名称查找。可以使用`*`作为文件名的通配符。
- `-type d`：按类型查找。

```bash
# 查看名字带有容量的文件或者目录
➜  /sys find -name '*capacity*'
./devices/LNXSYSTM:00/LNXSYBUS:00/ACPI0004:00/PNP0C0A:00/power_supply/BAT1/capacity
./devices/LNXSYSTM:00/LNXSYBUS:00/ACPI0004:00/PNP0C0A:00/power_supply/BAT1/capacity_level
./devices/LNXSYSTM:00/LNXSYBUS:00/ACPI0004:00/VMBUS:00/fd1d2cbd-ce7c-535c-966b-eb5f811c95f0/host0/target0:0:0/0:0:0:1/evt_capacity_change_reported
./devices/LNXSYSTM:00/LNXSYBUS:00/ACPI0004:00/VMBUS:00/fd1d2cbd-ce7c-535c-966b-eb5f811c95f0/host0/target0:0:0/0:0:0:2/evt_capacity_change_reported
./devices/LNXSYSTM:00/LNXSYBUS:00/ACPI0004:00/VMBUS:00/fd1d2cbd-ce7c-535c-966b-eb5f811c95f0/host0/target0:0:0/0:0:0:0/evt_capacity_change_reported
./fs/cgroup/misc/misc.capacity

# 获取笔记本的电量信息
➜  /sys cat ./devices/LNXSYSTM:00/LNXSYBUS:00/ACPI0004:00/PNP0C0A:00/power_supply/BAT1/capacity                                                               78
```

