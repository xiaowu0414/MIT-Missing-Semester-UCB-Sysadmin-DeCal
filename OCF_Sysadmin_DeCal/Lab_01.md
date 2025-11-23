---
date: 2025-11-18
---
## Part 1

用到的命令以及相关资料在 [Class_01_CommandLine](Class_01_CommandLine.md)中可以找到。

```bash
tulei@tulei:~/Sysadmin_DeCal/Lab_01/b01$ ll
total 77M
drwxr-xr-x 3 tulei tulei 4.0K Sep 11  2019 ./
drwxr-xr-x 3 tulei tulei 4.0K Nov 19 00:44 ../
-rw-r--r-- 1 tulei tulei    8 Jan 30  2018 .secret
-rw-r--r-- 1 tulei tulei   20 Sep 11  2019 a_script
-rw-r--r-- 1 tulei tulei  77M Sep 12  2018 big_data.txt
-rw-r--r-- 1 tulei tulei    0 Jan 30  2018 hello_world
drwxr-xr-x 2 tulei tulei 4.0K Nov 19 00:44 nonsense/
tulei@tulei:~/Sysadmin_DeCal/Lab_01/b01$ pwd
/home/tulei/Sysadmin_DeCal/Lab_01/b01
tulei@tulei:~/Sysadmin_DeCal/Lab_01/b01$ cat .secret 
hunter2
tulei@tulei:~/Sysadmin_DeCal/Lab_01/b01$ rm -rf nonsense/
tulei@tulei:~/Sysadmin_DeCal/Lab_01/b01$ grep -B 2 http big_data.txt 
=================== THE SOLUTION IS MORE COFFEE ===============================
hl)V:}mntUS4;iC':uH|G(;y6Ir;4uNLLRC?GDfRP%o+g]s$NCL9zM'SK[IV.e<i&_3&7L7NBL41N#f
;=:P0viNjebvs<+^Ae.SZYG'F}\> https://xkcd.com/705 e[a3]vF;Ny,*rpyC?3OA$Nm<.iH8M
tulei@tulei:~/Sysadmin_DeCal/Lab_01/b01$ chmod +x a_script 
tulei@tulei:~/Sysadmin_DeCal/Lab_01/b01$ . a_script 
Hello World!
tulei@tulei:~/Sysadmin_DeCal/Lab_01/b01$ echo tulei >> hello_world 
tulei@tulei:~/Sysadmin_DeCal/Lab_01/b01$ cat hello_world 
tulei
```

## Part 2

### 1. Linux/OSX 与 Windows 这类操作系统有什么区别？

- **内核与架构**：Linux 基于类 Unix 内核（如 Linux 内核），OSX 基于 Darwin（BSD Unix），而 Windows 使用 NT 内核。
    
- **开源与闭源**：Linux 是开源的，可自由修改；OSX 是闭源但部分基于开源组件；Windows 是闭源的，需购买许可证。
    
- **文件系统**：Linux/OSX 使用类 Unix 文件系统（如 ext4、APFS），Windows 使用 NTFS/FAT。
    
- **命令行工具**：Linux/OSX 默认提供强大的命令行（如 Bash、Zsh），Windows 以图形界面为主，命令行（如 CMD、PowerShell）相对较弱。
    
- **使用场景**：Linux 常用于服务器和开发，OSX 用于创意工作和苹果生态，Windows 更普及于日常办公和游戏。
    

### 2. 命令行的使用与普通（图形界面）操作系统的使用方式有哪些区别？

- **交互方式**：命令行通过文本命令输入/输出操作，需记忆命令；图形界面通过鼠标点击、窗口和图标进行直观操作。
    
- **效率**：命令行适合自动化、批量处理（如脚本），执行速度快；图形界面更适合新手和简单任务。
    
- **资源占用**：命令行轻量，占内存少；图形界面需要更多系统资源。
    
- **灵活性**：命令行可精确控制系统底层，图形界面更注重用户体验。
    

### 3. Linux 文件系统中的根目录是什么？请从概念上进行解释，尽可能深入详细。

根目录（符号为 `/`）是 Linux 文件系统的顶层起点，是所有目录和文件的源头。概念上：

- **绝对路径基准**：任何文件或目录的路径都从根目录开始（如 `/home/user/file`）。
    
- **系统结构核心**：根目录包含系统关键子目录，如 `/bin`（基本命令）、`/etc`（配置文件）、`/home`（用户目录）、`/var`（变量数据）。
    
- **唯一性**：根目录没有父目录，是文件树的锚点。在 Linux 中，所有设备（如硬盘、U 盘）都通过“挂载”到根目录下的子目录来访问。
    
- **重要性**：根目录代表了整个文件系统的命名空间，是系统启动和运行的基础。删除根目录会导致系统崩溃。
    

### 4. ls 命令有很多实用的参数。尝试使用它们来获取额外信息，比如文件权限、所有者名称、所属组、文件大小和最后编辑日期。另外，我希望能够查看文件大小，并让文件按最后编辑日期排序，最旧的文件排在最前面。该如何实现？

可以使用以下命令：

```
ls -ltr
```

- **参数解释**：
    
    - `-l`：以长格式显示，包括文件权限、所有者、组、大小和修改日期。
        
    - `-t`：按修改时间排序，最新文件默认在前。
        
    - `-r`：反转排序顺序，使最旧的文件在前。
        
    
- **补充**：如果还需按大小排序，可结合 `-S`（但时间优先时，以上命令已满足）。完整命令会显示详细信息，并按时间升序（最旧在上）排列。
    

### 5. 我不想显示文件 big_data.txt 的前 10 行，而是想用 head 命令显示前 4 行。该怎么操作？

使用以下命令：

```
head -n 4 big_data.txt
```

- **说明**：`head`默认显示前 10 行，`-n 4`指定显示前 4 行。
    

### 6. cat foo > out.txt 和 cat foo >> out.txt 有什么区别？


- **`cat foo > out.txt`**：将文件 `foo`的内容覆盖写入 `out.txt`。如果 `out.txt`已存在，原有内容会被清空；如果不存在，会创建新文件。
    
- **`cat foo >> out.txt`**：将文件 `foo`的内容追加到 `out.txt`的末尾。如果 `out.txt`不存在，会创建新文件；已存在则保留原有内容，在末尾添加新内容。
    
- **关键区别**：`>`是覆盖操作，`>>`是追加操作。
    

### 7. 简单来说，宽松许可证和 Copyleft 许可证有什么区别？

- **宽松许可证（Permissive License）**：允许用户自由使用、修改和分发软件，包括闭源衍生软件，只需保留版权声明即可。例子包括 MIT、BSD 许可证。
    
- **Copyleft 许可证**：也允许自由使用和修改，但要求衍生作品必须以相同许可证开源发布，防止闭源专有化。例子包括 GPL 许可证。
    
- **核心区别**：宽松许可证更“自由”，允许闭源；Copyleft 强调“开源传染性”，强制衍生作品开源。
    

### 8. 举一个宽松许可证的例子。

MIT 许可证是常见的宽松许可证。它简单允许几乎无限制的使用，只需在衍生作品中包含原许可证声明即可。

### 9. 举例说明（a）你使用的开源软件和（b）免费但闭源的软件。

- **(a) 开源软件**：例如 **Linux 内核**（操作系统核心）或 **Firefox 浏览器**，源代码公开，可自由修改。
    
- **(b) 免费但闭源软件**：例如 **Google Chrome 浏览器**或 **Spotify（免费版）**，用户可以免费使用，但源代码不公开，受版权保护。
    