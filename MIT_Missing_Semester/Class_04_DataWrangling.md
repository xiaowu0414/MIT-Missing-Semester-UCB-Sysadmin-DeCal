---
date: 2025-11-15
tags:
  - regular_expressions
---
[lectures](https://missing.csail.mit.edu/2020/) [about](https://missing.csail.mit.edu/about/)

[关于](https://missing.csail.mit.edu/about/)
# Data Wrangling  数据整理

Have you ever wanted to take data in one format and turn it into a different format? Of course you have! That, in very general terms, is what this lecture is all about. Specifically, massaging data, whether in text or binary format, until you end up with exactly what you wanted.  
你是否曾经想过将一种格式的数据转换成另一种格式？当然想过！从本质上讲，这正是本次讲座的主题。具体来说，就是如何处理数据，无论是文本格式还是二进制格式，直到最终得到你想要的结果。

We’ve already seen some basic data wrangling in past lectures. Pretty much any time you use the `|` operator, you are performing some kind of data wrangling. Consider a command like `journalctl | grep -i intel`. It finds all system log entries that mention Intel (case insensitive). You may not think of it as wrangling data, but it is going from one format (your entire system log) to a format that is more useful to you (just the intel log entries). Most data wrangling is about knowing what tools you have at your disposal, and how to combine them.  
我们在之前的课程中已经接触过一些基本的数据整理。基本上，只要你使用 \` `|` 运算符，你就是在进行某种数据整理。例如，像 `journalctl | grep -i intel` 这样的命令，它会找到所有包含“Intel”（不区分大小写）的系统日志条目。你可能不会把它看作是数据整理，但它实际上是将一种格式（整个系统日志）转换成一种对你更有用的格式（仅包含“Intel”的日志条目）。大多数数据整理工作都与了解你有哪些工具可用以及如何组合使用它们有关。
Let’s start from the beginning. To wrangle data, we need two things: data to wrangle, and something to do with it. Logs often make for a good use-case, because you often want to investigate things about them, and reading the whole thing isn’t feasible. Let’s figure out who’s trying to log into my server by looking at my server’s log:  
让我们从头开始。要处理数据，我们需要两样东西：要处理的数据，以及处理数据的方法。日志通常是一个很好的用例，因为我们经常需要调查日志中的一些信息，而阅读整个日志是不现实的。让我们通过查看服务器日志来找出是谁试图登录我的服务器：

```
ssh myserver journalctl
```

That’s far too much stuff. Let’s limit it to ssh stuff:  
东西太多了。我们还是只说 SSH 相关的内容吧：

```
ssh myserver journalctl | grep sshd
```

Notice that we’re using a pipe to stream a _remote_ file through `grep` on our local computer! `ssh` is magical, and we will talk more about it in the next lecture on the command-line environment. This is still way more stuff than we wanted though. And pretty hard to read. Let’s do better:  
请注意，我们使用管道将_远程_文件通过 `grep` 进行流式传输。 在我们的本地计算机上！ `ssh` 非常神奇，我们将在下一节关于命令行环境的课上详细讨论它。不过，这仍然比我们预想的要多得多，而且读起来也相当费劲。让我们改进一下：

```
ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' | less
```

Why the additional quoting? Well, our logs may be quite large, and it’s wasteful to stream it all to our computer and then do the filtering. Instead, we can do the filtering on the remote server, and then massage the data locally. `less` gives us a “pager” that allows us to scroll up and down through the long output. To save some additional traffic while we debug our command-line, we can even stick the current filtered logs into a file so that we don’t have to access the network while developing:  
为什么要额外加引号呢？因为我们的日志可能非常大，如果全部传输到本地计算机再进行过滤，会造成资源浪费。我们可以选择在远程服务器上进行过滤，然后在本地处理数据 `less` 提供了一个“分页器”，允许我们上下滚动浏览冗长的输出。为了在调试命令行时节省流量，我们甚至可以将当前过滤后的日志保存到一个文件中，这样在开发过程中就无需访问网络了：

```
$ ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' > ssh.log
$ less ssh.log
```

There’s still a lot of noise here. There are _a lot_ of ways to get rid of that, but let’s look at one of the most powerful tools in your toolkit: `sed`.  
这里仍然有很多噪音。有_很多_方法可以消除这些噪音，但让我们来看看你工具箱中最强大的工具之一： `sed` 。

`sed` is a “stream editor” that builds on top of the old `ed` editor. In it, you basically give short commands for how to modify the file, rather than manipulate its contents directly (although you can do that too). There are tons of commands, but one of the most common ones is `s`: substitution. For example, we can write:  
`sed` 是一个“流式编辑器”，它基于旧版的 `ed` 编辑器构建。在 sed 中，你主要通过简短的命令来修改文件，而不是直接操作文件内容（当然，你也可以这样做）。sed 提供了大量的命令，其中最常用的命令之一是 `s` ：替换。例如，我们可以这样写：

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed 's/.*Disconnected from //'
```

What we just wrote was a simple _regular expression_; a powerful construct that lets you match text against patterns. The `s` command is written in the form: `s/REGEX/SUBSTITUTION/`, where `REGEX` is the regular expression you want to search for, and `SUBSTITUTION` is the text you want to substitute matching text with.  
我们刚才写的是一个简单的_正则表达式_ ；这是一个强大的工具，可以让你用模式匹配文本。s 命令 `s` 格式为： `s/REGEX/SUBSTITUTION/` ，其中 `REGEX` 是你要搜索的正则表达式， `SUBSTITUTION` 是你要用来替换匹配文本的文本。

(You may recognize this syntax from the “Search and replace” section of our Vim [lecture notes](https://missing.csail.mit.edu/2020/editors/#advanced-vim)! Indeed, Vim uses a syntax for searching and replacing that is similar to `sed`’s substitution command. Learning one tool often helps you become more proficient with others.)  
（您可能在我们的 Vim 的“查找和替换”部分中见过这种语法） [课堂笔记](https://missing.csail.mit.edu/2021/editors/#advanced-vim) ！实际上，Vim 使用的搜索和替换语法与 `sed` 的替换命令类似。（学习一种工具通常有助于你更熟练地使用其他工具。）

vim中的替换：
- - `%s/foo/bar/g`
    - replace foo with bar globally in file  
        文件中的全局变量 foo 替换为 bar

## Regular expressions  正则表达式

Regular expressions are common and useful enough that it’s worthwhile to take some time to understand how they work. Let’s start by looking at the one we used above: `/.*Disconnected from /`. Regular expressions are usually (though not always) surrounded by `/`. Most ASCII characters just carry their normal meaning, but some characters have “special” matching behavior. Exactly which characters do what vary somewhat between different implementations of regular expressions, which is a source of great frustration. Very common patterns are:  
正则表达式非常常见且实用，因此值得花些时间了解其工作原理。我们先来看看上面用到的那个： `/.*Disconnected from /` 。正则表达式通常（但不总是）用 `/` 括起来。大多数 ASCII 字符都具有其通常的含义，但有些字符具有“特殊”的匹配行为。具体哪些字符执行哪些操作，在不同的正则表达式实现中略有不同，这常常令人感到困惑。一些非常常见的模式包括：

-   `.` means “any single character” except newline  
    `.` ”表示“除换行符外的任何单个字符”。
-   `*` zero or more of the preceding match  
    `*` 零次或多次匹配前一项
-   `+` one or more of the preceding match  
    `+` 前面一项或多项匹配
-   `[abc]` any one character of `a`, `b`, and `c`  
    `[abc]` `a` 、 `b` 和 `c` 中的任意一个字符
-   `(RX1|RX2)` either something that matches `RX1` or `RX2`  
    `(RX1|RX2)` 可以是与 `RX1` 或 `RX2` 匹配的任何内容
-   `^` the start of the line  
    `^` 行的开头
-   `$` the end of the line  
    `$` 尾

`sed`’s regular expressions are somewhat weird, and will require you to put a `\` before most of these to give them their special meaning. Or you can pass `-E`.  
`sed` 的正则表达式有点奇怪，大多数正则表达式都需要在前面加上反斜杠 `\` 才能赋予它们特殊含义。或者你可以传递 `-E` 参数。

So, looking back at `/.*Disconnected from /`, we see that it matches any text that starts with any number of characters, followed by the literal string “Disconnected from ”. Which is what we wanted. But beware, regular expressions are tricky. What if someone tried to log in with the username “Disconnected from”? We’d have:  
所以，回顾一下 `/.*Disconnected from /` ，我们发现它匹配任何以任意数量字符开头，后跟字符串“Disconnected from”的文本。这正是我们想要的。但要注意，正则表达式很棘手。如果有人尝试使用用户名“Disconnected from”登录会怎样？我们会得到：

```
Jan 17 03:13:00 thesquareplanet.com sshd[2631]: Disconnected from invalid user Disconnected from 46.97.239.16 port 55920 [preauth]
```

What would we end up with? Well, `*` and `+` are, by default, “greedy”. They will match as much text as they can. So, in the above, we’d end up with just  
最终结果会是什么？嗯， `*` 和 `+` 默认是“贪婪”的，它们会尽可能多地匹配文本。所以，在上面的例子中，最终结果只会是……

```
46.97.239.16 port 55920 [preauth]
```

Which may not be what we wanted. In some regular expression implementations, you can just suffix `*` or `+` with a `?` to make them non-greedy, but sadly `sed` doesn’t support that. We _could_ switch to perl’s command-line mode though, which _does_ support that construct:  
这可能并非我们想要的结果。在某些正则表达式实现中，只需在 `*` 或 `+` 后加上 `?` 即可使其变为非贪婪模式，但遗憾的是 `sed` 并不支持这种做法。不过，我们_可以_切换到 Perl 的命令行模式，它_支持_这种结构：

```
perl -pe 's/.*?Disconnected from //'
```

We’ll stick to `sed` for the rest of this, because it’s by far the more common tool for these kinds of jobs. `sed` can also do other handy things like print lines following a given match, do multiple substitutions per invocation, search for things, etc. But we won’t cover that too much here. `sed` is basically an entire topic in and of itself, but there are often better tools.  
接下来我们将继续使用 `sed` ，因为它是目前为止处理这类任务最常用的工具。sed 还可以做一些其他方便的事情，比如打印匹配项后面的行、每次调用执行多个替换、搜索内容等等。但我们在这里就不赘述了。sed 本身就是一个很大的话题，但通常 `sed` 下 `sed` 还有更好的工具。

Okay, so we also have a suffix we’d like to get rid of. How might we do that? It’s a little tricky to match just the text that follows the username, especially if the username can have spaces and such! What we need to do is match the _whole_ line:  
好的，我们还有一个后缀需要去掉。该怎么做呢？要只匹配用户名后面的文本有点棘手，尤其是用户名里可能包含空格之类的字符！我们需要匹配_整_行：

```
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user .* [^ ]+ port [0-9]+( \[preauth\])?$//'
```

Let’s look at what’s going on with a [regex debugger](https://regex101.com/r/qqbZqh/2). Okay, so the start is still as before. Then, we’re matching any of the “user” variants (there are two prefixes in the logs). Then we’re matching on any string of characters where the username is. Then we’re matching on any single word (`[^ ]+`; any non-empty sequence of non-space characters). Then the word “port” followed by a sequence of digits. Then possibly the suffix `[preauth]`, and then the end of the line.  
让我们用[正则表达式调试器](https://regex101.com/r/qqbZqh/2)看看发生了什么。好的，开头和之前一样。然后，我们匹配所有“user”变体（日志中有两个前缀）。接着，我们匹配包含用户名的任何字符串。然后，我们匹配任何单个单词（ `[^ ]+` ；任何非空的非空格字符序列）。然后是单词 “端口”后跟一串数字。然后可能还有后缀。 `[preauth]` ，然后是行尾。

Notice that with this technique, a username of “Disconnected from” won’t confuse us any more. Can you see why?  
请注意，使用这种方法后，“已断开连接”这样的用户名就不会再让我们感到困惑了。您明白为什么吗？

There is one problem with this though, and that is that the entire log becomes empty. We want to _keep_ the username after all. For this, we can use “capture groups”. Any text matched by a regex surrounded by parentheses is stored in a numbered capture group. These are available in the substitution (and in some engines, even in the pattern itself!) as `\1`, `\2`, `\3`, etc. So:  
但这存在一个问题，那就是整个日志会变成空的。毕竟，我们还是想_保留_用户名。为此，我们可以使用“捕获组”。任何被括号括起来的正则表达式匹配的文本都会存储在一个编号的捕获组中。这些捕获组可以在替换中使用（在某些引擎中，甚至可以在模式本身中使用！），例如 `\1` 、 `\2` 、 `\3` 等。所以：

```
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

As you can probably imagine, you can come up with _really_ complicated regular expressions. For example, here’s an article on how you might match an [e-mail address](https://www.regular-expressions.info/email.html). It’s [not easy](https://web.archive.org/web/20221223174323/http://emailregex.com/). And there’s [lots of discussion](https://stackoverflow.com/questions/201323/how-to-validate-an-email-address-using-a-regular-expression/1917982). And people have [written tests](https://fightingforalostcause.net/content/misc/2006/compare-email-regex.php). And [test matrices](https://mathiasbynens.be/demo/url-regex). You can even write a regex for determining if a given number [is a prime number](https://www.noulakaz.net/2007/03/18/a-regular-expression-to-check-for-prime-numbers/).  
正如你可能想象的那样，你可以写出_非常_复杂的正则表达式。例如，这里有一篇文章，讲的是如何匹配[电子邮件地址](https://www.regular-expressions.info/email.html) 。这[并不容易](https://web.archive.org/web/20221223174323/http://emailregex.com/) ，而且有[很多讨论](https://stackoverflow.com/questions/201323/how-to-validate-an-email-address-using-a-regular-expression/1917982) 。人们还[编写了测试用例](https://fightingforalostcause.net/content/misc/2006/compare-email-regex.php)和[测试矩阵](https://mathiasbynens.be/demo/url-regex) 。你甚至可以编写一个正则表达式来判断一个给定的数字是否[为质数](https://www.noulakaz.net/2007/03/18/a-regular-expression-to-check-for-prime-numbers/) 。

Regular expressions are notoriously hard to get right, but they are also very handy to have in your toolbox!  
正则表达式出了名的难用，但它们也是工具箱里非常实用的工具！

## Back to data wrangling  
回到数据整理

Okay, so we now have  
好的，所以我们现在有

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

`sed` can do all sorts of other interesting things, like injecting text (with the `i` command), explicitly printing lines (with the `p` command), selecting lines by index, and lots of other things. Check `man sed`!  
`sed` 还可以做很多其他有趣的事情，比如注入文本（使用 `i` 命令）、显式打印行（使用 `p` 命令）。 命令），按索引选择行，以及其他许多功能。请查看 `man sed` ！

Anyway. What we have now gives us a list of all the usernames that have attempted to log in. But this is pretty unhelpful. Let’s look for common ones:  
总之，我们现在得到的是所有尝试登录的用户名列表。但这没什么用。我们来找找常见的用户名：

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
```

`sort` will, well, sort its input. `uniq -c` will collapse consecutive lines that are the same into a single line, prefixed with a count of the number of occurrences. We probably want to sort that too and only keep the most common usernames:  
`sort` 会对输入进行排序。uniq `uniq -c` 会将连续相同的行合并成一行，并在行首加上出现次数的计数。我们可能也需要对合并后的行进行排序，只保留最常见的用户名：

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
```

`sort -n` will sort in numeric (instead of lexicographic) order. `-k1,1` means “sort by only the first whitespace-separated column”. The `,n` part says “sort until the `n`th field, where the default is the end of the line. In this _particular_ example, sorting by the whole line wouldn’t matter, but we’re here to learn!  
`sort -n` 将按数值顺序（而非字典顺序）排序。 `-k1,1` 表示“仅按第一个 `,n` 空格分隔的列排序”。 这部分内容是“排序到第 `n` 个字段，默认值为行尾”。在这个_例子_中，按整行排序无关紧要，但我们来这里是为了学习！

If we wanted the _least_ common ones, we could use `head` instead of `tail`. There’s also `sort -r`, which sorts in reverse order.  
如果我们想要_最不_常见的词，我们可以使用 `head` 代替 `tail` 。还有 `sort -r` ，它可以按相反的顺序排序。

Okay, so that’s pretty cool, but what if we’d like to extract only the usernames as a comma-separated list instead of one per line, perhaps for a config file?  
好的，这很棒，但如果我们只想提取用户名，并以逗号分隔的列表形式呈现，而不是每行一个，比如用于配置文件，该怎么办呢？

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
 | awk '{print $2}' | paste -sd,
```

If you’re using macOS: note that the command as shown won’t work with the BSD `paste` shipped with macOS. See [exercise 4 from the shell tools lecture](https://missing.csail.mit.edu/2020/shell-tools/#exercises) for more on the difference between BSD and GNU coreutils and instructions for how to install GNU coreutils on macOS.  
如果您使用的是 macOS：请注意，所示命令不适用于 BSD 系统。 macOS 自带的 `paste` 。有关 BSD 和 GNU coreutils 之间的区别以及如何在 macOS 上安装 GNU coreutils 的说明，请参阅 [shell 工具讲义中的练习 4](https://missing.csail.mit.edu/2020/shell-tools/#exercises) 。

Let’s start with `paste`: it lets you combine lines (`-s`) by a given single-character delimiter (`-d`; `,` in this case). But what’s this `awk` business?  
我们先来说说 `paste` ：它允许你使用指定的单字符分隔符（本例中 `,` `-d` ;）合并多行（ `-s` ）。但是 `awk` 又是什么呢？

## awk – another editor  
awk——另一位编辑

`awk` is a programming language that just happens to be really good at processing text streams. There is _a lot_ to say about `awk` if you were to learn it properly, but as with many other things here, we’ll just go through the basics.  
`awk` 是一种编程语言，它恰好非常擅长处理文本流。如果你要认真学习 `awk` ，你会发现它有_很多_值得探讨的地方，但就像这里提到的许多其他事物一样，我们只介绍一些基础知识。

First, what does `{print $2}` do? Well, `awk` programs take the form of an optional pattern plus a block saying what to do if the pattern matches a given line. The default pattern (which we used above) matches all lines. Inside the block, `$0` is set to the entire line’s contents, and `$1` through `$n` are set to the `n`th _field_ of that line, when separated by the `awk` field separator (whitespace by default, change with `-F`). In this case, we’re saying that, for every line, print the contents of the second field, which happens to be the username!  
首先， `{print $2}` 作用是什么？ `awk` 程序采用可选模式加上一个代码块的形式，该代码块用于指定当模式匹配到给定行时要执行的操作。默认模式（我们上面使用的）匹配所有行。在代码块内部， `$0` 被设置为整行的内容， `$1` 到 `$n` 被设置为该行的第 `n` 个_字段_ ，字段之间用 `awk` 字段分隔符（默认为空格，可以使用 `-F` 参数更改）分隔。在本例中，我们表示对于每一行，打印第二个字段的内容，而该字段恰好是用户名！

Let’s see if we can do something fancier. Let’s compute the number of single-use usernames that start with `c` and end with `e`:  
我们来看看能不能做点更复杂的。我们来计算一下以字母 `c` 开头、以字母 `e` 结尾的一次性用户名数量：

```
 | awk '$1 == 1 && $2 ~ /^c[^ ]*e$/ { print $2 }' | wc -l
```

There’s a lot to unpack here. First, notice that we now have a pattern (the stuff that goes before `{...}`). The pattern says that the first field of the line should be equal to 1 (that’s the count from `uniq -c`), and that the second field should match the given regular expression. And the block just says to print the username. We then count the number of lines in the output with `wc -l`.  
这里有很多信息需要解读。首先，请注意我们现在有了一个模式（ `{...}` 前面的部分）。该模式表示行的第一个字段应等于 1（这是 `uniq -c` 的计数结果），第二个字段应匹配给定的正则表达式。而该代码块的作用是打印用户名。然后，我们使用 `wc -l` 统计输出中的行数。

However, `awk` is a programming language, remember?  
但是，别忘了， `awk` 是一种编程语言！

```
BEGIN { rows = 0 }
$1 == 1 && $2 ~ /^c[^ ]*e$/ { rows += $1 }
END { print rows }
```

`BEGIN` is a pattern that matches the start of the input (and `END` matches the end). Now, the per-line block just adds the count from the first field (although it’ll always be 1 in this case), and then we print it out at the end. In fact, we _could_ get rid of `grep` and `sed` entirely, because `awk` [can do it all](https://backreference.org/2010/02/10/idiomatic-awk/), but we’ll leave that as an exercise to the reader.  
`BEGIN` 是一个匹配输入开头（以及 `END` 的模式。 匹配结尾）。现在，每行代码块只是将计数从末尾添加进去。 第一个字段（尽管在这种情况下它始终为 1），然后我们打印 最后再把它删掉。实际上，我们_可以_去掉 `grep` 和 `sed` 完全可以，因为 `awk` [可以做到这一切](https://backreference.org/2010/02/10/idiomatic-awk/) ，但我们把这留给读者作为练习。

## Analyzing data  分析数据

You can do math directly in your shell using `bc`, a calculator that can read from STDIN! For example, add the numbers on each line together by concatenating them together, delimited by `+`:  
你可以直接在 shell 中使用 `bc` 进行数学运算，bc 是一个可以从标准输入 (STDIN) 读取数据的计算器！例如，将每行中的数字用 `+` 分隔符连接起来，即可将它们相加：

```
 | paste -sd+ | bc -l
```

Or produce more elaborate expressions:  
或者生成更复杂的表达：

```
echo "2*($(data | paste -sd+))" | bc -l
```

You can get stats in a variety of ways. [`st`](https://github.com/nferraz/st) is pretty neat, but if you already have [R](https://www.r-project.org/):  
你可以通过多种方式获取统计数据。 [`st`](https://github.com/nferraz/st) 确实很棒，但如果你已经有了 [R](https://www.r-project.org/) ：

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | awk '{print $1}' | R --no-echo -e 'x <- scan(file="stdin", quiet=TRUE); summary(x)'
```

R is another (weird) programming language that’s great at data analysis and [plotting](https://ggplot2.tidyverse.org/). We won’t go into too much detail, but suffice to say that `summary` prints summary statistics for a vector, and we created a vector containing the input stream of numbers, so R gives us the statistics we wanted!  
R 是另一种（有点怪异的）编程语言，它非常擅长数据分析和[绘图](https://ggplot2.tidyverse.org/) 。我们就不赘述细节了，简单来说， `summary` 会打印向量的汇总统计信息，而我们创建了一个包含输入数字流的向量，所以 R 给出了我们想要的统计信息！

If you just want some simple plotting, `gnuplot` is your friend:  
如果你只是想绘制一些简单的图表， `gnuplot` 就是你的好帮手：

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
 | gnuplot -p -e 'set boxwidth 0.5; plot "-" using 1:xtic(2) with boxes'
```

## Data wrangling to make arguments  
数据整理以构建论点

Sometimes you want to do data wrangling to find things to install or remove based on some longer list. The data wrangling we’ve talked about so far + `xargs` can be a powerful combo.  
有时，你需要进行数据整理，以便根据较长的列表查找要安装或删除的项目。我们目前讨论的数据整理方法加上 `xargs` ，可以发挥强大的作用。

For example, as seen in lecture, I can use the following command to uninstall old nightly builds of Rust from my system by extracting the old build names using data wrangling tools and then passing them via `xargs` to the uninstaller:  
例如，正如课堂上所看到的，我可以使用以下命令从我的系统中卸载旧的 Rust nightly 版本：首先使用数据整理工具提取旧的构建名称，然后通过 `xargs` 将它们传递给卸载程序：

```
rustup toolchain list | grep nightly | grep -vE "nightly-x86" | sed 's/-x86.*//' | xargs rustup toolchain uninstall
```

## Wrangling binary data  处理二进制数据

So far, we have mostly talked about wrangling textual data, but pipes are just as useful for binary data. For example, we can use ffmpeg to capture an image from our camera, convert it to grayscale, compress it, send it to a remote machine over SSH, decompress it there, make a copy, and then display it.  
到目前为止，我们主要讨论的是处理文本数据，但管道对于二进制数据也同样有用。例如，我们可以使用 ffmpeg 从摄像头捕获图像，将其转换为灰度图像，压缩图像，通过 SSH 将其发送到远程机器，在远程机器上解压缩图像，复制图像，然后显示图像。

```
ffmpeg -loglevel panic -i /dev/video0 -frames 1 -f image2 -
 | convert - -colorspace gray -
 | gzip
 | ssh mymachine 'gzip -d | tee copy.jpg | env DISPLAY=:0 feh -'
```

# Exercises  练习

1.  Take this [short interactive regex tutorial](https://regexone.com/).  
    来学习一下这个[简短的交互式正则表达式教程](https://regexone.com/)吧。
2.  Find the number of words (in `/usr/share/dict/words`) that contain at least three `a`s and don’t have a `'s` ending. What are the three most common last two letters of those words? `sed`’s `y` command, or the `tr` program, may help you with case insensitivity. How many of those two-letter combinations are there? And for a challenge: which combinations do not occur?  
    找出 `/usr/share/dict/words` 目录中至少包含三个 `a` a\` 且不以 `'s` 结尾的单词数量。这些单词中最常见的三个最后两个字母是什么？可以使用 `sed` 的 \` `y` 命令或 `tr` 程序来解决大小写不敏感的问题。这些双字母组合共有多少种？还有一个挑战：哪些组合不会出现？
3.  To do in-place substitution it is quite tempting to do something like `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt`. However this is a bad idea, why? Is this particular to `sed`? Use `man sed` to find out how to accomplish this.  
    要进行原地替换，很容易会想到这样做： `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt` 。然而，这是一个糟糕的想法，为什么？这是 `sed` 命令特有的问题吗？使用 `man sed` 来了解如何实现这一点。
4.  Find your average, median, and max system boot time over the last ten boots. Use `journalctl` on Linux and `log show` on macOS, and look for log timestamps near the beginning and end of each boot. On Linux, they may look something like:  
    找出过去十个月内系统启动时间的平均值、中位数和最大值 启动时，在 Linux 上使用 `journalctl` ，在 macOS 上使用 `log show` ，并查看日志。 用于记录每次启动开始和结束附近的日志时间戳。在 Linux 系统上， 它们可能看起来像这样：
    
    ```
    Logs begin at ...
    ```
    
    and  和
    
    ```
    systemd[577]: Startup finished in ...
    ```
    
    On macOS, [look for](https://eclecticlight.co/2018/03/21/macos-unified-log-3-finding-your-way/):  
    在 macOS 系统中， [查找](https://eclecticlight.co/2018/03/21/macos-unified-log-3-finding-your-way/) ：
    
    ```
    === system boot:
    ```
    
    and  和
    
    ```
    Previous shutdown cause: 5
    ```
    
5.  Look for boot messages that are _not_ shared between your past three reboots (see `journalctl`’s `-b` flag). Break this task down into multiple steps. First, find a way to get just the logs from the past three boots. There may be an applicable flag on the tool you use to extract the boot logs, or you can use `sed '0,/STRING/d'` to remove all lines previous to one that matches `STRING`. Next, remove any parts of the line that _always_ varies (like the timestamp). Then, de-duplicate the input lines and keep a count of each one (`uniq` is your friend). And finally, eliminate any line whose count is 3 (since it _was_ shared among all the boots).  
    查找最近三次重启之间_没有_重复出现的启动信息（参见 `journalctl` 的 `-b` 参数）。将此任务分解为多个步骤。首先，找到一种方法，仅获取最近三次启动的日志。您用于提取启动日志的工具可能具有相应的参数，或者您可以使用 `sed '0,/STRING/d'` 删除所有匹配 `STRING` 行之前的行。接下来，删除行中_始终_变化的部分（例如时间戳）。然后，对输入行进行去重，并统计每行的重复次数（ `uniq` 参数非常有用）。最后，删除计数为 3 的行（因为它在所有启动中_都出现_过）。
6.  Find an online data set like [this one](https://commons.wikimedia.org/wiki/Data:Wikipedia_statistics/data.tab), [this one](https://ucr.fbi.gov/crime-in-the-u.s/2016/crime-in-the-u.s.-2016/topic-pages/tables/table-1), or maybe one [from here](https://www.springboard.com/blog/data-science/free-public-data-sets-data-science-project/). Fetch it using `curl` and extract out just two columns of numerical data. If you’re fetching HTML data, [`pup`](https://github.com/EricChiang/pup) might be helpful. For JSON data, try [`jq`](https://stedolan.github.io/jq/). Find the min and max of one column in a single command, and the difference of the sum of each column in another.  
    找到一个类似这样的在线数据集，比如[这个](https://commons.wikimedia.org/wiki/Data:Wikipedia_statistics/data.tab) 、 [这个](https://ucr.fbi.gov/crime-in-the-u.s/2016/crime-in-the-u.s.-2016/topic-pages/tables/table-1) ，或者[这个](https://www.springboard.com/blog/data-science/free-public-data-sets-data-science-project/) 。用 `curl` 获取它，然后提取其中的两列数值数据。 数据。如果您正在获取 HTML 数据， [`pup`](https://github.com/EricChiang/pup) 或许有用。对于 JSON 数据，可以试试 [`jq`](https://stedolan.github.io/jq/) 。它可以通过一条命令找到一列的最小值和最大值，再通过另一条命令计算两列总和的差值。

* * *