---
title: 别名和bash自定义
teaching: 10
exercises: 0
questions:
- "如何自定义我的 bash 环境？"
objectives:
- "创建alias."
- "将自定义添加到 `.bashrc` 和 `.bash_profile` 文件。"
- "在 bash 环境中更改提示。"
keypoints:
- "别名用于创建alias或缩写"
- "`.bashrc` 和 `.bash_profile` 文件允许我们自定义我们的bash 环境。"
- "可以更改 `PS1` 系统变量以迅速的自定义您的bash。"
---

Bash 允许我们自定义环境来填充我们自己的环境特殊需要。

## 别名

有时我们需要使用必须输入的长命令
再次。 幸运的是，`alias` 命令允许我们创建
这些长命令的快捷方式。

例如，让我们为上升一、二或三级父目录创建别名目录。

~~~
alias up='cd ..'
alias upup='cd ../..'
alias upupup='cd ../../..'
~~~
{: .bash}

让我们试试这些命令。

~~~
cd /usr/local/bin
upup
pwd
~~~
{: .bash}

~~~
/usr
~~~
{: .output}

我们还可以使用 `unalias` 删除快捷方式。

~~~
unalias upupup
~~~
{: .bash}

如果我们在 bash 会话中创建这些别名之一，它们只会
持续到该会话结束。 幸运的是，bash 允许我们
指定在我们开始新的 bash 时将起作用的自定义项
会议。

## Bash 自定义文件

Bash 环境可以通过向
我们家中的`.bashrc`、`.bash_profile`和`.bash_logout`文件
目录。 `.bashrc` 文件在输入时执行
交互式非登录 shell，而 `.bash_profile` 被执行
登录shell。 如果 `.bash_logout` 文件存在，那么它将被运行
退出 shell 会话后。

让我们将上述命令添加到我们的 .bashrc 文件中。
小心使用 `>>` 附加到 `.bashrc`。 用于连接，而不是一个会覆盖的`>`。

~~~
echo "alias up='cd ..'" >> ~/.bashrc
tail -n 1 ~/.bashrc
~~~
{: .bash}

~~~
alias up='cd ..'
~~~
{: .output}

我们可以使用 `source` 执行 `.bashrc` 中的命令，因此这会创建别名 `up`，然后我们可以在目录 `/usr/local/bin` 中使用它：

~~~
source ~/.bashrc
cd /usr/local/bin
up
pwd
~~~
{: .bash}

~~~
/usr/local
~~~
{: .local}

必须将自定义添加到两个文件可能很麻烦。 如果我们
希望始终使用我们的 .bashrc 文件中的自定义，
然后我们可以将以下行添加到我们的 .bash_profile 文件中。

~~~
if [ -f $HOME/.bashrc ]; then
        source $HOME/.bashrc
fi
~~~
{: .bash}

## 自定义提示

我们还可以通过设置 `PS1` 系统来自定义我们的 bash 提示符
多变的。 要将我们的提示设置为`$`，然后我们可以运行命令

~~~
export PS1="$ "
~~~
{: .bash}

要将所有 bash 会话的提示设置为 `$`，请将此行添加到
`.bashrc` 的结尾。

进一步[自定义bash提示](https://www.yuanmadesign.com/ymdesign/linux-shell-bash-ps1)
是可能的。 让我们的提示是 `username@hostname[directory]: `,
我们会设置:

~~~
export PS1="\u@\h[\W]: "
~~~
{: .bash}

其中`\u`代表用户名，`\h`代表主机名，`\W`
表示当前目录。
