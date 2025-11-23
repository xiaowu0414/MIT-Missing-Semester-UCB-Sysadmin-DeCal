---
date: 2025-11-15
---
# Metaprogramming  元编程

What do we mean by “metaprogramming”? Well, it was the best collective term we could come up with for the set of things that are more about _process_ than they are about writing code or working more efficiently. In this lecture, we will look at systems for building and testing your code, and for managing dependencies. These may seem like they are of limited importance in your day-to-day as a student, but the moment you interact with a larger code base through an internship or once you enter the “real world”, you will see this everywhere. We should note that “metaprogramming” can also mean “[programs that operate on programs](https://en.wikipedia.org/wiki/Metaprogramming)”, whereas that is not quite the definition we are using for the purposes of this lecture.  
“元编程”是什么意思？嗯，它是最好的集体编程。 我们可以想出一个术语来指代这一系列更……的事物。 与其说是编写代码或提高工作效率，不如说是_流程_管理。本次讲座我们将探讨构建和测试代码以及​​管理依赖项的系统。作为学生，这些内容在日常学习中可能看起来并不重要，但一旦你通过实习接触到更大的代码库，或者真正步入职场，你就会发现它们无处不在。需要注意的是，“元编程”也可以指“ [对程序进行操作的程序](https://en.wikipedia.org/wiki/Metaprogramming) ”，但这并非本次讲座所使用的定义。

# Build systems  构建系统

If you write a paper in LaTeX, what are the commands you need to run to produce your paper? What about the ones used to run your benchmarks, plot them, and then insert that plot into your paper? Or to compile the code provided in the class you’re taking and then running the tests?  
如果用 LaTeX 写论文，需要运行哪些命令才能生成论文？运行基准测试、绘制图表并将图表插入论文又需要哪些命令？或者编译课程中提供的代码并运行测试又需要哪些命令？

For most projects, whether they contain code or not, there is a “build process”. Some sequence of operations you need to do to go from your inputs to your outputs. Often, that process might have many steps, and many branches. Run this to generate this plot, that to generate those results, and something else to produce the final paper. As with so many of the things we have seen in this class, you are not the first to encounter this annoyance, and luckily there exist many tools to help you!  
大多数项目，无论是否包含代码，都存在一个“构建过程”。你需要执行一系列操作，才能从输入生成输出。通常，这个过程可能包含许多步骤和分支。例如，运行此程序生成此图，运行彼程序生成这些结果，以及运行其他程序生成最终论文。正如我们在本课程中看到的许多事情一样，你并非第一个遇到这种烦恼的人，幸运的是，有很多工具可以帮助你！

These are usually called “build systems”, and there are _many_ of them. Which one you use depends on the task at hand, your language of preference, and the size of the project. At their core, they are all very similar though. You define a number of _dependencies_, a number of _targets_, and _rules_ for going from one to the other. You tell the build system that you want a particular target, and its job is to find all the transitive dependencies of that target, and then apply the rules to produce intermediate targets all the way until the final target has been produced. Ideally, the build system does this without unnecessarily executing rules for targets whose dependencies haven’t changed and where the result is available from a previous build.  
这些通常被称为“构建系统”，而且种类_繁多_ 。具体使用哪一个取决于手头的任务、你偏好的编程语言以及项目规模。不过，它们的核心原理都非常相似。你需要定义一些_依赖项_ ，以及一些…… 构建系统需要_设定目标_ ，并制定从一个目标到另一个目标的转换_规则_ 。你告诉构建系统你想要一个特定的目标，它的任务是找到该目标的所有传递依赖项，然后应用这些规则生成中间目标，直到最终目标生成为止。理想情况下，构建系统在执行此操作时，不会不必要地重复执行那些依赖项未发生变化且结果已在先前构建中可用的目标的规则。

`make` is one of the most common build systems out there, and you will usually find it installed on pretty much any UNIX-based computer. It has its warts, but works quite well for simple-to-moderate projects. When you run `make`, it consults a file called `Makefile` in the current directory. All the targets, their dependencies, and the rules are defined in that file. Let’s take a look at one:  
`make` 是最常见的构建系统之一，几乎所有基于 UNIX 的计算机都预装了它。它虽然有一些缺点，但对于简单到中等的项目来说已经足够好用了。运行 `make` 时，它​​会读取当前目录下的 `Makefile` 文件。所有目标、它们的依赖项以及规则都在该文件中定义。我们来看一个例子：

```
paper.pdf: paper.tex plot-data.png
	pdflatex paper.tex

plot-%.png: %.dat plot.py
	./plot.py -i $*.dat -o $@
```

Each directive in this file is a rule for how to produce the left-hand side using the right-hand side. Or, phrased differently, the things named on the right-hand side are dependencies, and the left-hand side is the target. The indented block is a sequence of programs to produce the target from those dependencies. In `make`, the first directive also defines the default goal. If you run `make` with no arguments, this is the target it will build. Alternatively, you can run something like `make plot-data.png`, and it will build that target instead.  
此文件中的每条指令都是一条规则，用于说明如何使用右侧的内容生成左侧的内容。或者换句话说，右侧列出的内容是依赖项，左侧是目标。缩进的代码块是一系列程序，用于根据这些依赖项生成目标。在 `make` 中，第一条指令还定义了默认目标。如果您运行 `make` 时不带任何参数，则默认目标为 \`make\`。 它将构建的目标。或者，您可以运行类似这样的命令。 `make plot-data.png` ，它将构建该目标文件。

The `%` in a rule is a “pattern”, and will match the same string on the left and on the right. For example, if the target `plot-foo.png` is requested, `make` will look for the dependencies `foo.dat` and `plot.py`. Now let’s look at what happens if we run `make` with an empty source directory.  
规则中的 `%` 是一个“模式”，它会匹配左右两侧相同的字符串。例如，如果请求目标 `plot-foo.png` ， `make` 会查找依赖项 `foo.dat` 和 foo.dat。 `plot.py` 现在让我们看看如果使用空的源目录运行 `make` 会发生什么。

```
$ make
make: *** No rule to make target 'paper.tex', needed by 'paper.pdf'.  Stop.
```

`make` is helpfully telling us that in order to build `paper.pdf`, it needs `paper.tex`, and it has no rule telling it how to make that file. Let’s try making it!  
`make` 很贴心地告诉我们，要生成 `paper.pdf` ，它需要 `paper.tex` ，但它没有规则告诉它如何生成这个文件。让我们试试看！

```
$ touch paper.tex
$ make
make: *** No rule to make target 'plot-data.png', needed by 'paper.pdf'.  Stop.
```

Hmm, interesting, there _is_ a rule to make `plot-data.png`, but it is a pattern rule. Since the source files do not exist (`data.dat`), `make` simply states that it cannot make that file. Let’s try creating all the files:  
嗯，有意思，确实_有_一条规则可以生成 `plot-data.png` ，但这是一条模式规则。由于源文件（ `data.dat` ）不存在，所以 `make` 它只是说无法创建该文件。我们来尝试创建所有文件。 文件：

```
$ cat paper.tex
\documentclass{article}
\usepackage{graphicx}
\begin{document}
\includegraphics[scale=0.65]{plot-data.png}
\end{document}
$ cat plot.py
#!/usr/bin/env python
import matplotlib
import matplotlib.pyplot as plt
import numpy as np
import argparse

parser = argparse.ArgumentParser()
parser.add_argument('-i', type=argparse.FileType('r'))
parser.add_argument('-o')
args = parser.parse_args()

data = np.loadtxt(args.i)
plt.plot(data[:, 0], data[:, 1])
plt.savefig(args.o)
$ cat data.dat
1 1
2 2
3 3
4 4
5 8
```

Now what happens if we run `make`?  
如果运行 `make` 会发生什么？

```
$ make
./plot.py -i data.dat -o plot-data.png
pdflatex paper.tex
... lots of output ...
```

And look, it made a PDF for us! What if we run `make` again?  
你看，它生成了一个 PDF 文件！如果我们再运行一​​次 `make` 呢？

```
$ make
make: 'paper.pdf' is up to date.
```

It didn’t do anything! Why not? Well, because it didn’t need to. It checked that all of the previously-built targets were still up to date with respect to their listed dependencies. We can test this by modifying `paper.tex` and then re-running `make`:  
它什么也没做！为什么？因为它不需要做。 检查了所有先前构建的目标是否仍然是最新的。 就其列出的依赖项而言，我们可以通过修改来测试这一点。 `paper.tex` 然后重新运行 `make` ：

```
$ vim paper.tex
$ make
pdflatex paper.tex
...
```

Notice that `make` did _not_ re-run `plot.py` because that was not necessary; none of `plot-data.png`’s dependencies changed!  
请注意， `make` _没有_重新运行 `plot.py` 因为没有必要； `plot-data.png` 的任何依赖项都没有改变！

# Dependency management  依赖关系管理

At a more macro level, your software projects are likely to have dependencies that are themselves projects. You might depend on installed programs (like `python`), system packages (like `openssl`), or libraries within your programming language (like `matplotlib`). These days, most dependencies will be available through a _repository_ that hosts a large number of such dependencies in a single place, and provides a convenient mechanism for installing them. Some examples include the Ubuntu package repositories for Ubuntu system packages, which you access through the `apt` tool, RubyGems for Ruby libraries, PyPI for Python libraries, or the Arch User Repository for Arch Linux user-contributed packages.  
从更宏观的角度来看，你的软件项目很可能依赖于一些本身也是项目的项目。你可能依赖于已安装的程序（例如 `python` ）、系统软件包（例如 `openssl` ）或编程语言中的库（例如 `matplotlib` ）。如今，大多数依赖项都可以通过软件_仓库_获取，这些仓库将大量此类依赖项集中在一个地方，并提供便捷的安装机制。例如，Ubuntu 系统软件包的 Ubuntu 软件包仓库（可通过 `apt` 工具访问）、Ruby 库的 RubyGems、Python 库的 PyPI，以及 Arch Linux 用户贡献软件包的 Arch 用户仓库。

Since the exact mechanisms for interacting with these repositories vary a lot from repository to repository and from tool to tool, we won’t go too much into the details of any specific one in this lecture. What we _will_ cover is some of the common terminology they all use. The first among these is _versioning_. Most projects that other projects depend on issue a _version number_ with every release. Usually something like 8.1.3 or 64.1.20192004. They are often, but not always, numerical. Version numbers serve many purposes, and one of the most important of them is to ensure that software keeps working. Imagine, for example, that I release a new version of my library where I have renamed a particular function. If someone tried to build some software that depends on my library after I release that update, the build might fail because it calls a function that no longer exists! Versioning attempts to solve this problem by letting a project say that it depends on a particular version, or range of versions, of some other project. That way, even if the underlying library changes, dependent software continues building by using an older version of my library.  
由于与这些存储库交互的具体机制各不相同 我们经常需要在不同的代码库和工具之间切换，所以我们不会这样做。 本次讲座不会过多地深入探讨任何具体细节。 _本文将_介绍一些它们都常用的术语。首先是_版本控制_ 。大多数被其他项目依赖的项目都会在每次发布时发布一个_版本号_ ，例如 8.1.3 或 64.1.20192004。版本号通常是数字，但不总是如此。版本号有很多用途，其中最重要的用途之一是确保软件能够持续运行。例如，假设我发布了一个新版本的库，其中重命名了一个特定的函数。如果有人在我发布更新后尝试构建依赖于我的库的软件，构建可能会失败，因为它调用了一个已不存在的函数！版本控制试图通过允许项目声明它依赖于其他项目的特定版本或版本范围来解决这个问题。这样，即使底层库发生更改，依赖的软件也可以通过使用旧版本的库继续构建。

That also isn’t ideal though! What if I issue a security update which does _not_ change the public interface of my library (its “API”), and which any project that depended on the old version should immediately start using? This is where the different groups of numbers in a version come in. The exact meaning of each one varies between projects, but one relatively common standard is [_semantic versioning_](https://semver.org/). With semantic versioning, every version number is of the form: major.minor.patch. The rules are:  
但这也不是理想的解决方案！如果我发布一个安全更新，但该更新_不会_更改库的公共接口（即“API”），并且所有依赖旧版本的项目都应该立即开始使用新版本，该怎么办？这就涉及到版本号中不同数字组的作用了。每个数字组的具体含义因项目而异，但[_语义化版本控制_](https://semver.org/)是一个相对常见的标准。在语义化版本控制中，每个版本号的格式为：主版本号.次版本号.补丁版本号。规则如下：

-   If a new release does not change the API, increase the patch version.  
    如果新版本没有改变 API，则增加补丁版本号。
-   If you _add_ to your API in a backwards-compatible way, increase the minor version.  
    如果以向后兼容的方式向 API _添加新_功能，请增加次版本号。
-   If you change the API in a non-backwards-compatible way, increase the major version.  
    如果以不向后兼容的方式更改 API，请增加主版本号。

This already provides some major advantages. Now, if my project depends on your project, it _should_ be safe to use the latest release with the same major version as the one I built against when I developed it, as long as its minor version is at least what it was back then. In other words, if I depend on your library at version `1.3.7`, then it _should_ be fine to build it with `1.3.8`, `1.6.1`, or even `1.3.0`. Version `2.2.4` would probably not be okay, because the major version was increased. We can see an example of semantic versioning in Python’s version numbers. Many of you are probably aware that Python 2 and Python 3 code do not mix very well, which is why that was a _major_ version bump. Similarly, code written for Python 3.5 might run fine on Python 3.7, but possibly not on 3.4.  
这已经带来了一些主要优势。现在，如果我的项目依赖于你的项目， _那么只要_最新版本的主版本号与我开发时所用的版本相同，并且次版本号至少与当时的版本号相同，那么使用最新版本应该是安全的。换句话说，如果我依赖于你的 `1.3.7` 版本库，那么它_应该……_ 使用 `1.3.8` 甚至 `1.6.1` 版本构建都没问题 `1.3.0` `2.2.4` 可能不太合适，因为主版本号增加了。Python 的版本号就是语义化版本控制的一个例子。很多人可能都知道 Python 2 和 Python 3 的代码兼容性很差，所以 Python 3 的版本号才会增加一个_主_版本号。同样地，为 Python 3.5 编写的代码可能在 Python 3.7 上运行良好，但在 Python 3.4 上可能无法运行。

When working with dependency management systems, you may also come across the notion of _lock files_. A lock file is simply a file that lists the exact version you are _currently_ depending on of each dependency. Usually, you need to explicitly run an update program to upgrade to newer versions of your dependencies. There are many reasons for this, such as avoiding unnecessary recompiles, having reproducible builds, or not automatically updating to the latest version (which may be broken). An extreme version of this kind of dependency locking is _vendoring_, which is where you copy all the code of your dependencies into your own project. That gives you total control over any changes to it, and lets you introduce your own changes to it, but also means you have to explicitly pull in any updates from the upstream maintainers over time.  
在使用依赖管理系统时，您可能还会遇到_锁定文件（lock file）_ 的概念。锁定文件就是一个列出您_当前_所依赖的每个依赖项的确切版本的文件。 依赖项。通常，您需要显式运行更新程序才能更新。 升级到更高版本的依赖项。原因有很多。 为此，例如避免不必要的重新编译，以及确保结果可复现。 构建版本，或者无法自动更新到最新版本（这可能导致） （被破坏）。这种依赖锁定的极端版本​​是 _vendoring（_ 依赖项导入）是指将所有依赖项的代码复制到自己的项目中。这样，您可以完全控制对依赖项的任何更改，并允许您引入自己的更改，但也意味着您必须显式地从上游维护者那里获取任何更新。

# Continuous integration systems  
持续集成系统

As you work on larger and larger projects, you’ll find that there are often additional tasks you have to do whenever you make a change to it. You might have to upload a new version of the documentation, upload a compiled version somewhere, release the code to pypi, run your test suite, and all sort of other things. Maybe every time someone sends you a pull request on GitHub, you want their code to be style checked and you want some benchmarks to run? When these kinds of needs arise, it’s time to take a look at continuous integration.  
随着项目规模越来越大，你会发现每次修改代码后，通常都需要完成一些额外的任务。例如，你可能需要上传新版本的文档、将编译好的版本上传到某个地方、将代码发布到 PyPI、运行测试套件等等。或许每次有人在 GitHub 上提交 pull request 时，你都希望代码风格得到检查，并且运行一些基准测试？当出现这些需求时，就该考虑持续集成了。

Continuous integration, or CI, is an umbrella term for “stuff that runs whenever your code changes”, and there are many companies out there that provide various types of CI, often for free for open-source projects. Some of the big ones are Travis CI, Azure Pipelines, and GitHub Actions. They all work in roughly the same way: you add a file to your repository that describes what should happen when various things happen to that repository. By far the most common one is a rule like “when someone pushes code, run the test suite”. When the event triggers, the CI provider spins up a virtual machines (or more), runs the commands in your “recipe”, and then usually notes down the results somewhere. You might set it up so that you are notified if the test suite stops passing, or so that a little badge appears on your repository as long as the tests pass.  
持续集成（CI）是一个统称，指的是“代码变更时自动运行的一系列操作”。许多公司提供各种类型的 CI 服务，通常为开源项目免费提供。其中一些比较知名的 CI 服务包括 Travis CI、Azure Pipelines 和 GitHub Actions。它们的工作原理大致相同：你需要在代码仓库中添加一个文件，描述当仓库发生各种变化时应该执行的操作。最常见的规则是“当有人推送代码时，运行测试套件”。当事件触发时，CI 提供商会启动一个或多个虚拟机，运行你“配置”文件中的命令，然后通常会将结果记录下来。你可以设置当测试套件不再通过时收到通知，或者在测试通过时在代码仓库上显示一个小徽章。

As an example of a CI system, the class website is set up using GitHub Pages. Pages is a CI action that runs the Jekyll blog software on every push to `master` and makes the built site available on a particular GitHub domain. This makes it trivial for us to update the website! We just make our changes locally, commit them with git, and then push. CI takes care of the rest.  
以持续集成 (CI) 系统为例，课程网站使用 GitHub Pages 搭建。Pages 是一个 CI 操作，它会在每次向 `master` 分支推送代码时运行 Jekyll 博客软件，并将构建好的网站发布到指定的 GitHub 域名上。这使得我们更新网站变得非常简单！我们只需在本地进行更改，使用 Git 提交，然后推送即可。CI 会自动处理其余的工作。

## A brief aside on testing  
关于测试的简要说明

Most large software projects come with a “test suite”. You may already be familiar with the general concept of testing, but we thought we’d quickly mention some approaches to testing and testing terminology that you may encounter in the wild:  
大多数大型软件项目都会配备“测试套件”。您可能已经熟悉测试的一般概念，但我们想快速介绍一些您可能会在实际工作中遇到的测试方法和测试术语：

-   Test suite: a collective term for all the tests  
    测试套件：所有测试的统称
-   Unit test: a “micro-test” that tests a specific feature in isolation  
    单元测试：一种“微型测试”，用于单独测试某个特定功能。
-   Integration test: a “macro-test” that runs a larger part of the system to check that different feature or components work _together_.  
    集成测试：一种“宏测试”，它运行系统的大部分，以检查不同的功能或组件是否_协同_工作。
-   Regression test: a test that implements a particular pattern that _previously_ caused a bug to ensure that the bug does not resurface.  
    回归测试：一种实现特定模式的测试， _之前_故意制造了一个漏洞，以确保该漏洞不会再次出现。
-   Mocking: to replace a function, module, or type with a fake implementation to avoid testing unrelated functionality. For example, you might “mock the network” or “mock the disk”.  
    模拟：用虚假的实现替换函数、模块或类型，以避免测试无关的功能。例如，您可以“模拟网络”或“模拟磁盘”。

# Exercises  练习

1.  Most makefiles provide a target called `clean`. This isn’t intended to produce a file called `clean`, but instead to clean up any files that can be re-built by make. Think of it as a way to “undo” all of the build steps. Implement a `clean` target for the `paper.pdf` `Makefile` above. You will have to make the target [phony](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html). You may find the [`git ls-files`](https://git-scm.com/docs/git-ls-files) subcommand useful. A number of other very common make targets are listed [here](https://www.gnu.org/software/make/manual/html_node/Standard-Targets.html#Standard-Targets).  
    大多数 Makefile 都提供了一个名为 `clean` 目标。它的目的并非生成一个名为 `clean` 文件，而是清理所有可以被 make 重新构建的文件。你可以把它理解为一种“撤销”所有构建步骤的方法。请为上面的 `paper.pdf` `Makefile` 实现一个 `clean` 目标。你需要执行 make 命令来创建该目标。 [伪造的](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html) 。您可能会发现 [`git ls-files`](https://git-scm.com/docs/git-ls-files) 子命令很有用。 还列出了一些其他非常常见的目标物。 [这里](https://www.gnu.org/software/make/manual/html_node/Standard-Targets.html#Standard-Targets) 。
2.  Take a look at the various ways to specify version requirements for dependencies in [Rust’s build system](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html). Most package repositories support similar syntax. For each one (caret, tilde, wildcard, comparison, and multiple), try to come up with a use-case in which that particular kind of requirement makes sense.  
    了解一下 [Rust 构建系统](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html)中指定依赖项版本要求的各种方法。大多数包仓库都支持类似的语法。对于每一种语法（插入符号、波浪号、通配符、比较符号和多选符号），试着找出一种适合使用这种特定要求的用例。
3.  Git can act as a simple CI system all by itself. In `.git/hooks` inside any git repository, you will find (currently inactive) files that are run as scripts when a particular action happens. Write a [`pre-commit`](https://git-scm.com/docs/githooks#_pre_commit) hook that runs `make paper.pdf` and refuses the commit if the `make` command fails. This should prevent any commit from having an unbuildable version of the paper.  
    Git 本身就可以作为一个简单的持续集成系统。在 `.git/hooks` 中 在任何 Git 仓库中，您都会找到（当前不活跃的）文件 这些脚本会在特定操作发生时运行。编写一个 [`pre-commit`](https://git-scm.com/docs/githooks#_pre_commit) hook 会运行 `make paper.pdf` ，如果 `make` 则拒绝提交。 命令失败。这应该可以防止任何提交出现问题。 论文的无法构建的版本。
4.  Set up a simple auto-published page using [GitHub Pages](https://pages.github.com/). Add a [GitHub Action](https://github.com/features/actions) to the repository to run `shellcheck` on any shell files in that repository (here is [one way to do it](https://github.com/marketplace/actions/shellcheck)). Check that it works!  
    使用 [GitHub Pages](https://pages.github.com/) 设置一个简单的自动发布页面。向该仓库添加一个 [GitHub Action](https://github.com/features/actions) ，用于对该仓库中的所有 shell 文件运行 `shellcheck` （这里[提供了一种方法](https://github.com/marketplace/actions/shellcheck) ）。检查它是否有效！
5.  [Build your own](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/building-actions) GitHub action to run [`proselint`](https://github.com/amperser/proselint) or [`write-good`](https://github.com/btford/write-good) on all the `.md` files in the repository. Enable it in your repository, and check that it works by filing a pull request with a typo in it.  
    [打造你自己的](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/building-actions) GitHub 操作以运行 [`proselint`](https://github.com/amperser/proselint) 或 [`write-good`](https://github.com/btford/write-good) 一切都好 仓库中的 `.md` 文件。在您的仓库中启用此功能，并通过提交一个包含拼写错误的拉取请求来检查其是否有效。

* * *