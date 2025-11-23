---
date: 2025-11-15
---
# Q&A  问答

For the last lecture, we answered questions that the students submitted:  
最后一节课，我们回答了学生们提交的问题：

-   [Any recommendations on learning Operating Systems related topics like processes, virtual memory, interrupts, memory management, etc  
    有什么学习操作系统相关主题（例如进程、虚拟内存、中断、内存管理等）的建议吗？](https://missing.csail.mit.edu/2020/qa/#any-recommendations-on-learning-operating-systems-related-topics-like-processes-virtual-memory-interrupts-memory-management-etc)
-   [What are some of the tools you’d prioritize learning first?  
    你认为应该优先学习哪些工具？](https://missing.csail.mit.edu/2020/qa/#what-are-some-of-the-tools-youd-prioritize-learning-first)
-   [When do I use Python versus a Bash scripts versus some other language?  
    什么时候应该使用 Python，什么时候应该使用 Bash 脚本，什么时候应该使用其他语言？](https://missing.csail.mit.edu/2020/qa/#when-do-i-use-python-versus-a-bash-scripts-versus-some-other-language)
-   [What is the difference between `source script.sh` and `./script.sh`  
    `source script.sh` 和 `./script.sh` 有什么区别？](https://missing.csail.mit.edu/2020/qa/#what-is-the-difference-between-source-scriptsh-and-scriptsh)
-   [What are the places where various packages and tools are stored and how does referencing them work? What even is `/bin` or `/lib`?  
    各种软件包和工具都存储在哪些位置？如何引用它们？ `/bin` 和 `/lib` 又是什么？](https://missing.csail.mit.edu/2020/qa/#what-are-the-places-where-various-packages-and-tools-are-stored-and-how-does-referencing-them-work-what-even-is-bin-or-lib)
-   [Should I `apt-get install` a python-whatever, or `pip install` whatever package?  
    我应该 `apt-get install` python-whatever，还是 `pip install` 某个软件包？](https://missing.csail.mit.edu/2020/qa/#should-i-apt-get-install-a-python-whatever-or-pip-install-whatever-package)
-   [What’s the easiest and best profiling tools to use to improve performance of my code?  
    哪些性能分析工具最简单易用，最适合用来提升代码性能？](https://missing.csail.mit.edu/2020/qa/#whats-the-easiest-and-best-profiling-tools-to-use-to-improve-performance-of-my-code)
-   [What browser plugins do you use?  
    你使用哪些浏览器插件？](https://missing.csail.mit.edu/2020/qa/#what-browser-plugins-do-you-use)
-   [What are other useful data wrangling tools?  
    还有哪些其他好用的数据整理工具？](https://missing.csail.mit.edu/2020/qa/#what-are-other-useful-data-wrangling-tools)
-   [What is the difference between Docker and a Virtual Machine?  
    Docker 和虚拟机有什么区别？](https://missing.csail.mit.edu/2020/qa/#what-is-the-difference-between-docker-and-a-virtual-machine)
-   [What are the advantages and disadvantages of each OS and how can we choose between them (e.g. choosing the best Linux distribution for our purposes)?  
    每种操作系统有哪些优点和缺点？我们如何在它们之间做出选择（例如，如何选择最适合我们需求的 Linux 发行版）？](https://missing.csail.mit.edu/2020/qa/#what-are-the-advantages-and-disadvantages-of-each-os-and-how-can-we-choose-between-them-eg-choosing-the-best-linux-distribution-for-our-purposes)
-   [Vim vs Emacs?  Vim 和 Emacs 哪个更好？](https://missing.csail.mit.edu/2020/qa/#vim-vs-emacs)
-   [Any tips or tricks for Machine Learning applications?  
    关于机器学习应用，有什么技巧或窍门吗？](https://missing.csail.mit.edu/2020/qa/#any-tips-or-tricks-for-machine-learning-applications)
-   [Any more Vim tips?  
    还有其他 Vim 使用技巧吗？](https://missing.csail.mit.edu/2020/qa/#any-more-vim-tips)
-   [What is 2FA and why should I use it?  
    什么是双因素认证（2FA）？我为什么要使用它？](https://missing.csail.mit.edu/2020/qa/#what-is-2fa-and-why-should-i-use-it)
-   [Any comments on differences between web browsers?  
    关于不同浏览器之间的区别，大家有什么看法？](https://missing.csail.mit.edu/2020/qa/#any-comments-on-differences-between-web-browsers)

## Any recommendations on learning Operating Systems related topics like processes, virtual memory, interrupts, memory management, etc  
有什么学习操作系统相关主题（例如进程、虚拟内存、中断、内存管理等）的建议吗？

First, it is unclear whether you actually need to be very familiar with all of these topics since they are very low level topics. They will matter as you start writing more low level code like implementing or modifying a kernel. Otherwise, most topics will not be relevant, with the exception of processes and signals that were briefly covered in other lectures.  
首先，由于这些都是非常底层的概念，所以你是否真的需要非常熟悉所有这些主题还不明确。当你开始编写更底层的代码，例如实现或修改内核时，它们才会变得重要。否则，除了在其他课程中简要介绍过的进程和信号之外，大多数主题都无关紧要。

Some good resources to learn about this topic:  
以下是一些学习该主题的优质资源：

-   [MIT’s 6.828 class](https://pdos.csail.mit.edu/6.828/) - Graduate level class on Operating System Engineering. Class materials are publicly available.  
    [麻省理工学院的 6.828 课程](https://pdos.csail.mit.edu/6.828/) ——操作系统工程研究生课程。课程资料公开可用。
-   Modern Operating Systems (4th ed) - by Andrew S. Tanenbaum is a good overview of many of the mentioned concepts.  
    《现代操作系统（第 4 版）》——作者 Andrew S. Tanenbaum——对文中提到的许多概念进行了很好的概述。
-   The Design and Implementation of the FreeBSD Operating System - A good resource about the FreeBSD OS (note that this is not Linux).  
    FreeBSD 操作系统的设计与实现 - 关于 FreeBSD 操作系统的优秀资源（请注意，这不是 Linux）。
-   Other guides like [Writing an OS in Rust](https://os.phil-opp.com/) where people implement a kernel step by step in various languages, mostly for teaching purposes.  
    其他指南，例如 [《用 Rust 编写操作系统》](https://os.phil-opp.com/) ，其中人们用各种语言逐步实现内核，主要是为了教学目的。

## What are some of the tools you’d prioritize learning first?  
你认为应该优先学习哪些工具？

Some topics worth prioritizing:  
一些值得优先考虑的议题：

-   Learning how to use your keyboard more and your mouse less. This can be through keyboard shortcuts, changing interfaces, &c.  
    学习如何更多地使用键盘，减少鼠标的使用。这可以通过键盘快捷键、切换界面等方式实现。
-   Learning your editor well. As a programmer most of your time is spent editing files so it really pays off to learn this skill well.  
    熟练掌握编辑器至关重要。作为程序员，你大部分时间都花在编辑文件上，所以精通这项技能绝对物有所值。
-   Learning how to automate and/or simplify repetitive tasks in your workflow because the time savings will be enormous…  
    学习如何自动化和/或简化工作流程中的重复性任务，因为这将节省大量时间……
-   Learning about version control tools like Git and how to use it in conjunction with GitHub to collaborate in modern software projects.  
    学习 Git 等版本控制工具，以及如何将其与 GitHub 结合使用，以便在现代软件项目中进行协作。

## When do I use Python versus a Bash scripts versus some other language?  
什么时候应该使用 Python，什么时候应该使用 Bash 脚本，什么时候应该使用其他语言？

In general, bash scripts are useful for short and simple one-off scripts when you just want to run a specific series of commands. bash has a set of oddities that make it hard to work with for larger programs or scripts:  
一般来说，bash 脚本适用于简短简单的单次脚本，例如只想运行一系列特定命令的情况。但 bash 的一些特性使其难以用于编写大型程序或脚本：

-   bash is easy to get right for a simple use case but it can be really hard to get right for all possible inputs. For example, spaces in script arguments have led to countless bugs in bash scripts.  
    对于简单的用例，bash 很容易编写，但要处理所有可能的输入却非常困难。例如，脚本参数中的空格会导致 bash 脚本出现无数错误。
-   bash is not amenable to code reuse so it can be hard to reuse components of previous programs you have written. More generally, there is no concept of software libraries in bash.  
    bash 不支持代码重用，因此很难重用之前编写的程序中的组件。更普遍地说，bash 中没有软件库的概念。
-   bash relies on many magic strings like `$?` or `$@` to refer to specific values, whereas other languages refer to them explicitly, like `exitCode` or `sys.args` respectively.  
    bash 依赖许多魔法字符串，如 `$?` 或 `$@` 来引用特定值，而其他语言则显式地引用它们，例如 `exitCode` 或 `sys.args` 。

Therefore, for larger and/or more complex scripts we recommend using more mature scripting languages like Python or Ruby. You can find online countless libraries that people have already written to solve common problems in these languages. If you find a library that implements the specific functionality you care about in some language, usually the best thing to do is to just use that language.  
因此，对于规模更大或更复杂的脚本，我们建议使用更成熟的脚本语言，例如 Python 或 Ruby。您可以在网上找到无数用这些语言编写的库，它们可以解决常见问题。如果您找到一个用某种语言实现您所需功能的库，通常最好的做法就是直接使用该语言。

## What is the difference between `source script.sh` and `./script.sh`  
`source script.sh` 和 `./script.sh` 有什么区别？

In both cases the `script.sh` will be read and executed in a bash session, the difference lies in which session is running the commands. For `source` the commands are executed in your current bash session and thus any changes made to the current environment, like changing directories or defining functions will persist in the current session once the `source` command finishes executing. When running the script standalone like `./script.sh`, your current bash session starts a new instance of bash that will run the commands in `script.sh`. Thus, if `script.sh` changes directories, the new bash instance will change directories but once it exits and returns control to the parent bash session, the parent session will remain in the same place. Similarly, if `script.sh` defines a function that you want to access in your terminal, you need to `source` it for it to be defined in your current bash session. Otherwise, if you run it, the new bash process will be the one to process the function definition instead of your current shell.  
无论哪种情况， `script.sh` 都会在 bash 会话中被读取和执行，区别在于哪个会话在运行命令。对于 `source` 命令，命令会在当前 bash 会话中执行，因此对当前环境所做的任何更改（例如更改目录或定义函数）都会在 `source` 命令执行完毕后保留在当前会话中。而当单独运行脚本（例如 `./script.sh` 时，当前 bash 会话会启动一个新的 bash 实例来运行 `script.sh` 中的命令。因此，如果 `script.sh` 更改了目录，新的 bash 实例也会更改目录，但一旦它退出并将控制权返回给父 bash 会话，父会话将保持在原来的位置。同样，如果 `script.sh` 定义了一个你想在终端中访问的函数，你需要使用 `source` 命令才能使其在当前 bash 会话中生效。否则，如果你直接运行它，新的 bash 进程将处理该函数定义，而不是你当前的 shell。

## What are the places where various packages and tools are stored and how does referencing them work? What even is `/bin` or `/lib`?  
各种软件包和工具都存储在哪些位置？如何引用它们？ `/bin` 和 `/lib` 又是什么？

Regarding programs that you execute in your terminal, they are all found in the directories listed in your `PATH` environment variable and you can use the `which` command (or the `type` command) to check where your shell is finding a specific program. In general, there are some conventions about where specific types of files live. Here are some of the ones we talked about, check the [Filesystem, Hierarchy Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard) for a more comprehensive list.  
对于你在终端中执行的程序，它们都位于 `PATH` 环境变量列出的目录中。你可以使用 `which` 命令（或 `type` 命令）来查看 shell 中特定程序的存放位置。一般来说，特定类型文件的存放位置有一些约定俗成的规则。以下是我们讨论过的一些规则，更全面的列表请参阅[文件系统层次结构标准](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard) 。

-   `/bin` - Essential command binaries  
    `/bin` - 基本命令二进制文件
-   `/sbin` - Essential system binaries, usually to be run by root  
    `/sbin` - 必要的系统二进制文件，通常由 root 用户运行。
-   `/dev` - Device files, special files that often are interfaces to hardware devices  
    `/dev` - 设备文件，通常是与硬件设备交互的特殊文件。
-   `/etc` - Host-specific system-wide configuration files  
    `/etc` - 主机特定的系统级配置文件
-   `/home` - Home directories for users in the system  
    `/home` - 系统中用户的家目录
-   `/lib` - Common libraries for system programs  
    `/lib` - 系统程序的通用库
-   `/opt` - Optional application software  
    `/opt` - 可选应用程序软件
-   `/sys` - Contains information and configuration for the system (covered in the [first lecture](https://missing.csail.mit.edu/2020/course-shell/))  
    `/sys` - 包含系统信息和配置（ [第一讲](https://missing.csail.mit.edu/2020/course-shell/)已涵盖）
-   `/tmp` - Temporary files (also `/var/tmp`). Usually deleted between reboots.  
    `/tmp` - 临时文件（也包括 `/var/tmp` ）。通常在重启后删除。
-   `/usr/` - Read only user data  
    `/usr/` - 只读用户数据
    -   `/usr/bin` - Non-essential command binaries  
        `/usr/bin` - 非必要命令二进制文件
    -   `/usr/sbin` - Non-essential system binaries, usually to be run by root  
        `/usr/sbin` - 非必要的系统二进制文件，通常由 root 用户运行。
    -   `/usr/local/bin` - Binaries for user compiled programs  
        `/usr/local/bin` - 用户编译程序的二进制文件
-   `/var` - Variable files like logs or caches  
    `/var` - 可变文件，例如日志或缓存。

## Should I `apt-get install` a python-whatever, or `pip install` whatever package?  
我应该 `apt-get install` python-whatever，还是 `pip install` 某个软件包？

There’s no universal answer to this question. It’s related to the more general question of whether you should use your system’s package manager or a language-specific package manager to install software. A few things to take into account:  
这个问题没有统一的答案。它与一个更普遍的问题相关：安装软件时，应该使用系统自带的软件包管理器还是特定语言的软件包管理器？以下几点需要考虑：

-   Common packages will be available through both, but less popular ones or more recent ones might not be available in your system package manager. In this case, using the language-specific tool is the better choice.  
    常用软件包可以通过这两种方式获取，但不太常用或较新的软件包可能无法在您的系统软件包管理器中找到。在这种情况下，使用特定语言的工具是更好的选择。
-   Similarly, language-specific package managers usually have more up to date versions of packages than system package managers.  
    同样，特定语言的软件包管理器通常比系统软件包管理器拥有更新的软件包版本。
-   When using your system package manager, libraries will be installed system wide. This means that if you need different versions of a library for development purposes, the system package manager might not suffice. For this scenario, most programming languages provide some sort of isolated or virtual environment so you can install different versions of libraries without running into conflicts. For Python, there’s virtualenv, and for Ruby, there’s RVM.  
    使用系统包管理器时，库文件会被安装到系统全局范围内。这意味着，如果您出于开发目的需要使用不同版本的库文件，系统包管理器可能无法满足需求。针对这种情况，大多数编程语言都提供了某种隔离或虚拟环境，以便您可以安装不同版本的库文件而不会发生冲突。例如，Python 有 virtualenv，Ruby 有 RVM。
-   Depending on the operating system and the hardware architecture, some of these packages might come with binaries or might need to be compiled. For instance, in ARM computers like the Raspberry Pi, using the system package manager can be better than the language specific one if the former comes in form of binaries and the latter needs to be compiled. This is highly dependent on your specific setup.  
    根据操作系统和硬件架构的不同，某些软件包可能以二进制文件的形式提供，也可能需要编译。例如，在像 Raspberry Pi 这样的 ARM 计算机上，如果软件包以二进制文件的形式提供，而语言特定的软件包管理器需要编译，那么使用系统软件包管理器可能比使用语言特定的软件包管理器更好。这很大程度上取决于您的具体配置。

You should try to use one solution or the other and not both since that can lead to conflicts that are hard to debug. Our recommendation is to use the language-specific package manager whenever possible, and to use isolated environments (like Python’s virtualenv) to avoid polluting the global environment.  
你应该尽量选择其中一种解决方案，而不是两种都用，因为这可能会导致难以调试的冲突。我们建议尽可能使用特定语言的包管理器，并使用隔离环境（例如 Python 的 virtualenv）来避免污染全局环境。

## What’s the easiest and best profiling tools to use to improve performance of my code?  
哪些性能分析工具最简单易用，最适合用来提升代码性能？

The easiest tool that is quite useful for profiling purposes is [print timing](https://missing.csail.mit.edu/2020/debugging-profiling/#timing). You just manually compute the time taken between different parts of your code. By repeatedly doing this, you can effectively do a binary search over your code and find the segment of code that took the longest.  
最简单且非常实用的性能分析工具是[打印计时工具](https://missing.csail.mit.edu/2020/debugging-profiling/#timing) 。你只需手动计算代码不同部分之间的执行时间。通过反复执行此操作，你可以有效地对代码进行二分查找，从而找到耗时最长的代码段。

For more advanced tools, Valgrind’s [Callgrind](http://valgrind.org/docs/manual/cl-manual.html) lets you run your program and measure how long everything takes and all the call stacks, namely which function called which other function. It then produces an annotated version of your program’s source code with the time taken per line. However, it slows down your program by an order of magnitude and does not support threads. For other cases, the [`perf`](http://www.brendangregg.com/perf.html) tool and other language specific sampling profilers can output useful data pretty quickly. [Flamegraphs](http://www.brendangregg.com/flamegraphs.html) are a good visualization tool for the output of said sampling profilers. You should also try to use specific tools for the programming language or task you are working with. For example, for web development, the dev tools built into Chrome and Firefox have fantastic profilers.  
对于更高级的工具，Valgrind 的 [Callgrind](http://valgrind.org/docs/manual/cl-manual.html) 可以让你运行程序并测量每个步骤的耗时以及所有调用堆栈，即哪个函数调用了哪个函数。然后，它会生成一个带有注释的程序源代码版本，其中显示了每行代码的耗时。但是，它会使程序运行速度降低一个数量级，并且不支持多线程。对于其他情况， [`perf`](http://www.brendangregg.com/perf.html) 工具和其他特定语言的采样分析器可以快速输出有用的数据。 [火焰图](http://www.brendangregg.com/flamegraphs.html)是可视化这些采样分析器输出结果的好工具。你也应该尝试使用针对你所使用的编程语言或任务的特定工具。例如，对于 Web 开发，Chrome 和 Firefox 内置的开发者工具就拥有非常出色的分析器。

Sometimes the slow part of your code will be because your system is waiting for an event like a disk read or a network packet. In those cases, it is worth checking that back-of-the-envelope calculations about the theoretical speed in terms of hardware capabilities do not deviate from the actual readings. There are also specialized tools to analyze the wait times in system calls. These include tools like [eBPF](http://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html) that perform kernel tracing of user programs. In particular [`bpftrace`](https://github.com/iovisor/bpftrace) is worth checking out if you need to perform this sort of low level profiling.  
有时，代码运行缓慢的部分是因为系统正在等待某个事件，例如磁盘读取或网络数据包。在这种情况下，值得检查一下基于硬件性能的理论速度估算是否与实际读数存在偏差。此外，还有一些专门的工具可以分析系统调用中的等待时间。例如， [eBPF](http://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html) 等工具可以对用户程序进行内核跟踪。如果您需要进行这种底层性能分析， [`bpftrace`](https://github.com/iovisor/bpftrace) 尤其值得一试。

## What browser plugins do you use?  
你使用哪些浏览器插件？

Some of our favorites, mostly related to security and usability:  
我们最喜欢的一些功能，主要与安全性和易用性相关：

-   [uBlock Origin](https://github.com/gorhill/uBlock) - It is a [wide-spectrum](https://github.com/gorhill/uBlock/wiki/Blocking-mode) blocker that doesn’t just stop ads, but all sorts of third-party communication a page may try to do. This also covers inline scripts and other types of resource loading. If you’re willing to spend some time on configuration to make things work, go to [medium mode](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-medium-mode) or even [hard mode](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-hard-mode). Those will make some sites not work until you’ve fiddled with the settings enough, but will also significantly improve your online security. Otherwise, the [easy mode](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-easy-mode) is already a good default that blocks most ads and tracking. You can also define your own rules about what website objects to block.  
    [uBlock Origin](https://github.com/gorhill/uBlock) 是一款[功能全面的](https://github.com/gorhill/uBlock/wiki/Blocking-mode)拦截器，它不仅能拦截广告，还能拦截网页可能尝试进行的各种第三方通信。这包括内联脚本和其他类型的资源加载。如果您愿意花些时间进行配置，可以尝试[中等模式](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-medium-mode)甚至[高级模式](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-hard-mode) 。这些模式会导致某些网站暂时无法访问，直到您进行足够的设置调整，但也能显著提升您的网络安全。否则， [简单模式](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-easy-mode)已经是一个不错的默认设置，可以拦截大多数广告和跟踪信息。您还可以自定义规则，指定要拦截哪些网站对象。
-   [Stylus](https://github.com/openstyles/stylus/) - a fork of Stylish (don’t use Stylish, it was shown to [steal users’ browsing history](https://www.theregister.co.uk/2018/07/05/browsers_pull_stylish_but_invasive_browser_extension/)), allows you to sideload custom CSS stylesheets to websites. With Stylus you can easily customize and modify the appearance of websites. This can be removing a sidebar, changing the background color or even the text size or font choice. This is fantastic for making websites that you visit frequently more readable. Moreover, Stylus can find styles written by other users and published in [userstyles.org](https://userstyles.org/). Most common websites have one or several dark theme stylesheets for instance.  
    [Stylus](https://github.com/openstyles/stylus/) 是 Stylish 的一个分支（请勿使用 Stylish，它已被证实会[窃取用户的浏览历史记录](https://www.theregister.co.uk/2018/07/05/browsers_pull_stylish_but_invasive_browser_extension/) ），它允许您将自定义 CSS 样式表侧载到网站。使用 Stylus，您可以轻松自定义和修改网站的外观，例如移除侧边栏、更改背景颜色，甚至更改文本大小或字体。这对于提高您经常访问的网站的可读性非常有用。此外，Stylus 还可以找到其他用户编写并发布在 [userstyles.org](https://userstyles.org/) 上的样式表。例如，大多数常见网站都有一个或多个深色主题样式表。
-   Full Page Screen Capture - [Built into Firefox](https://screenshots.firefox.com/) and [Chrome extension](https://chrome.google.com/webstore/detail/full-page-screen-capture/fdpohaocaechififmbbbbbknoalclacl?hl=en). Lets you take a screenshot of a full website, often much better than printing for reference purposes.  
    全页面屏幕截图 - [内置于 Firefox](https://screenshots.firefox.com/) 和 [Chrome 扩展程序](https://chrome.google.com/webstore/detail/full-page-screen-capture/fdpohaocaechififmbbbbbknoalclacl?hl=en)中。可让您截取整个网站的屏幕截图，通常比打印出来用于参考要好得多。
-   [Multi Account Containers](https://addons.mozilla.org/en-US/firefox/addon/multi-account-containers/) - lets you separate cookies into “containers”, allowing you to browse the web with different identities and/or ensuring that websites are unable to share information between them.  
    [多账户容器](https://addons.mozilla.org/en-US/firefox/addon/multi-account-containers/) \- 允许您将 cookie 分成“容器”，从而允许您使用不同的身份浏览网页，和/或确保网站之间无法共享信息。
-   Password Manager Integration - Most password managers have browser extensions that make inputting your credentials into websites not only more convenient but also more secure. Compared to simply copy-pasting your user and password, these tools will first check that the website domain matches the one listed for the entry, preventing phishing attacks that impersonate popular websites to steal credentials.  
    密码管理器集成 - 大多数密码管理器都提供浏览器扩展程序，让您在网站上输入凭据不仅更方便，而且更安全。与直接复制粘贴用户名和密码相比，这些工具会先检查网站域名是否与条目中列出的域名匹配，从而防止冒充热门网站窃取凭据的网络钓鱼攻击。
-   [Vimium](https://github.com/philc/vimium) - A browser extension that provides keyboard-based navigation and control of the web in the spirit of the Vim editor.  
    [Vimium](https://github.com/philc/vimium) - 一款浏览器扩展程序，它以 Vim 编辑器的精神提供基于键盘的网页导航和控制。

## What are other useful data wrangling tools?  
还有哪些其他好用的数据整理工具？

Some of the data wrangling tools we did not have time to cover during the data wrangling lecture include `jq` or `pup` which are specialized parsers for JSON and HTML data respectively. The Perl programming language is another good tool for more advanced data wrangling pipelines. Another trick is the `column -t` command that can be used to convert whitespace text (not necessarily aligned) into properly column aligned text.  
在数据整理课程中，我们没有时间介绍一些数据整理工具，例如 `jq` 和 `pup` ，它们分别是专门用于解析 JSON 和 HTML 数据的解析器。Perl 编程语言是另一个用于构建更高级数据整理流程的优秀工具。此外，还有一个技巧是使用 `column -t` 命令，它可以将包含空白字符（不一定已对齐）的文本转换为正确对齐的文本。

More generally a couple of more unconventional data wrangling tools are vim and Python. For some complex and multi-line transformations, vim macros can be a quite invaluable tool to use. You can just record a series of actions and repeat them as many times as you want, for instance in the editors [lecture notes](https://missing.csail.mit.edu/2020/editors/#macros) (and last year’s [video](https://missing.csail.mit.edu/2019/editors/)) there is an example of converting an XML-formatted file into JSON just using vim macros.  
更广泛地说，Vim 和 Python 是两种比较另类的数据处理工具。对于一些复杂的多行转换，Vim 宏非常实用。你可以录制一系列操作并根据需要重复执行，例如，在编辑的[讲义](https://missing.csail.mit.edu/2020/editors/#macros) （以及去年的[视频](https://missing.csail.mit.edu/2019/editors/) ）中，就有一个仅使用 Vim 宏将 XML 格式的文件转换为 JSON 的示例。

For tabular data, often presented in CSVs, the [pandas](https://pandas.pydata.org/) Python library is a great tool. Not only because it makes it quite easy to define complex operations like group by, join or filters; but also makes it quite easy to plot different properties of your data. It also supports exporting to many table formats including XLS, HTML or LaTeX. Alternatively the R programming language (an arguably [bad](http://arrgh.tim-smith.us/) programming language) has lots of functionality for computing statistics over data and can be quite useful as the last step of your pipeline. [ggplot2](https://ggplot2.tidyverse.org/) is a great plotting library in R.  
对于通常以 CSV 格式呈现的表格数据， [pandas](https://pandas.pydata.org/) Python 库是一个非常棒的工具。它不仅可以轻松定义诸如分组、连接或筛选等复杂操作，还能轻松绘制数据的各种属性图。此外，它还支持导出为多种表格格式，包括 XLS、HTML 和 LaTeX。另一方面，R 编程语言（尽管有人认为它是一种[不太好的](http://arrgh.tim-smith.us/)编程语言）也拥有丰富的统计计算功能，可以作为数据处理流程的最后[一步](https://ggplot2.tidyverse.org/) 。ggplot2 是 R 语言中一个优秀的绘图库。

## What is the difference between Docker and a Virtual Machine?  
Docker 和虚拟机有什么区别？

Docker is based on a more general concept called containers. The main difference between containers and virtual machines is that virtual machines will execute an entire OS stack, including the kernel, even if the kernel is the same as the host machine. Unlike VMs, containers avoid running another instance of the kernel and instead share the kernel with the host. In Linux, this is achieved through a mechanism called LXC, and it makes use of a series of isolation mechanisms to spin up a program that thinks it’s running on its own hardware but it’s actually sharing the hardware and kernel with the host. Thus, containers have a lower overhead than a full VM. On the flip side, containers have a weaker isolation and only work if the host runs the same kernel. For instance if you run Docker on macOS, Docker needs to spin up a Linux virtual machine to get an initial Linux kernel and thus the overhead is still significant. Lastly, Docker is a specific implementation of containers and it is tailored for software deployment. Because of this, it has some quirks: for example, Docker containers will not persist any form of storage between reboots by default.  
Docker 基于一种名为容器的通用概念。容器与虚拟机的主要区别在于，虚拟机需要运行完整的操作系统栈，包括内核，即使内核与宿主机相同。与虚拟机不同，容器避免运行另一个内核实例，而是与宿主机共享内核。在 Linux 系统中，这是通过一种名为 LXC 的机制实现的，它利用一系列隔离机制来启动一个程序，该程序会认为自己运行在独立的硬件上，但实际上它与宿主机共享硬件和内核。因此，容器的开销比完整的虚拟机要低。另一方面，容器的隔离性较弱，并且只有在宿主机运行相同内核的情况下才能正常工作。例如，如果在 macOS 上运行 Docker，Docker 需要启动一个 Linux 虚拟机来获取初始 Linux 内核，因此开销仍然相当可观。最后，Docker 是容器的一种特定实现，专为软件部署而设计。正因如此，它有一些特性：例如，默认情况下，Docker 容器在重启后不会保留任何形式的存储。

## What are the advantages and disadvantages of each OS and how can we choose between them (e.g. choosing the best Linux distribution for our purposes)?  
每种操作系统有哪些优点和缺点？我们如何在它们之间做出选择（例如，如何选择最适合我们需求的 Linux 发行版）？

Regarding Linux distros, even though there are many, many distros, most of them will behave fairly identically for most use cases. Most of Linux and UNIX features and inner workings can be learned in any distro. A fundamental difference between distros is how they deal with package updates. Some distros, like Arch Linux, use a rolling update policy where things are bleeding-edge but things might break every so often. On the other hand, some distros like Debian, CentOS or Ubuntu LTS releases are much more conservative with releasing updates in their repositories so things are usually more stable at the expense of sacrificing newer features. Our recommendation for an easy and stable experience with both desktops and servers is to use Debian or Ubuntu.  
关于 Linux 发行版，尽管种类繁多，但大多数发行版在大多数情况下表现都大同小异。大多数 Linux 和 UNIX 的特性和内部机制都可以在任何发行版中学习。发行版之间的一个根本区别在于它们处理软件包更新的方式。一些发行版，例如 Arch Linux，采用滚动更新策略，虽然会使用最新的功能，但偶尔也会出现一些问题。另一方面，一些发行版，例如 Debian、CentOS 或 Ubuntu LTS 版本，在软件仓库中发布更新时则更为保守，因此通常更加稳定，但代价是牺牲了一些新功能。我们建议，为了获得简单稳定的桌面和服务器体验，可以使用 Debian 或 Ubuntu。

Mac OS is a good middle point between Windows and Linux that has a nicely polished interface. However, Mac OS is based on BSD rather than Linux, so some parts of the system and commands are different. An alternative worth checking is FreeBSD. Even though some programs will not run on FreeBSD, the BSD ecosystem is much less fragmented and better documented than Linux. We discourage Windows for anything but for developing Windows applications or if there is some deal breaker feature that you need, like good driver support for gaming.  
macOS 是 Windows 和 Linux 之间不错的折中方案，拥有简洁流畅的界面。然而，macOS 基于 BSD 而非 Linux，因此系统和命令的某些部分有所不同。FreeBSD 是一个值得考虑的替代方案。尽管某些程序无法在 FreeBSD 上运行，但 BSD 生态系统比 Linux 更加完善，文档也更加齐全。我们不建议使用 Windows，除非是用于开发 Windows 应用程序，或者您需要一些关键功能，例如良好的游戏驱动程序支持。

For dual boot systems, we think that the most working implementation is macOS’ bootcamp and that any other combination can be problematic on the long run, specially if you combine it with other features like disk encryption.  
对于双启动系统，我们认为最可行的实现方式是 macOS 的 Bootcamp，任何其他组合从长远来看都可能存在问题，特别是如果将其与其他功能（例如磁盘加密）结合使用时。

## Vim vs Emacs?  Vim 和 Emacs 哪个更好？

The three of us use vim as our primary editor but Emacs is also a good alternative and it’s worth trying both to see which works better for you. Emacs does not follow vim’s modal editing, but this can be enabled through Emacs plugins like [Evil](https://github.com/emacs-evil/evil) or [Doom Emacs](https://github.com/hlissner/doom-emacs). An advantage of using Emacs is that extensions can be implemented in Lisp, a better scripting language than vimscript, Vim’s default scripting language.  
我们三人主要使用 Vim 作为编辑器，但 Emacs 也是一个不错的选择，建议两者都尝试一下，看看哪个更适合你。Emacs 不支持 Vim 的模态编辑，但可以通过 [Evil](https://github.com/emacs-evil/evil) 或 [Doom Emacs](https://github.com/hlissner/doom-emacs) 等 Emacs 插件启用。使用 Emacs 的一个优势是，扩展程序可以用 Lisp 编写，Lisp 是一种比 Vim 默认脚本语言 Vimscript 更优秀的脚本语言。

## Any tips or tricks for Machine Learning applications?  
关于机器学习应用，有什么技巧或窍门吗？

Some of the lessons and takeaways from this class can directly be applied to ML applications. As it is the case with many science disciplines, in ML you often perform a series of experiments and want to check what things worked and what didn’t. You can use shell tools to easily and quickly search through these experiments and aggregate the results in a sensible way. This could mean subselecting all experiments in a given time frame or that use a specific dataset. By using a simple JSON file to log all relevant parameters of the experiments, this can be incredibly simple with the tools we covered in this class. Lastly, if you do not work with some sort of cluster where you submit your GPU jobs, you should look into how to automate this process since it can be a quite time consuming task that also eats away your mental energy.  
这门课的一些经验和技巧可以直接应用于机器学习（ML）应用。与许多科学领域一样，在机器学习中，你经常需要进行一系列实验，并检验哪些方法有效，哪些无效。你可以使用 shell 工具轻松快捷地搜索这些实验，并以合理的方式汇总结果。这可能意味着筛选出特定时间段内的所有实验，或者筛选出使用特定数据集的实验。通过使用简单的 JSON 文件记​​录所有相关的实验参数，借助我们在本课程中介绍的工具，这项工作可以变得非常简单。最后，如果你没有使用集群来提交 GPU 作业，你应该研究如何自动化这个过程，因为这可能是一项非常耗时且消耗精力的任务。

## Any more Vim tips?  
还有其他 Vim 使用技巧吗？

A few more tips:  
还有一些建议：

-   Plugins - Take your time and explore the plugin landscape. There are a lot of great plugins that address some of vim’s shortcomings or add new functionality that composes well with existing vim workflows. For this, good resources are [VimAwesome](https://vimawesome.com/) and other programmers’ dotfiles.  
    插件——花点时间探索一下插件的世界。有很多优秀的插件可以弥补 Vim 的一些不足，或者添加一些新功能，与现有的 Vim 工作流程完美契合。VimAwesome 和其他程序员的 dotfiles [文件](https://vimawesome.com/)都是不错的资源。
-   Marks - In vim, you can set a mark doing `m<X>` for some letter `X`. You can then go back to that mark doing `'<X>`. This lets you quickly navigate to specific locations within a file or even across files.  
    标记 - 在 Vim 中，你可以使用 `m<X>` 设置一个标记，其中 m<X 代表字母 `X` 然后，你可以使用 `'<X>` 返回到该标记。这使你可以快速导航到文件内的特定位置，甚至跨文件导航。
-   Navigation - `Ctrl+O` and `Ctrl+I` move you backward and forward respectively through your recently visited locations.  
    导航 - `Ctrl+O` 和 `Ctrl+I` 分别可在最近访问过的位置之间向后和向前移动。
-   Undo Tree - Vim has a quite fancy mechanism for keeping track of changes. Unlike other editors, vim stores a tree of changes so even if you undo and then make a different change you can still go back to the original state by navigating the undo tree. Some plugins like [gundo.vim](https://github.com/sjl/gundo.vim) and [undotree](https://github.com/mbbill/undotree) expose this tree in a graphical way.  
    撤销树 - Vim 拥有非常精妙的机制来跟踪更改。与其他编辑器不同，Vim 会存储一个更改树，因此即使您撤销了某个操作，然后又进行了不同的更改，您仍然可以通过浏览撤销树回到原始状态。一些插件，例如 [gundo.vim](https://github.com/sjl/gundo.vim) 和 [undotree，](https://github.com/mbbill/undotree) 会以图形化的方式显示这个撤销树。
-   Undo with time - The `:earlier` and `:later` commands will let you navigate the files using time references instead of one change at a time.  
    使用时间撤销 - `:earlier` 和 `:later` 命令允许您使用时间参考来浏览文件，而不是一次只进行一项更改。
-   [Persistent undo](https://vim.fandom.com/wiki/Using_undo_branches#Persistent_undo) is an amazing built-in feature of vim that is disabled by default. It persists undo history between vim invocations. By setting `undofile` and `undodir` in your `.vimrc`, vim will store a per-file history of changes.  
    [持久撤销](https://vim.fandom.com/wiki/Using_undo_branches#Persistent_undo)是 Vim 的一项强大的内置功能，默认情况下处于禁用状态。它可以在 Vim 每次调用之间保留撤销历史记录。通过在 `.vimrc` 文件中设置 `undofile` 和 `undodir` ，Vim 会为每个文件保存更改历史记录。
-   Leader Key - The leader key is a special key that is often left to the user to be configured for custom commands. The pattern is usually to press and release this key (often the space key) and then some other key to execute a certain command. Often, plugins will use this key to add their own functionality, for instance the UndoTree plugin uses `<Leader> U` to open the undo tree.  
    引导键 - 引导键是一个特殊的按键，通常由用户自行配置用于自定义命令。其使用模式通常是先按下并松开此键（通常是空格键），然后再按下其他按键来执行特定命令。插件经常会使用此键来添加自身的功能，例如，UndoTree 插件使用 `<Leader> U` 来打开撤销树。
-   Advanced Text Objects - Text objects like searches can also be composed with vim commands. E.g. `d/<pattern>` will delete to the next match of said pattern or `cgn` will change the next occurrence of the last searched string.  
    高级文本对象 - 文本对象（例如搜索）也可以使用 vim 命令编写。例如， `d/<pattern>` 会删除指定模式的下一个匹配项，而 `cgn` 会更改上次搜索字符串的下一个出现位置。

## What is 2FA and why should I use it?  
什么是双因素认证（2FA）？我为什么要使用它？

Two Factor Authentication (2FA) adds an extra layer of protection to your accounts on top of passwords. In order to login, you not only have to know some password, but you also have to “prove” in some way you have access to some hardware device. In the most simple case, this can be achieved by receiving an SMS on your phone, although there are [known issues](https://www.kaspersky.com/blog/2fa-practical-guide/24219/) with SMS 2FA. A better alternative we endorse is to use a [U2F](https://en.wikipedia.org/wiki/Universal_2nd_Factor) solution like [YubiKey](https://www.yubico.com/).  
双因素认证 (2FA) 在密码的基础上为您的帐户增加了一层额外的保护。要登录，您不仅需要知道密码，还需要以某种方式“证明”您有权访问某个硬件设备。最简单的方法是通过手机接收短信验证，但短信 2FA 存在一些[已知问题](https://www.kaspersky.com/blog/2fa-practical-guide/24219/) 。我们推荐使用 [YubiKey](https://www.yubico.com/) 等 [U2F](https://en.wikipedia.org/wiki/Universal_2nd_Factor) 解决方案，这是一种更好的选择。

## Any comments on differences between web browsers?  
关于不同浏览器之间的区别，大家有什么看法？

The current landscape of browsers as of 2020 is that most of them are like Chrome because they use the same engine (Blink). This means that Microsoft Edge which is also based on Blink, and Safari, which is based on WebKit, a similar engine to Blink, are just worse versions of Chrome. Chrome is a reasonably good browser both in terms of performance and usability. Should you want an alternative, Firefox is our recommendation. It is comparable to Chrome in pretty much every regard and it excels for privacy reasons. Another browser called [Flow](https://www.ekioh.com/flow-browser/) is not user ready yet, but it is implementing a new rendering engine that promises to be faster than the current ones.  
截至 2020 年，大多数浏览器都与 Chrome 类似，因为它们都使用相同的渲染引擎（Blink）。这意味着同样基于 Blink 的 Microsoft Edge 和基于 WebKit（与 Blink 类似的引擎）的 Safari，都只是 Chrome 的简化版。Chrome 在性能和易用性方面都相当不错。如果您想要其他选择，我们推荐 Firefox。它在几乎所有方面都与 Chrome 不相上下，并且在隐私保护方面表现更佳。另一款名为 [Flow](https://www.ekioh.com/flow-browser/) 的浏览器目前尚未正式发布，但它正在开发一种新的渲染引擎，有望比现有引擎更快。

* * *
