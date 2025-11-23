---
date: 2025-11-15
---
# Debugging and Profiling  调试和性能分析

A golden rule in programming is that code does not do what you expect it to do, but what you tell it to do. Bridging that gap can sometimes be a quite difficult feat. In this lecture we are going to cover useful techniques for dealing with buggy and resource hungry code: debugging and profiling.  
编程中的一条黄金法则就是：代码不会按照你的预期运行，而是按照你的指令运行。弥合这种预期与实际运行之间的差距有时相当困难。本次讲座我们将介绍一些处理存在缺陷且资源消耗巨大的代码的实用技巧：调试和性能分析。

# Debugging  调试
## Printf debugging and Logging  
printf 调试和日志记录

“The most effective debugging tool is still careful thought, coupled with judiciously placed print statements” — Brian Kernighan, _Unix for Beginners_.  
“最有效的调试工具仍然是仔细思考，再加上谨慎放置的打印语句”——布莱恩·克尼根， _《Unix 入门》_ 。

A first approach to debug a program is to add print statements around where you have detected the problem, and keep iterating until you have extracted enough information to understand what is responsible for the issue.  
调试程序的第一步是在发现问题的地方周围添加打印语句，并不断迭代，直到提取出足够的信息来了解问题的原因。

A second approach is to use logging in your program, instead of ad hoc print statements. Logging is better than regular print statements for several reasons:  
第二种方法是在程序中使用日志记录，而不是临时打印语句。日志记录比常规打印语句更好，原因有以下几点：

-   You can log to files, sockets or even remote servers instead of standard output.  
    您可以将日志记录到文件、套接字甚至远程服务器，而不是标准输出。
-   Logging supports severity levels (such as INFO, DEBUG, WARN, ERROR, &c), that allow you to filter the output accordingly.  
    日志记录支持严重级别（例如 INFO、DEBUG、WARN、ERROR 等），允许您相应地筛选输出。
-   For new issues, there’s a fair chance that your logs will contain enough information to detect what is going wrong.  
    对于新出现的问题，您的日志很有可能包含足够的信息来检测出哪里出了问题。

