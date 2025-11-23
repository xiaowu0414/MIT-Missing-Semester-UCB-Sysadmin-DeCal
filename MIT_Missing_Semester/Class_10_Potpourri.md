---
date: 2025-11-15
---
# Potpourri  杂烩

## Table of Contents  目录

-   [Keyboard remapping  键盘重映射](https://missing.csail.mit.edu/2020/potpourri/#keyboard-remapping)
-   [Daemons  恶魔](https://missing.csail.mit.edu/2020/potpourri/#daemons)
-   [FUSE  保险丝](https://missing.csail.mit.edu/2020/potpourri/#fuse)
-   [Backups  备份](https://missing.csail.mit.edu/2020/potpourri/#backups)
-   [APIs  蜜蜂](https://missing.csail.mit.edu/2020/potpourri/#apis)
-   [Common command-line flags/patterns  
    常用命令行标志/模式](https://missing.csail.mit.edu/2020/potpourri/#common-command-line-flagspatterns)
-   [Window managers  窗口管理器](https://missing.csail.mit.edu/2020/potpourri/#window-managers)
-   [VPNs  VPN](https://missing.csail.mit.edu/2020/potpourri/#vpns)
-   [Markdown](https://missing.csail.mit.edu/2020/potpourri/#markdown)
-   [Hammerspoon (desktop automation on macOS)  
    Hammerspoon（macOS 上的桌面自动化工具）](https://missing.csail.mit.edu/2020/potpourri/#hammerspoon-desktop-automation-on-macos)
-   [Booting + Live USBs  
    启动盘 + Live USB](https://missing.csail.mit.edu/2020/potpourri/#booting--live-usbs)
-   [Docker, Vagrant, VMs, Cloud, OpenStack  
    Docker、Vagrant、虚拟机、云、OpenStack](https://missing.csail.mit.edu/2020/potpourri/#docker-vagrant-vms-cloud-openstack)
-   [Notebook programming  笔记本编程](https://missing.csail.mit.edu/2020/potpourri/#notebook-programming)
-   [GitHub](https://missing.csail.mit.edu/2020/potpourri/#github)

## Keyboard remapping  键盘重映射

As a programmer, your keyboard is your main input method. As with pretty much anything in your computer, it is configurable (and worth configuring).  
作为程序员，键盘是你的主要输入方式。就像电脑上的几乎所有东西一样，它是可以配置的（而且值得配置）。

The most basic change is to remap keys. This usually involves some software that is listening and, whenever a certain key is pressed, it intercepts that event and replaces it with another event corresponding to a different key. Some examples:  
最基本的更改是重新映射按键。这通常需要一些软件监听按键事件，并在每次按下某个键时拦截该事件，并将其替换为对应于另一个键的事件。例如：

-   Remap Caps Lock to Ctrl or Escape. We (the instructors) highly encourage this setting since Caps Lock has a very convenient location but is rarely used.  
    将 Caps Lock 键重新映射到 Ctrl 或 Esc 键。我们（授课老师）强烈建议您这样做，因为 Caps Lock 键位置很方便，但却很少用到。
-   Remapping PrtSc to Play/Pause music. Most OSes have a play/pause key.  
    将 PrtSc 键重新映射为播放/暂停音乐。大多数操作系统都有播放/暂停键。
-   Swapping Ctrl and the Meta (Windows or Command) key.  
    交换 Ctrl 键和 Meta 键（Windows 或 Command 键）。

You can also map keys to arbitrary commands of your choosing. This is useful for common tasks that you perform. Here, some software listens for a specific key combination and executes some script whenever that event is detected.  
你还可以将按键映射到你选择的任意命令。这对于你经常执行的任务非常有用。例如，某些软件会监听特定的按键组合，并在检测到该事件时执行相应的脚本。

-   Open a new terminal or browser window.  
    打开一个新的终端或浏览器窗口。
-   Inserting some specific text, e.g. your long email address or your MIT ID number.  
    插入一些特定文本，例如您的完整电子邮件地址或您的麻省理工学院 ID 号。
-   Sleeping the computer or the displays.  
    让电脑或显示器进入睡眠状态。

There are even more complex modifications you can configure:  
您还可以配置更复杂的修改：

-   Remapping sequences of keys, e.g. pressing shift five times toggles Caps Lock.  
    重新映射按键序列，例如按五次 Shift 键切换 Caps Lock 键。
-   Remapping on tap vs on hold, e.g. Caps Lock key is remapped to Esc if you quickly tap it, but is remapped to Ctrl if you hold it and use it as a modifier.  
    轻按与长按可重新映射按键，例如，如果快速轻按 Caps Lock 键，则将其重新映射为 Esc 键；但如果按住 Caps Lock 键并将其用作修饰键，则将其重新映射为 Ctrl 键。
-   Having remaps being keyboard or software specific.  
    按键映射可能与键盘或软件有关。

Some software resources to get started on the topic:  
以下是一些可以帮助你入门的软件资源：

-   macOS - [karabiner-elements](https://karabiner-elements.pqrs.org/), [skhd](https://github.com/koekeishiya/skhd) or [BetterTouchTool](https://folivora.ai/)  
    macOS - [karabiner-elements](https://karabiner-elements.pqrs.org/) 、 [skhd](https://github.com/koekeishiya/skhd) 或 [BetterTouchTool](https://folivora.ai/)
-   Linux - [xmodmap](https://wiki.archlinux.org/index.php/Xmodmap) or [Autokey](https://github.com/autokey/autokey)  
    Linux - [xmodmap](https://wiki.archlinux.org/index.php/Xmodmap) 或 [Autokey](https://github.com/autokey/autokey)
-   Windows - Builtin in Control Panel, [AutoHotkey](https://www.autohotkey.com/) or [SharpKeys](https://www.randyrants.com/category/sharpkeys/)  
    Windows 系统 - 内置控制面板、 [AutoHotkey](https://www.autohotkey.com/) 或 [SharpKeys](https://www.randyrants.com/category/sharpkeys/)
-   QMK - If your keyboard supports custom firmware you can use [QMK](https://docs.qmk.fm/) to configure the hardware device itself so the remaps works for any machine you use the keyboard with.  
    QMK - 如果您的键盘支持自定义固件，您可以使用 [QMK](https://docs.qmk.fm/) 配置硬件设备本身，以便重新映射功能适用于您使用该键盘的任何机器。

## Daemons  恶魔

You are probably already familiar with the notion of daemons, even if the word seems new. Most computers have a series of processes that are always running in the background rather than waiting for a user to launch them and interact with them. These processes are called daemons and the programs that run as daemons often end with a `d` to indicate so. For example `sshd`, the SSH daemon, is the program responsible for listening to incoming SSH requests and checking that the remote user has the necessary credentials to log in.  
即使“守护进程”这个词听起来很陌生，你可能已经熟悉它的概念了。大多数计算机都有一系列始终在后台运行的进程，它们无需用户启动和交互即可运行。这些进程被称为守护进程，作为守护进程运行的程序通常以“ `d` 结尾。例如，SSH 守护进程 `sshd` 负责监听传入的 SSH 请求，并检查远程用户是否拥有登录所需的凭据。

In Linux, `systemd` (the system daemon) is the most common solution for running and setting up daemon processes. You can run `systemctl status` to list the current running daemons. Most of them might sound unfamiliar but are responsible for core parts of the system such as managing the network, solving DNS queries or displaying the graphical interface for the system. Systemd can be interacted with the `systemctl` command in order to `enable`, `disable`, `start`, `stop`, `restart` or check the `status` of services (those are the `systemctl` commands).  
在 Linux 系统中， `systemd` （系统守护进程）是运行和设置守护进程最常用的解决方案。您可以使用 `systemctl status` 列出当前正在运行的守护进程。其中大多数可能听起来很陌生，但它们负责系统的核心部分，例如管理网络、解析 DNS 查询或显示系统图形界面。您可以使用 `systemctl` 命令与 systemd 进行交互，以 `enable` 、 `disable` 、 `start` 、 `stop` 、 `restart` 或检查服务的 `status` （这些都是 `systemctl` 命令的功能）。

More interestingly, `systemd` has a fairly accessible interface for configuring and enabling new daemons (or services). Below is an example of a daemon for running a simple Python app. We won’t go in the details but as you can see most of the fields are pretty self explanatory.  
更有趣的是， `systemd` 提供了一个相当易用的界面，用于配置和启用新的守护进程（或服务）。以下是一个运行简单 Python 应用程序的守护进程示例。我们不会深入探讨细节，但正如您所见，大多数字段都非常直观易懂。

```
# /etc/systemd/system/myapp.service
[Unit]
Description=My Custom App
After=network.target

[Service]
User=foo
Group=foo
WorkingDirectory=/home/foo/projects/mydaemon
ExecStart=/usr/bin/local/python3.7 app.py
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Also, if you just want to run some program with a given frequency there is no need to build a custom daemon, you can use [`cron`](https://www.man7.org/linux/man-pages/man8/cron.8.html), a daemon your system already runs to perform scheduled tasks.  
此外，如果您只是想按给定的频率运行某个程序，则无需构建自定义守护进程，您可以使用 [`cron`](https://www.man7.org/linux/man-pages/man8/cron.8.html) ，这是您的系统已经运行的用于执行计划任务的守护进程。

## FUSE  保险丝

Modern software systems are usually composed of smaller building blocks that are composed together. Your operating system supports using different filesystem backends because there is a common language of what operations a filesystem supports. For instance, when you run `touch` to create a file, `touch` performs a system call to the kernel to create the file and the kernel performs the appropriate filesystem call to create the given file. A caveat is that UNIX filesystems are traditionally implemented as kernel modules and only the kernel is allowed to perform filesystem calls.  
现代软件系统通常由许多较小的构建模块组成。操作系统之所以支持使用不同的文件系统后端，是因为文件系统支持的操作遵循通用的语言。例如，当您运行 `touch` 创建文件时， `touch` 会向内核发出系统调用来创建文件，而内核则会执行相应的文件系统调用来创建该文件。需要注意的是，UNIX 文件系统传统上是以内核模块的形式实现的，并且只有内核才能执行文件系统调用。

[FUSE](https://en.wikipedia.org/wiki/Filesystem_in_Userspace) (Filesystem in User Space) allows filesystems to be implemented by a user program. FUSE lets users run user space code for filesystem calls and then bridges the necessary calls to the kernel interfaces. In practice, this means that users can implement arbitrary functionality for filesystem calls.  
[FUSE](https://en.wikipedia.org/wiki/Filesystem_in_Userspace) （用户空间文件系统）允许用户程序实现文件系统。FUSE 允许用户运行用户空间代码进行文件系统调用，然后将必要的调用桥接到内核接口。实际上，这意味着用户可以为文件系统调用实现任意功能。

For example, FUSE can be used so whenever you perform an operation in a virtual filesystem, that operation is forwarded through SSH to a remote machine, performed there, and the output is returned back to you. This way, local programs can see the file as if it was in your computer while in reality it’s in a remote server. This is effectively what `sshfs` does.  
例如，FUSE 可以实现这样的功能：当你在虚拟文件系统中执行操作时，该操作会通过 SSH 转发到远程机器，在那里执行操作，然后将结果返回给你。这样，本地程序就可以像访问本地计算机一样访问该文件，而实际上它位于远程服务器上。这与 `sshfs` 工作原理基本相同。

Some interesting examples of FUSE filesystems are:  
FUSE 文件系统的一些有趣示例包括：

-   [sshfs](https://github.com/libfuse/sshfs) - Open locally remote files/folder through an SSH connection.  
    [sshfs](https://github.com/libfuse/sshfs) - 通过 SSH 连接在本地打开远程文件/文件夹。
-   [rclone](https://rclone.org/commands/rclone_mount/) - Mount cloud storage services like Dropbox, GDrive, Amazon S3 or Google Cloud Storage and open data locally.  
    [rclone](https://rclone.org/commands/rclone_mount/) - 挂载 Dropbox、GDrive、Amazon S3 或 Google Cloud Storage 等云存储服务，并在本地打开数据。
-   [gocryptfs](https://nuetzlich.net/gocryptfs/) - Encrypted overlay system. Files are stored encrypted but once the FS is mounted they appear as plaintext in the mountpoint.  
    [gocryptfs——](https://nuetzlich.net/gocryptfs/) 加密覆盖系统。文件以加密形式存储，但文件系统挂载后，它们在挂载点上以明文形式显示。
-   [kbfs](https://keybase.io/docs/kbfs) - Distributed filesystem with end-to-end encryption. You can have private, shared and public folders.  
    [kbfs——](https://keybase.io/docs/kbfs) 一种具有端到端加密功能的分布式文件系统。您可以拥有私有文件夹、共享文件夹和公共文件夹。
-   [borgbackup](https://borgbackup.readthedocs.io/en/stable/usage/mount.html) - Mount your deduplicated, compressed and encrypted backups for ease of browsing.  
    [borgbackup](https://borgbackup.readthedocs.io/en/stable/usage/mount.html) - 挂载您的去重、压缩和加密备份，以便轻松浏览。

## Backups  备份

Any data that you haven’t backed up is data that could be gone at any moment, forever. It’s easy to copy data around, it’s hard to reliably backup data. Here are some good backup basics and the pitfalls of some approaches.  
任何未备份的数据都可能随时丢失，而且可能永远丢失。复制数据很容易，但可靠地备份数据却很难。以下是一些基本的备份知识以及一些备份方法的缺陷。

First, a copy of the data in the same disk is not a backup, because the disk is the single point of failure for all the data. Similarly, an external drive in your home is also a weak backup solution since it could be lost in a fire/robbery/&c. Instead, having an off-site backup is a recommended practice.  
首先，将数据复制到同一磁盘上并非备份，因为该磁盘是所有数据的单点故障。同样，家中的外置硬盘也并非可靠的备份方案，因为它可能在火灾、盗窃等意外事件中丢失。因此，建议采用异地备份。

Synchronization solutions are not backups. For instance, Dropbox/GDrive are convenient solutions, but when data is erased or corrupted they propagate the change. For the same reason, disk mirroring solutions like RAID are not backups. They don’t help if data gets deleted, corrupted or encrypted by ransomware.  
同步方案并非备份。例如，Dropbox/Google Drive 等同步方案虽然方便，但一旦数据被删除或损坏，它们也会同步更改。同样，磁盘镜像方案（例如 RAID）也并非备份。如果数据被删除、损坏或被勒索软件加密，它们也无济于事。

Some core features of good backups solutions are versioning, deduplication and security. Versioning backups ensure that you can access your history of changes and efficiently recover files. Efficient backup solutions use data deduplication to only store incremental changes and reduce the storage overhead. Regarding security, you should ask yourself what someone would need to know/have in order to read your data and, more importantly, to delete all your data and associated backups. Lastly, blindly trusting backups is a terrible idea and you should verify regularly that you can use them to recover data.  
优秀备份方案的核心功能包括版本控制、数据去重和安全性。版本控制备份确保您可以访问更改历史记录并高效地恢复文件。高效的备份方案使用数据去重技术，仅存储增量更改，从而降低存储开销。关于安全性，您应该扪心自问：需要哪些信息/资源才能读取您的数据？更重要的是，需要哪些信息才能删除您的所有数据及其相关备份？最后，盲目信任备份是极其危险的，您应该定期验证备份是否可用于恢复数据。

Backups go beyond local files in your computer. Given the significant growth of web applications, large amounts of your data are only stored in the cloud. For instance, your webmail, social media photos, music playlists in streaming services or online docs are gone if you lose access to the corresponding accounts. Having an offline copy of this information is the way to go, and you can find online tools that people have built to fetch the data and save it.  
备份不仅仅局限于电脑本地文件。随着网络应用的蓬勃发展，大量数据都存储在云端。例如，如果您无法访问相应的账户，您的网络邮件、社交媒体照片、流媒体服务中的音乐播放列表或在线文档都将丢失。因此，拥有这些信息的离线副本至关重要。您可以找到许多在线工具，它们可以帮助您获取并保存这些数据。

For a more detailed explanation, see 2019’s lecture notes on [Backups](https://missing.csail.mit.edu/2019/backups).  
有关更详细的解释，请参阅 2019 年关于[备份的](https://missing.csail.mit.edu/2019/backups)讲义。

## APIs  蜜蜂

We’ve talked a lot in this class about using your computer more efficiently to accomplish _local_ tasks, but you will find that many of these lessons also extend to the wider internet. Most services online will have “APIs” that let you programmatically access their data. For example, the US government has an API that lets you get weather forecasts, which you could use to easily get a weather forecast in your shell.  
这门课我们已经多次讨论过如何更高效地使用电脑完成_本地_任务，但你会发现，其中许多技巧也适用于更广泛的互联网。大多数在线服务都提供“API”，允许你通过编程方式访问它们的数据。例如，美国政府提供了一个 API，可以让你获取天气预报，你可以使用它在命令行界面（shell）中轻松获取天气预报。

Most of these APIs have a similar format. They are structured URLs, often rooted at `api.service.com`, where the path and query parameters indicate what data you want to read or what action you want to perform. For the US weather data for example, to get the forecast for a particular location, you issue GET request (with `curl` for example) to https://api.weather.gov/points/42.3604,-71.094. The response itself contains a bunch of other URLs that let you get specific forecasts for that region. Usually, the responses are formatted as JSON, which you can then pipe through a tool like [`jq`](https://stedolan.github.io/jq/) to massage into what you care about.  
大多数 API 的格式都类似。它们是结构化的 URL，通常以 `api.service.com` 为根，其中路径和查询参数指示您想要读取的数据或想要执行的操作。例如，要获取美国特定地点的天气预报，您可以向 https://api.weather.gov/points/42.3604,-71.094 发送 GET 请求（例如使用 `curl` ）。响应本身包含许多其他 URL，可用于获取该地区的具体预报。通常，响应格式为 JSON，您可以使用 [`jq`](https://stedolan.github.io/jq/) 等工具将其转换为您需要的内容。

Some APIs require authentication, and this usually takes the form of some sort of secret _token_ that you need to include with the request. You should read the documentation for the API to see what the particular service you are looking for uses, but “[OAuth](https://www.oauth.com/)” is a protocol you will often see used. At its heart, OAuth is a way to give you tokens that can “act as you” on a given service, and can only be used for particular purposes. Keep in mind that these tokens are _secret_, and anyone who gains access to your token can do whatever the token allows under _your_ account!  
某些 API 需要身份验证，通常需要将某种密钥_令牌_包含在请求中。您应该阅读 API 文档，了解您要查找的特定服务使用哪种身份验证方式，但“ [OAuth](https://www.oauth.com/) ” OAuth 是一种您经常会看到的协议。从本质上讲，OAuth 是一种…… 给你一些令牌，这些令牌可以在特定服务上“代表你”行事，并且只能 可用于特定用途。请记住，这些令牌是 _秘密_ ，任何获得您令牌的人都可以在_您的_帐户下执行该令牌允许的任何操作！

[IFTTT](https://ifttt.com/) is a website and service centered around the idea of APIs — it provides integrations with tons of services, and lets you chain events from them in nearly arbitrary ways. Give it a look!  
[IFTTT](https://ifttt.com/) 是一个以 API 为核心的网站和服务——它集成了海量服务，并允许你以几乎任意的方式将这些服务中的事件串联起来。快去看看吧！

## Common command-line flags/patterns  
常用命令行标志/模式

Command-line tools vary a lot, and you will often want to check out their `man` pages before using them. They often share some common features though that can be good to be aware of:  
命令行工具种类繁多，使用前通常需要查阅它们的手册页 `man` page）。不过，它们也常常有一些共同的特性，了解这些特性很有帮助：

-   Most tools support some kind of `--help` flag to display brief usage instructions for the tool.  
    大多数工具都支持某种 `--help` 标志，用于显示工具的简要使用说明。
-   Many tools that can cause irrevocable change support the notion of a “dry run” in which they only print what they _would have done_, but do not actually perform the change. Similarly, they often have an “interactive” flag that will prompt you for each destructive action.  
    许多能够造成不可逆更改的工具都支持“试运行”的概念，即它们只会打印出原本_应该执行的_操作，但实际上并不会执行更改。同样，它们通常也带有“交互式”标志，会在每次执行破坏性操作前提示用户。
-   You can usually use `--version` or `-V` to have the program print its own version (handy for reporting bugs!).  
    通常可以使用 `--version` 或 `-V` 让程序打印自己的版本（方便报告错误！）。
-   Almost all tools have a `--verbose` or `-v` flag to produce more verbose output. You can usually include the flag multiple times (`-vvv`) to get _more_ verbose output, which can be handy for debugging. Similarly, many tools have a `--quiet` flag for making it only print something on error.  
    几乎所有工具都提供 `--verbose` 或 `-v` 参数来生成更详细的输出。通常可以多次使用 \` `-vvv` 参数来获取_更_详细的输出，这对于调试非常有用。类似地，许多工具还提供 `--quiet` 参数，使其仅在出错时才输出信息。
-   In many tools, `-` in place of a file name means “standard input” or “standard output”, depending on the argument.  
    在许多工具中， `-` 代替文件名表示“标准输入”或“标准输出”，具体取决于参数。
-   Possibly destructive tools are generally not recursive by default, but support a “recursive” flag (often `-r`) to make them recurse.  
    可能具有破坏性的工具通常默认情况下不是递归的，但支持“递归”标志（通常 `-r` ）使其递归。
-   Sometimes, you want to pass something that _looks_ like a flag as a normal argument. For example, imagine you wanted to remove a file called `-r`. Or you want to run one program “through” another, like `ssh machine foo`, and you want to pass a flag to the “inner” program (`foo`). The special argument `--` makes a program _stop_ processing flags and options (things starting with `-`) in what follows, letting you pass things that look like flags without them being interpreted as such: `rm -- -r` or `ssh machine --for-ssh -- foo --for-foo`.  
    有时，你可能想把_类似_标志的东西当作普通参数传递。例如，假设你想删除一个名为 `-r` 的文件。或者你想让一个程序“通过”另一个程序运行，就像这样。 `ssh machine foo` ，并且你想向“内部”程序（ `foo` ）传递一个标志。特殊参数 `--` 会使程序_停止_处理后面的标志和选项（以 `-` 开头的内容），从而允许你传递看起来像标志的内容，而不会被解释为标志： `rm -- -r` 或 `ssh machine --for-ssh -- foo --for-foo` 。

## Window managers  窗口管理器

Most of you are used to using a “drag and drop” window manager, like what comes with Windows, macOS, and Ubuntu by default. There are windows that just sort of hang there on screen, and you can drag them around, resize them, and have them overlap one another. But these are only one _type_ of window manager, often referred to as a “floating” window manager. There are many others, especially on Linux. A particularly common alternative is a “tiling” window manager. In a tiling window manager, windows never overlap, and are instead arranged as tiles on your screen, sort of like panes in tmux. With a tiling window manager, the screen is always filled by whatever windows are open, arranged according to some _layout_. If you have just one window, it takes up the full screen. If you then open another, the original window shrinks to make room for it (often something like 2/3 and 1/3). If you open a third, the other windows will again shrink to accommodate the new window. Just like with tmux panes, you can navigate around these tiled windows with your keyboard, and you can resize them and move them around, all without touching the mouse. They are worth looking into!  
你们大多数人都习惯使用“拖放式”窗口管理器，例如 Windows、macOS 和 Ubuntu 默认包含的内容。Windows 系统包含以下内容。 它们就那样悬挂在屏幕上，你可以拖动它们。 调整它们的大小，并使它们相互重叠。但这只是其中之一。 _这种_窗口管理器通常被称为“浮动”窗口管理器。当然，还有很多其他类型的窗口管理器，尤其是在 Linux 系统上。一种特别常见的替代方案是“平铺”窗口管理器。在平铺窗口管理器中，窗口永远不会重叠，而是像 tmux 中的窗格一样排列在屏幕上。使用平铺窗口管理器，屏幕始终会被打开的窗口填满，并按照一定的_布局_排列。如果只有一个窗口，它会占据整个屏幕。如果打开另一个窗口，原来的窗口会缩小以腾出空间（通常是 2/3 和 1/3）。如果打开第三个窗口，其他窗口会再次缩小以容纳新窗口。就像 tmux 窗格一样，您可以使用键盘在这些平铺窗口之间导航，并且可以调整它们的大小和位置，所有操作都不需要使用鼠标。它们值得您了解一下！

## VPNs  VPN

VPNs are all the rage these days, but it’s not clear that’s for [any good reason](https://web.archive.org/web/20230710155258/https://gist.github.com/joepie91/5a9909939e6ce7d09e29). You should be aware of what a VPN does and does not get you. A VPN, in the best case, is _really_ just a way for you to change your internet service provider as far as the internet is concerned. All your traffic will look like it’s coming from the VPN provider instead of your “real” location, and the network you are connected to will only see encrypted traffic.  
VPN 如今风靡一时，但这是否真的有[其合理之处](https://web.archive.org/web/20230710155258/https://gist.github.com/joepie91/5a9909939e6ce7d09e29)尚不明确。你应该了解 VPN 的功能和局限性。在理想情况下，VPN _实际上_只是让你在互联网上更改你的网络服务提供商。你的所有流量看起来都像是来自 VPN 提供商，而不是你的“真实”位置，而你连接的网络只会看到加密流量。

While that may seem attractive, keep in mind that when you use a VPN, all you are really doing is shifting your trust from you current ISP to the VPN hosting company. Whatever your ISP _could_ see, the VPN provider now sees _instead_. If you trust them _more_ than your ISP, that is a win, but otherwise, it is not clear that you have gained much. If you are sitting on some dodgy unencrypted public Wi-Fi at an airport, then maybe you don’t trust the connection much, but at home, the trade-off is not quite as clear.  
虽然这看起来很有吸引力，但请记住，使用 VPN 的本质是将信任从当前的 ISP 转移到 VPN 服务商。你的 ISP _能_看到的一切，现在都由 VPN 提供商看到_了_ 。如果你_更_信任 VPN 而不是你的 ISP，那当然是好事，但除此之外，你似乎并没有获得多少好处。如果你在机场使用不太可靠的未加密公共 Wi-Fi，那么你可能不太信任这种连接，但在家里，这种权衡就没那么明显了。

You should also know that these days, much of your traffic, at least of a sensitive nature, is _already_ encrypted through HTTPS or TLS more generally. In that case, it usually matters little whether you are on a “bad” network or not – the network operator will only learn what servers you talk to, but not anything about the data that is exchanged.  
您还应该知道，如今，您的大部分网络流量，至少是敏感流量，都_已经_通过 HTTPS 或更常见的 TLS 加密。在这种情况下，您是否身处“不良”网络通常无关紧要——网络运营商只会知道您与哪些服务器通信，而不会知道交换的数据内容。

Notice that I said “in the best case” above. It is not unheard of for VPN providers to accidentally misconfigure their software such that the encryption is either weak or entirely disabled. Some VPN providers are malicious (or at the very least opportunist), and will log all your traffic, and possibly sell information about it to third parties. Choosing a bad VPN provider is often worse than not using one in the first place.  
请注意，我上面用的是“在最好的情况下”。VPN 提供商意外配置软件导致加密功能减弱甚至完全失效的情况并不少见。有些 VPN 提供商心怀恶意（或者至少是投机取巧），会记录你的所有网络流量，甚至可能将这些信息出售给第三方。选择一家糟糕的 VPN 提供商往往比一开始就不使用 VPN 更糟糕。

In a pinch, MIT [runs a VPN](https://ist.mit.edu/vpn) for its students, so that may be worth taking a look at. Also, if you’re going to roll your own, give [WireGuard](https://www.wireguard.com/) a look.  
如果实在不行，麻省理工学院[会为学生提供 VPN](https://ist.mit.edu/vpn) ，或许值得考虑一下。另外，如果你打算自己搭建 VPN，可以看看 [WireGuard](https://www.wireguard.com/) 。

## Markdown

There is a high chance that you will write some text over the course of your career. And often, you will want to mark up that text in simple ways. You want some text to be bold or italic, or you want to add headers, links, and code fragments. Instead of pulling out a heavy tool like Word or LaTeX, you may want to consider using the lightweight markup language [Markdown](https://commonmark.org/help/).  
在你的职业生涯中，你很可能会撰写一些文本。而且通常情况下，你会希望以简单的方式标记这些文本。你可能希望某些文本加粗或倾斜，或者添加标题、链接和代码片段。与其使用像 Word 或 LaTeX 这样功能强大的工具，不如考虑使用轻量级的标记语言 [Markdown](https://commonmark.org/help/) 。

You have probably seen Markdown already, or at least some variant of it. Subsets of it are used and supported almost everywhere, even if it’s not under the name Markdown. At its core, Markdown is an attempt to codify the way that people already often mark up text when they are writing plain text documents. Emphasis (_italics_) is added by surrounding a word with `*`. Strong emphasis (**bold**) is added using `**`. Lines starting with `#` are headings (and the number of `#`s is the subheading level). Any line starting with `-` is a bullet list item, and any line starting with a number + `.` is a numbered list item. Backtick is used to show words in `code font`, and a code block can be entered by indenting a line with four spaces or surrounding it with triple-backticks:  
你可能已经见过 Markdown，或者至少见过它的某种变体。它的子集几乎被广泛使用和支持，即使它们不叫 Markdown。Markdown 的核心在于尝试将人们在编写纯文本文档时常用的标记方式规范化。强调（ _斜体_ ）通过用 `*` 括起来的单词添加。强烈的强调（ **粗体** ）通过 `**` 添加。以 `#` 开头的行是标题（ `#` 的数量代表子标题级别）。任何以 `-` 开头的行都是项目符号列表项，任何以数字 + `.` 开头的行都是编号列表项。反引号用于以 `code font` 显示单词，可以通过缩进四个空格或用三个反引号括起来来输入代码块：

````
```
code goes here
```
````

To add a link, place the _text_ for the link in square brackets, and the URL immediately following that in parentheses: `[name](url)`. Markdown is easy to get started with, and you can use it nearly everywhere. In fact, the lecture notes for this lecture, and all the others, are written in Markdown, and you can see the raw Markdown [here](https://raw.githubusercontent.com/missing-semester/missing-semester/master/_2020/potpourri.md).  
要添加链接，请将链接_文本_放在方括号中，并将 URL 紧随其后放在圆括号中： `[name](url)` 。 Markdown 入门简单，几乎可以随时随地使用。 到处都是。事实上，这次讲座的讲义，以及所有…… 其他一些是用 Markdown 编写的，你可以看到原始的 Markdown 代码。 [这里](https://raw.githubusercontent.com/missing-semester/missing-semester/master/_2020/potpourri.md) 。

## Hammerspoon (desktop automation on macOS)  
Hammerspoon（macOS 上的桌面自动化工具）

[Hammerspoon](https://www.hammerspoon.org/) is a desktop automation framework for macOS. It lets you write Lua scripts that hook into operating system functionality, allowing you to interact with the keyboard/mouse, windows, displays, filesystem, and much more.  
[Hammerspoon](https://www.hammerspoon.org/) 是一个适用于 macOS 的桌面自动化框架。它允许你编写 Lua 脚本来接入操作系统功能，从而让你能够与键盘/鼠标、窗口、显示器、文件系统等等进行交互。

Some examples of things you can do with Hammerspoon:  
以下是一些使用 Hammerspoon 可以做的事情的例子：

-   Bind hotkeys to move windows to specific locations  
    绑定快捷键将窗口移动到特定位置
-   Create a menu bar button that automatically lays out windows in a specific layout  
    创建一个菜单栏按钮，使其能够自动按照特定布局排列窗口。
-   Mute your speaker when you arrive in lab (by detecting the Wi-Fi network)  
    到达实验室后（通过检测 Wi-Fi 网络），请将扬声器静音。
-   Show you a warning if you’ve accidentally taken your friend’s power supply  
    如果你不小心拿了朋友的电源，它会给你发出警告。

At a high level, Hammerspoon lets you run arbitrary Lua code, bound to menu buttons, key presses, or events, and Hammerspoon provides an extensive library for interacting with the system, so there’s basically no limit to what you can do with it. Many people have made their Hammerspoon configurations public, so you can generally find what you need by searching the internet, but you can always write your own code from scratch.  
从宏观层面来说，Hammerspoon 允许你运行任意 Lua 代码，并将其绑定到菜单按钮、按键或事件上。Hammerspoon 还提供了一个功能强大的系统交互库，因此你几乎可以实现任何功能。许多用户已经公开了他们的 Hammerspoon 配置，所以你通常可以通过网络搜索找到所需的内容，当然你也可以从头开始编写自己的代码。

### Resources  资源

-   [Getting Started with Hammerspoon  
    Hammerspoon 入门指南](https://www.hammerspoon.org/go/)
-   [Sample configurations  示例配置](https://github.com/Hammerspoon/hammerspoon/wiki/Sample-Configurations)
-   [Anish’s Hammerspoon config  
    Anish 的 Hammerspoon 配置](https://github.com/anishathalye/dotfiles-local/tree/mac/hammerspoon)

## Booting + Live USBs  
启动盘 + Live USB

When your machine boots up, before the operating system is loaded, the [BIOS](https://en.wikipedia.org/wiki/BIOS)/[UEFI](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) initializes the system. During this process, you can press a specific key combination to configure this layer of software. For example, your computer may say something like “Press F9 to configure BIOS. Press F12 to enter boot menu.” during the boot process. You can configure all sorts of hardware-related settings in the BIOS menu. You can also enter the boot menu to boot from an alternate device instead of your hard drive.  
当你的机器启动时，在操作系统加载之前， [BIOS](https://en.wikipedia.org/wiki/BIOS) / [UEFI](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) 初始化系统。在此过程中，您可以按下特定按键。 组合方式用于配置此软件层。例如，您的计算机可能 例如：“按 F9 配置 BIOS。按 F12 进入启动菜单。” 在启动过程中，您可以配置各种硬件相关的设置。 在 BIOS 菜单中进行设置。您也可以进入启动菜单从某个位置启动。 使用备用设备代替硬盘。

[Live USBs](https://en.wikipedia.org/wiki/Live_USB) are USB flash drives containing an operating system. You can create one of these by downloading an operating system (e.g. a Linux distribution) and burning it to the flash drive. This process is a little bit more complicated than simply copying a `.iso` file to the disk. There are tools like [UNetbootin](https://unetbootin.github.io/) to help you create live USBs.  
[Live USB](https://en.wikipedia.org/wiki/Live_USB) 是一种包含操作系统的 U 盘。您可以下载操作系统（例如 Linux 发行版）并将其刻录到 U 盘来创建 Live USB。这个过程比简单地将 `.iso` 文件复制到磁盘要复杂一些。有一些工具可以帮助您完成这项工作，例如 [UNetbootin](https://unetbootin.github.io/) 。 帮助您创建 Live USB。

Live USBs are useful for all sorts of purposes. Among other things, if you break your existing operating system installation so that it no longer boots, you can use a live USB to recover data or fix the operating system.  
Live USB 用途广泛。例如，如果您损坏了现有的操作系​​统，导致无法启动，您可以使用 Live USB 来恢复数据或修复操作系统。

## Docker, Vagrant, VMs, Cloud, OpenStack  
Docker、Vagrant、虚拟机、云、OpenStack

[Virtual machines](https://en.wikipedia.org/wiki/Virtual_machine) and similar tools like containers let you emulate a whole computer system, including the operating system. This can be useful for creating an isolated environment for testing, development, or exploration (e.g. running potentially malicious code).  
[虚拟机](https://en.wikipedia.org/wiki/Virtual_machine)和容器等类似工具可以模拟整个计算机系统，包括操作系统。这对于创建隔离环境进行测试、开发或探索（例如运行潜在的恶意代码）非常有用。

[Vagrant](https://www.vagrantup.com/) is a tool that lets you describe machine configurations (operating system, services, packages, etc.) in code, and then instantiate VMs with a simple `vagrant up`. [Docker](https://www.docker.com/) is conceptually similar but it uses containers instead.  
[Vagrant](https://www.vagrantup.com/) 是一个工具，它允许你用代码描述机器配置（操作系统、服务、软件包等），然后通过简单的 `vagrant up` [命令](https://www.docker.com/)实例化虚拟机。 概念上类似，但它使用的是容器。

You can also rent virtual machines on the cloud, and it’s a nice way to get instant access to:  
您还可以租用云端虚拟机，这是一种快速访问以下资源的绝佳方式：

-   A cheap always-on machine that has a public IP address, used to host services  
    一台价格低廉、始终在线且拥有公共 IP 地址的机器，用于托管服务
-   A machine with a lot of CPU, disk, RAM, and/or GPU  
    一台拥有大量 CPU、硬盘、内存和/或 GPU 的机器
-   Many more machines than you physically have access to (billing is often by the second, so if you want a lot of computing for a short amount of time, it’s feasible to rent 1000 computers for a couple of minutes)  
    机器数量远超您实际可访问的数量（通常按秒计费，因此如果您需要在短时间内进行大量计算，租用 1000 台计算机几分钟也是可行的）。

Popular services include [Amazon AWS](https://aws.amazon.com/), [Google Cloud](https://cloud.google.com/), [Microsoft Azure](https://azure.microsoft.com/), [DigitalOcean](https://www.digitalocean.com/).  
热门服务包括[亚马逊 AWS](https://aws.amazon.com/) 、 [谷歌云](https://cloud.google.com/) 、 [微软 Azure](https://azure.microsoft.com/) 。 [DigitalOcean](https://www.digitalocean.com/) 。

If you’re a member of MIT CSAIL, you can get free VMs for research purposes through the [CSAIL OpenStack instance](https://tig.csail.mit.edu/shared-computing/open-stack/).  
如果您是 MIT CSAIL 的成员，您可以通过 [CSAIL OpenStack 实例](https://tig.csail.mit.edu/shared-computing/open-stack/)获得用于研究目的的免费虚拟机。

## Notebook programming  笔记本编程

[Notebook programming environments](https://en.wikipedia.org/wiki/Notebook_interface) can be really handy for doing certain types of interactive or exploratory development. Perhaps the most popular notebook programming environment today is [Jupyter](https://jupyter.org/), for Python (and several other languages). [Wolfram Mathematica](https://www.wolfram.com/mathematica/) is another notebook programming environment that’s great for doing math-oriented programming.  
[笔记本编程环境](https://en.wikipedia.org/wiki/Notebook_interface)可能非常 适用于进行某些类型的交互式或探索性开发。 目前最流行的笔记本编程环境可能是 [Jupyter](https://jupyter.org/) ，适用于 Python（以及其他几种语言）。 [Wolfram Mathematica](https://www.wolfram.com/mathematica/) 是另一个笔记本编程环境，非常适合进行面向数学的编程。

## GitHub

[GitHub](https://github.com/) is one of the most popular platforms for open-source software development. Many of the tools we’ve talked about in this class, from [vim](https://github.com/vim/vim) to [Hammerspoon](https://github.com/Hammerspoon/hammerspoon), are hosted on GitHub. It’s easy to get started contributing to open-source to help improve the tools that you use every day.  
[GitHub](https://github.com/) 是最受欢迎的开源软件开发平台之一。我们在课堂上讨论过的许多工具，从 [vim](https://github.com/vim/vim) 到 [Hammerspoon](https://github.com/Hammerspoon/hammerspoon) 项目托管在 GitHub 上。参与开源项目非常简单，可以帮助改进你每天使用的工具。

There are two primary ways in which people contribute to projects on GitHub:  
人们可以通过两种主要方式为 GitHub 项目做出贡献：

-   Creating an [issue](https://help.github.com/en/github/managing-your-work-on-github/creating-an-issue). This can be used to report bugs or request a new feature. Neither of these involves reading or writing code, so it can be pretty lightweight to do. High-quality bug reports can be extremely valuable to developers. Commenting on existing discussions can be helpful too.  
    创建一个 [问题反馈](https://help.github.com/en/github/managing-your-work-on-github/creating-an-issue) 。您可以使用此功能报告错误或请求新功能。这两项操作都不涉及代码的读写，因此非常便捷。高质量的错误报告对开发者来说极其宝贵。参与现有讨论并发表评论也很有帮助。
-   Contribute code through a [pull request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests). This is generally more involved than creating an issue. You can [fork](https://help.github.com/en/github/getting-started-with-github/fork-a-repo) a repository on GitHub, clone your fork, create a new branch, make some changes (e.g. fix a bug or implement a feature), push the branch, and then [create a pull request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request). After this, there will generally be some back-and-forth with the project maintainers, who will give you feedback on your patch. Finally, if all goes well, your patch will be merged into the upstream repository. Often times, larger projects will have a contributing guide, tag beginner-friendly issues, and some even have mentorship programs to help first-time contributors become familiar with the project.  
    通过提交 [pull request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests) 来贡献代码。 这通常比创建问题更复杂。您可以 [叉](https://help.github.com/en/github/getting-started-with-github/fork-a-repo) 在 GitHub 上创建一个仓库，克隆你的 fork，创建一个新分支，进行一些更改 （例如修复 bug 或实现新功能），推送分支，然后[创建拉取请求](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request) 。之后，通常会与项目维护者进行一些沟通，他们会就你的补丁提供反馈。最后，如果一切顺利，你的补丁将被合并到上游仓库。通常，大型项目会提供贡献指南，为新手友好的 issue 添加标签，有些项目甚至提供导师计划，帮助新贡献者熟悉项目。

* * *