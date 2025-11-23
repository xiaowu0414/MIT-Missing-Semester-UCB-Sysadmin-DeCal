[./missing-semester](https://missing.csail.mit.edu/) 

[lectures](https://missing.csail.mit.edu/2020/) [about](https://missing.csail.mit.edu/about/)

  
[./missing-semester](https://missing.csail.mit.edu/)

[关于](https://missing.csail.mit.edu/about/)[讲座](https://missing.csail.mit.edu/2020/)

# Version Control (Git)  版本控制（Git）

Version control systems (VCSs) are tools used to track changes to source code (or other collections of files and folders). As the name implies, these tools help maintain a history of changes; furthermore, they facilitate collaboration. VCSs track changes to a folder and its contents in a series of snapshots, where each snapshot encapsulates the entire state of files/folders within a top-level directory. VCSs also maintain metadata like who created each snapshot, messages associated with each snapshot, and so on.  
版本控制系统 (VCS) 是用于跟踪源代码（或其他文件和文件夹集合）变更的工具。顾名思义，这些工具有助于维护变更历史记录；此外，它们还有助于协作。VCS 通过一系列快照来跟踪文件夹及其内容的变更，每个快照都封装了顶级目录中所有文件/文件夹的完整状态。VCS 还维护元数据，例如每个快照的创建者、与每个快照关联的消息等等。

Why is version control useful? Even when you’re working by yourself, it can let you look at old snapshots of a project, keep a log of why certain changes were made, work on parallel branches of development, and much more. When working with others, it’s an invaluable tool for seeing what other people have changed, as well as resolving conflicts in concurrent development.  
为什么版本控制有用？即使独自工作，它也能让你查看项目的旧版本快照，记录每次更改的原因，并行处理多个开发分支等等。与他人协作时，它更是不可或缺的工具，可以帮助你了解其他人的修改，并解决并行开发中的冲突。

Modern VCSs also let you easily (and often automatically) answer questions like:  
现代版本控制系统还能让你轻松（而且通常是自动地）回答诸如此类的问题：

-   Who wrote this module?  
    这门课程是谁编写的？
-   When was this particular line of this particular file edited? By whom? Why was it edited?  
    该文件的这一行代码是什么时候被编辑的？由谁编辑的？为什么要编辑？
-   Over the last 1000 revisions, when/why did a particular unit test stop working?  
    在过去的 1000 次版本修改中，某个单元测试何时/为何停止工作？

While other VCSs exist, **Git** is the de facto standard for version control. This [XKCD comic](https://xkcd.com/1597/) captures Git’s reputation:  
虽然还有其他版本控制系统，但 **Git** 是事实上的版本控制标准。这幅 [XKCD 漫画生动地](https://xkcd.com/1597/)描绘了 Git 的声誉：

![xkcd 1597](https://imgs.xkcd.com/comics/git.png)

Because Git’s interface is a leaky abstraction, learning Git top-down (starting with its interface / command-line interface) can lead to a lot of confusion. It’s possible to memorize a handful of commands and think of them as magic incantations, and follow the approach in the comic above whenever anything goes wrong.  
由于 Git 的接口抽象层存在缺陷，自上而下地学习 Git（从其接口/命令行界面入手）可能会导致很多困惑。你可以记住几个命令，并将它们视为魔法咒语，然后在出现问题时按照上面漫画中的方法进行操作。

While Git admittedly has an ugly interface, its underlying design and ideas are beautiful. While an ugly interface has to be _memorized_, a beautiful design can be _understood_. For this reason, we give a bottom-up explanation of Git, starting with its data model and later covering the command-line interface. Once the data model is understood, the commands can be better understood in terms of how they manipulate the underlying data model.  
虽然 Git 的界面确实不太美观，但它的底层设计和理念却非常精妙。丑陋的界面需要_记忆_ ，而精妙的设计却可以_理解_ 。因此，我们将采用自下而上的方法来解释 Git，首先介绍其数据模型，然后再讲解命令行界面。一旦理解了数据模型，就能更好地理解命令是如何操作底层数据模型的。

# Git’s data model  Git 的数据模型

There are many ad-hoc approaches you could take to version control. Git has a well-thought-out model that enables all the nice features of version control, like maintaining history, supporting branches, and enabling collaboration.  
版本控制有很多临时性的方法。Git 拥有一个精心设计的模型，能够实现版本控制的所有优点，例如维护历史记录、支持分支和促进协作。

## Snapshots  快照

Git models the history of a collection of files and folders within some top-level directory as a series of snapshots. In Git terminology, a file is called a “blob”, and it’s just a bunch of bytes. A directory is called a “tree”, and it maps names to blobs or trees (so directories can contain other directories). A snapshot is the top-level tree that is being tracked. For example, we might have a tree as follows:  
Git 将某个顶级目录中的文件和文件夹集合的历史记录建模为一系列快照。在 Git 术语中，文件被称为“blob”，它只是一堆字节。目录被称为“tree”，它将名称映射到 blob 或树（因此目录可以包含其他目录）。快照是正在跟踪的顶级树。例如，我们可能有一棵如下所示的树：

```
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```

The top-level tree contains two elements, a tree “foo” (that itself contains one element, a blob “bar.txt”), and a blob “baz.txt”.  
顶层树包含两个元素，树“foo”（它本身包含一个元素，即 blob“bar.txt”），以及 blob“baz.txt”。

## Modeling history: relating snapshots  
建模历史：关联快照

How should a version control system relate snapshots? One simple model would be to have a linear history. A history would be a list of snapshots in time-order. For many reasons, Git doesn’t use a simple model like this.  
版本控制系统应该如何关联快照？一个简单的模型是采用线性历史记录。历史记录就是按时间顺序排列的快照列表。但由于诸多原因，Git 并没有使用这种简单的模型。

In Git, a history is a directed acyclic graph (DAG) of snapshots. That may sound like a fancy math word, but don’t be intimidated. All this means is that each snapshot in Git refers to a set of “parents”, the snapshots that preceded it. It’s a set of parents rather than a single parent (as would be the case in a linear history) because a snapshot might descend from multiple parents, for example, due to combining (merging) two parallel branches of development.  
在 Git 中，历史记录是一个由快照组成的有向无环图 (DAG)。这听起来可能像个高深的数学术语，但别被吓到。它的意思很简单：Git 中的每个快照都指向一组“父快照”，也就是它之前的快照。之所以是父快照集合而不是单个父快照（线性历史记录中通常只有一个父快照），是因为一个快照可能源自多个父快照，例如，由于合并了两个并行的开发分支。

Git calls these snapshots “commit”s. Visualizing a commit history might look something like this:  
Git 将这些快照称为“提交”。提交历史记录的可视化可能如下所示：

```
o <-- o <-- o <-- o
            ^
             \
              --- o <-- o
```

In the ASCII art above, the `o`s correspond to individual commits (snapshots). The arrows point to the parent of each commit (it’s a “comes before” relation, not “comes after”). After the third commit, the history branches into two separate branches. This might correspond to, for example, two separate features being developed in parallel, independently from each other. In the future, these branches may be merged to create a new snapshot that incorporates both of the features, producing a new history that looks like this, with the newly created merge commit shown in bold:  
在上面的 ASCII 图中，字母 `o` 代表单个提交（快照）。箭头指向每个提交的父提交（这是“在前”关系，而不是“在后”关系）。在第三次提交之后，历史记录分成两个独立的分支。这可能对应于例如两个彼此独立、并行开发的功能。将来，这些分支可能会合并，创建一个包含这两个功能的新快照，从而生成如下所示的新历史记录，其中新创建的合并提交以粗体显示：

```

o <-- o <-- o <-- o <---- o
            ^            /
             \          v
              --- o <-- o
```

Commits in Git are immutable. This doesn’t mean that mistakes can’t be corrected, however; it’s just that “edits” to the commit history are actually creating entirely new commits, and references (see below) are updated to point to the new ones.  
Git 中的提交是不可变的。但这并不意味着错误无法纠正；只是对提交历史的“编辑”实际上会创建全新的提交，并且引用（见下文）会更新以指向新的提交。

## Data model, as pseudocode  
数据模型（伪代码）

It may be instructive to see Git’s data model written down in pseudocode:  
将 Git 的数据模型用伪代码写出来或许会很有启发：

```
// a file is a bunch of bytes
type blob = array<byte>

// a directory contains named files and directories
type tree = map<string, tree | blob>

// a commit has parents, metadata, and the top-level tree
type commit = struct {
    parents: array<commit>
    author: string
    message: string
    snapshot: tree
}
```

It’s a clean, simple model of history.  
这是一个简洁明了的历史模型。

## Objects and content-addressing  
对象和内容寻址

An “object” is a blob, tree, or commit:  
“对象”指的是数据块、树状结构或提交记录：

```
type object = blob | tree | commit
```

In Git data store, all objects are content-addressed by their [SHA-1 hash](https://en.wikipedia.org/wiki/SHA-1).  
在 Git 数据存储中，所有对象都通过其 [SHA-1 哈希值](https://en.wikipedia.org/wiki/SHA-1)进行内容寻址。

```
objects = map<string, object>

def store(object):
    id = sha1(object)
    objects[id] = object

def load(id):
    return objects[id]
```

Blobs, trees, and commits are unified in this way: they are all objects. When they reference other objects, they don’t actually _contain_ them in their on-disk representation, but have a reference to them by their hash.  
Blob、树和提交在这方面是统一的：它们都是对象。当它们引用其他对象时，它们实际上并不在磁盘上_存储_这些对象，而是通过哈希值来引用它们。

For example, the tree for the example directory structure [above](https://missing.csail.mit.edu/2020/version-control/#snapshots) (visualized using `git cat-file -p 698281bc680d1995c5f4caaf3359721a5a58d48d`), looks like this:  
例如， [上面](https://missing.csail.mit.edu/2020/version-control/#snapshots)示例目录结构的树状图。 （使用 `git cat-file -p 698281bc680d1995c5f4caaf3359721a5a58d48d` 可视化）如下所示：

```
100644 blob 4448adbf7ecd394f42ae135bbeed9676e894af85    baz.txt
040000 tree c68d233a33c5c06e0340e4c224f0afca87c8ce87    foo
```

The tree itself contains pointers to its contents, `baz.txt` (a blob) and `foo` (a tree). If we look at the contents addressed by the hash corresponding to baz.txt with `git cat-file -p 4448adbf7ecd394f42ae135bbeed9676e894af85`, we get the following:  
树本身包含指向其内容、 `baz.txt` （一个 blob）和 `foo` 指针。 （一棵树）。如果我们查看与哈希对应的内容， 使用 `git cat-file -p 4448adbf7ecd394f42ae135bbeed9676e894af85` 导入 baz.txt 文件，得到以下结果：

```
git is wonderful
```

## References  参考

Now, all snapshots can be identified by their SHA-1 hashes. That’s inconvenient, because humans aren’t good at remembering strings of 40 hexadecimal characters.  
现在，所有快照都可以通过它们的 SHA-1 哈希值来识别。这很不方便，因为人类不擅长记住 40 个十六进制字符的字符串。

Git’s solution to this problem is human-readable names for SHA-1 hashes, called “references”. References are pointers to commits. Unlike objects, which are immutable, references are mutable (can be updated to point to a new commit). For example, the `master` reference usually points to the latest commit in the main branch of development.  
Git 解决这个问题的方法是使用人类可读的 SHA-1 哈希值名称，称为“引用”。引用是指向提交的指针。与不可变的对象不同，引用是可变的（可以更新以指向新的提交）。例如， `master` 引用通常指向开发主分支中的最新提交。

```
references = map<string, string>

def update_reference(name, id):
    references[name] = id

def read_reference(name):
    return references[name]

def load_reference(name_or_id):
    if name_or_id in references:
        return load(references[name_or_id])
    else:
        return load(name_or_id)
```

With this, Git can use human-readable names like “master” to refer to a particular snapshot in the history, instead of a long hexadecimal string.  
这样一来，Git 可以使用像“master”这样易于理解的名称来指代历史记录中的特定快照，而不是使用冗长的十六进制字符串。

One detail is that we often want a notion of “where we currently are” in the history, so that when we take a new snapshot, we know what it is relative to (how we set the `parents` field of the commit). In Git, that “where we currently are” is a special reference called “HEAD”.  
一个细节是，我们通常需要一个关于历史记录中“当前位置”的概念，这样当我们创建一个新的快照时，就知道它是相对于哪个提交的（也就是我们如何设置提交的 `parents` 字段）。在 Git 中，这个“当前位置”是一个叫做“HEAD”的特殊引用。

## Repositories  存储库

Finally, we can define what (roughly) is a Git _repository_: it is the data `objects` and `references`.  
最后，我们可以大致定义一下 Git _仓库_ ：它就是数据。 `objects` 和 `references` 。

On disk, all Git stores are objects and references: that’s all there is to Git’s data model. All `git` commands map to some manipulation of the commit DAG by adding objects and adding/updating references.  
在磁盘上，所有 Git 存储的内容都是对象和引用：这就是 Git 数据模型的全部内容。所有 `git` 命令都映射到对提交 DAG 的某种操作，即添加对象和添加/更新引用。

Whenever you’re typing in any command, think about what manipulation the command is making to the underlying graph data structure. Conversely, if you’re trying to make a particular kind of change to the commit DAG, e.g. “discard uncommitted changes and make the ‘master’ ref point to commit `5d83f9e`”, there’s probably a command to do it (e.g. in this case, `git checkout master; git reset --hard 5d83f9e`).  
每当你输入任何命令时，都要思考该命令会对底层图数据结构进行怎样的操作。反之，如果你试图对提交 DAG 进行某种特定类型的更改，例如“丢弃未提交的更改并将‘master’引用指向提交 `5d83f9e` ”，那么很可能存在一个命令可以实现这一点（例如，在本例中为 `git checkout master; git reset --hard 5d83f9e` ）。

# Staging area  集结区

This is another concept that’s orthogonal to the data model, but it’s a part of the interface to create commits.  
这是与数据模型正交的另一个概念，但它是创建提交的接口的一部分。

One way you might imagine implementing snapshotting as described above is to have a “create snapshot” command that creates a new snapshot based on the _current state_ of the working directory. Some version control tools work like this, but not Git. We want clean snapshots, and it might not always be ideal to make a snapshot from the current state. For example, imagine a scenario where you’ve implemented two separate features, and you want to create two separate commits, where the first introduces the first feature, and the next introduces the second feature. Or imagine a scenario where you have debugging print statements added all over your code, along with a bugfix; you want to commit the bugfix while discarding all the print statements.  
实现上述快照的一种方法是添加一个“创建快照”命令，该命令基于_当前工作目录的状态_创建一个新的快照。一些版本控制工具就是这样工作的，但 Git 并非如此。我们需要的是干净的快照，而从当前状态创建快照并非总是理想的选择。例如，假设您实现了两个独立的功能，并且想要创建两个独立的提交，第一个提交引入第一个功能，第二个提交引入第二个功能。或者，假设您的代码中到处都添加了调试打印语句，同时还修复了一个 bug；您想要提交 bug 修复，同时丢弃所有打印语句。

Git accommodates such scenarios by allowing you to specify which modifications should be included in the next snapshot through a mechanism called the “staging area”.  
Git 通过一种称为“暂存区”的机制，允许您指定哪些修改应包含在下一个快照中，从而适应这种情况。

# Git command-line interface  
Git 命令行界面

To avoid duplicating information, we’re not going to explain the commands below in detail. See the highly recommended [Pro Git](https://git-scm.com/book/en/v2) for more information, or watch the lecture video.  
为了避免信息重复，我们不会详细解释以下命令。请参阅强烈推荐的 [Pro Git。](https://git-scm.com/book/en/v2) 欲了解更多信息，请观看讲座视频。

## Basics  基础知识

-   `git help <command>`: get help for a git command  
    `git help <command>` : 获取 git 命令的帮助
-   `git init`: creates a new git repo, with data stored in the `.git` directory  
    `git init` ：创建一个新的 Git 仓库，数据存储在 `.git` 目录中。
-   `git status`: tells you what’s going on  
    `git status` ：告诉你发生了什么
-   `git add <filename>`: adds files to staging area  
    `git add <filename>` ：将文件添加到暂存区
-   `git commit`: creates a new commit  
    `git commit` ：创建一个新的提交
    -   Write [good commit messages](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)!  
        写好[提交信息](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html) ！
    -   Even more reasons to write [good commit messages](https://chris.beams.io/posts/git-commit/)!  
        编写[好的提交信息](https://chris.beams.io/posts/git-commit/)又多了一个理由！
-   `git log`: shows a flattened log of history  
    `git log` ：显示扁平化的历史记录日志
-   `git log --all --graph --decorate`: visualizes history as a DAG  
    `git log --all --graph --decorate` ：将历史可视化为有向无环图 (DAG)。
-   `git diff <filename>`: show changes you made relative to the staging area  
    `git diff <filename>` ：显示相对于暂存区所做的更改
-   `git diff <revision> <filename>`: shows differences in a file between snapshots  
    `git diff <revision> <filename>` ：显示快照之间文件的差异。
-   `git checkout <revision>`: updates HEAD (and current branch if checking out a branch)  
    `git checkout <revision>` ：更新 HEAD（如果检出分支，则更新当前分支）

## Branching and merging  分支和合并

-   `git branch`: shows branches  
    `git branch` ：显示分支
-   `git branch <name>`: creates a branch  
    `git branch <name>` ：创建一个分支
-   `git checkout -b <name>`: creates a branch and switches to it  
    `git checkout -b <name>` ：创建一个分支并切换到该分支。
    -   same as `git branch <name>; git checkout <name>`  与 `git branch <name>; git checkout <name>` 相同
-   `git merge <revision>`: merges into current branch  
    `git merge <revision>` ：合并到当前分支
-   `git mergetool`: use a fancy tool to help resolve merge conflicts  
    `git mergetool` ：使用一个强大的工具来帮助解决合并冲突
-   `git rebase`: rebase set of patches onto a new base  
    `git rebase` ：将一组补丁重新定基到新的基上

## Remotes  遥控器

-   `git remote`: list remotes  
    `git remote` ：列出远程服务器
-   `git remote add <name> <url>`: add a remote  
    `git remote add <name> <url>` : 添加远程仓库
-   `git push <remote> <local branch>:<remote branch>`: send objects to remote, and update remote reference  
    `git push <remote> <local branch>:<remote branch>` ：将对象发送到远程，并更新远程引用
-   `git branch --set-upstream-to=<remote>/<remote branch>`: set up correspondence between local and remote branch  
    `git branch --set-upstream-to=<remote>/<remote branch>` ：建立本地分支和远程分支之间的对应关系
-   `git fetch`: retrieve objects/references from a remote  
    `git fetch` ：从远程仓库检索对象/引用
-   `git pull`: same as `git fetch; git merge`  
    `git pull` ：与 `git fetch; git merge`
-   `git clone`: download repository from remote  
    `git clone` ：从远程仓库下载仓库

## Undo  撤销

-   `git commit --amend`: edit a commit’s contents/message  
    `git commit --amend` ：编辑提交的内容/消息
-   `git reset HEAD <file>`: unstage a file  
    `git reset HEAD <file>` ：取消暂存文件
-   `git checkout -- <file>`: discard changes  
    `git checkout -- <file>` : 丢弃更改

# Advanced Git  高级 Git

-   `git config`: Git is [highly customizable](https://git-scm.com/docs/git-config)  
    `git config` ：Git 具有[高度可定制性。](https://git-scm.com/docs/git-config)
-   `git clone --depth=1`: shallow clone, without entire version history  
    `git clone --depth=1` ：浅克隆，不包含完整的版本历史记录
-   `git add -p`: interactive staging  
    `git add -p` : 交互式暂存区
-   `git rebase -i`: interactive rebasing  
    `git rebase -i` ：交互式变基
-   `git blame`: show who last edited which line  
    `git blame` ：显示最后编辑了哪一行的用户
-   `git stash`: temporarily remove modifications to working directory  
    `git stash` ：临时移除对工作目录的修改
-   `git bisect`: binary search history (e.g. for regressions)  
    `git bisect` ：二分查找历史记录（例如用于回归测试）
-   `.gitignore`: [specify](https://git-scm.com/docs/gitignore) intentionally untracked files to ignore  
    `.gitignore` ： [指定](https://git-scm.com/docs/gitignore)要故意忽略的未跟踪文件

# Miscellaneous  各种各样的

-   **GUIs**: there are many [GUI clients](https://git-scm.com/downloads/guis) out there for Git. We personally don’t use them and use the command-line interface instead.  
    **图形用户界面** ：有很多[图形用户界面客户端](https://git-scm.com/downloads/guis) 。 市面上有很多 Git 工具。我们个人不使用它们，而是使用命令行。 改为界面。
-   **Shell integration**: it’s super handy to have a Git status as part of your shell prompt ([zsh](https://github.com/olivierverdier/zsh-git-prompt), [bash](https://github.com/magicmonty/bash-git-prompt)). Often included in frameworks like [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh).  
    **Shell 集成** ：在 shell 提示符（ [zsh](https://github.com/olivierverdier/zsh-git-prompt) ）中显示 Git 状态非常方便。 [bash](https://github.com/magicmonty/bash-git-prompt) ）。通常包含在 [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh) 等框架中。
-   **Editor integration**: similarly to the above, handy integrations with many features. [fugitive.vim](https://github.com/tpope/vim-fugitive) is the standard one for Vim.  
    **编辑器集成** ：与上述类似，它[提供](https://github.com/tpope/vim-fugitive)了许多便捷的功能集成。fugitive.vim 是 Vim 的标准集成。
-   **Workflows**: we taught you the data model, plus some basic commands; we didn’t tell you what practices to follow when working on big projects (and there are [many](https://nvie.com/posts/a-successful-git-branching-model/) [different](https://www.endoflineblog.com/gitflow-considered-harmful) [approaches](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)).  
    **工作流程** ：我们教了你数据模型和一些基本命令；但我们没有告诉你处理大型项目时应该遵循哪些实践（而且有[很多](https://nvie.com/posts/a-successful-git-branching-model/)[不同的](https://www.endoflineblog.com/gitflow-considered-harmful)[方法](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) ）。
-   **GitHub**: Git is not GitHub. GitHub has a specific way of contributing code to other projects, called [pull requests](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests).  
    **GitHub** ：Git 不是 GitHub。GitHub 有其特定的代码贡献方式，称为[拉取请求 (pull requests)](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests) 。
-   **Other Git providers**: GitHub is not special: there are many Git repository hosts, like [GitLab](https://about.gitlab.com/) and [BitBucket](https://bitbucket.org/).  
    **其他 Git 提供商** ：GitHub 并不特殊：还有许多其他 Git 代码库托管服务商，例如 [GitLab](https://about.gitlab.com/) 和 [BitBucket](https://bitbucket.org/) 。

# Resources  资源

-   [Pro Git](https://git-scm.com/book/en/v2) is **highly recommended reading**. Going through Chapters 1–5 should teach you most of what you need to use Git proficiently, now that you understand the data model. The later chapters have some interesting, advanced material.  
    **强烈推荐阅读** [《Pro Git》](https://git-scm.com/book/en/v2) 。既然你已经理解了数据模型，那么读完前五章应该就能掌握熟练使用 Git 所需的大部分知识。后面的章节包含一些有趣且更高级的内容。
-   [Oh Shit, Git!?!](https://ohshitgit.com/) is a short guide on how to recover from some common Git mistakes.  
    [《糟糕，Git！？！》](https://ohshitgit.com/) 是一篇关于如何从一些常见的 Git 错误中恢复的简短指南。
-   [Git for Computer Scientists](https://eagain.net/articles/git-for-computer-scientists/) is a short explanation of Git’s data model, with less pseudocode and more fancy diagrams than these lecture notes.  
    [《面向计算机科学家的 Git》](https://eagain.net/articles/git-for-computer-scientists/) 是对 Git 数据模型的简要解释，与这些讲义相比，伪代码更少，图表更精美。
-   [Git from the Bottom Up](https://jwiegley.github.io/git-from-the-bottom-up/) is a detailed explanation of Git’s implementation details beyond just the data model, for the curious.  
    [自下而上地使用 Git](https://jwiegley.github.io/git-from-the-bottom-up/) 除了数据之外，本文还详细解释了 Git 的实现细节。 模型，满足好奇心。
-   [How to explain git in simple words  
    如何用简单的语言解释 Git](https://smusamashah.github.io/blog/2017/10/14/explain-git-in-simple-words)
-   [Learn Git Branching](https://learngitbranching.js.org/) is a browser-based game that teaches you Git.  
    [Learn Git Branching](https://learngitbranching.js.org/) 是一款基于浏览器的游戏，可以教你使用 Git。

# Exercises  练习

1.  If you don’t have any past experience with Git, either try reading the first couple chapters of [Pro Git](https://git-scm.com/book/en/v2) or go through a tutorial like [Learn Git Branching](https://learngitbranching.js.org/). As you’re working through it, relate Git commands to the data model.  
    如果你之前没有任何 Git 使用经验，可以尝试阅读 [《Pro Git》](https://git-scm.com/book/en/v2) 的前几章，或者学习像 [《Learn Git Branching》](https://learngitbranching.js.org/) 这样的教程。在学习过程中，要将 Git 命令与数据模型联系起来。
2.  Clone the [repository for the class website](https://github.com/missing-semester/missing-semester).  
    克隆[课程网站的存储库](https://github.com/missing-semester/missing-semester) 。
    1.  Explore the version history by visualizing it as a graph.  
        通过图表可视化方式探索版本历史记录。
    2.  Who was the last person to modify `README.md`? (Hint: use `git log` with an argument).  
        最后修改 `README.md` 人是谁？（提示：使用带参数的 `git log` ）。
    3.  What was the commit message associated with the last modification to the `collections:` line of `_config.yml`? (Hint: use `git blame` and `git show`).  
        最后一次修改的提交信息是什么？ `collections:` `_config.yml` 中的哪一行？（提示：使用 `git blame` 和 `git show` ）。
3.  One common mistake when learning Git is to commit large files that should not be managed by Git or adding sensitive information. Try adding a file to a repository, making some commits and then deleting that file from history (you may want to look at [this](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)).  
    学习 Git 时一个常见的错误是提交应该提交的大文件。 请勿使用 Git 管理或添加敏感信息。尝试添加文件到 一个代码仓库，进行一些提交，然后从历史记录中删除该文件。 （您可能想看看） [这](https://help.github.com/articles/removing-sensitive-data-from-a-repository/) ）。
4.  Clone some repository from GitHub, and modify one of its existing files. What happens when you do `git stash`? What do you see when running `git log --all --oneline`? Run `git stash pop` to undo what you did with `git stash`. In what scenario might this be useful?  
    从 GitHub 克隆一个仓库，并修改其中一个现有文件。执行 `git stash` 会发生什么？运行 `git log --all --oneline` 会看到什么？运行 `git stash pop` 撤销你使用 `git stash` 所做的操作。这在什么情况下会有用？
5.  Like many command line tools, Git provides a configuration file (or dotfile) called `~/.gitconfig`. Create an alias in `~/.gitconfig` so that when you run `git graph`, you get the output of `git log --all --graph --decorate --oneline`. You can do this by directly [editing](https://git-scm.com/docs/git-config#Documentation/git-config.txt-alias) the `~/.gitconfig` file, or you can use the `git config` command to add the alias. Information about git aliases can be found [here](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases).  
    与许多命令行工具一样，Git 提供了一个名为 `~/.gitconfig` 的配置文件（或点文件）。在 `~/.gitconfig` 中创建一个别名，这样当你运行 `git graph` 时，就能得到 `git log --all --graph --decorate --oneline` 的输出。你可以直接这样做。 [编辑](https://git-scm.com/docs/git-config#Documentation/git-config.txt-alias) 在 `~/.gitconfig` 文件中添加配置，或者您可以使用 `git config` 命令添加配置。 别名。有关 Git 别名的信息可以在这里找到。 [这里](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases) 。
6.  You can define global ignore patterns in `~/.gitignore_global` after running `git config --global core.excludesfile ~/.gitignore_global`. This sets the location of the global ignore file that Git will use, but you still need to manually create the file at that path. Set up your global gitignore file to ignore OS-specific or editor-specific temporary files, like `.DS_Store`.  
    运行后，您可以在 `~/.gitignore_global` 中定义全局忽略模式。 `git config --global core.excludesfile ~/.gitignore_global` 。这将设置 Git 将使用的全局忽略文件的位置，但您仍然需要在该路径手动创建该文件。设置全局 gitignore 文件以忽略操作系统或编辑器特定的临时文件，例如 `.DS_Store` 。
7.  Fork the [repository for the class website](https://github.com/missing-semester/missing-semester), find a typo or some other improvement you can make, and submit a pull request on GitHub (you may want to look at [this](https://github.com/firstcontributions/first-contributions)). Please only submit PRs that are useful (don’t spam us, please!). If you can’t find an improvement to make, you can skip this exercise.  
    请 fork [课程网站的仓库](https://github.com/missing-semester/missing-semester) ，找出拼写错误或其他可以改进的地方，然后在 GitHub 上提交 pull request（您可以参考[这里](https://github.com/firstcontributions/first-contributions) ）。请只提交有用的 PR（请勿发送垃圾信息！）。如果您找不到任何可以改进的地方，可以跳过此练习。


* * *