[Here](https://missing.csail.mit.edu/static/files/logger.py) is an example code that logs messages:  
[以下](https://missing.csail.mit.edu/static/files/logger.py)是一个记录消息的示例代码：

```
$ python logger.py
# Raw output as with just prints
$ python logger.py log
# Log formatted output
$ python logger.py log ERROR
# Print only ERROR levels and above
$ python logger.py color
# Color formatted output
```

One of my favorite tips for making logs more readable is to color code them. By now you probably have realized that your terminal uses colors to make things more readable. But how does it do it? Programs like `ls` or `grep` are using [ANSI escape codes](https://en.wikipedia.org/wiki/ANSI_escape_code), which are special sequences of characters to indicate your shell to change the color of the output. For example, executing `echo -e "\e[38;2;255;0;0mThis is red\e[0m"` prints the message `This is red` in red on your terminal, as long as it supports [true color](https://github.com/termstandard/colors#truecolor-support-in-output-devices). If your terminal doesn’t support this (e.g. macOS’s Terminal.app), you can use the more universally supported escape codes for 16 color choices, for example `echo -e "\e[31;1mThis is red\e[0m"`.  
我最喜欢的提高日志​​可读性的技巧之一就是给日志添加颜色编码。你可能已经意识到终端会使用颜色来提高可读性。但它是如何实现的呢？像 `ls` 或 `grep` 这样的程序会使用 [ANSI 转义码](https://en.wikipedia.org/wiki/ANSI_escape_code) ，这是一种特殊的字符序列，用来指示 shell 更改输出的颜色。例如，执行 \` `echo -e "\e[38;2;255;0;0mThis is red\e[0m"` \` 会在终端上以红色打印 `This is red` 条消息，前提是你的终端支持[真彩色](https://github.com/termstandard/colors#truecolor-support-in-output-devices) 。如果你的终端不支持真彩色（例如 macOS 的 Terminal.app），你可以使用更通用的转义码来选择 16 种颜色，例如 \` `echo -e "\e[31;1mThis is red\e[0m"` \`。

The following script shows how to print many RGB colors into your terminal (again, as long as it supports true color).  
以下脚本展示了如何将多种 RGB 颜色打印到终端中（同样，前提是终端支持真彩色）。

```
#!/usr/bin/env bash
for R in $(seq 0 20 255); do
    for G in $(seq 0 20 255); do
        for B in $(seq 0 20 255); do
            printf "\e[38;2;${R};${G};${B}m█\e[0m";
        done
    done
done
```

## Third party logs  第三方日志

As you start building larger software systems you will most probably run into dependencies that run as separate programs. Web servers, databases or message brokers are common examples of this kind of dependencies. When interacting with these systems it is often necessary to read their logs, since client side error messages might not suffice.  
随着软件系统规模的扩大，您很可能会遇到一些作为独立程序运行的依赖项。Web 服务器、数据库或消息代理就是这类依赖项的常见例子。与这些系统交互时，通常需要查看它们的日志，因为客户端的错误信息可能不足以解决问题。

Luckily, most programs write their own logs somewhere in your system. In UNIX systems, it is commonplace for programs to write their logs under `/var/log`. For instance, the [NGINX](https://www.nginx.com/) webserver places its logs under `/var/log/nginx`. More recently, systems have started using a **system log**, which is increasingly where all of your log messages go. Most (but not all) Linux systems use `systemd`, a system daemon that controls many things in your system such as which services are enabled and running. `systemd` places the logs under `/var/log/journal` in a specialized format and you can use the [`journalctl`](https://www.man7.org/linux/man-pages/man1/journalctl.1.html) command to display the messages. Similarly, on macOS there is still `/var/log/system.log` but an increasing number of tools use the system log, that can be displayed with [`log show`](https://www.manpagez.com/man/1/log/). On most UNIX systems you can also use the [`dmesg`](https://www.man7.org/linux/man-pages/man1/dmesg.1.html) command to access the kernel log.  
幸运的是，大多数程序都会在系统某个地方写入自己的日志。在 UNIX 系统中，程序通常会将日志写入 `/var/log` 目录。例如， [NGINX](https://www.nginx.com/) Web 服务器会将日志放在 `/var/log/nginx` 目录下。近年来，越来越多的系统开始使用**系统日志** ，所有日志消息都越来越多地保存在系统日志中。大多数（但并非全部）Linux 系统使用 `systemd` ，这是一个系统守护进程，可以控制系统中的许多事项，例如哪些服务已启用并正在运行。 `systemd` 会将日志以特定格式保存在 `/var/log/journal` 下，您可以使用 [`journalctl`](https://www.man7.org/linux/man-pages/man1/journalctl.1.html) 命令显示这些日志。类似地，macOS 系统中仍然存在 `/var/log/system.log` 文件，但越来越多的工具开始使用系统日志，您可以使用 [`log show`](https://www.manpagez.com/man/1/log/) 命令查看系统日志。在大多数 UNIX 系统中，您还可以使用 [`dmesg`](https://www.man7.org/linux/man-pages/man1/dmesg.1.html) 命令访问内核日志。

For logging under the system logs you can use the [`logger`](https://www.man7.org/linux/man-pages/man1/logger.1.html) shell program. Here’s an example of using `logger` and how to check that the entry made it to the system logs. Moreover, most programming languages have bindings logging to the system log.  
您可以使用 [`logger`](https://www.man7.org/linux/man-pages/man1/logger.1.html) shell 程序来记录系统日志。以下示例展示了如何使用 `logger` 以及如何检查日志条目是否已写入系统日志。此外，大多数编程语言都提供了系统日志记录的绑定。

```
logger "Hello Logs"
# On macOS
log show --last 1m | grep Hello
# On Linux
journalctl --since "1m ago" | grep Hello
```

As we saw in the data wrangling lecture, logs can be quite verbose and they require some level of processing and filtering to get the information you want. If you find yourself heavily filtering through `journalctl` and `log show` you can consider using their flags, which can perform a first pass of filtering of their output. There are also some tools like [`lnav`](http://lnav.org/), that provide an improved presentation and navigation for log files.  
正如我们在数据整理课程中看到的，日志可能非常冗长，需要进行一定程度的处理和过滤才能获取所需信息。如果您发现自己需要大量使用 `journalctl` 和 `log show` 进行过滤，可以考虑使用它们的标志位，这些标志位可以对输出进行初步过滤。此外，还有一些工具，例如 [`lnav`](http://lnav.org/) ，可以改进日志文件的呈现方式和导航功能。

## Debuggers  调试器

When printf debugging is not enough you should use a debugger. Debuggers are programs that let you interact with the execution of a program, allowing the following:  
当 printf 调试不足以解决问题时，您应该使用调试器。调试器是一种允许您与程序执行过程交互的程序，可以执行以下操作：

-   Halt execution of the program when it reaches a certain line.  
    当程序执行到某一行时，停止程序的执行。
-   Step through the program one instruction at a time.  
    请按照程序指示一步一步操作。
-   Inspect values of variables after the program crashed.  
    程序崩溃后检查变量的值。
-   Conditionally halt the execution when a given condition is met.  
    当满足给定条件时，有条件地停止执行。
-   And many more advanced features  
    以及更多高级功能

Many programming languages come with some form of debugger. In Python this is the Python Debugger [`pdb`](https://docs.python.org/3/library/pdb.html).  
许多编程语言都带有某种形式的调试器。在 Python 中，这个调试器是 Python 调试器 [`pdb`](https://docs.python.org/3/library/pdb.html) 。

Here is a brief description of some of the commands `pdb` supports:  
以下是对 `pdb` 支持的部分命令的简要说明：

-   **l**(ist) - Displays 11 lines around the current line or continue the previous listing.  
    **l** （列表）- 在当前行周围显示 11 行，或继续上一个列表。
-   **s**(tep) - Execute the current line, stop at the first possible occasion.  
    **s** （步）- 执行当前行，在第一个可能的机会处停止。
-   **n**(ext) - Continue execution until the next line in the current function is reached or it returns.  
    **n** （ext）- 继续执行，直到到达当前函数中的下一行或函数返回为止。
-   **b**(reak) - Set a breakpoint (depending on the argument provided).  
    **b** （中断）- 设置断点（取决于提供的参数）。
-   **p**(rint) - Evaluate the expression in the current context and print its value. There’s also **pp** to display using [`pprint`](https://docs.python.org/3/library/pprint.html) instead.  
    **p** （print）- 计算当前上下文中的表达式并打印其值。也可以使用 **pp** ，通过 [`pprint`](https://docs.python.org/3/library/pprint.html) 来显示结果。
-   **r**(eturn) - Continue execution until the current function returns.  
    **r** （返回）- 继续执行，直到当前函数返回。
-   **q**(uit) - Quit the debugger.  
    **q** (uit) - 退出调试器。

Let’s go through an example of using `pdb` to fix the following buggy python code. (See the lecture video).  
让我们通过一个例子来学习如何使用 `pdb` 来修复以下有缺陷的 Python 代码。（参见讲座视频）。

```
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(n):
            if arr[j] > arr[j+1]:
                arr[j] = arr[j+1]
                arr[j+1] = arr[j]
    return arr

print(bubble_sort([4, 2, 1, 8, 7, 6]))
```

Note that since Python is an interpreted language we can use the `pdb` shell to execute commands and to execute instructions. [`ipdb`](https://pypi.org/project/ipdb/) is an improved `pdb` that uses the [`IPython`](https://ipython.org) REPL enabling tab completion, syntax highlighting, better tracebacks, and better introspection while retaining the same interface as the `pdb` module.  
请注意，由于 Python 是一种解释型语言，我们可以使用 `pdb` shell 来执行命令和执行指令。 [`ipdb`](https://pypi.org/project/ipdb/) 是一个改进的 `pdb` ，它使用 [`IPython`](https://ipython.org) REPL，实现了 Tab 键自动补全、语法高亮、更好的回溯和更好的内省，同时保留了与 `pdb` 模块相同的接口。

For more low level programming you will probably want to look into [`gdb`](https://www.gnu.org/software/gdb/) (and its quality of life modification [`pwndbg`](https://github.com/pwndbg/pwndbg)) and [`lldb`](https://lldb.llvm.org/). They are optimized for C-like language debugging but will let you probe pretty much any process and get its current machine state: registers, stack, program counter, &c.  
对于更底层的编程，你可能需要了解一下 [`gdb`](https://www.gnu.org/software/gdb/) （以及它的便捷修改版 [`pwndbg`](https://github.com/pwndbg/pwndbg) ）和 [`lldb`](https://lldb.llvm.org/) 。它们针对类 C 语言的调试进行了优化，但几乎可以让你探测任何进程并获取其当前的机器状态：寄存器、堆栈、程序计数器等等。

## Specialized Tools  专用工具

Even if what you are trying to debug is a black box binary there are tools that can help you with that. Whenever programs need to perform actions that only the kernel can, they use [System Calls](https://en.wikipedia.org/wiki/System_call). There are commands that let you trace the syscalls your program makes. In Linux there’s [`strace`](https://www.man7.org/linux/man-pages/man1/strace.1.html) and macOS and BSD have [`dtrace`](http://dtrace.org/blogs/about/). `dtrace` can be tricky to use because it uses its own `D` language, but there is a wrapper called [`dtruss`](https://www.manpagez.com/man/1/dtruss/) that provides an interface more similar to `strace` (more details [here](https://8thlight.com/blog/colin-jones/2015/11/06/dtrace-even-better-than-strace-for-osx.html)).  
即使你要调试的是一个黑盒二进制文件，也有工具可以帮到你。当程序需要执行只有内核才能执行的操作时，它们会使用[系统调用](https://en.wikipedia.org/wiki/System_call) 。有一些命令可以让你跟踪程序发出的系统调用。在 Linux 系统中，有 [`strace`](https://www.man7.org/linux/man-pages/man1/strace.1.html) ，macOS 和 BSD 系统则有 [`dtrace`](http://dtrace.org/blogs/about/) 使用起来可能比较棘手 `dtrace` 因为它使用自己的 `D` 语言，但有一个名为 [`dtruss`](https://www.manpagez.com/man/1/dtruss/) 封装库，它提供了一个更类似于 `strace` 接口（更多详情请点击[此处](https://8thlight.com/blog/colin-jones/2015/11/06/dtrace-even-better-than-strace-for-osx.html) ）。

Below are some examples of using `strace` or `dtruss` to show [`stat`](https://www.man7.org/linux/man-pages/man2/stat.2.html) syscall traces for an execution of `ls`. For a deeper dive into `strace`, [this article](https://blogs.oracle.com/linux/strace-the-sysadmins-microscope-v2) and [this zine](https://jvns.ca/strace-zine-unfolded.pdf) are good reads.  
以下是一些使用 `strace` 或 `dtruss` 显示 `ls` 命令执行的 [`stat`](https://www.man7.org/linux/man-pages/man2/stat.2.html) 系统调用跟踪的示例。要深入了解 `strace` ，可以阅读[这篇文章](https://blogs.oracle.com/linux/strace-the-sysadmins-microscope-v2)和[这本杂志](https://jvns.ca/strace-zine-unfolded.pdf) 。

```
# On Linux
sudo strace -e lstat ls -l > /dev/null
# On macOS
sudo dtruss -t lstat64_extended ls -l > /dev/null
```

Under some circumstances, you may need to look at the network packets to figure out the issue in your program. Tools like [`tcpdump`](https://www.man7.org/linux/man-pages/man1/tcpdump.1.html) and [Wireshark](https://www.wireshark.org/) are network packet analyzers that let you read the contents of network packets and filter them based on different criteria.  
在某些情况下，您可能需要查看网络数据包来找出程序中的问题。像 [`tcpdump`](https://www.man7.org/linux/man-pages/man1/tcpdump.1.html) 和 [Wireshark](https://www.wireshark.org/) 这样的工具是网络数据包分析器，它们可以读取网络数据包的内容并根据不同的标准进行过滤。

For web development, the Chrome/Firefox developer tools are quite handy. They feature a large number of tools, including:  
对于网页开发而言，Chrome/Firefox 的开发者工具非常实用。它们包含大量工具，其中包括：

-   Source code - Inspect the HTML/CSS/JS source code of any website.  
    源代码 - 检查任何网站的 HTML/CSS/JS 源代码。
-   Live HTML, CSS, JS modification - Change the website content, styles and behavior to test (you can see for yourself that website screenshots are not valid proofs).  
    实时修改 HTML、CSS、JS - 更改网站内容、样式和行为进行测试（您可以自己验证，网站截图并非有效证据）。
-   Javascript shell - Execute commands in the JS REPL.  
    Javascript shell - 在 JS REPL 中执行命令。
-   Network - Analyze the requests timeline.  
    网络 - 分析请求时间线。
-   Storage - Look into the Cookies and local application storage.  
    存储 - 查看 Cookie 和本地应用程序存储。

## Static Analysis  静态分析

For some issues you do not need to run any code. For example, just by carefully looking at a piece of code you could realize that your loop variable is shadowing an already existing variable or function name; or that a program reads a variable before defining it. Here is where [static analysis](https://en.wikipedia.org/wiki/Static_program_analysis) tools come into play. Static analysis programs take source code as input and analyze it using coding rules to reason about its correctness.  
有些问题无需运行任何代码即可发现。例如，只需仔细查看一段代码，您就能发现循环变量与已存在的变量或函数名冲突；或者程序在定义变量之前就读取了它。这时， [静态分析](https://en.wikipedia.org/wiki/Static_program_analysis)工具就派上用场了。静态分析程序以源代码为输入，并运用编码规则对其进行分析，从而判断其正确性。

In the following Python snippet there are several mistakes. First, our loop variable `foo` shadows the previous definition of the function `foo`. We also wrote `baz` instead of `bar` in the last line, so the program will crash after completing the `sleep` call (which will take one minute).  
以下 Python 代码片段存在几个错误。首先，循环变量 `foo` 覆盖了之前函数 `foo` 的定义。此外，最后一行我们写成了 `baz` 而不是 `bar` ，因此程序会在 `sleep` 调用完成后崩溃（sleep 调用需要一分钟）。

```
import time

def foo():
    return 42

for foo in range(5):
    print(foo)
bar = 1
bar *= 0.2
time.sleep(60)
print(baz)
```

Static analysis tools can identify these kinds of issues. When we run [`pyflakes`](https://pypi.org/project/pyflakes) on the code we get the errors related to both bugs. [`mypy`](http://mypy-lang.org/) is another tool that can detect type checking issues. Here, `mypy` will warn us that `bar` is initially an `int` and is then casted to a `float`. Again, note that all these issues were detected without having to run the code.  
静态分析工具可以识别这类问题。当我们运行 [`pyflakes`](https://pypi.org/project/pyflakes) 分析代码时，会发现与这两个 bug 相关的错误 [`mypy`](http://mypy-lang.org/) 是另一个可以检测类型检查问题的工具。在这里， `mypy` 会警告我们 `bar` 最初是 `int` ，然后被强制转换为 `float` 。再次强调，所有这些问题都是在无需运行代码的情况下检测到的。

```
$ pyflakes foobar.py
foobar.py:6: redefinition of unused 'foo' from line 3
foobar.py:11: undefined name 'baz'

$ mypy foobar.py
foobar.py:6: error: Incompatible types in assignment (expression has type "int", variable has type "Callable[[], Any]")
foobar.py:9: error: Incompatible types in assignment (expression has type "float", variable has type "int")
foobar.py:11: error: Name 'baz' is not defined
Found 3 errors in 1 file (checked 1 source file)
```

In the shell tools lecture we covered [`shellcheck`](https://www.shellcheck.net/), which is a similar tool for shell scripts.  
在 shell 工具讲座中，我们介绍了 [`shellcheck`](https://www.shellcheck.net/) ，这是一个类似的 shell 脚本工具。

Most editors and IDEs support displaying the output of these tools within the editor itself, highlighting the locations of warnings and errors. This is often called **code linting** and it can also be used to display other types of issues such as stylistic violations or insecure constructs.  
大多数编辑器和集成开发环境 (IDE) 都支持在编辑器内显示这些工具的输出结果，并高亮显示警告和错误的位置。这通常被称为**代码检查 (linting)** ，它还可以用于显示其他类型的问题，例如代码风格违规或不安全的结构。

In vim, the plugins [`ale`](https://vimawesome.com/plugin/ale) or [`syntastic`](https://vimawesome.com/plugin/syntastic) will let you do that. For Python, [`pylint`](https://github.com/PyCQA/pylint) and [`pep8`](https://pypi.org/project/pep8/) are examples of stylistic linters and [`bandit`](https://pypi.org/project/bandit/) is a tool designed to find common security issues. For other languages people have compiled comprehensive lists of useful static analysis tools, such as [Awesome Static Analysis](https://github.com/mre/awesome-static-analysis) (you may want to take a look at the _Writing_ section) and for linters there is [Awesome Linters](https://github.com/caramelomartins/awesome-linters).  
在 Vim 中，可以使用 [`ale`](https://vimawesome.com/plugin/ale) 或 [`syntastic`](https://vimawesome.com/plugin/syntastic) 插件来实现这一点。对于 Python， [`pylint`](https://github.com/PyCQA/pylint) 和 [`pep8`](https://pypi.org/project/pep8/) 是代码风格检查工具的例子，而 [`bandit`](https://pypi.org/project/bandit/) 则是一款用于查找常见安全问题的工具。对于其他语言，人们已经整理出了一系列实用的静态分析工具，例如 [Awesome Static Analysis](https://github.com/mre/awesome-static-analysis) （您可以查看其中的 _“编写”_ 部分），而代码检查工具方面则有 [Awesome Linters](https://github.com/caramelomartins/awesome-linters) 。

A complementary tool to stylistic linting are code formatters such as [`black`](https://github.com/psf/black) for Python, `gofmt` for Go, `rustfmt` for Rust or [`prettier`](https://prettier.io/) for JavaScript, HTML and CSS. These tools autoformat your code so that it’s consistent with common stylistic patterns for the given programming language. Although you might be unwilling to give stylistic control about your code, standardizing code format will help other people read your code and will make you better at reading other people’s (stylistically standardized) code.  
代码格式化工具是代码风格检查的有力补充，例如 Python 的 [`black`](https://github.com/psf/black) 、Go 的 `gofmt` 、Rust 的 `rustfmt` 以及 JavaScript、HTML 和 CSS 的 [`prettier`](https://prettier.io/) 。这些工具会自动格式化代码，使其符合特定编程语言的常见风格模式。虽然你可能不愿意让别人控制你的代码风格，但规范代码格式有助于其他人阅读你的代码，也能让你更好地阅读其他人编写的（风格规范的）代码。

# Profiling  个人资料

Even if your code functionally behaves as you would expect, that might not be good enough if it takes all your CPU or memory in the process. Algorithms classes often teach big _O_ notation but not how to find hot spots in your programs. Since [premature optimization is the root of all evil](http://wiki.c2.com/?PrematureOptimization), you should learn about profilers and monitoring tools. They will help you understand which parts of your program are taking most of the time and/or resources so you can focus on optimizing those parts.  
即使你的代码在功能上运行正常，但如果它占用了你所有的 CPU 或内存，那可能仍然不够好。算法课程通常会教授大 _O_ 表示法，但不会教你如何找到程序中的资源瓶颈。由于[过早优化是万恶之源](http://wiki.c2.com/?PrematureOptimization) ，你应该学习性能分析器和监控工具。它们可以帮助你了解程序中哪些部分占用了最多的时间和/或资源，从而让你能够集中精力优化这些部分。

## Timing  定时

Similarly to the debugging case, in many scenarios it can be enough to just print the time it took your code between two points. Here is an example in Python using the [`time`](https://docs.python.org/3/library/time.html) module.  
与调试情况类似，在很多情况下，只需打印代码在两个步骤之间运行所花费的时间就足够了。以下是一个使用 Python [`time`](https://docs.python.org/3/library/time.html) 模块的示例。

```
import time, random
n = random.randint(1, 10) * 100

# Get current time
start = time.time()

# Do some work
print("Sleeping for {} ms".format(n))
time.sleep(n/1000)

# Compute time between start and now
print(time.time() - start)

# Output
# Sleeping for 500 ms
# 0.5713930130004883
```

However, wall clock time can be misleading since your computer might be running other processes at the same time or waiting for events to happen. It is common for tools to make a distinction between _Real_, _User_ and _Sys_ time. In general, _User_ + _Sys_ tells you how much time your process actually spent in the CPU (more detailed explanation [here](https://stackoverflow.com/questions/556405/what-do-real-user-and-sys-mean-in-the-output-of-time1)).  
然而，实际运行时间可能会产生误导，因为你的计算机可能同时运行着其他进程或等待某些事件发生。通常，工具会区分_实际时间_ 、 _用户时间_和_系统_时间。一般来说， _用户时间_ + _系统时间_可以告诉你进程实际占用 CPU 的时间（更详细的解释请点击[此处](https://stackoverflow.com/questions/556405/what-do-real-user-and-sys-mean-in-the-output-of-time1) ）。

-   _Real_ - Wall clock elapsed time from start to finish of the program, including the time taken by other processes and time taken while blocked (e.g. waiting for I/O or network)  
    _实际_时间 - 从程序开始到结束所经过的时间，包括其他进程所花费的时间和阻塞所花费的时间（例如等待 I/O 或网络）。
-   _User_ - Amount of time spent in the CPU running user code  
    _用户_ \- 用户代码在 CPU 上花费的时间
-   _Sys_ - Amount of time spent in the CPU running kernel code  
    _系统_ \- CPU 运行内核代码所花费的时间量

For example, try running a command that performs an HTTP request and prefixing it with [`time`](https://www.man7.org/linux/man-pages/man1/time.1.html). Under a slow connection you might get an output like the one below. Here it took over 2 seconds for the request to complete but the process only took 15ms of CPU user time and 12ms of kernel CPU time.  
例如，尝试运行一个执行 HTTP 请求的命令，并在命令前加上 [`time`](https://www.man7.org/linux/man-pages/man1/time.1.html) 前缀。在连接速度较慢的情况下，您可能会得到类似下面的输出。这里，请求完成耗时超过 2 秒，但整个过程仅占用 15 毫秒的 CPU 用户时间和 12 毫秒的内核 CPU 时间。

```
$ time curl https://missing.csail.mit.edu &> /dev/null
real    0m2.561s
user    0m0.015s
sys     0m0.012s
```

## Profilers  分析器
### CPU  中央处理器

Most of the time when people refer to _profilers_ they actually mean _CPU profilers_, which are the most common. There are two main types of CPU profilers: _tracing_ and _sampling_ profilers. Tracing profilers keep a record of every function call your program makes whereas sampling profilers probe your program periodically (commonly every millisecond) and record the program’s stack. They use these records to present aggregate statistics of what your program spent the most time doing. [Here](https://jvns.ca/blog/2017/12/17/how-do-ruby---python-profilers-work-) is a good intro article if you want more detail on this topic.  
大多数情况下，人们提到_性能分析器_时，实际上指的是 _CPU 性能分析器_ ，这也是最常见的性能分析器。CPU 性能分析器主要分为两大类： _跟踪型性能_分析器和_采样型性能_分析器。 跟踪分析器会记录程序发出的每一个函数调用，而采样分析器会定期（通常每毫秒一次）探测程序，并记录程序的堆栈。 他们利用这些记录来呈现你的程序花费时间最多的各项工作的汇总统计数据。 如果你想了解更多关于这个主题的细节， [这里](https://jvns.ca/blog/2017/12/17/how-do-ruby---python-profilers-work-)有一篇不错的入门文章。

Most programming languages have some sort of command line profiler that you can use to analyze your code. They often integrate with full fledged IDEs but for this lecture we are going to focus on the command line tools themselves.  
大多数编程语言都有某种命令行性能分析器，可以用来分析代码。它们通常与功能齐全的集成开发环境（IDE）集成，但本次讲座我们将重点介绍命令行工具本身。

In Python we can use the `cProfile` module to profile time per function call. Here is a simple example that implements a rudimentary grep in Python:  
在 Python 中，我们可以使用 `cProfile` 模块来分析每次函数调用的时间。以下是一个简单的示例，它在 Python 中实现了一个基本的 grep 命令：

```
#!/usr/bin/env python

import sys, re

def grep(pattern, file):
    with open(file, 'r') as f:
        print(file)
        for i, line in enumerate(f.readlines()):
            pattern = re.compile(pattern)
            match = pattern.search(line)
            if match is not None:
                print("{}: {}".format(i, line), end="")

if __name__ == '__main__':
    times = int(sys.argv[1])
    pattern = sys.argv[2]
    for i in range(times):
        for file in sys.argv[3:]:
            grep(pattern, file)
```

We can profile this code using the following command. Analyzing the output we can see that IO is taking most of the time and that compiling the regex takes a fair amount of time as well. Since the regex only needs to be compiled once, we can factor it out of the for.  
我们可以使用以下命令分析这段代码。分析输出结果可知，IO 操作耗时最长，编译正则表达式也需要相当长的时间。由于正则表达式只需要编译一次，我们可以将其从 for 循环中分离出来。

```
$ python -m cProfile -s tottime grep.py 1000 '^(import|\s*def)[^,]*$' *.py

[omitted program output]

 ncalls  tottime  percall  cumtime  percall filename:lineno(function)
     8000    0.266    0.000    0.292    0.000 {built-in method io.open}
     8000    0.153    0.000    0.894    0.000 grep.py:5(grep)
    17000    0.101    0.000    0.101    0.000 {built-in method builtins.print}
     8000    0.100    0.000    0.129    0.000 {method 'readlines' of '_io._IOBase' objects}
    93000    0.097    0.000    0.111    0.000 re.py:286(_compile)
    93000    0.069    0.000    0.069    0.000 {method 'search' of '_sre.SRE_Pattern' objects}
    93000    0.030    0.000    0.141    0.000 re.py:231(compile)
    17000    0.019    0.000    0.029    0.000 codecs.py:318(decode)
        1    0.017    0.017    0.911    0.911 grep.py:3(<module>)

[omitted lines]
```

A caveat of Python’s `cProfile` profiler (and many profilers for that matter) is that they display time per function call. That can become unintuitive really fast, especially if you are using third party libraries in your code since internal function calls are also accounted for. A more intuitive way of displaying profiling information is to include the time taken per line of code, which is what _line profilers_ do.  
Python 的 `cProfile` 分析器（以及许多其他分析器）的一个缺点是，它们显示的是每次函数调用的耗时。这很快就会变得难以理解，尤其是在代码中使用第三方库时，因为内部函数调用也会被计算在内。更直观的分析信息显示方式是包含每行代码的耗时，这也是_行分析器_所做的。

For instance, the following piece of Python code performs a request to the class website and parses the response to get all URLs in the page:  
例如，以下 Python 代码片段向课程网站发出请求，并解析响应以获取页面中的所有 URL：

```
#!/usr/bin/env python
import requests
from bs4 import BeautifulSoup

# This is a decorator that tells line_profiler
# that we want to analyze this function
@profile
def get_urls():
    response = requests.get('https://missing.csail.mit.edu')
    s = BeautifulSoup(response.content, 'lxml')
    urls = []
    for url in s.find_all('a'):
        urls.append(url['href'])

if __name__ == '__main__':
    get_urls()
```

If we used Python’s `cProfile` profiler we’d get over 2500 lines of output, and even with sorting it’d be hard to understand where the time is being spent. A quick run with [`line_profiler`](https://github.com/pyutils/line_profiler) shows the time taken per line:  
如果使用 Python 的 `cProfile` 分析器，我们会得到超过 2500 行的输出，即使进行排序也很难看出时间都消耗在哪里。使用 [`line_profiler`](https://github.com/pyutils/line_profiler) 快速运行一下，就能显示每行代码的耗时：

```
$ kernprof -l -v a.py
Wrote profile results to urls.py.lprof
Timer unit: 1e-06 s

Total time: 0.636188 s
File: a.py
Function: get_urls at line 5

Line #  Hits         Time  Per Hit   % Time  Line Contents
==============================================================
 5                                           @profile
 6                                           def get_urls():
 7         1     613909.0 613909.0     96.5      response = requests.get('https://missing.csail.mit.edu')
 8         1      21559.0  21559.0      3.4      s = BeautifulSoup(response.content, 'lxml')
 9         1          2.0      2.0      0.0      urls = []
10        25        685.0     27.4      0.1      for url in s.find_all('a'):
11        24         33.0      1.4      0.0          urls.append(url['href'])
```

### Memory  记忆

In languages like C or C++ memory leaks can cause your program to never release memory that it doesn’t need anymore. To help in the process of memory debugging you can use tools like [Valgrind](https://valgrind.org/) that will help you identify memory leaks.  
在 C 或 C++ 等语言中，内存泄漏会导致程序永远无法释放不再需要的内存。为了帮助进行内存调试，您可以使用 [Valgrind](https://valgrind.org/) 等工具来识别内存泄漏。

In garbage collected languages like Python it is still useful to use a memory profiler because as long as you have pointers to objects in memory they won’t be garbage collected. Here’s an example program and its associated output when running it with [memory-profiler](https://pypi.org/project/memory-profiler/) (note the decorator like in `line-profiler`).  
即使在像 Python 这样有垃圾回收机制的语言中，使用内存分析器仍然很有用，因为只要内存中存在指向对象的指针，这些对象就不会被垃圾回收。以下是一个示例程序及其使用[内存分析器](https://pypi.org/project/memory-profiler/)运行时的输出（注意它使用了类似于 `line-profiler` 装饰器）。

```
@profile
def my_func():
    a = [1] * (10 ** 6)
    b = [2] * (2 * 10 ** 7)
    del b
    return a

if __name__ == '__main__':
    my_func()
```

```
$ python -m memory_profiler example.py
Line #    Mem usage  Increment   Line Contents
==============================================
     3                           @profile
     4      5.97 MB    0.00 MB   def my_func():
     5     13.61 MB    7.64 MB       a = [1] * (10 ** 6)
     6    166.20 MB  152.59 MB       b = [2] * (2 * 10 ** 7)
     7     13.61 MB -152.59 MB       del b
     8     13.61 MB    0.00 MB       return a
```

### Event Profiling  事件分析

As it was the case for `strace` for debugging, you might want to ignore the specifics of the code that you are running and treat it like a black box when profiling. The [`perf`](https://www.man7.org/linux/man-pages/man1/perf.1.html) command abstracts CPU differences away and does not report time or memory, but instead it reports system events related to your programs. For example, `perf` can easily report poor cache locality, high amounts of page faults or livelocks. Here is an overview of the command:  
就像使用 `strace` 进行调试一样，在进行性能分析时，您可能需要忽略正在运行的代码的具体细节，将其视为一个黑盒。perf [`perf`](https://www.man7.org/linux/man-pages/man1/perf.1.html) 会抽象掉 CPU 差异，不报告时间或内存使用情况，而是报告与程序相关的系统事件。例如， `perf` 可以轻松报告缓存局部性差、页面错误过多或出现活锁等问题。以下是该命令的概述：

-   `perf list` - List the events that can be traced with perf  
    `perf list` - 列出可以使用 perf 跟踪的事件
-   `perf stat COMMAND ARG1 ARG2` - Gets counts of different events related to a process or command  
    `perf stat COMMAND ARG1 ARG2` - 获取与进程或命令相关的不同事件的计数
-   `perf record COMMAND ARG1 ARG2` - Records the run of a command and saves the statistical data into a file called `perf.data`  
    `perf record COMMAND ARG1 ARG2` - 记录命令的运行情况，并将统计数据保存到名为 `perf.data` 的文件中。
-   `perf report` - Formats and prints the data collected in `perf.data`  
    `perf report` - 格式化并打印 `perf.data` 中收集的数据

### Visualization  可视化

Profiler output for real world programs will contain large amounts of information because of the inherent complexity of software projects. Humans are visual creatures and are quite terrible at reading large amounts of numbers and making sense of them. Thus there are many tools for displaying profiler’s output in an easier to parse way.  
由于软件项目固有的复杂性，实际程序的性能分析输出会包含大量信息。人类是视觉动物，很难阅读和理解大量数字。因此，有很多工具可以将性能分析输出以更易于理解的方式显示出来。

One common way to display CPU profiling information for sampling profilers is to use a [Flame Graph](http://www.brendangregg.com/flamegraphs.html), which will display a hierarchy of function calls across the Y axis and time taken proportional to the X axis. They are also interactive, letting you zoom into specific parts of the program and get their stack traces (try clicking in the image below).  
一种常见的用于显示 CPU 分析信息的采样分析器方法是使用[火焰图](http://www.brendangregg.com/flamegraphs.html) ，它会在 Y 轴上显示函数调用的层级结构，并在 X 轴上显示与执行时间成正比的耗时。火焰图还具有交互性，允许您放大程序的特定部分并获取其堆栈跟踪（尝试点击下图）。

[![FlameGraph](http://www.brendangregg.com/FlameGraphs/cpu-bash-flamegraph.svg)](http://www.brendangregg.com/FlameGraphs/cpu-bash-flamegraph.svg)

Call graphs or control flow graphs display the relationships between subroutines within a program by including functions as nodes and functions calls between them as directed edges. When coupled with profiling information such as the number of calls and time taken, call graphs can be quite useful for interpreting the flow of a program. In Python you can use the [`pycallgraph`](https://pycallgraph.readthedocs.io/) library to generate them.  
调用图或控制流图通过将函数作为节点，并将函数调用作为有向边，来展示程序中子程序之间的关系。结合调用次数和耗时等性能分析信息，调用图对于理解程序流程非常有用。在 Python 中，可以使用 [`pycallgraph`](https://pycallgraph.readthedocs.io/) 库来生成调用图。

![Call Graph](https://upload.wikimedia.org/wikipedia/commons/2/2f/A_Call_Graph_generated_by_pycallgraph.png)

## Resource Monitoring  资源监测

Sometimes, the first step towards analyzing the performance of your program is to understand what its actual resource consumption is. Programs often run slowly when they are resource constrained, e.g. without enough memory or on a slow network connection. There are a myriad of command line tools for probing and displaying different system resources like CPU usage, memory usage, network, disk usage and so on.  
有时，分析程序性能的第一步是了解其实际资源消耗情况。程序在资源受限时（例如内存不足或网络连接速度慢）运行缓慢。有很多命令行工具可以用来探测和显示不同的系统资源，例如 CPU 使用率、内存使用率、网络使用率、磁盘使用率等等。

-   **General Monitoring** - Probably the most popular is [`htop`](https://htop.dev/), which is an improved version of [`top`](https://www.man7.org/linux/man-pages/man1/top.1.html). `htop` presents various statistics for the currently running processes on the system. `htop` has a myriad of options and keybinds, some useful ones are: `<F6>` to sort processes, `t` to show tree hierarchy and `h` to toggle threads. See also [`glances`](https://nicolargo.github.io/glances/) for similar implementation with a great UI. For getting aggregate measures across all processes, [`dstat`](http://dag.wiee.rs/home-made/dstat/) is another nifty tool that computes real-time resource metrics for lots of different subsystems like I/O, networking, CPU utilization, context switches, &c.  
    **通用监控** \- 最受欢迎的可能是 [`htop`](https://htop.dev/) ，它是 [`top`](https://www.man7.org/linux/man-pages/man1/top.1.html) 的改进版本。 `htop` 会显示系统上当前运行进程的各种统计信息。htop 提供了大量的选项和快捷键，其中一些常用的包括：% `htop` `<F6>` 用于对进程进行排序， `t` 用于显示树状层级结构， `h` 用于切换线程。另请参阅 [`glances`](https://nicolargo.github.io/glances/) ，它提供了类似的功能，并拥有出色的用户界面。要获取所有进程的汇总指标， [`dstat`](http://dag.wiee.rs/home-made/dstat/) 是另一个非常实用的工具，它可以计算许多不同子系统的实时资源指标，例如 I/O、网络、CPU 利用率、上下文切换等等。
-   **I/O operations** - [`iotop`](https://www.man7.org/linux/man-pages/man8/iotop.8.html) displays live I/O usage information and is handy to check if a process is doing heavy I/O disk operations  
    **I/O 操作** \- [`iotop`](https://www.man7.org/linux/man-pages/man8/iotop.8.html) 显示实时 I/O 使用情况信息，方便检查进程是否正在进行大量的 I/O 磁盘操作
-   **Disk Usage** - [`df`](https://www.man7.org/linux/man-pages/man1/df.1.html) displays metrics per partitions and [`du`](http://man7.org/linux/man-pages/man1/du.1.html) displays **d**isk **u**sage per file for the current directory. In these tools the `-h` flag tells the program to print with **h**uman readable format. A more interactive version of `du` is [`ncdu`](https://dev.yorhel.nl/ncdu) which lets you navigate folders and delete files and folders as you navigate.  
    **磁盘使用情况** \- [`df`](https://www.man7.org/linux/man-pages/man1/df.1.html) 显示每个分区的指标， [`du`](http://man7.org/linux/man-pages/man1/du.1.html) 显示当前目录中每个文件的**磁盘****使用**情况。在这些工具中 [`ncdu`](https://dev.yorhel.nl/ncdu) `-h` 标志指示程序以**人类**可读格式打印。ncdu 是 `du` 的一个更具交互性的版本，它允许您浏览文件夹并在浏览过程中删除文件和文件夹。
-   **Memory Usage** - [`free`](https://www.man7.org/linux/man-pages/man1/free.1.html) displays the total amount of free and used memory in the system. Memory is also displayed in tools like `htop`.  
    **内存使用情况** \- [`free`](https://www.man7.org/linux/man-pages/man1/free.1.html) 显示系统中可用内存和已用内存的总量 `htop` 等工具也会显示内存使用情况。
-   **Open Files** - [`lsof`](https://www.man7.org/linux/man-pages/man8/lsof.8.html) lists file information about files opened by processes. It can be quite useful for checking which process has opened a specific file.  
    **打开的文件** \- [`lsof`](https://www.man7.org/linux/man-pages/man8/lsof.8.html) 命令会列出进程打开的文件信息。它对于检查哪个进程打开了特定文件非常有用。
-   **Network Connections and Config** - [`ss`](https://www.man7.org/linux/man-pages/man8/ss.8.html) lets you monitor incoming and outgoing network packets statistics as well as interface statistics. A common use case of `ss` is figuring out what process is using a given port in a machine. For displaying routing, network devices and interfaces you can use [`ip`](http://man7.org/linux/man-pages/man8/ip.8.html). Note that `netstat` and `ifconfig` have been deprecated in favor of the former tools respectively.  
    **网络连接和配置** \- [`ss`](https://www.man7.org/linux/man-pages/man8/ss.8.html) 命令可用于监控传入和传出的网络数据包统计信息以及接口统计信息。ss `ss` 一个常见用途是确定计算机上哪个进程正在使用特定端口。要显示路由、网络设备和接口信息，可以使用 [`ip`](http://man7.org/linux/man-pages/man8/ip.8.html) 。请注意， `netstat` 和 `ifconfig` 已被弃用，建议使用 ip 命令。
-   **Network Usage** - [`nethogs`](https://github.com/raboof/nethogs) and [`iftop`](https://pdw.ex-parrot.com/iftop/) are good interactive CLI tools for monitoring network usage.  
    **网络使用情况** \- [`nethogs`](https://github.com/raboof/nethogs) 和 [`iftop`](https://pdw.ex-parrot.com/iftop/) 是优秀的交互式 CLI 工具，可用于监控网络使用情况。

If you want to test these tools you can also artificially impose loads on the machine using the [`stress`](https://linux.die.net/man/1/stress) command.  
如果您想测试这些工具，还可以使用 [`stress`](https://linux.die.net/man/1/stress) 命令人为地对机器施加负载。

### Specialized tools  专用工具

Sometimes, black box benchmarking is all you need to determine what software to use. Tools like [`hyperfine`](https://github.com/sharkdp/hyperfine) let you quickly benchmark command line programs. For instance, in the shell tools and scripting lecture we recommended `fd` over `find`. We can use `hyperfine` to compare them in tasks we run often. E.g. in the example below `fd` was 20x faster than `find` in my machine.  
有时候，只需进行黑盒基准测试就能确定使用哪款软件。像 [`hyperfine`](https://github.com/sharkdp/hyperfine) 这样的工具可以让你快速测试命令行程序的性能。例如，在 shell 工具和脚本讲解中，我们推荐使用 `fd` 而不是 `find` 。我们可以使用 `hyperfine` 来比较它们在常用任务中的表现。例如，在下面的例子中 `fd` 在我的机器上比 `find` 快 20 倍。

```
$ hyperfine --warmup 3 'fd -e jpg' 'find . -iname "*.jpg"'
Benchmark #1: fd -e jpg
  Time (mean ± σ):      51.4 ms ±   2.9 ms    [User: 121.0 ms, System: 160.5 ms]
  Range (min … max):    44.2 ms …  60.1 ms    56 runs

Benchmark #2: find . -iname "*.jpg"
  Time (mean ± σ):      1.126 s ±  0.101 s    [User: 141.1 ms, System: 956.1 ms]
  Range (min … max):    0.975 s …  1.287 s    10 runs

Summary
  'fd -e jpg' ran
   21.89 ± 2.33 times faster than 'find . -iname "*.jpg"'
```

As it was the case for debugging, browsers also come with a fantastic set of tools for profiling webpage loading, letting you figure out where time is being spent (loading, rendering, scripting, &c). More info for [Firefox](https://profiler.firefox.com/docs/) and [Chrome](https://developers.google.com/web/tools/chrome-devtools/rendering-tools).  
与调试工具一样，浏览器也自带一套强大的网页加载性能分析工具，可以帮助您了解时间都消耗在了哪些环节（加载、渲染、脚本执行等等）。更多信息请参见 [Firefox](https://profiler.firefox.com/docs/) 和 [Chrome 的](https://developers.google.com/web/tools/chrome-devtools/rendering-tools)相关说明。

# Exercises  练习
## Debugging  调试

1.  Use `journalctl` on Linux or `log show` on macOS to get the super user accesses and commands in the last day. If there aren’t any you can execute some harmless commands such as `sudo ls` and check again.  
    在 Linux 系统上使用 `journalctl` ，或在 macOS 系统上使用 `log show` ，可以获取过去一天内的超级用户访问权限和命令记录。如果没有记录，您可以执行一些无害的命令，例如 `sudo ls` ，然后再次检查。
    
2.  Do [this](https://github.com/spiside/pdb-tutorial) hands on `pdb` tutorial to familiarize yourself with the commands. For a more in depth tutorial read [this](https://realpython.com/python-debugging-pdb).  
    完成[这篇](https://github.com/spiside/pdb-tutorial) `pdb` 实战教程，熟悉各项命令。如需更深入的教程，请阅读[此文](https://realpython.com/python-debugging-pdb) 。
    
3.  Install [`shellcheck`](https://www.shellcheck.net/) and try checking the following script. What is wrong with the code? Fix it. Install a linter plugin in your editor so you can get your warnings automatically.  
    安装 [`shellcheck`](https://www.shellcheck.net/) 并尝试检查以下脚本。代码有什么问题？请修复它。在编辑器中安装一个代码检查插件，以便自动接收警告。
    
    ```
    #!/bin/sh
    ## Example: a typical script with several problems
    for f in $(ls *.m3u)
    do
      grep -qi hq.*mp3 $f \
        && echo -e 'Playlist $f contains a HQ file in mp3 format'
    done
    ```
    
4.  (Advanced) Read about [reversible debugging](https://undo.io/resources/reverse-debugging-whitepaper/) and get a simple example working using [`rr`](https://rr-project.org/) or [`RevPDB`](https://morepypy.blogspot.com/2016/07/reverse-debugging-for-python.html).  
    （进阶）阅读有关[可逆调试的内容](https://undo.io/resources/reverse-debugging-whitepaper/) ，并使用 [`rr`](https://rr-project.org/) 或 [`RevPDB`](https://morepypy.blogspot.com/2016/07/reverse-debugging-for-python.html) 获取一个简单的示例。
    ## Profiling  分析
    
5.  [Here](https://missing.csail.mit.edu/static/files/sorts.py) are some sorting algorithm implementations. Use [`cProfile`](https://docs.python.org/3/library/profile.html) and [`line_profiler`](https://github.com/pyutils/line_profiler) to compare the runtime of insertion sort and quicksort. What is the bottleneck of each algorithm? Use then `memory_profiler` to check the memory consumption, why is insertion sort better? Check now the inplace version of quicksort. Challenge: Use `perf` to look at the cycle counts and cache hits and misses of each algorithm.  
    [这里](https://missing.csail.mit.edu/static/files/sorts.py)有一些排序算法的实现。使用 [`cProfile`](https://docs.python.org/3/library/profile.html) 和 [`line_profiler`](https://github.com/pyutils/line_profiler) 比较插入排序和快速排序的运行时间。每种算法的瓶颈是什么？然后使用 `memory_profiler` 检查内存消耗，为什么插入排序更好？现在检查快速排序的原地版本。挑战：使用 `perf` 查看每种算法的循环次数以及缓存命中和未命中次数。
    
6.  Here’s some (arguably convoluted) Python code for computing Fibonacci numbers using a function for each number.  
    以下是一些（或许有些复杂的）Python 代码，用于使用一个函数来计算斐波那契数列中的每个数字。
    
    ```
    #!/usr/bin/env python
    def fib0(): return 0
    
    def fib1(): return 1
    
    s = """def fib{}(): return fib{}() + fib{}()"""
    
    if __name__ == '__main__':
    
        for n in range(2, 10):
            exec(s.format(n, n-1, n-2))
        # from functools import lru_cache
        # for n in range(10):
        #     exec("fib{} = lru_cache(1)(fib{})".format(n, n))
        print(eval("fib9()"))
    ```
    
    Put the code into a file and make it executable. Install prerequisites: [`pycallgraph`](https://lewiscowles1986.github.io/py-call-graph/) and [`graphviz`](http://graphviz.org/). (If you can run `dot`, you already have GraphViz.) Run the code as is with `pycallgraph graphviz -- ./fib.py` and check the `pycallgraph.png` file. How many times is `fib0` called?. We can do better than that by memoizing the functions. Uncomment the commented lines and regenerate the images. How many times are we calling each `fibN` function now?  
    将代码放入一个文件中并使其可执行。安装先决条件： [`pycallgraph`](https://lewiscowles1986.github.io/py-call-graph/) 和 [`graphviz`](http://graphviz.org/) 。（如果您可以运行 `dot` ，则说明您已经安装了 GraphViz。）使用 `pycallgraph graphviz -- ./fib.py` 运行代码，并检查 `pycallgraph.png` 文件。fib0 被调用了多少次？我们可以通过记忆化函数来提高效率。取消注释注释行并重新生成图像。现在 `fibN` `fib0` 被调用了多少次？
    
7.  A common issue is that a port you want to listen on is already taken by another process. Let’s learn how to discover that process pid. First execute `python -m http.server 4444` to start a minimal web server listening on port `4444`. On a separate terminal run `lsof | grep LISTEN` to print all listening processes and ports. Find that process pid and terminate it by running `kill <PID>`.  
    常见问题是您想要监听的端口已被其他进程占用。让我们学习如何找到该进程的进程 ID (PID)。首先执行 `python -m http.server 4444` 启动一个监听 `4444` 端口的最小 Web 服务器。在另一个终端中运行 `lsof | grep LISTEN` 以打印所有监听的进程和端口。找到该进程的 PID，然后运行 `kill <PID>` 将其终止。
    
8.  Limiting a process’s resources can be another handy tool in your toolbox. Try running `stress -c 3` and visualize the CPU consumption with `htop`. Now, execute `taskset --cpu-list 0,2 stress -c 3` and visualize it. Is `stress` taking three CPUs? Why not? Read [`man taskset`](https://www.man7.org/linux/man-pages/man1/taskset.1.html). Challenge: achieve the same using [`cgroups`](https://www.man7.org/linux/man-pages/man7/cgroups.7.html). Try limiting the memory consumption of `stress -m`.  
    限制进程资源是另一个实用的工具 `stress` 尝试运行 `stress -c 3` 并使用 `htop` 可视化 CPU 使用情况。现在，执行 \` `taskset --cpu-list 0,2 stress -c 3` \` 并进行可视化。\`stress\` 是否占用了三个 CPU？为什么？请阅读 [`man taskset`](https://www.man7.org/linux/man-pages/man1/taskset.1.html) 。挑战：使用 [`cgroups`](https://www.man7.org/linux/man-pages/man7/cgroups.7.html) 实现相同的目标。尝试限制 `stress -m` 的内存消耗。
    
9.  (Advanced) The command `curl ipinfo.io` performs a HTTP request and fetches information about your public IP. Open [Wireshark](https://www.wireshark.org/) and try to sniff the request and reply packets that `curl` sent and received. (Hint: Use the `http` filter to just watch HTTP packets).  
    （进阶）命令 `curl ipinfo.io` 会执行一个 HTTP 请求并获取你的公网 IP 地址信息。打开 [Wireshark](https://www.wireshark.org/) 并尝试抓取 `curl` 发送和接收的请求和回复数据包。（提示：使用 `http` 过滤器仅监视 HTTP 数据包）。

* * *