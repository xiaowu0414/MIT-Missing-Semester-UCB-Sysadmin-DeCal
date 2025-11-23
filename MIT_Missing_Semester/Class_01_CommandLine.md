---
date: 2025-11-09
tags:
  - Shell
---
简单介绍linux: [linuxjourney.com](https://labex.io/linuxjourney) 
命令行的一些实用技巧：[the-art-of-command-line](https://github.com/jlevy/the-art-of-command-line/blob/master/README-zh.md)
命令的解释：[https://explainshell.com/](https://explainshell.com/)
命令行中快速移动：[http://teohm.com/blog/shortcuts-to-move-faster-in-bash-command-line/](http://teohm.com/blog/shortcuts-to-move-faster-in-bash-command-line/)



因为自己计算机目前是 Windows，所以这节课我会通过 wsl2，利用 Windows 上的 Ubuntu 22.04子系统来完成。

ssh部分链接的是VMware上的Ubuntu24.04

对 wsl2 不了解的可以去看：[https://www.bilibili.com/video/BV1ce46ziE1r/?spm_id_from=333.337.search-card.all.click](https://www.bilibili.com/video/BV1ce46ziE1r/?spm_id_from=333.337.search-card.all.click)
## Linux 简单介绍

在介绍 Shell 之前，先对 Linux 做简单介绍，以便后续学习

Linux 是在GNU 的基础上 完善得到的，GNU当时并没有及时完成自己的Kernal

The kernel is the core component of an operating system. It acts as a bridge, allowing the hardware to communicate with the software. The kernel manages system resources, such as the CPU, memory, and peripheral devices. Essentially, the kernel controls everything that happens on your system.

A Linux system is divided into three main parts:

- **Hardware** - This includes the physical components of your computer, such as the CPU, memory, and storage devices.
- **Linux Kernel** - As the core of the operating system, the kernel manages the hardware and facilitates communication between software and hardware.
- **User Space** - This is the environment where you, the user, interact with the system through applications and command-line interfaces.

### Linux 系统目录结构：

![](content/
BasicTools/MIT_Missing_Semester/static/LFQrbXOJzoxxowxAlglcLPX8nlg.png)

```shell
tulei@tulei:~$ cd /
tulei@tulei:/$ ls
bin   dev  home  lib    lib64   lost+found  mnt  proc  run   snap  sys  usr
boot  etc  init  lib32  libx32  media       opt  root  sbin  srv   tmp  var
```

 - bin：全部为执行命令，执行程序，（Linux 一般绿色默认为可执行程序）bin 中为普通用户也可执行的命令
- boot：引导分区，用来装载开机启动项的一些东西
- dev：一般存放一些存储介质，Linux 将硬件分区设备等都表示为文件
- etc：系统的与服务的一些配置文件，该根目录是系统上最重要的根目录之一。etc 文件夹（etcetera 的缩写）是存储操作系统使用的系统文件的常见位置。
- home：普通用户家目录
- lib：存放一些库文件
- lost+found：表示被挂载的文件夹
- media    mnt：挂载外部存储介质
- opt：一些大的应用程序
- proc：系统开机前不存在，存放一些临时文件
- root：管理员的家目录
- / ：根目录：与/home 目录不同，**/root** 文件夹实际上是“root”系统用户的主目录。除了理解这是“root”用户的主目录之外，该文件夹没有其他任何内容
- sbin：root 账号可执行的命令
- selinux：系统防护
- srv：系统存放的一些目录
- sys：不是真实存在的文件，而是一些内核参数
- tmp（重要）：拥有对源代码的编译权限，一般通过 shell 把 exp 传入根目录后移动到此编译。这是 Linux 安装中的唯一根目录。**/tmp 目录是“ temporary** ”的缩写，是易失性目录，用于存储只需要访问一次或两次的数据。与计算机上的内存类似，一旦计算机重新启动，该文件夹的内容就会被清除。对我们进行渗透测试有用的是，默认情况下任何用户都可以写入此文件夹。这意味着一旦我们能够访问一台机器，它就可以作为存储枚举脚本等内容的好地方。
- usr（重要）：源代码安装程序
- var（重要）：日志文件夹，网站根目录，网站日志。“/var”目录（“var”是变量数据的缩写）是 Linux 安装中的主要根文件夹之一。此文件夹存储系统上运行的服务或应用程序经常访问或写入的数据。例如，正在运行的服务和应用程序的日志文件写入此处（**/var/log**），或者不一定与特定用户关联的其他数据（即数据库等）。

### **Linux 将硬件分区设备等都表示为文件:**

![](content/BasicTools/MIT_
Missing_Semester/static/XClxbVRuUojVLbxe8Yrc0aEynpe.png)
![](content/BasicTools/MIT_Missing_Semester/static/SNbUbnBd7oAO2VxUZR2cS9Tkn1e.png)
查看系统目录结构和磁盘分区：

```typescript
tulei@tulei:~$ cd /
tulei@tulei:/$ ls
bin   dev  home  lib    lib64   lost+found  mnt  proc  run   snap  sys  usr
boot  etc  init  lib32  libx32  media       opt  root  sbin  srv   tmp  var
tulei@tulei:/$ ls /dev
autofs           hvc2          loop6       ram11   sdc       tty13  tty3   tty46  tty62        vcs2   vcsu6
block            hvc3          loop7       ram12   sdd       tty14  tty30  tty47  tty63        vcs3   vfio
bsg              hvc4          mapper      ram13   sg0       tty15  tty31  tty48  tty7         vcs4   vga_arbiter
btrfs-control    hvc5          mcelog      ram14   sg1       tty16  tty32  tty49  tty8         vcs5   vhost-net
char             hvc6          mem         ram15   sg2       tty17  tty33  tty5   tty9         vcs6   vhost-vsock
console          hvc7          mptctl      ram2    sg3       tty18  tty34  tty50  ttyS0        vcsa   virtio-ports
core             hwrng         mqueue      ram3    shm       tty19  tty35  tty51  ttyS1        vcsa1  vport0p0
cpu_dma_latency  initctl       net         ram4    snapshot  tty2   tty36  tty52  ttyS2        vcsa2  vport0p1
cuse             kmsg          null        ram5    snd       tty20  tty37  tty53  ttyS3        vcsa3  vport0p2
disk             kvm           nvram       ram6    stderr    tty21  tty38  tty54  ttyS4        vcsa4  vsock
dxg              log           ppp         ram7    stdin     tty22  tty39  tty55  ttyS5        vcsa5  zero
fd               loop-control  ptmx        ram8    stdout    tty23  tty4   tty56  ttyS6        vcsa6
full             loop0         ptp0        ram9    tty       tty24  tty40  tty57  ttyS7        vcsu
fuse             loop1         ptp_hyperv  random  tty0      tty25  tty41  tty58  uinput       vcsu1
hpet             loop2         pts         rtc     tty1      tty26  tty42  tty59  urandom      vcsu2
hugepages        loop3         ram0        rtc0    tty10     tty27  tty43  tty6   userfaultfd  vcsu3
hvc0             loop4         ram1        sda     tty11     tty28  tty44  tty60  vcs          vcsu4
hvc1             loop5         ram10       sdb     tty12     tty29  tty45  tty61  vcs1         vcsu5
```

使用 df -Th 可以显示磁盘分区挂载：

```typescript
tulei@tulei:/$ df -Th
Filesystem     Type     Size  Used Avail Use% Mounted on
none           overlay  4.8G     0  4.8G   0% /usr/lib/modules/6.6.87.2-microsoft-standard-WSL2
none           tmpfs    4.8G  4.0K  4.8G   1% /mnt/wsl
drivers        9p       937G  567G  371G  61% /usr/lib/wsl/drivers
/dev/sdd       ext4    1007G  1.8G  954G   1% /
none           tmpfs    4.8G   80K  4.8G   1% /mnt/wslg
none           overlay  4.8G     0  4.8G   0% /usr/lib/wsl/lib
rootfs         rootfs   4.8G  2.7M  4.8G   1% /init
none           tmpfs    4.8G  508K  4.8G   1% /run
none           tmpfs    4.8G     0  4.8G   0% /run/lock
none           tmpfs    4.8G     0  4.8G   0% /run/shm
none           overlay  4.8G   76K  4.8G   1% /mnt/wslg/versions.txt
none           overlay  4.8G   76K  4.8G   1% /mnt/wslg/doc
C:\            9p       937G  567G  371G  61% /mnt/c
tmpfs          tmpfs    4.8G  4.0K  4.8G   1% /run/user/1000
```


---


## Shell 命令简单演示

1. shell是一个统称，它是一种命令行解释器，提供了用户与操作系统内核交互的界面。
2. bash（Bourne-Again SHell）是shell的一种，是许多Linux发行版和macOS（在较新版本中已改为zsh，但bash仍常见）默认的shell。

当你第一次打开时，屏幕上一般会有以下内容：

```bash
username@hostname:current_directory$
```

### Linux 命令

Shell 是用 C 语言编写好的程序，用户可以用它来使用 Linux

同时他也是一种程序设计语言，可以用它来编写脚本（Script）

接下来所演示的诸如 echo 等在 Linux 中直接输入的命令，实际上是他人已经提前编写好的内容，你可以在下边的位置中找到他们：/bin/bash（在后边会学习 bash 脚本的编写）

一般来说，我们的 Linux 命令分为两类：

- 内部命令：属于 Shell 解释器的一部分
- 外部命令：独立于 Shell 解释器之外的程序文件

Linux 命令的通用命令格式  ：命令字  [选项]  [参数]

选项及参数含义

选项：用于调节命令的具体功能

参数：命令操作的对象，如文件、目录名等

---
### 查询命令用法 

#### type
会告诉你命令的类型

```bash
tulei@tulei:~$ type cd
cd is a shell builtin
tulei@tulei:~$ type ll
ll is aliased to `ls -alF'
tulei@tulei:~$ type ls
ls is aliased to `ls --color=auto'
```


在开始之前，先介绍 --help 以及 man 。在你遇到任何困难时，查看帮助文档往往可以快速解决问题

#### help

help command·可以打开对应的帮助文档；

```bash
tulei@tulei:~$ help pwd
pwd: pwd [-LP]
    Print the name of the current working directory.
    
    Options:
      -L        print the value of $PWD if it names the current working
                directory
      -P        print the physical directory, without any symbolic links
    
    By default, `pwd' behaves as if `-L' were specified.
    
    Exit Status:
    Returns 0 unless an invalid option is given or the current directory
    cannot be read.
```

command --help 也可以：

```shell
tulei@tulei:~$ pwd --help
pwd: pwd [-LP]
    Print the name of the current working directory.
    
    Options:
      -L        print the value of $PWD if it names the current working
                directory
      -P        print the physical directory, without any symbolic links
    
    By default, `pwd' behaves as if `-L' were specified.
    
    Exit Status:
    Returns 0 unless an invalid option is given or the current directory
    cannot be read.
```

#### man

会直接打开完整文档：（按 q 即可退出文档的查看）

```shell
tulei@tulei:~$ man pwd
```

#### tldr 

我已经为Ubuntu下载了开源的 `tldr` ,他会输出更具体一点的示例，方便阅读与使用

```bash
tulei@tulei:~$ tldr pwd

  pwd

  Print the name of current/working directory.
  More information: https://www.gnu.org/software/coreutils/manual/html_node/pwd-invocation.html.

  - Print the current directory:
    pwd

  - Print the current directory, and resolve all symlinks (i.e. show the "physical" path):
    pwd --physical

  - Display help:
    pwd --help

Found 1 page with the same name under the platform: windows.
```

#### whatis 

如果你觉得前边的文档内容都太长了，想要简短了提醒，可以使用whatis，他会输出help文档中 name 边上的一句话：

```bash
tulei@tulei:~$ whatis pwd
pwd (1)              - print name of current/working directory
```

#### which

告诉你当前使用的命令在哪里可以找到，（当你同一个命令有很多可能的路径时，这个命令是很有用的）
```bash
ulei@tulei:~$ which tldr
/snap/bin/tldr
```

which -a : 输出你所有下载过的 命令的地址
```bash
tulei@tulei:~$ which -a tldr
/snap/bin/tldr
/snap/bin/tldr
/snap/bin/tldr
/snap/bin/tldr
```

which 只能输出 $PATH 中的命令，如果你的命令不在系统路径中，可以使用如下命令将其加入系统路径，推荐将其写在 bashrc 中：

```bash
export PATH=~/.local/bin:$PATH
```

#### whereis

查找命令的 可执行文件，操作手册，以及来源

```bash
tulei@tulei:~$ whereis pwd
pwd: /usr/bin/pwd /usr/share/man/man1/pwd.1.gz
tulei@tulei:~$ which pwd
/usr/bin/pwd
tulei@tulei:~$ which -a pwd
/usr/bin/pwd
/bin/pwd
```

-b ： 只搜索 二进制 可执行文件
-m : 只搜索 手册
-s : 搜索原文件

---
### 基本信息的查看与简单计算：

#### date:
```shell
tulei@tulei:~$ date
Sat Nov  8 14:42:38 CST
```

#### expr:

数字和符号中间需要有空格
使用乘法时，需要用转义字符 或者 `''` 来限制 `*` 的含义
```bash
tulei@tulei:~$ expr 5 + 3
8
tulei@tulei:~$ expr 5+3
5+3
tulei@tulei:~$ expr 16 - 9
7
tulei@tulei:~$ expr 60 / 5
12
tulei@tulei:~$ expr 3 * 11
expr: syntax error: unexpected argument ‘snap’
tulei@tulei:~$ expr 3 \* 11
33
tulei@tulei:~$ expr 3 "*" 11
33
tulei@tulei:~$ expr 3 '*' 11
33
```

#### figlet:
```bash
tulei@tulei:~$ figlet "I Love Linux"
 ___   _                     _     _                  
|_ _| | |    _____   _____  | |   (_)_ __  _   ___  __
 | |  | |   / _ \ \ / / _ \ | |   | | '_ \| | | \ \/ /
 | |  | |__| (_) \ V /  __/ | |___| | | | | |_| |>  < 
|___| |_____\___/ \_/ \___| |_____|_|_| |_|\__,_/_/\_\
                                                      
tulei@tulei:~$ figlet -f slant "I Love Linux"
    ____   __                       __    _                 
   /  _/  / /   ____ _   _____     / /   (_)___  __  ___  __
   / /   / /   / __ \ | / / _ \   / /   / / __ \/ / / / |/_/
 _/ /   / /___/ /_/ / |/ /  __/  / /___/ / / / / /_/ />  <  
/___/  /_____/\____/|___/\___/  /_____/_/_/ /_/\__,_/_/|_|  
                                                            
tulei@tulei:~$ 
```

#### Clear:

或者使用 CTRL+L 也是可以达到相同效果的
```shell
#清空界面
```
#### whoami:

```shell
#输出用户身份
tulei@tulei:~$ whoami
tulei
```

#### id:
- `uid`: Your User ID (a unique numerical identifier).
- `gid`: Your primary Group ID.
- `groups`: All the groups you are a member of.

```bash
tulei@tulei:~$ id
uid=1000(tulei) gid=1000(tulei) groups=1000(tulei),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),117(netdev)

# 你也可以查看其他用户id
tulei@tulei:~$ id root
uid=0(root) gid=0(root) groups=0(root)
```

#### uname:
```shell
#查看系统信息
#uname 查看系统
#uname -r 查看版本号
#uname -a 查看系统详细信息
tulei@tulei:~$ uname
Linux
tulei@tulei:~$ uname -r
6.6.87.2-microsoft-standard-WSL2
tulei@tulei:~$ uname -a
Linux tulei 6.6.87.2-microsoft-standard-WSL2 
#1 SMP PREEMPT_DYNAMIC Thu Jun  5 18:30:46 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
```
#### hostname:
```shell
#查看主机信息，并修改名称（只有root权限用户才可修改）
tulei@tulei:~$ hostname
tulei
tulei@tulei:~$ hostname testname
hostname: you must be root to change the host name
```

#### htop:

![](Pasted%20image%2020251120152613.png)
htop shows:

- Top: CPU and memory usage, as well as how long your computer has been running (uptime).
- Middle: A list of all the running programs (processes).
- Bottom: Options for interacting with htop.

记得按 q 退出

#### neofetch

![](Pasted%20image%2020251120152451.png)

#### du

- `du -sh` - displays disk usage (the flag `s` is for summarized form).  
    `du -sh` - 显示磁盘使用情况（标志 `s` 表示摘要表单）。
- `du -sh *` - displays the disk usage for every folder or file in that location.  
    `du -sh *` - 显示该位置每个文件夹或文件的磁盘使用情况。
- Another option is to do `du -s * | sort -n` which will sort the results based on the size from smallest to largest.  
    另一种选择是使用 `du -s * | 排序 -n`，它会根据大小从小到大排序结果。
- arrow up or arrow down, brings up the last commands. Space bar to go to next page.  
    向上箭头或向下箭头，会弹出最后的指令。空格键可以跳到下一页。

---

### 文件查看与分割

#### echo：

echo 这个程序会将他接收到的所有参数直接输出

```shell
tulei@tulei:~$ echo hello
hello
#因为shell的逻辑是：把你输入的第一个词作为程序，第二个词作为这个程序的参数（argument）
#所以要是输入的参数具有 “ ” 或者可能造成歧义的符号时，请使用'' 或""将他们括起来
tulei@tulei:~$ echo "Hello world"
Hello world
#当然也可以使用转义字符，来限制转义字符后边符号的意义
# 这里用转移字符将后边 space 意义限制为 space 而非用于分隔输入参数
tulei@tulei:~$ echo Hello\ world
Hello world
```

#### Echo $PATH 和 which：

计算机怎么知道他是程序呢？

一般来说，shell 解释器会有一些固定的程序，被存储在/bin 文件夹中

而对于外部命令，shell 第一时间在/bin 中没有找到对应的程序，就会去系统环境变量 PATH 中寻找

```shell
# echo $PATH会打印出你所配置的所有环境变量
tulei@tulei:~$ echo $PATH

#which echo 可以使用which查看echo这个脚本在哪里
tulei@tulei:~$ which echo
/usr/bin/echo
```

which 只能输出 $PATH 中的命令，如果你的命令不在系统路径中，可以使用如下命令将其加入系统路径，推荐将其写在 bashrc 中：

```bash
export PATH=~/.local/bin:$PATH
```

#### echo & cat：

echo 可以直接将文字写入文档

cat 可以查看文档内容
```sql
tulei@tulei:~$ cd ~
tulei@tulei:~$ ls
tulei@tulei:~$ echo hello > hello.txt
tulei@tulei:~$ ls
hello.txt
tulei@tulei:~$ cat hello.txt
hello
```

实际上cat 也可以将两个文件内容拼起来

```bash
tulei@tulei:~/test_1$ echo "hello" > exampel_1
tulei@tulei:~/test_1$ echo "world" > exampel_2
tulei@tulei:~/test_1$ cat exampel_1 exampel_2 > conbin
tulei@tulei:~/test_1$ cat conbin 
hello
world
```

cat -E 告诉cat在每行末尾加入 $ 分隔

```bash
tulei@tulei:~/test_1$ cat -E conbin 
hello$
world$
```

cat 还可以为文档内容加上行号，在下文中有介绍

#### head & tail

head 可以同时查看多个文件

```bash
tulei@tulei:~$ head -n 3 sample.txt  sample_cp.txt 
==> sample.txt <==
sample2.txt
The
quick

==> sample_cp.txt <==
sample2.txt
The
quick
```

使用head 和tail 可以查看指定的行的内容

```bash
# cat 为内容加上行号
tulei@tulei:~/test_2$ ls
hello
tulei@tulei:~/test_2$ cat hello
hello
tulei@tulei:~/test_2$ echo "hello again" >> hello 
tulei@tulei:~/test_2$ cat hello
hello
hello again
tulei@tulei:~/test_2$ cat -n hello
     1  hello
     2  hello again

# 使用head 和 tail
tulei@tulei:~/test_2$ head -n1 hello
hello
tulei@tulei:~/test_2$ head -n2 hello
hello
hello again
tulei@tulei:~/test_2$ tail -n1 hello
hello again
tulei@tulei:~/test_2$ tail -n2 hello
hello
hello again
```

可以使用 | 结合 head 与 tail 输出精确的行：

```bash
tulei@tulei:~/test_2$ echo "hello_3" >> hello
tulei@tulei:~/test_2$ cat hello 
hello
hello again
hello_3
tulei@tulei:~/test_2$ cat hello | head -n2 | tail -n1
hello again
```

使用 -c 可以查看指定字符
```bash
tulei@tulei:~/test_2$ head -c1 hello 
htulei@tulei:~/test_2$ head -c2 hello 
hetulei@tulei:~/test_2$ head -c 10 hello
hello
helltulei@tulei:~/test_2$ 
```

使用 tail -f 可以实现实时更新的效果

```
tail -f /var/log/syslog
```

#### cut

cut 可以按特定字符分隔文件，之后输出分割后的对应文件

```bash
tulei@tulei:~$ cat sample.txt 
ID,Name,Age,Email
1,John Doe,25,john.doe@email.com
2,Jane Smith,35,jane.smith@email.com
3,Lily Chen,30,lily.chen@email.com
4,Andy Brown,22,andy.brown@email.com
tulei@tulei:~$ cut -d ',' -f 2 ./sample.txt 
Name
John Doe
Jane Smith
Lily Chen
Andy Brown
```

利用 tail 可以从对应行开始输出，这里是从第二行开始（也就是去掉第一行）

```bash
tulei@tulei:~$ cut -d ',' -f 2 ./sample.txt | tail -n +2
John Doe
Jane Smith
Lily Chen
Andy Brown
```

输出多个分隔后的文件

```bash
tulei@tulei:~$ cat ./sample.txt 
ID,Name,Age,Email
1,John Doe,25,john.doe@email.com
2,Jane Smith,35,jane.smith@email.com
3,Lily Chen,30,lily.chen@email.com
4,Andy Brown,22,andy.brown@email.com

# 利用 ， 分隔
tulei@tulei:~$ cut -d ',' -f 2,4 ./sample.txt | tail -n +2
John Doe,john.doe@email.com
Jane Smith,jane.smith@email.com
Lily Chen,lily.chen@email.com
Andy Brown,andy.brown@email.com

# 利用 - 连续
tulei@tulei:~$ cut -d ',' -f 1-2,4 ./sample.txt | tail -n +2
1,John Doe,john.doe@email.com
2,Jane Smith,jane.smith@email.com
3,Lily Chen,lily.chen@email.com
4,Andy Brown,andy.brown@email.com
```

cut -c 按字符分隔

有时并没用特定的分隔符，就可以利用字节来分隔

```bash
tulei@tulei:~$ cat sample.txt 
ID,Name,Age,Email
1,John Doe,25,john.doe@email.com
2,Jane Smith,35,jane.smith@email.com
3,Lily Chen,30,lily.chen@email.com
4,Andy Brown,22,andy.brown@email.com
tulei@tulei:~$ cut -c 5 ./sample.txt 
a
h
n
l
d
```

```bash
tulei@tulei:~$ cat sample.txt 
ISBN     Title          Quantity
1234567890The Great Adv      100
2345678901Mystery in th       75
3456789012Cooking Basi       50
4567890123Science Exp        125
tulei@tulei:~$ cut -c 11-25 ./sample.txt 
itle          Q
The Great Adv  
Mystery in th  
Cooking Basi   
Science Exp    
```

#### paste

paste 和 cat 相似，但他不是链接文件，而是将文件中不同行合并为1行。

```bash
tulei@tulei:~$ cat sample.txt 
sample2.txt
The
quick
brown
fox

# 按行合并 
tulei@tulei:~$ paste -s sample.txt 
sample2.txt     The     quick   brown   fox

# 合并时删掉空格
tulei@tulei:~$ paste -d '' -s sample.txt 
sample2.txtThequickbrownfox
```

#### join & split

join 

依据第一列的编号，合并表格

```bash
tulei@tulei:~$ cat sample.txt 
1001 John Engineering
1002 Sarah Marketing
1003 Mike Sales
1004 Emily HR
1005 David Finance
tulei@tulei:~$ cat date.txt 
1001 75000
1002 65000
1003 70000
1004 60000
1005 80000
tulei@tulei:~$ join sample.txt date.txt 
1001 John Engineering 75000
1002 Sarah Marketing 65000
1003 Mike Sales 70000
1004 Emily HR 60000
1005 David Finance 80000
```

也可以专门指定合并的列(第一个文件的第一列，第一个文件的第三列，第二个文件的第二列，第一个文件的)

```bash
tulei@tulei:~$ join -o 1.2,1.3,2.2,1.1 sample.txt date.txt
John Engineering 75000 1001
Sarah Marketing 65000 1002
Mike Sales 70000 1003
Emily HR 60000 1004
David Finance 80000 1005
```

要是存在部分元素在另一个文件中并不存在，可以使用 -a 来指定完整展开的文件

```bash
tulei@tulei:~$ cat sample.txt 
1001 John Engineering
1002 Sarah Marketing
1003 Mike Sales
1004 Emily HR
1005 David Finance
1006 TEST
tulei@tulei:~$ join sample.txt date.txt 
1001 John Engineering 75000
1002 Sarah Marketing 65000
1003 Mike Sales 70000
1004 Emily HR 60000
1005 David Finance 80000
tulei@tulei:~$ join -a 1 sample.txt date.txt 
1001 John Engineering 75000
1002 Sarah Marketing 65000
1003 Mike Sales 70000
1004 Emily HR 60000
1005 David Finance 80000
1006 TEST
```

也可以不用第一个字段来匹配，
join -1 3 -2 1 
用第一个文件的 第三个字段 与第二个文件的 第一个字段 来匹配

```bash
tulei@tulei:~$ cat example.txt 
Engineering ENG
Marketing MKT
Sales SLS
HR HRS
Finance FIN
IT ITS
tulei@tulei:~$ join -1 3 -2 1 sample.txt example.txt
Engineering 1001 John ENG
Marketing 1002 Sarah MKT
Sales 1003 Mike SLS
HR 1004 Emily HRS
Finance 1005 David FIN
```

split




#### diff

diff 可以查看两个文件的不同，具体来说，他会告诉你，第一个文件的那些行需要修改，使其可以变成第二个文件，下边例子可以看到：
1，2c1，2：第一个文件的12两个行需要换成第二个文件的12两行
4c4：第一个文件的第4行需要换成第二个文件的第4行
```bash
tulei@tulei:~/test_2$ cat -n file_1
     1  this is the first file?
     2  the first file
     3  the file
     4  the first file
tulei@tulei:~/test_2$ cat -n file_2
     1  this is the second file!
     2  the second file
     3  the file
     4  the second file
tulei@tulei:~/test_2$ diff file_1 file_2
1,2c1,2
< this is the first file?
< the first file
---
> this is the second file!
> the second file
4c4
< the first file
---
> the second file
```

实际上也可以递归的来对比文件夹的不同，但只会输出有不同的文件：
```bash
tulei@tulei:~$ ls
snap  test_0  test_2
tulei@tulei:~$ diff -r test_0 test_2
Only in test_0: TEST
Only in test_0: aaa
Only in test_0: fdbug.sh
Only in test_2: file_1
Only in test_2: file_2
Only in test_0: marco_polo_pidwait.sh
Only in test_0: sigint.py
Only in test_0: test.sh
Only in test_0: wher
```

#### less &  more

当你的文件特别长时，可以使用 more  filename 或者 less filename 打开。

然后使用 space 翻页
按q退出

---

### 导航操作：

#### pwd

```shell
#查看目前所在位置：
tulei@tulei:~$ pwd
/home/tulei
```

#### ls

```bash
#查看当前目录下文件：当前目录没有，回到上一级，可以看到
tulei@tulei:~$ ls
tulei@tulei:~$ cd ..
tulei@tulei:/home$ ls
tulei

#实际上可以直接使用ls ..来查看上一级目录中的文件，也就是和自己同级的文件有哪些
tulei@tulei:/home$ ls ..
bin   dev  home  lib    lib64   lost+found  mnt  proc  run   snap  sys  usr
boot  etc  init  lib32  libx32  media       opt  root  sbin  srv   tmp  var
tulei@tulei:/home$ cd tulei 
tulei@tulei:~$ ls ..
tulei

```

ls -l: 查看具体信息：
- 信息包括：_文件类型，创建者权限，_所在分组对其权限，_其他人权限，节点，创建时属组(This is the username that owns the file.)，_目前所在分组(A group is a collection of users that can share permissions.)，_大小，最后一次修改日期，名称_
- 对权限的说明：r可读，w可写，x可执行_
- 想要进入一个目录的子目录时，需要对父目录和对应的子目录都具有执行权限x
- 想要对一个目录使用ls 列出他的子目录有哪些，则需要对这个目录有阅读权限r
- 想阅读一个目录的子文件时，需要对文件有阅读权限r

具体对权限的修改部分可以在后边 对文件的操作 部分看到：

```
#查看详细的目录信息：ls -l
tulei@tulei:/$ ls
bin   dev  home  lib    lib64   lost+found  mnt  proc  run   snap  sys  usr
boot  etc  init  lib32  libx32  media       opt  root  sbin  srv   tmp  var

tulei@tulei:/$ ls -l
total 2728
lrwxrwxrwx   1 root root       7 Jan  7  2025 bin -> usr/bin
drwxr-xr-x   2 root root    4096 Apr 18  2022 boot
drwxr-xr-x  15 root root    3860 Nov  8 14:36 dev
drwxr-xr-x  81 root root    4096 Nov  8 14:36 etc
drwxr-xr-x   3 root root    4096 Nov  7 23:49 home
-rwxrwxrwx   1 root root 2724480 Jun 10 02:32 init
lrwxrwxrwx   1 root root       7 Jan  7  2025 lib -> usr/lib
lrwxrwxrwx   1 root root       9 Jan  7  2025 lib32 -> usr/lib32
lrwxrwxrwx   1 root root       9 Jan  7  2025 lib64 -> usr/lib64
lrwxrwxrwx   1 root root      10 Jan  7  2025 libx32 -> usr/libx32
drwx------   2 root root   16384 Nov  7 23:45 lost+found
drwxr-xr-x   2 root root    4096 Jan  7  2025 media
drwxr-xr-x   5 root root    4096 Nov  7 23:45 mnt
drwxr-xr-x   2 root root    4096 Jan  7  2025 opt
dr-xr-xr-x 232 root root       0 Nov  8 14:36 proc
drwx------   3 root root    4096 Nov  7 23:45 root
drwxr-xr-x  19 root root     560 Nov  8 14:36 run
lrwxrwxrwx   1 root root       8 Jan  7  2025 sbin -> usr/sbin
drwxr-xr-x   2 root root    4096 Nov  7 23:45 snap
drwxr-xr-x   2 root root    4096 Jan  7  2025 srv
dr-xr-xr-x  13 root root       0 Nov  8 14:36 sys
drwxrwxrwt   7 root root    4096 Nov  8 14:37 tmp
drwxr-xr-x  14 root root    4096 Jan  7  2025 usr
drwxr-xr-x  13 root root    4096 Jan  7  2025 var
```

ls -a : 可以查看所有文件：包括隐藏文件
ls -r : 以相反的顺序查看：

```bash
tulei@tulei:~$ ls
snap  test_0  test_1
tulei@tulei:~$ cd test_1
tulei@tulei:~/test_1$ ls
tulei@tulei:~/test_1$ cd ..
tulei@tulei:~$ ls -a
.              .bashrc  .gitconfig  .motd_shown  .sudo_as_admin_successful  .vim                       .vscode-server  .zshrc
..             .cache   .landscape  .npm         .swo                       .viminfo                   .wget-hsts      snap
.bash_history  .config  .lesshst    .profile     .swp                       .vimrc                     .zcompdump      test_0
.bash_logout   .dotnet  .local      .ssh         .tmux.conf                 .vscode-remote-containers  .zsh_history    test_1
tulei@tulei:~$ ls -r
test_1  test_0  snap
```

`ls */` 显示当前目录的子目录文件；

```bash
tulei@tulei:~$ ls */
snap/:
tldr

test_0/:
TEST  aaa  fdbug.sh  marco_polo_pidwait.sh  sigint.py  test.sh  wher

test_1/:
exampel_1  exampel_2
```

#### ==tree==

因为 ls 一步步搜索过于丑陋，有一种简单的插件：tree，这是他的效果：

```bash
tulei@tulei:~$ tree
.
└── test
    ├── aaa.md
    ├── example.sh
    └── example_1.sh

1 directory, 3 files
```

tree -p : 输出更详细的内容：
```bash
tulei@tulei:~$ tree
.
├── snap
│   └── tldr
│       ├── 728
│       ├── common
│       └── current -> 728
├── test1
├── test2
├── test3
└── test_0
    ├── TEST
    ├── aaa
    ├── fdbug.sh
    ├── marco_polo_pidwait.sh
    ├── sigint.py
    ├── test.sh
    └── wher

9 directories, 7 files
tulei@tulei:~$ tree -p
[drwxr-x---]  .
├── [drwx------]  snap
│   └── [drwxr-xr-x]  tldr
│       ├── [drwxr-xr-x]  728
│       ├── [drwxr-xr-x]  common
│       └── [lrwxrwxrwx]  current -> 728
├── [drwxr-xr-x]  test1
├── [drwx------]  test2
├── [drwxr-xr-x]  test3
└── [drwxr-xr-x]  test_0
    ├── [-rw-r--r--]  TEST
    ├── [-rw-r--r--]  aaa
    ├── [-rwxr-xr-x]  fdbug.sh
    ├── [-rwxr-xr-x]  marco_polo_pidwait.sh
    ├── [-rw-r--r--]  sigint.py
    ├── [-rwxr-xr-x]  test.sh
    └── [-rw-r--r--]  wher

9 directories, 7 files
```

#### cd

```bash
#移动到绝对路径：
tulei@tulei:~$ cd /home
tulei@tulei:/home$ pwd
/home

#移动到上一级：
tulei@tulei:/home$ cd ..
tulei@tulei:/$ pwd
/

#移动到根目录: cd /
tulei@tulei:~$ cd /
tulei@tulei:/$ ls
bin   dev  home  lib    lib64   lost+found  mnt  proc  run   snap  sys  usr
boot  etc  init  lib32  libx32  media       opt  root  sbin  srv   tmp  var

#移动到用户所在目录：cd ~
tulei@tulei:/$ cd ~
tulei@tulei:~$ pwd
/home/tulei

#移动到刚才所在目录； cd -
tulei@tulei:/dev$ cd ~
tulei@tulei:~$ pwd
/home/tulei
tulei@tulei:~$ cd -
/dev
tulei@tulei:/dev$ cd -
/home/tulei
```


#### ==nnn==

一层层挪动太慢，而记住路径又太困难，所有有了这个插件，只需要输入nnn，就可以利用上下左右来快速在不同文件之间切换


#### find

find 使用时记得加 . -name,并且，后边名称部分可以使用[ShellScript#通配符](Class_02_ShellScript.md#通配符)中的`*`或者`?`来进行模糊查找 

```shell
tulei@tulei:~$ ls
hello.txt  test
tulei@tulei:~$ ls test
hello_1.txt
tulei@tulei:~$ find hello.txt
hello.txt
tulei@tulei:~$ find hello_1.txt
find: ‘hello_1.txt’: No such file or directory
tulei@tulei:~$ find -name hello_1.txt
./test/hello_1.txt
tulei@tulei:~$ find . -name hello_1.txt
./test/hello_1.txt
```

当然也可以使用 -type 来指定查找的文件类型：
- d：目录
- f：文件

可以结合通配符 `-path '**/test/*.py'`来进行路径查找（这里的`**`代表可能是多层目录，我们只知道最后边是什么样，前边已经忘完了也可以找到）

可以使用 -o 来表示 or:
```bash
tulei@tulei:~$ find . -type f -name "TEST" -o -type d -name "test*"
./test_0
./test_0/TEST
./test3
./test2
./test1
```

- `-size +1M` specifies that we want files larger than 1 megabyte
- `-size -1M` specifies that we want files less than 1 megabyte


可以结合修改时间来查找：`-mtime -1`表示在最近一天之内修改过的东西
+1 表示一天以上

```bash
# Find all directories named src
find . -name src -type d
# Find all python files that have a folder named test in their path
find . -path '*/test/*.py' -type f
# Find all files modified in the last day
find . -mtime -1
# Find all zip files with size in range 500k to 10M
find . -size +500k -size -10M -name '*.tar.gz'
```

在找到之后，可以结合 `-exec rm {} \;`来删除所有你找到的文件

-exec : 表示执行后边的动作
{}：是占位符
\; 表示 -exec 的动作结束

```bash
# Delete all files with .tmp extension
find . -name '*.tmp' -exec rm {} \;

# Find all PNG files and convert them to JPG
find . -name '*.png' -exec magick {} {}.jpg \;
```

#### ==fd==

为方便查找，我下载了fd，他更简便一些

```bash
tulei@tulei:~$ ls
test
tulei@tulei:~$ ls test
aaa.md  example.sh  example_1.sh
tulei@tulei:~$ fd exampl*
test/example.sh
test/example_1.sh
```

#### grep

有时你并不只是想找到文件，而是想定位文字(注意grep后边之只能是文件而不能是文件夹)：

```bash
tulei@tulei:~$ ls
test
tulei@tulei:~$ grep foobar test
grep: test: Is a directory

tulei@tulei:~$ ls test
aaa.md  example.sh  example_1.sh
tulei@tulei:~$ grep foobar ./test/*
./test/aaa.md:aaa# foobar
./test/example.sh:    grep foobar "$file" > /dev/null 2> /dev/null   
./test/example.sh:        echo "File $file doed not have any foobar"
./test/example.sh:        echo "# foobar" >> "$file"
tulei@tulei:~$ 
```

当使用正则表达式时，加入 -E 就不需要再使用转义字符

基本用法
```bash
grep -E "pattern" file 
# 等同于 
egrep "pattern" file
```

与普通 `grep`的区别

 普通 `grep`（基本正则表达式）

```bash
# 需要转义特殊字符 
grep "a\|b" file      # 匹配 a 或 b 
grep "a\{2,\}" file   # 匹配 2个或更多 a
```

 `grep -E`（扩展正则表达式）

```bash
# 不需要转义特殊字符 
grep -E "a|b" file     # 匹配 a 或 b 
grep -E "a{2,}" file   # 匹配 2个或更多 a
```

- `-B 2` shows 2 lines Before the match
- `-A 2` shows 2 lines After the match

#### ==rg==

由于grep可能不会自动排除二进制文件等一系列配置文件，我下载了rg，他更方便：

```bash
tulei@tulei:~$ rg foobar
test/aaa.md
1:aaa# foobar

test/example.sh
9:    grep foobar "$file" > /dev/null 2> /dev/null   
12:        echo "File $file doed not have any foobar"
13:        echo "# foobar" >> "$file"
tulei@tulei:~$ 
```

-C  2可以展示上下两行
-u 是不忽略隐藏文件
-t 是文件的后缀

```bash
# Find all python files where I used the requests library
rg -t py 'import requests'
# Find all files (including hidden files) without a shebang line
rg -u --files-without-match "^#\!"
# Find all matches of foo and print the following 5 lines
rg foo -A 5
# Print statistics of matches (# of matched lines and files )
rg --stats PATTERN
```

---

### 对文件的操作：

#### mkdir 

mkdir：创建新的子目录

mkdir -p :可以在需要的时候为其添加父目录。
(如果不加-p 那么当父目录不存在时，你的子目录也不会被创建)
可以在你是bashrc中添加这条命令，自动将mkdir 设置为 mkdir -p 

```
alias mkdir="mkdir -p"     # -p make parent dirs as needed
```

mkdir -m :在创建时为文件夹设置权限

```bash
tulei@tulei:~$ ls -l
total 8.0K
drwx------ 3 tulei tulei 4.0K Nov 11 17:44 snap
drwxr-xr-x 2 tulei tulei 4.0K Nov 16 14:34 test_0
tulei@tulei:~$ mkdir test1
tulei@tulei:~$ mkdir -m 700 test2
tulei@tulei:~$ ls -l
total 16K
drwx------ 3 tulei tulei 4.0K Nov 11 17:44 snap
drwxr-xr-x 2 tulei tulei 4.0K Nov 16 21:54 test1
drwx------ 2 tulei tulei 4.0K Nov 16 21:55 test2
drwxr-xr-x 2 tulei tulei 4.0K Nov 16 14:34 test_0
```

mkdir -v ; 会输出他做了什么
```bash
tulei@tulei:~$ mkdir -v test3
mkdir: created directory 'test3'
```

#### touch: 

The `touch` command is used to create an empty file. If the file already exists, it updates the file's timestamp without changing its content.

```bash
tulei@tulei:~$ mkdir test_1
tulei@tulei:~$ ls
snap  test_0  test_1
tulei@tulei:~$ cd test_1
tulei@tulei:~/test_1$ ls
tulei@tulei:~/test_1$ touch hello
tulei@tulei:~/test_1$ ls
hello
tulei@tulei:~/test_1$ 
```

touch 可以一次性创建多个文件：

**并且可以和 通配符一起用**

```bash
tulei@tulei:~/test_1$ touch a.txt b.txt aab.txt
tulei@tulei:~/test_1$ ls
a.txt  aab.txt  b.txt
tulei@tulei:~/test_1$ ls *.txt
a.txt  aab.txt  b.txt
tulei@tulei:~/test_1$ ls a*.txt
a.txt  aab.txt
```

```bash
tulei@tulei:~/test_1$ touch note_{1..5}.txt
tulei@tulei:~/test_1$ ls
note_1.txt  note_2.txt  note_3.txt  note_4.txt  note_5.txt
```

touch 修改时间：

```bash
tulei@tulei:~/test_1$ ls
tulei@tulei:~/test_1$ touch exampel_1
tulei@tulei:~/test_1$ touch exampel_2
tulei@tulei:~/test_1$ ls -l
total 0
-rw-r--r-- 1 tulei tulei 0 Nov 16 20:56 exampel_1
-rw-r--r-- 1 tulei tulei 0 Nov 16 20:56 exampel_2

# 更新时间
tulei@tulei:~/test_1$ touch exampel_1
tulei@tulei:~/test_1$ ls -l
total 0
-rw-r--r-- 1 tulei tulei 0 Nov 16 20:57 exampel_1
-rw-r--r-- 1 tulei tulei 0 Nov 16 20:56 exampel_2

# 修改时间
tulei@tulei:~/test_1$ touch -d "2004-01-01 12:00:00" exampel_1
tulei@tulei:~/test_1$ ls -l
total 0
-rw-r--r-- 1 tulei tulei 0 Jan  1  2004 exampel_1
-rw-r--r-- 1 tulei tulei 0 Nov 16 20:56 exampel_2

# 将后一个文件的时间修改为 第一个文件的时间
tulei@tulei:~/test_1$ touch -r exampel_2 exampel_1
tulei@tulei:~/test_1$ ls -l
total 0
-rw-r--r-- 1 tulei tulei 0 Nov 16 20:56 exampel_1
-rw-r--r-- 1 tulei tulei 0 Nov 16 20:56 exampel_2
tulei@tulei:~/test_1$ touch -d "2004-01-01 12:00:00" exampel_1
tulei@tulei:~/test_1$ touch -r exampel_1 exampel_2
tulei@tulei:~/test_1$ ls -l
total 0
-rw-r--r-- 1 tulei tulei 0 Jan  1  2004 exampel_1
-rw-r--r-- 1 tulei tulei 0 Jan  1  2004 exampel_2
```

#### file 查看文件类型

在Linux中 你可以创建一个文件 为他命名为 xxx.txt 但实际上他可以不是txt 文件，你要是想直到他真正的类型，可以使用 file filename 来查看。


#### mv：重命名，或移动文件/文件夹

```shell
#重命名文件
tulei@tulei:~$ ls
hello.txt
tulei@tulei:~$ mv hello.txt a.txt
tulei@tulei:~$ ls
a.txt
tulei@tulei:~$ cat a.txt
hello

#创建一个文件夹test,并将之前的文件移动到文件夹
tulei@tulei:~$ mkdir test
tulei@tulei:~$ ls
hello.txt  test
tulei@tulei:~$ mv hello.txt test
tulei@tulei:~$ ls
test
tulei@tulei:~$ ls test
hello.txt
tulei@tulei:~$
```

mv -i : 安全的移动文件，当目的地已经存在已有文件时，提醒你

mv -f : 强制移动文件，如果目的地有文件，就覆盖他而不提示

mv -v : 可以输出他具体做了什么

mv -b : 在移动文件时会备份，但重命名文件夹时不会

当最后一个参数是 `文件夹名` 时，可以接收前边有多个参数，意味着一次移动多个文件，可以使用通配符。

移动文件夹时，不需要像cp 一样使用 -r :

```bash
tulei@tulei:~$ ls
snap  test_0  test_1
tulei@tulei:~$ mv test_1 test2
tulei@tulei:~$ ls
snap  test2  test_0
```

#### cp

复制到目标位置：

```bash
tulei@tulei:~$ ls
test
tulei@tulei:~$ cp test/hello.txt ./
tulei@tulei:~$ ls
hello.txt  test
tulei@tulei:~$ ls test
hello.txt
tulei@tulei:~$
```

复制到另一个文件：
```bash
tulei@tulei:~/test_1$ cat hello
tulei@tulei:~/test_1$ echo "hello" > hello
tulei@tulei:~/test_1$ ls
hello
tulei@tulei:~/test_1$ cat hello
hello
tulei@tulei:~/test_1$ cp hello hello_copy
tulei@tulei:~/test_1$ ls
hello  hello_copy
tulei@tulei:~/test_1$ cat hello_copy 
hello
tulei@tulei:~/test_1$ 
```

cp -r 递归复制文件夹：
```bash
tulei@tulei:~/test_1$ cd ..
tulei@tulei:~$ ls
snap  test_0  test_1
tulei@tulei:~$ cp -r test_1 test_1_copy
tulei@tulei:~$ ls
snap  test_0  test_1  test_1_copy
tulei@tulei:~$ ls test_1_copy
hello  hello_copy
tulei@tulei:~$ cat test_1_copy/hello_copy 
hello
tulei@tulei:~$ 
```

cp -i : 安全的复制文件，当目的地已经存在已有文件时，提醒你

cp -f : 强制复制文件，如果目的地有文件，就覆盖他而不提示

cp -p : 不只复制文件内容，还一并复制其更新时间，所有权等信息。

#### rm

rm 只能删除文件，不能删除文件夹，而删除文件夹需要 rmdir

但是 rmdir 只允许删除空文件夹，

所以想要完整删除整个文件夹，需要使用递归操作，不过真正使用时比较简短

```bash
tulei@tulei:~$ ls
hello.txt  test

#rm 无法删除文件夹但可以直接删除文件
tulei@tulei:~$ rm test
rm: cannot remove 'test': Is a directory
tulei@tulei:~$ rm hello.txt
tulei@tulei:~$ ls
test

#rmdir 只能删除空文件夹
tulei@tulei:~$ rmdir test
rmdir: failed to remove 'test': Directory not empty

#rm -r 递归的删除整个文件夹
tulei@tulei:~$ rm -r test
tulei@tulei:~$ ls
tulei@tulei:~$

# 可以使用 -i 来安全删除，在删除前会让你看看你删掉了什么
tulei@tulei:~$ rm -ri test_1_copy/
rm: descend into directory 'test_1_copy/'? y
rm: remove regular file 'test_1_copy/hello'? y
rm: remove regular file 'test_1_copy/hello_copy'? y
rm: remove directory 'test_1_copy/'? y
tulei@tulei:~$ 

# -f 是强制删除，-rf 会递归删除所有，但不推荐如此做
```

rm -v : 执行结束后告诉我你做了什么

####  chown & chmod

可以利用 chown 来改变文件的归属
```bash
tulei@tulei:~/test_2$ ls
tulei@tulei:~/test_2$ touch test.txt
tulei@tulei:~/test_2$ touch example
tulei@tulei:~/test_2$ ls -l
total 0
-rw-r--r-- 1 tulei tulei 0 Nov 16 15:45 example
-rw-r--r-- 1 tulei tulei 0 Nov 16 15:44 test.txt
tulei@tulei:~/test_2$ sudo chown root:root example 
[sudo] password for tulei: 
tulei@tulei:~/test_2$ ls -l
total 0
-rw-r--r-- 1 root  root  0 Nov 16 15:45 example
-rw-r--r-- 1 tulei tulei 0 Nov 16 15:44 test.txt
```

文件归属改变后，文件对自己就变得不可写了。

```bash
tulei@tulei:~/test_2$ ls -l
total 0
-rw-r--r-- 1 root  root  0 Nov 16 15:45 example
-rw-r--r-- 1 tulei tulei 0 Nov 16 15:44 test.txt
tulei@tulei:~/test_2$ echo "hello" > example 
bash: example: Permission denied
tulei@tulei:~/test_2$ echo "hello" > test.txt 
tulei@tulei:~/test_2$ cat example 
tulei@tulei:~/test_2$ cat test.txt 
hello
```

可以使用 chmod 来为自己增加写入权限，使得文件可写。

chmod 可以利用二进制数来表示权限
0：没用权限
1：可执行
2：可写
4：可读
连续的三个数分别代表：所有者权限，所属组别的权限，其他人的权限

```bash
tulei@tulei:~/test_2$ ls -l
total 4
-rw-r--r-- 1 root  root  0 Nov 16 15:45 example
-rw-r--r-- 1 tulei tulei 6 Nov 16 16:30 test.txt
tulei@tulei:~/test_2$ chmod 602 example 
chmod: changing permissions of 'example': Operation not permitted
tulei@tulei:~/test_2$ sudo chmod 602 example 
tulei@tulei:~/test_2$ sudo chmod 602 test.txt 
tulei@tulei:~/test_2$ ls -l
total 4
-rw-----w- 1 root  root  0 Nov 16 15:45 example
-rw-----w- 1 tulei tulei 6 Nov 16 16:30 test.txt
tulei@tulei:~/test_2$ echo "hello" >> example 
tulei@tulei:~/test_2$ echo "hello" >> test.txt 
```

chmod 也可以使用 + - 来增加减少权限

u：文件所有者
g：组织
o：其他人
a：all

```bash
tulei@tulei:~/test_2$ ls -l
total 8
---------- 1 root  root   6 Nov 16 16:52 example
---------- 1 tulei tulei 12 Nov 16 16:52 test.txt
tulei@tulei:~/test_2$ chmod u+x test.txt 
tulei@tulei:~/test_2$ ls -l
total 8
---------- 1 root  root   6 Nov 16 16:52 example
---x------ 1 tulei tulei 12 Nov 16 16:52 test.txt
tulei@tulei:~/test_2$ chmod g+w test.txt 
tulei@tulei:~/test_2$ ls -l
total 8
---------- 1 root  root   6 Nov 16 16:52 example
---x-w---- 1 tulei tulei 12 Nov 16 16:52 test.txt
tulei@tulei:~/test_2$ chmod o+w test.txt 
tulei@tulei:~/test_2$ ls -l
total 8
---------- 1 root  root   6 Nov 16 16:52 example
---x-w--w- 1 tulei tulei 12 Nov 16 16:52 test.txt
tulei@tulei:~/test_2$ chmod a+r test.txt 
tulei@tulei:~/test_2$ ls -l
total 8
---------- 1 root  root   6 Nov 16 16:52 example
-r-xrw-rw- 1 tulei tulei 12 Nov 16 16:52 test.txt
```

chown 也可以用来改变文件夹的归属。

```bash
tulei@tulei:~$ ls
snap  test_0  test_2
tulei@tulei:~$ mkdir test_1/example
tulei@tulei:~$ echo "hello" > test_1/helloworld
tulei@tulei:~$ echo "hello" > test_1/example/hello1
tulei@tulei:~$ ls -lR test_1
test_1:
total 8
drwxr-xr-x 2 tulei tulei 4096 Nov 16 16:43 example
-rw-r--r-- 1 tulei tulei    6 Nov 16 16:43 helloworld

test_1/example:
total 4
-rw-r--r-- 1 tulei tulei 6 Nov 16 16:43 hello1
tulei@tulei:~$ sudo chown -R root:root test_1
[sudo] password for tulei: 
tulei@tulei:~$ ls -lR test_1
test_1:
total 8
drwxr-xr-x 2 root root 4096 Nov 16 16:43 example
-rw-r--r-- 1 root root    6 Nov 16 16:43 helloworld

test_1/example:
total 4
-rw-r--r-- 1 root root 6 Nov 16 16:43 hello1
```



---

### I/O Stream

程序与外界交互实际上依赖于 stream

他的输入都来自于 input stream，而输出都会去 output stream（一般来说，input stream 和 output stream 都被定位在我们的屏幕上）

但，shell 提供了重定向这些 stream 的方法：

#### > & <

`<`：重定向这个程序的输入流，把程序的输入变成后边文件的内容
`>` ：重定向这个程序的输出流，把程序的输出变成后边文件的输入

```shell
#最开始，当我使用 echo hello，这个程序的输出流就有了 hello,并将输出流的内容打印在屏幕上
tulei@tulei:~$ echo hello
hello

#而我可以使用 > 将上述程序的输出流重定向到一个文件
tulei@tulei:~$ ls
tulei@tulei:~$ echo hello > hello.txt
tulei@tulei:~$ ls
hello.txt
tulei@tulei:~$ cat hello.txt
hello

#实际上,cat 的逻辑是,将输入原封不动的打印出来
#所以我可以使用 < 将一个文件的内容替换为程序的输入
tulei@tulei:~$ cat < hello.txt
hello
tulei@tulei:~$ 

#尽管上边几个操作看起来差不多,但请你理解stream实在做什么
```

在理解了 input stream 和 output stream 之后,我想你可以理解以下操作:

```shell
#流操作是从左向右依次执行的
tulei@tulei:~$ cat < hello.txt > hello_2.txt
tulei@tulei:~$ ls
hello.txt  hello_2.txt
tulei@tulei:~$ cat hello_2.txt
hello
tulei@tulei:~$
```

他将一个文件的内容读取入自己的 input stream, 并把自己的 out stream 重定向为另一个文件,实现了复制操作

#### >>

`>>` :他不是像`>`一样暴力地覆盖掉原内容,而是将输出内容加入原内容后边

```bash
tulei@tulei:~$ ls
hello.txt
tulei@tulei:~$ cat hello.txt
hello
tulei@tulei:~$ echo test > hello.txt
tulei@tulei:~$ cat hello.txt
test
tulei@tulei:~$ echo "hello again" >> hello.txt
tulei@tulei:~$ cat hello.txt
test
hello again
tulei@tulei:~$
```

#### stderr

当你报错的时候，他并不是 stdin 或者 stdout ，而是另一条输出路线 stderr

所以他并不会直接被 `>` 重定向到其他文件

不过可以通过一些其他设定来达到你想要的效果

- `0`: stdin (standard input)
- `1`: stdout (standard output)
- `2`: stderr (standard error)

直接用重定向不了：

```bash
tulei@tulei:~$ ls
snap  test_0
tulei@tulei:~$ ls /fake/directory > peanuts.txt
ls: cannot access '/fake/directory': No such file or directory
tulei@tulei:~$ ls
peanuts.txt  snap  test_0
tulei@tulei:~$ cat peanuts.txt 
```

但可以使用 2 来指定 stderr 的输出

```
tulei@tulei:~$ ls /fake/directory 2> peanuts.txt
tulei@tulei:~$ cat peanuts.txt 
ls: cannot access '/fake/directory': No such file or directory
```

`2>&1` 可以直接合并 stdout 和 stderr ，并将其重定向
对这个用法，有一种更现代的方法 `&>`

这里 ls 接受了两个参数，第一个会输出 stderr 第二个输出 stdout，正常 > 只会将这两种输出分开，stderr 依旧会输出在terminal上，而stdout会进入对应文件

			但使用 `> filename 2>&1` 或者 `&> filename` ，则stderr 与 stdout 都会被重定向到对应文件之中。

```bash
tulei@tulei:~$ rm peanuts.txt 
tulei@tulei:~$ ls /fake/directory /etc/passwd &> peanuts.txt
tulei@tulei:~$ ls
peanuts.txt  snap  test_0
tulei@tulei:~$ cat peanuts.txt 
ls: cannot access '/fake/directory': No such file or directory
/etc/passwd
```

`/dev/null` 可以扔掉任何你不想要的 stdout 或者 stderr

```bash
ls /fake/directory 2> /dev/null
```

#### |

Pipe(|):他可以将左边程序的输入变成右边程序的输出

```shell
tulei@tulei:~$ ls
hello.txt  hello_2.txt
tulei@tulei:~$ ls -l
total 8
-rw-r--r-- 1 tulei tulei 6 Nov  8 20:19 hello.txt
-rw-r--r-- 1 tulei tulei 6 Nov  8 20:28 hello_2.txt
# tail 是将输入内容的最后几行输出的程序,后边的选项 -1表示只输出最后一行
#下边这个程序就将ls -l输出的内容,只选取最后一行输出
tulei@tulei:~$ ls -l | tail -n1
-rw-r--r-- 1 tulei tulei 6 Nov  8 20:28 hello_2.txt
tulei@tulei:~$
```

实际上你可以意识到,shell 通过对 stream 的操作,可以实现不同程序之间的交互

pipe 不只可以用来处理文本,他也可以被用来处理二进制图片,甚至视频

#### tee

同时将 | 接收到的信息 作为 stdout 打印 ，并 作为后续操作的 stdin 

```bash
tulei@tulei:~$ ls
snap  test_0
tulei@tulei:~$ ls | tee peanuts.txt
snap
test_0
tulei@tulei:~$ ls
peanuts.txt  snap  test_0
tulei@tulei:~$ cat peanuts.txt 
snap
test_0
```

---
### 找到之前运行过的命令：

- 上下箭头：慢慢翻
- history 命令可以打印历史记录
- history 利用管道（|）结合grep
```bash
tulei@tulei:~$ history | grep convert
  544  tldr convert
  591  history | grep convert
```
- CTRL + R ：倒着从后往前搜索你使用过多命令（不断按ctrl + R 就可以不断回溯）
- fzf：一种动态的模糊搜索，你可以把输出的管道接到他这里，然后就可以动态模糊搜索

---
### sudo

在我们需要以 root 权限运行某些程序时，实际上就在使用 sudo

直接使用会报错：

```shell
#因为在使用linux子系统，所以实际上这个系统中并不存在一个真实的屏幕，
#所以也就无法完成这个演示的后半部分：修改屏幕亮度

#使用普通用户权限时会出现：Permission denied
#但直接使用sudo echo 500 > brightness，也会出现 Permission denied

#为什么呢？ 
#因为程序只知道自己的输入和输出，所以上边的代码做了如下两件事情：
#1，为程序 sudo 输入 “echo 500”
#2,发送sudo的输出到 brightness这个文件 
#（但shell 在打开 brightness这个文件时，并不是在使用root权限，依旧是自己在尝试打开brightness,所以没有权限）

#正确的打开方式
#使用sudo su 登录root用户的身份，之后再进行更改

tulei@tulei:/$ ls
bin   dev  home  lib    lib64   lost+found  mnt  proc  run   snap  sys  usr
boot  etc  init  lib32  libx32  media       opt  root  sbin  srv   tmp  var
tulei@tulei:/$ cd sys
tulei@tulei:/sys$ ls
block  bus  class  dev  devices  firmware  fs  hypervisor  kernel  module  power
tulei@tulei:/sys$ cd class
tulei@tulei:/sys/class$ ls
ata_device   devlink          hwmon             mdio_bus        power_supply    sas_expander   thermal
ata_link     dma              input             mem             powercap        sas_host       tpm
ata_port     drm              intel_scu_ipc     misc            ppp             sas_phy        tpmrm
backlight    fc_host          iommu             nd              pps             sas_port       tty
bdi          fc_remote_ports  iscsi_connection  net             ptp             scsi_device    vc
block        fc_transport     iscsi_endpoint    nvme            pwm             scsi_disk      virtio-ports
bsg          fc_vports        iscsi_host        nvme-generic    raid_devices    scsi_generic   vtconsole
dca          firmware         iscsi_iface       nvme-subsystem  rtc             scsi_host      wakeup
devcoredump  gpio             iscsi_session     pci_bus         sas_device      spi_host       watchdog
devfreq      graphics         iscsi_transport   phy             sas_end_device  spi_transport
tulei@tulei:/sys/class$ cd backlight
tulei@tulei:/sys/class/backlight$ ls
```

登录 root 用户身份

```shell
tulei@tulei:/sys/class/backlight$ sudo su
[sudo] password for tulei: 
root@tulei:/sys/class/backlight#
```

可以看到，现在我以 root 身份登录了终端，并且由原来的 $ 变成了` #`，表明我可以使用管理员权限来进行操作

退出 root 身份：

```typescript
root@tulei:/sys/class/backlight# exit
exit
tulei@tulei:/sys/class/backlight$
```

当然上述打开亮度的操作还有比较方便的一种方式

```shell
#tee 是一个命令，他将自己的收到的输入 写入 一个文件中，并打印结果
echo 500 | sudo tee brightness
```

为什么他能成功？

因为在打开 brightness 时，使用了 root 权限（之前不行是因为你依旧是在使用用户权限去打开文件）

---
### 用户管理

#### useradd

```bash
tulei@tulei:~$ sudo useradd joker
tulei@tulei:~$ sudo grep -w 'joker' /etc/passwd
joker:x:1001:1001::/home/joker:/bin/sh
```

新用户的信息会在 /etc/passwd 中找到：

This line shows:

- Username: joker
- Password: x (the actual password is stored securely elsewhere)
- User ID: 1001
- Group ID: 1001
- Home Directory: `/home/joker`, but it hasn't been created yet
- Default Shell: `/bin/sh`

可以看到，home 目录下依旧只有自己，这是因为，我们并没有为其创建目录（可以使用 sudo useradd -m joker 来在新用户创建时就为其创建目录）

```bash
tulei@tulei:~$ cd ..
tulei@tulei:/home$ ls -l
total 4
drwxr-x--- 14 tulei tulei 4096 Nov 16 17:14 tulei
```

这里我删掉了之前的用户，重新创建了joker 用户：
(可以看到，新用户的群组和他的用户名实际上是相同的)
可以在 ect/group 里看到

```bash
tulei@tulei:/home$ ls -l
total 8
drwxr-x---  2 joker joker 4096 Nov 16 19:51 joker
drwxr-x--- 15 tulei tulei 4096 Nov 16 19:26 tulei
tulei@tulei:/home$ id joker
uid=1001(joker) gid=1001(joker) groups=1001(joker)
tulei@tulei:~$ cat /etc/group | grep -E "joker"
joker:x:1001:
```


可以使用 sudo passwd 来为新用户设置密码：

```bash
tulei@tulei:~$ sudo passwd joker 
New password: 
Retype new password: 
passwd: password updated successfully
```

密码实际上存储在 shadow 中，他和passwd 权限的区别在于：

```bash
tulei@tulei:~$ ls -l /etc/passwd
-rw-r--r-- 1 root root 1515 Nov 16 17:15 /etc/passwd
tulei@tulei:~$ ls -l /etc/shadow
-rw-r----- 1 root shadow 958 Nov 16 17:27 /etc/shadow
```

#### usermod

实际上可以通过 usermod 来修改用户的 目录：

```bash
tulei@tulei:/home$ sudo grep -w 'joker' /etc/passwd
joker:x:1001:1001::/home/joker:/bin/sh
tulei@tulei:/home$ sudo usermod -d /home/joker/test joker
tulei@tulei:/home$ sudo grep -w 'joker' /etc/passwd
joker:x:1001:1001::/home/joker/test:/bin/sh
```

也可以修改用户的默认 shell

```bash
tulei@tulei:/home$ sudo usermod -s /bin/bash joker
tulei@tulei:/home$ sudo grep -w 'joker' /etc/passwd
joker:x:1001:1001::/home/joker:/bin/bash
```

可以更该 用户的 groups:
```bash
tulei@tulei:/home$ sudo usermod -aG sudo joker
tulei@tulei:/home$ groups joker
joker : joker sudo
```

也可以创建一个 group 然后把用户加进去：

```bash
tulei@tulei:~$ sudo groupadd developers
tulei@tulei:~$ sudo usermod -aG developers joker
tulei@tulei:~$ id joker 
uid=1001(joker) gid=1001(joker) groups=1001(joker),1002(developers)
```



#### su 

更换用户：

```bash
tulei@tulei:/home$ su - joker
Password: 
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.6.87.2-microsoft-standard-WSL2 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Sun Nov 16 17:52:12 CST 2025

  System load:  0.19                Processes:             62
  Usage of /:   0.5% of 1006.85GB   Users logged in:       1
  Memory usage: 18%                 IPv4 address for eth0: 172.20.156.97
  Swap usage:   0%

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

This message is shown once a day. To disable it please create the
/home/joker/.hushlogin file.
joker@tulei:~$ 
```

#### passwd -l -u

如果你不想该账号被登录，可以使用sudo passwd -l

```bash
tulei@tulei:~$ sudo passwd -l joker
passwd: password expiry information changed.
tulei@tulei:~$ su - joker
Password: 
su: Authentication failure
tulei@tulei:~$ sudo passwd -u joker
passwd: password expiry information changed.
tulei@tulei:~$ su - joker
Password: 
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

joker@tulei:~$ 
```

-l 意味着这个密码被锁了，用了也没用
-u 指 unlock

#### userdel

可以使用userdel 来删除用户， -r 表示删除用户目录和邮箱等

```bash
tulei@tulei:/home$ sudo userdel -r joker
userdel: joker mail spool (/var/mail/joker) not found
tulei@tulei:/home$ ls
tulei
tulei@tulei:/home$ sudo grep -w 'joker' /etc/passwd
tulei@tulei:/home$ 
```


---

## Exercises

- For this course, you need to be using a Unix shell like Bash or ZSH. If you are on Linux or macOS, you don’t have to do anything special. If you are on Windows, you need to make sure you are not running cmd.exe or PowerShell; you can use <u>Windows Subsystem for Linux</u> or a Linux virtual machine to use Unix-style command-line tools. To make sure you’re running an appropriate shell, you can try the command `echo $SHELL`. If it says something like `/bin/bash` or `/usr/bin/zsh`, that means you’re running the right program.

![](content/BasicTools/MIT_Missing_Semester/static/G42xb00nToyuABxn9G4cgLmSnfb.png)

- Create a new directory called `missing` under `/tmp`.
  ![](content/BasicTools/MIT_Missing_Semester/static/Aepvb5SqgoHXedx6xRLcOegxnHc.png)
- Look up the `touch` program. The `man` program is your friend.
- Use `touch` to create a new file called `semester` in `missing`.

![](content/BasicTools/MIT_Missing_Semester/static/W58wbEh3yoiz5OxToeNcTwnhnjc.png)

- Write the following into that file, one line at a time:

```
#!/bin/sh
curl --head --silent https://missing.csail.mit.edu
```

- The first line might be tricky to get working. It’s helpful to know that `#` starts a comment in Bash, and `!` has a special meaning even within double-quoted (`"`) strings. Bash treats single-quoted strings (`'`) differently: they will do the trick in this case. See the Bash <u>quoting</u> manual page for more information.

![](content/BasicTools/MIT_Missing_Semester/static/EOfkbIxhHoeutIxDcUUc84Ecnrg.png)

- Try to execute the file, i.e. type the path to the script (`./semester`) into your shell and press enter. Understand why it doesn’t work by consulting the output of `ls` (hint: look at the permission bits of the file).

![](content/BasicTools/MIT_Missing_Semester/static/LfDlbzKtqoDV58xuPA9cOI4Jnyd.png)

![](content/BasicTools/MIT_Missing_Semester/static/GXJNbtBOAoUOupxbuohckxgEnFb.png)

- Run the command by explicitly starting the `sh` interpreter, and giving it the file `semester` as the first argument, i.e. `sh semester`. Why does this work, while `./semester` didn’t?

![](content/BasicTools/MIT_Missing_Semester/static/XUjpb8zfzo4NbFx0PxJcXrgAndr.png)

- Look up the `chmod` program (e.g. use `man chmod`).
- Use `chmod` to make it possible to run the command `./semester` rather than having to type `sh semester`. How does your shell know that the file is supposed to be interpreted using `sh`? See this page on the <u>shebang</u> line for more information.

![](content/BasicTools/MIT_Missing_Semester/static/P9I9b4ClnoHPVMxYIZacQCODndf.png)

![](content/BasicTools/MIT_Missing_Semester/static/IkImbsgw8oMbdsxX1vBc4dxvngf.png)

所以你可以看到，在使用 sh 来运行后边程序时，实际上我们只是读取了后边程序文件的内容，而不是真正执行了后边的程序，所以并不会出现权限问题。

- Use `|` and `>` to write the “last modified” date output by `semester` into a file called `last-modified.txt` in your home directory.

![](content/BasicTools/MIT_Missing_Semester/static/MTP7bl1Jooxq2wxaDPycF3RBnxb.png)

- Write a command that reads out your laptop battery’s power level or your desktop machine’s CPU temperature from `/sys`. Note: if you’re a macOS user, your OS doesn’t have sysfs, so you can skip this exercise.

windows 中的 linux 子系统貌似也找不到对应的温度信息
