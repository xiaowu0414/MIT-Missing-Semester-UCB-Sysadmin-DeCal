---
date: 2025-11-20
---
## Vim

Try playing around with [lab2.md](https://raw.githubusercontent.com/0xcf/decal-web/master/labs/2.md) while looking up some new commands. Use `wget` to download it!

1. How would you delete the previous 10 lines?

d 10 k

2. How would you jump back to the shell without exiting `vim`?

Ctrl + z 退出，使用 % （任务编号）再次进入

3. How would you edit a new file alongside another file?

使用 CtrlP 插件，可以在vim内搜索文件并打开，然后使用 Shift W + Shift W 来变换不同的窗口

4. How would you indent a block of text?

使用 >> 向右缩进，使用 << 向左缩进

5. Tell us about one other cool vim feature you found out about that isn’t mentioned in this lab!

其他在MIT-Missing-Semester的Vim章节有

## tmux

6. Make a new tmux session. Using tmux shortcuts, try to make your session have a similar layout to the one below, and **upload a screenshot of it to Gradescope!**

![](Pasted%20image%2020251120152056.png)

10. If you haven’t already, detach from your current tmux session using `Ctrl+b d`. Now, what command would you type to attach back to it?

tmux a

11. What command will delete your session?

ctrl+d

12. What command will create a new session?

tmux new -s name

## Scripting

运行效果：

```bash
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ll
total 28K
drwxr-xr-x 2 tulei tulei 4.0K Nov 21 20:57 ./
drwxr-xr-x 4 tulei tulei 4.0K Nov 19 21:49 ../
-rw-r--r-- 1 tulei tulei  13K Nov 20 13:46 2.md
-rwxr-xr-x 1 tulei tulei  798 Nov 21 20:54 phonebook*
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ./phonebook new "Linus Torvalds" 101-110-0111
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ./phonebook list
Linus Torvalds 101-110-0111
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ./phonebook new "Tux Penguin" 555-666-7777
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ./phonebook new "Linus Torvalds" 222-222-2222
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ./phonebook list
Linus Torvalds 101-110-0111
Tux Penguin 555-666-7777
Linus Torvalds 222-222-2222
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ./phonebook lookup "Linus Torvalds"
Linus Torvalds 101-110-0111
Linus Torvalds 222-222-2222
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ./phonebook remove "Linus Torvalds"
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ./phonebook list
Tux Penguin 555-666-7777
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ./phonebook clear
tulei@tulei:~/Sysadmin_DeCal/Lab_02$ ./phonebook list
phonebook is empty
```

脚本代码

```bash
#!/bin/bash

PHONEBOOK_ENTRIES="bash_phonebook_entries"


if [ "$#" -lt 1 ]; then
    exit 1

elif [ "$1" = "new" ]; then
    # YOUR CODE HERE #
    echo "$2 $3" >> $PHONEBOOK_ENTRIES

elif [ "$1" = "list" ]; then
    if [ ! -e $PHONEBOOK_ENTRIES ] || [ ! -s $PHONEBOOK_ENTRIES ]; then
        echo "phonebook is empty"
    else
        # YOUR CODE HERE #
        cat $PHONEBOOK_ENTRIES
    fi

elif [ "$1" = "lookup" ]; then
    # YOUR CODE HERE #
    grep "$2" $PHONEBOOK_ENTRIES

elif [ "$1" = "remove" ]; then
    # YOUR CODE HERE #
    sed -i "/$2/d" $PHONEBOOK_ENTRIES

elif [ "$1" = "clear" ]; then
    # YOUR CODE HERE #

	# 前两种方法不会让文件变成0k，还会保留一个空行，以至于list时并不会输出文件夹为空
    # echo "" > $PHONEBOOK_ENTRIES
    # sed -i "/*/d" $PHONEBOOK_ENTRIES
    > $PHONEBOOK_ENTRIES

else
     # YOUR CODE HERE #
    echo "Please enter the correct format"
fi
```