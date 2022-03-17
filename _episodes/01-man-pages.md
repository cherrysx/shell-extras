---
title: "用户手册"
teaching: 10
exercises: 0
questions:
- "如何使用手册？"
objectives:
- "使用 `man` 显示给定命令的手册。" 
- "解释如何在使用“man”阅读给定命令的概要。"
- "在手册中搜索给定命令的特定选项或标志。 " 
keypoints:
- "`man command` 显示给定命令的手册。"
- "`[OPTION]...` 表示给定命令后面可以跟一个或多个可选标志。"
- "在省略号之后指定的标志仍然是可选的，但必须在所有其他标志之后。"
- "在手册中，使用 `/` 后跟您的字符串模式进行交互式搜索。"
---

我们可以使用 `man` 获得任何Linux命令的帮助（manual的缩写）命令。例如，这是查找有关cp信息的命令：

~~~
$ man cp
~~~
{: .bash}

显示的输出称为“手册页”。

手册将显示在我们 shell 的默认文件查看器中，这通常是一个名为“more”的程序。当`more`显示冒号':'时，我们可以按空格键进入下一页，字母“h”寻求帮助，或字母“q”退出。

`man` 的输出通常是完整但简洁的，
因为它旨在用作参考而不是教程。
大多数手册页分为几个部分：

* NAME：
  给出命令的名称和简要说明
* SYNOPSIS：
  如何运行命令，包括可选参数和强制参数。（我们稍后会解释语法。）
* DESCRIPTION：
  比概要更完整的描述，包括对命令的所有选项的描述。本节还可能包括示例用法或有关该命令如何工作的详细信息。
* EXAMPLES：
  不言自明。
* SEE ALSO：
  列出我们可能觉得有用的其他命令或其他可能对我们有帮助的信息来源。

我们可能会看到的其他部分包括作者、报告错误、版权、历史、（已知）错误和兼容性(AUTHOR, REPORTING BUGS, COPYRIGHT, HISTORY, (known) BUGS, and COMPATIBILITY)。

## 如何阅读概要

以下是Linux上 `cp` 命令的概要：

~~~
SYNOPSIS
   cp [OPTION]... [-T] SOURCE DEST
   cp [OPTION]... SOURCE... DIRECTORY
   cp [OPTION]... -t DIRECTORY SOURCE...
~~~
{: .output}

这告诉读者，有三种方法可以使用该命令。我们来看第一种用法：

~~~
cp [OPTION]... [-T] SOURCE DEST
~~~
{: .output}

`[OPTION]` 表示 `cp` 命令后面可以跟一个或多个可选的 [flags]({{ page.root }}/reference/{{ site.index }}#command)。
我们可以说它们是可选的，因为方括号，我们可以看出，由于省略号 (...)，一个或多个是受欢迎的。
例如，`[-T]` 在方括号中的事实，但在省略号之后，意味着它是可选的，
但如果使用，它必须在所有其他选项之后。

`SOURCE` 指的是源文件或目录，和 `DEST` 到目标文件或目录。它们的确切含义在“DESCRIPTION”部分的顶部进行了解释。

其他两个用法示例可以以类似的方式阅读。请注意，要使用最后一个，`-t` 选项是强制性的（因为它没有显示在方括号中）。

`DESCRIPTION` 部分以几段开始，解释命令及其使用，然后一一扩展可能的选项：

~~~
     The following options are available:

     -a    Same as -pPR options. Preserves structure and attributes of
           files but not directory structure.

     -f    If the destination file cannot be opened, remove it and create
           a new file, without prompting for confirmation regardless of
           its permissions.  (The -f option overrides any previous -n
           option.)

           The target file is not unlinked before the copy.  Thus, any
           existing access rights will be retained.

      ...  ...
~~~
{: .output}

## 寻找特定选项的帮助

如果我们想跳到您感兴趣的选项，我们可以使用斜杠键'/'来搜索它。（这不是 `man` 命令的一部分：这是`more`的一个特性。）
例如，了解`-t`，我们可以输入 `/-t` 并按回车键。在那之后，我们可以使用“n”键导航到下一场比赛直到我们找到我们需要的详细信息：

~~~
-t, --target-directory=DIRECTORY
     copy all SOURCE arguments into DIRECTORY
~~~
{: .output}

这意味着此选项具有短格式 `-t` 和长格式 `--target-directory`并且它需要一个参数。
其含义是将所有 SOURCE 参数复制到 DIRECTORY 中。因此，我们可以明确地给出目的地而不是依赖于必须将目录放在最后。

## 手册页的限制

手册页可用于快速确认如何运行命令，但它们并不以可读性而闻名。如果您在手册页中找不到您需要的内容&mdash;或者你无法理解你发现了什么&mdash;
尝试在您喜欢的搜索引擎中输入“Linux 命令复制文件”：它通常会产生更有用的结果。

> ## 更多资料
>
> [explainshell.com](http://explainshell.com/) 网站
> 在将复杂的 Linux 命令分解为多个部分方面做得很好
> 并解释各自的作用。
{: .callout}

{% include links.md %}
