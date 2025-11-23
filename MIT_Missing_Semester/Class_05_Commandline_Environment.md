---
date: 2025-11-14
tags:
  - Shell
---
tmux 常用命令行：[https://gist.github.com/MohamedAlaa/2961058](https://gist.github.com/MohamedAlaa/2961058)


## Job Control

### killing a process

shell 使用一种称为信号（signal） 的 UNIX 通信机制来向进程传递信息。大多数情况下，您可以按 Ctrl-C 来停止命令.按下 Ctrl-C 会提示 shell 向进程发送 SIGINT 信号。

这是一个最简单的 python 程序示例，它可以捕获 sigint 并忽略它，不再停止运行。现在，我们可以通过输入 ctrl-\ 来使用 sigquit 信号终止此程序。

```python
#!/usr/bin/env python
import signal, time

def handler(signum, time):
    print("\nI got a SIGINT, but I am not stopping")

signal.signal(signal.SIGINT, handler)
i = 0
while True:
    time.sleep(.1)
    print("\r{}".format(i), end="")
    i += 1
```

可以看到，在使用python3运行这个python脚本时，并不会终止进程，需要使用 Ctrl-\ 来进行终止。

```bash
tulei@tulei:~/test_0$ python3 sigint.py 
670^C
I got a SIGINT, but I am not stopping
680^C
I got a SIGINT, but I am not stopping
691^C
I got a SIGINT, but I am not stopping
768^_
1034^\Quit (core dumped)
```

虽然 SIGINT 和 SIGQUIT 通常都与终端相关的请求有关，但更通用的用于请求进程优雅退出的信号是 SIGTERM 信号。要发送此信号，我们可以使用 kill 命令，语法为```kill -TERM <PID> ``` 。

### 暂停和后台进程

除了终止进程之外，信号还可以执行其他操作。例如， SIGSTOP 信号可以暂停进程。在终端中，按下 Ctrl-Z 会提示 shell 发送 SIGTSTP 信号，然后，我们可以分别使用 fg 或 bg 在前台或后台继续执行暂停的作业。

```bash
tulei@tulei:~/test_0$ python3 sigint.py 
20^Z
[1]+  Stopped                 python3 sigint.py
tulei@tulei:~/test_0$ fg
python3 sigint.py
34^Z
[1]+  Stopped                 python3 sigint.py
tulei@tulei:~/test_0$ bg
[1]+ python3 sigint.py &
72gei@tulei:~/test_0$ 
python3 sigint.py
95^Z
[1]+  Stopped                 python3 sigint.py
tulei@tulei:~/test_0$ 
```

按bg后，进入后台执行的作业可以再次按fg，让他来到前台。在后台运行的程序是不能被 CTRL + z , Ctrl + \ 等信号关闭的。

jobs 命令会列出与当前终端会话关联的未完成作业。您可以使用作业的进程 ID (PID) 来引用这些作业（可以使用 pgrep 查找 PID）。更直观的方式是，您还可以使用百分号 (%) 后跟作业编号（由 jobs 显示）来引用进程。要引用最后一个后台作业，您可以使用特殊参数 $!

```bash
tulei@tulei:~/test_0$ jobs
tulei@tulei:~/test_0$ python3 sigint.py 
14^Z
[1]+  Stopped                 python3 sigint.py
tulei@tulei:~/test_0$ jobs
[1]+  Stopped                 python3 sigint.py
tulei@tulei:~/test_0$ bg
[1]+ python3 sigint.py &
160python3 sigint.py 
208^Z
[2]+  Stopped                 python3 sigint.py
240gi@tulei:~/test_0$ 
[2]+ python3 sigint.py &
283si@tulei:~/test_0$ 
[1]-  Running                 python3 sigint.py &
[2]+  Running                 python3 sigint.py &
```

当你按下 %1 时，会把id为1的任务从后台移到前台。如果他之前被暂停了，也会被重新启动。

还有一点，当你使用jobs 来查看还没结束的程序时，```& ``` 符号表示这个程序存在于后台。在运行程序时你也可以加上此后缀，来表示将程序放在后台运行。

```bash
tulei@tulei:~/test_0$ jobs
tulei@tulei:~/test_0$ python3 sigint.py 
39^Z
[1]+  Stopped                 python3 sigint.py
tulei@tulei:~/test_0$ bg
[1]+ python3 sigint.py &
82sei@tulei:~/test_0$ 
[1]+  Running                 python3 sigint.py &
1681i@tulei:~/test_0$ 
python3 sigint.py
186^\Quit (core dumped)
tulei@tulei:~/test_0$ 
```

请注意，后台进程仍然是终端的子进程，如果您关闭终端，它们将会终止（这将发送另一个信号 SIGHUP ）。为了防止这种情况发生，您可以使用 nohup （一个忽略 SIGHUP 的包装器）运行程序，或者如果进程已经启动，则使用 disown 。此外，您还可以使用终端复用器terminal multiplexers，我们将在下一节中看到。

下边示例中，任务2使用了nohup,任务一没有使用，我们可以看到，任务一在收到 kill -HUP 时就停止了，而任务二就算收到了 kill -HUP 依然会忽略他，继续运行。

```bash
tulei@tulei:~/test_0$ jobs
tulei@tulei:~/test_0$ python3 sigint.py 
196^Z
[1]+  Stopped                 python3 sigint.py
tulei@tulei:~/test_0$ nohup python3 sigint.py 
nohup: ignoring input and appending output to 'nohup.out'
^Z
[2]+  Stopped                 nohup python3 sigint.py
tulei@tulei:~/test_0$ jobs
[1]-  Stopped                 python3 sigint.py
[2]+  Stopped                 nohup python3 sigint.py
tulei@tulei:~/test_0$ kill -HUP %1
[1]-  Hangup                  python3 sigint.py
tulei@tulei:~/test_0$ jobs
[2]+  Stopped                 nohup python3 sigint.py
tulei@tulei:~/test_0$ kill -HUP %2
tulei@tulei:~/test_0$ jobs
[2]+  Running                 nohup python3 sigint.py &
tulei@tulei:~/test_0$ 
```

nohup 运行的命令可以被 Ctrl + \ 来终止，因为 nohup 只是让程序忽略了 kill -HUP 信号。

```bash
tulei@tulei:~/test_0$ jobs
[2]+  Running                 nohup python3 sigint.py &
tulei@tulei:~/test_0$ %2
nohup python3 sigint.py
^\Quit (core dumped)
tulei@tulei:~/test_0$ jobs
tulei@tulei:~/test_0$ 
```

kill -STOP 也可以作为暂停符号来替代 Ctrl + Z

SIGKILL 信号比较特殊，因为它无法被进程捕获，并且会立即终止进程。但是，它也可能带来一些不良的副作用，例如留下孤儿进程。（也就是，这个进程还有很多子进程在运行，如果你只关掉了父进程，但是子进程依旧在运行）

您可以点击此处或输入 man signal 或 kill -l 来了解更多关于这些信号和其他信号的信息。

---
## terminal multiplexers

像 tmux 这样的终端复用器允许你使用 panes and tabs 来复用多个终端窗口，从而可以同时与多个 shell sessions进行交互。此外，终端复用器还允许你分离当前的终端会话，并在稍后重新连接。这可以大大提升你与远程机器交互时的工作流程，因为它避免了使用 nohup 等类似技巧。

在tmux中运行的程序不会因为关闭外部终端（比如你通过SSH连接的客户端，或者本地终端窗口）而停止。tmux是一个终端多路复用器，它会在后台运行，保持会话中的程序继续执行。

当你断开与tmux的连接（比如关闭终端窗口）时，tmux会话会在后台继续运行，包括其中正在运行的程序。你可以重新连接到这个tmux会话来查看程序的输出和交互。

他的一些常用命令在下边：有人会将tmux 的 Ctrl +b 映射为 Ctrl + a.

tmux expects you to know its keybindings, and they all have the form `<C-b> x` where that means (1) press Ctrl+b, (2) release Ctrl+b, and then (3) press x. tmux has the following hierarchy of objects:

- **Sessions** - a session is an independent workspace with one or more windows
	- `tmux` starts a new session.
	- `tmux new -s NAME` starts it with that name.
	- `tmux ls` lists the current sessions
	- Within `tmux` typing `<C-b> d` detaches the current session
	- `tmux a` attaches the last session. You can use `-t` flag to specify which
- **Windows** - Equivalent to tabs in editors or browsers, they are visually separate parts of the same session
	- `<C-b> c` Creates a new window. To close it you can just terminate the shells doing `<C-d>`
	- `<C-b> N` Go to the N th window. Note they are numbered
	- `<C-b> p` Goes to the previous window
	- `<C-b> n` Goes to the next window
	- `<C-b> ,` Rename the current window
	- `<C-b> w` List current windows
- **Panes** - Like vim splits, panes let you have multiple shells in the same visual display.
	- `<C-b> "` Split the current pane horizontally
	- `<C-b> %` Split the current pane vertically
	- `<C-b> <direction>` Move to the pane in the specified direction. Direction here means arrow keys.
	- `<C-b> z` Toggle zoom for the current pane
	- `<C-b> [` Start scrollback. You can then press `<space>` to start a selection and `<enter>` to copy that selection.
	- `<C-b> <space>` Cycle through pane arrangements.

For further reading, [here](https://hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) is a quick tutorial on tmux and [this](http://linuxcommand.org/lc3_adv_termmux.php) has a more detailed explanation that covers the original screen command. You might also want to familiarize yourself with [screen](https://www.man7.org/linux/man-pages/man1/screen.1.html), since it comes installed in most UNIX systems.

---
## Aliases

输入包含大量标志或冗长选项的长命令可能会很麻烦。因此，大多数 shell 都支持别名 。shell 别名是另一个命令的简写形式，shell 会自动将其替换为你实际使用的命令。例如，bash 中的别名结构如下：

```bash
alias alias_name="command_to_alias arg1 arg2"
```

这里等号左右没有空格，和一般shell的变量命名相同。

```bash
# Make shorthands for common flags
alias ll="ls -lh"

# Save a lot of typing for common commands
alias gs="git status"
alias gc="git commit"
alias v="vim"

# Save you from mistyping
alias sl=ls

# Overwrite existing commands for better defaults
alias mv="mv -i"           # -i prompts before overwrite
alias mkdir="mkdir -p"     # -p make parent dirs as needed
# 在shell那一节，使用df -Th 可以输出人类可读的磁盘挂载
alias df="df -h"           # -h prints human readable format

# Alias can be composed
alias la="ls -A"
alias lla="la -l"

# To ignore an alias run it prepended with \
\ls
# Or disable an alias altogether with unalias
unalias la

# To get an alias definition just call it with alias
alias ll
# Will print ll='ls -lh'
```

这种映射并不会永久从在，而如果每次打开新的terminal都要再输入一遍也不方便，所以下一节会介绍一种方法，可以解决这个问题。

在终端中对 bashrc 修改后不会立刻生效 需要使用 source .bashrc 来重新加载配置文件，才会生效。

---
## Dotfiles

对于 bash ，在大多数系统中，编辑 .bashrc 或 .bash_profile 文件即可。您可以在这里添加希望在启动时运行的命令，例如我们刚才提到的别名，或者修改 PATH 环境变量。实际上，许多程序都会要求您在 shell 配置文件中添加类似 `export PATH="$PATH:/path/to/program/bin"` 的行，以便找到它们的二进制文件。

为了让不同机器可以共用一套配置文件，实际上你可以使用 if else 等语句来进行细微的处理：

```bash
if [[ "$(uname)" == "Linux" ]]; then {do_something}; fi

# Check before using shell-specific features
if [[ "$SHELL" == "zsh" ]]; then {do_something}; fi

# You can also make it machine-specific
if [[ "$(hostname)" == "myServer" ]]; then {do_something}; fi
```

甚至你也可以让不同的软件 比如 bash 与 zsh ，共享同一套 Alianses 规则。

```bash
# Test if ~/.aliases exists and source it
if [ -f ~/.aliases ]; then
    source ~/.aliases
fi
```

---
## Remote Machines

如今，程序员在日常工作中越来越频繁地使用远程服务器。如果您需要使用远程服务器部署后端软件，或者需要一台计算能力更强的服务器，最终都会用到安全外壳协议 (SSH)。与大多数工具一样，SSH 具有高度可配置性，因此值得学习。

### Executing commands 

实际上可以通过对管道位置的调整，来调整传输与执行所需要的花费。

An often overlooked feature of ssh is the ability to run commands directly. `ssh foobar@server ls` will execute `ls` in the home folder of foobar. It works with pipes, so `ssh foobar@server ls | grep PATTERN` will grep locally the remote output of `ls` and `ls | ssh foobar@server grep PATTERN` will grep remotely the local output of `ls`.

### Key generation

密钥生成可以使用下边的命令：

```bash
ssh-keygen -a 100 -t ed25519 -f ~/.ssh/id_ed25519
```

使用如下命令检查是否已经有生成的密钥：

```bash
 ssh-keygen -y -f /path/to/key
```

`ssh+tee`, the simplest is to use ssh command execution and STDIN input by doing `cat localfile | ssh remote_server tee serverfile`. Recall that `tee` writes the output from STDIN into a file.
`scp` when copying large amounts of files/directories, the secure copy `scp` command is more convenient since it can easily recurse over paths. The syntax is `scp path/to/local_file remote_host:path/to/remote_file`
`rsync` improves upon scp by detecting identical files in local and remote, and preventing copying them again. It also provides more fine grained control over symlinks, permissions and has extra features like the --partial flag that can resume from a previously interrupted copy. `rsync` has a similar syntax to `scp`.

---

## Exercises

### Job control
##### 1
-  From what we have seen, we can use some ps aux | grep commands to get our jobs’ pids and then kill them, but there are better ways to do it. Start a sleep 10000 job in a terminal, background it with Ctrl-Z and continue its execution with bg. Now use pgrep to find its pid and pkill to kill it without ever typing the pid itself. (Hint: use the -af flags).

```bash
tulei@tulei:~$ jobs
tulei@tulei:~$ sleep 10000
^Z
[1]+  Stopped                 sleep 10000
tulei@tulei:~$ bg
[1]+ sleep 10000 &
tulei@tulei:~$ pgrep -af sleep
174393 sleep 10000
tulei@tulei:~$ pkill sleep
[1]+  Terminated              sleep 10000
tulei@tulei:~$ jobs
```

##### 2
- Say you don’t want to start a process until another completes. How would you go about it? In this exercise, our limiting process will always be sleep 60 &. One way to achieve this is to use the wait command. Try launching the sleep command and having an ls wait until the background process finishes.

 在后台工作的程序并不会阻碍前台程序的工作，而在前台运行的程序却会造成阻碍：
 
```bash
tulei@tulei:~$ jobs
tulei@tulei:~$ sleep 60 
ls
tulei@tulei:~$ ls
snap  test_0
tulei@tulei:~$ sleep 60 &
[1] 175471
tulei@tulei:~$ ls
snap  test_0
tulei@tulei:~$ jobs
[1]+  Running                 sleep 60 &
```

```bash
tulei@tulei:~$ sleep 60 &
[1] 175752
tulei@tulei:~$ wait 175752
ls
[1]+  Done                    sleep 60
tulei@tulei:~$ ls
snap  test_0
tulei@tulei:~$ jobs
tulei@tulei:~$ 
```

 - However, this strategy will fail if we start in a different bash session, since wait only works for child processes. One feature we did not discuss in the notes is that the kill command’s exit status will be zero on success and nonzero otherwise. kill -0 does not send a signal but will give a nonzero exit status if the process does not exist. Write a bash function called pidwait that takes a pid and waits until the given process completes. You should use sleep to avoid wasting CPU unnecessarily.

函数：
```bash
#!/bin/sh
pidwait() {
    pid=$1

    while kill -0 "$pid" 2>/dev/null; do
        sleep 5
        echo "The process $pid is still running..."
    done

    echo "Process $pid has completed."
}
```

运行效果：
```bash
tulei@tulei:~/test_0$ sleep 30 &
[1] 177144
tulei@tulei:~/test_0$ pidwait $!
The process 177144 is still running...
The process 177144 is still running...
The process 177144 is still running...
The process 177144 is still running...
The process 177144 is still running...
[1]+  Done                    sleep 30
The process 177144 is still running...
Process 177144 has completed.
tulei@tulei:~/test_0$ 
```
### Terminal multiplexer
 
- Follow this tmux[tutorial](https://hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) and then learn how to do some basic customizations following these [steps](https://hamvocke.com/blog/a-guide-to-customizing-your-tmux-conf/).
### Aliases

- Create an alias dc that resolves to cd for when you type it wrong.

```bash
alias dc="cd"
```

- Run `history | awk '{$1="";print substr($0,2)}' | sort | uniq -c | sort -n | tail -n 10` to get your top 10 most used commands and consider writing shorter aliases for them. Note: this works for Bash; if you’re using ZSH, use history 1 instead of just history.

```bash
tulei@tulei:~$ history | awk '{$1="";print substr($0,2)}' | sort | uniq -c | sort -n | tail -n 10
     10 cd ..
     10 tmux
     10 tree
     11 %1
     11 python3 sigint.py
     12 bg
     12 exit
     34 vim
     36 jobs
    145 ls
```

### Dotfiles
 
- Let’s get you up to speed with dotfiles.

Create a folder for your dotfiles and set up version control.
Add a configuration for at least one program, e.g. your shell, with some customization (to start off, it can be something as simple as customizing your shell prompt by setting $PS1).
Set up a method to install your dotfiles quickly (and without manual effort) on a new machine. This can be as simple as a shell script that calls ln -s for each file, or you could use a specialized utility.
Test your installation script on a fresh virtual machine.
Migrate all of your current tool configurations to your dotfiles repository.
Publish your dotfiles on GitHub.

### Remote Machines

- Install a Linux virtual machine (or use an already existing one) for this exercise. If you are not familiar with virtual machines check out this tutorial for installing one.

- Go to ~/.ssh/ and check if you have a pair of SSH keys there. If not, generate them with ssh-keygen -a 100 -t ed25519. It is recommended that you use a password and use ssh-agent , more info here.
Edit .ssh/config to have an entry as follows

 Host vm
     User username_goes_here
     HostName ip_goes_here
     IdentityFile ~/.ssh/id_ed25519
     LocalForward 9999 localhost:8888
Use ssh-copy-id vm to copy your ssh key to the server.
Start a webserver in your VM by executing python -m http.server 8888. Access the VM webserver by navigating to http://localhost:9999 in your machine.
Edit your SSH server config by doing sudo vim /etc/ssh/sshd_config and disable password authentication by editing the value of PasswordAuthentication. Disable root login by editing the value of PermitRootLogin. Restart the ssh service with sudo service sshd restart. Try sshing in again.
(Challenge) Install mosh in the VM and establish a connection. Then disconnect the network adapter of the server/VM. Can mosh properly recover from it?
(Challenge) Look into what the -N and -f flags do in ssh and figure out a command to achieve background port forwarding.















