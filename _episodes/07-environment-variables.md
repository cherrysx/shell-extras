---
title: Shell环境变量
teaching: 10
exercises: 0
questions:
- "如何在 Linux shell 中设置和访问变量？"
objectives:
- "了解变量是如何在 shell 中实现的"
- "解释 shell 如何使用 `PATH` 变量来搜索可执行文件"
- "读取现有变量的值"
- "创建新变量并更改其值"
keypoints:
- "Shell 变量默认被视为字符串"
- "`PATH` 变量定义了 shell 的搜索路径"
- "使用 \"`=`\" 分配变量并使用前缀为 \"`$`\" 的变量名称调用"
---

shell 只是一个程序，和其他程序一样，它也有变量。
这些变量控制着它的执行，
所以通过改变他们的价值观
您可以更改 shell 和其他程序的行为方式。

让我们首先运行命令 `set` 并查看典型 shell 会话中的一些变量：

~~~
$ set
~~~
{: .bash}

~~~
COMPUTERNAME=TURING
HOME=/home/vlad
HOMEDRIVE=C:
HOSTNAME=TURING
HOSTTYPE=i686
NUMBER_OF_PROCESSORS=4
OS=Windows_NT
PATH=/Users/vlad/bin:/usr/local/git/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin
PWD=/home/vlad
UID=1000
USERNAME=vlad
...
~~~
{: .output}

正如你所看到的，有很多——事实上，比这里显示的多四到五倍。
是的，
使用 `set` 来 *show* 事情可能看起来有点奇怪，
即使对于 Unix，
但如果你不给它任何论据，
它也可以向您展示您*可以*设置的东西。

每个变量都有一个名称。
按照惯例，总是存在的变量被赋予大写名称。
所有 shell 变量的值都是字符串，即使是那些看起来像数字的（如 `UID`）。
必要时由程序将这些字符串转换为其他类型。
例如，如果一个程序想知道计算机有多少个处理器，
它将“NUMBER_OF_PROCESSORS”变量的值从字符串转换为整数。

类似地，一些变量（如`PATH`）存储值列表。
在这种情况下，约定是使用冒号“:”作为分隔符。
如果一个程序想要这样一个列表的各个元素，
程序负责将变量的字符串值分成几部分。

## `PATH` 变量

让我们仔细看看那个 PATH 变量。
它的值定义了 shell 的 [搜索路径]({{ page.root }}/reference/{{ site.index }}#search-path)，
即，shell 查找可运行程序的目录列表
当您键入程序名称而不指定它所在的目录时。

例如，
当我们输入一个像“分析”这样的命令时，
shell需要决定是运行`./analyze`还是`/bin/analyze`。
它使用的规则很简单：
shell依次检查`PATH`变量中的每个目录，
在该目录中查找具有请求名称的程序。
一旦找到匹配项，它就会停止搜索并运行程序。

为了展示这是如何工作的，
以下是每行列出一个“PATH”的组件：

~~~
/Users/vlad/bin
/usr/local/git/bin
/usr/bin
/bin
/usr/sbin
/sbin
/usr/local/bin
~~~
{: .output}

在我们的电脑上，
实际上有三个名为“分析”的程序
在三个不同的目录中：
`/bin/analyze`,
`/usr/local/bin/analyze`,
和`/users/vlad/analyze`。
由于 shell 按照它们在 `PATH` 中列出的顺序搜索目录，
它首先找到 `/bin/analyze` 并运行它。
请注意，它将*永远*找不到程序`/users/vlad/analyze`
除非我们输入程序的完整路径，
因为目录 `/users/vlad` 不在 `PATH` 中。

## 显示变量的值

让我们显示变量 `HOME` 的值：

~~~
$ echo HOME
~~~
{: .bash}

~~~
HOME
~~~
{: .output}

那只是打印“HOME”，这不是我们想要的
（尽管这是我们实际要求的）。
让我们试试这个：

~~~
$ echo $HOME
~~~
{: .bash}

~~~
/home/vlad
~~~
{: .output}

$符号告诉 shell 我们想要变量的 *value*
而不是它的名字。
这就像通配符一样工作：
在运行我们要求的程序之前，shell 会进行替换。
由于这个扩展，我们实际运行的是`echo /home/vlad`，
显示正确的东西。

## 创建和更改变量

创建变量很容易——我们只需使用“=”为名称赋值：

~~~
$ SECRET_IDENTITY=Dracula
$ echo $SECRET_IDENTITY
~~~
{: .bash}

~~~
Dracula
~~~
{: .output}

要更改值，只需分配一个新值：

~~~
$ SECRET_IDENTITY=Camilla
$ echo $SECRET_IDENTITY
~~~
{: .bash}

~~~
Camilla
~~~
{: .output}

如果我们想在每次运行 shell 时自动设置一些变量，
我们可以将执行此操作的命令放入主目录中名为 `.bashrc` 的文件中。
（前面的 '.' 字符阻止 `ls` 列出此文件
除非我们特别要求它使用 `-a`：
我们通常不想担心它。
末尾的“rc”是“run control”的缩写，
这意味着几十年前非常重要的事情，
现在只是一个惯例，每个人都遵循而不理解为什么。）

例如，
这是`/home/vlad/.bashrc`中的两行：

~~~
export SECRET_IDENTITY=Dracula
export TEMP_DIR=/tmp
export BACKUP_DIR=$TEMP_DIR/backup
~~~
{: .output}

这三行创建了变量 `SECRET_IDENTITY`，
`TEMP_DIR`,
和`BACKUP_DIR`，
并导出它们，以便 shell 运行的任何程序也可以看到它们。
请注意，`BACKUP_DIR` 的定义依赖于 `TEMP_DIR` 的值，
这样如果我们改变放置临时文件的位置，
我们的备份将自动重新定位。

当我们在这里时，
使用 `alias` 命令为我们经常输入的内容创建快捷方式也很常见。
例如，我们可以定义别名`backup`
使用一组特定的参数运行`/bin/zback`：

~~~
alias backup=/bin/zback -v --nostir -R 20000 $HOME $BACKUP_DIR
~~~
{: .bash}

如你看到的，
别名可以为我们节省很多打字时间，从而减少很多打字错误。
您可以找到其他别名的有趣建议
通过搜索“sample bashrc”和其他 bash 技巧
在您最喜欢的搜索引擎中。

{% include links.md %}
