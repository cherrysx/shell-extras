---
title: "远程工作"
teaching: 10
exercises: 0
questions:
- "如何使用 `ssh` 和 `scp` ？"
objectives:
- "了解什么是SSH"
- "了解什么是 SSH 密钥"
- "生成您自己的 SSH 密钥对"
- "了解如何使用 SSH 密钥"
- "了解如何使用 `ssh` 和 `scp` 远程工作"
- "将 SSH 密钥添加到远程服务器"
keypoints:
- "SSH 是用户名/密码授权的安全替代方案"
- "SSH 密钥以公共/私人对的形式生成。您的公钥可以与他人共享。私钥仅保留在您的机器上。"
- "'ssh' 和 'scp' 实用程序是登录远程计算机和将文件复制到远程计算机/从远程计算机复制文件的安全替代方案"
---
在台式机或笔记本电脑上，让我们仔细看看当我们使用 shell 时会发生什么。
第一步是登录以便操作系统知道我们是谁以及我们可以做什么。我们通过输入用户名和密码来做到这一点；操作系统根据其记录检查这些值，
如果它们匹配，为我们运行一个shell。

当我们键入命令时，代表我们正在输入的字符的1和0从键盘发送到shell。shell在屏幕上显示这些字符来代表我们输入的内容，
然后，如果我们输入的是命令，shell执行它并显示它的输出（如果有的话）。

如果我们想在另一台机器上运行一些命令怎么办，例如管理我们实验结果数据库的Linux服务器？
去做这个，我们必须先登录到那台机器。我们称之为[远程登录]({{ page.root }}/reference/{{ site.index }}#remote-login)。

为了让我们能够登录，远程计算机必须正在运行一个[远程登录服务器]({{ page.root }}/reference/{{ site.index }}#remote-login-server)，我们将运行可以与该服务器通信的客户端程序。客户端程序将我们的登录凭据传递给远程登录服务器
并且，如果我们被允许登录，那么该服务器会在远程计算机为我们打开一个shell。

一旦我们的本地客户端连接到远程服务器，我们在客户端输入的所有内容都由服务器传递到shell并在远程计算机上运行。
那个远程shell代替我们运行这些命令，就像本地shell一样，然后通过服务器将输出发送回我们的客户端，供我们的计算机显示。

## SSH历史

早些时候，当每个人都相互信任，并且知道他们计算机中每个芯片的名字时，人们在通过网络发送时除了最敏感的信息外没有加密任何东西。

但是，任何人都可以看到未加密的网络流量，这意味着恶棍可以窃取用户名和密码，并将它们用于各种邪恶目的。

[SSH 协议]({{ page.root }}/reference/{{ site.index }}#ssh-protocol)
被发明来防止这种情况（或至少减慢它的速度）。它使用多种复杂且经过大量测试的加密协议确保外人看不到消息中的内容，并在不同的计算机之间来回传输。

接受来自客户端程序的连接的远程登录服务被称为[SSH 守护进程]({{ page.root }}/reference/{{ site.index }}#ssh-daemon) 或 `sshd`。

我们用来远程登录的客户端程序是[安全shell]（{{ page.root }}/reference/{{ site.index }}#secure-shell），或 `ssh`，想想 (`s`)ecure `sh`。

`ssh` 登录客户端有一个名为 `scp` 的配套程序，想想 (`s`)ecure `cp`，这允许我们使用相同类型的加密连接将文件复制到远程计算机或从远程计算机复制文件。

## 使用 `ssh` 进行远程登录

要进行远程登录，我们发出命令 `ssh username@host`它尝试连接到我们指定的远程计算机上运行的 SSH 守护程序。

我们登录后，我们可以使用远程shell来使用远程计算机的文件和目录。

键入 `exit` 或 Control-D终止远程 shell 和本地客户端程序，并将我们返回到之前的 shell。

在下面的示例中，远程机器的命令提示符是`pp>`而不仅仅是`$`。为了更清楚哪台机器在做什么，我们将缩进发送到远程机器的命令和他们的输出。

~~~
$ pwd
~~~
{: .bash}

~~~
/users/ppju
~~~
{: .output}

~~~
$ ssh ppju@examplehost
Password: ********
~~~
{: .bash}

~~~
    pp> hostname
~~~
{: .bash}

~~~
    pphost
~~~
{: .output}

~~~
    pp> pwd
~~~
{: .bash}

~~~
    /home/ppju
~~~
{: .output}

~~~
    pp> ls -F
~~~
{: .bash}

~~~
    bin/     cheese.txt   dark_side/   rocks.cfg
~~~
{: .output}

~~~
    pp> exit
~~~
{: .bash}

~~~
$ pwd
~~~
{: .bash}

~~~
/home/ppju
~~~
{: .output}

## 使用 `scp` 将文件复制到远程计算机或从远程计算机复制文件

要复制文件，我们指定源路径和目标路径，其中任何一个都可能包含计算机名称。
如果我们省略计算机名称，`scp` 假设我们的意思是我们正在运行的机器。
例如，该命令将我们的最新结果复制到地下室的备份服务器，打印出它的进度：

~~~
$ scp results.txt ppju@exampleserver:backups/results.txt
Password: ********
~~~
{: .bash}

~~~
results.txt              100%  9  1.0 MB/s 00:00
~~~
{: .output}

注意冒号`:`，分隔服务器的主机名和路径名我们要复制到的文件。正是这个字符通知 `scp` 副本的源或目标是在远程机器上以及需要它的原因可以解释如下：

和我们运行时放置的默认目录一样远程机器上的 shell 是我们在该机器上的主目录，默认远程副本的目标也是主目录。

这意味着：

~~~
$ scp results.txt ppju@exampleserver:
~~~
{: .bash}

上面的命令仍然是有效的，它将“results.txt”复制到“exampleserver”上的主目录中。但是，如果我们不这样做，带有冒号通知远程机器的`scp`：

~~~
$ scp results.txt ppju@exampleserver
~~~
{: .bash}

那么，奇怪的现象发生了，现在我们只是在本地机器上创建了一个名为 `ppju@exampleserver` 的文件，就像我们对`cp`所做的那样。

~~~
$ cp results.txt ppju@exampleserver
~~~
{: .bash}

在远程机器之间复制整个目录使用与 `cp` 命令相同的语法：我们只是使用 `-r` 选项来表示我们希望以递归方式进行复制。例如，下面的命令将我们所有的结果从备份服务器复制到我们的笔记本电脑：

~~~
$ scp -r ppju@exampleserver:backups ./backups
Password: ********
~~~
{: .bash}

~~~
results-2011-09-18.dat              100%  7  1.0 MB/s 00:00
results-2011-10-04.dat              100%  9  1.0 MB/s 00:00
results-2011-10-28.dat              100%  8  1.0 MB/s 00:00
results-2011-11-11.dat              100%  9  1.0 MB/s 00:00
~~~
{: .output}

## 使用 `ssh` 在远程机器上运行命令

这是`ssh`客户端程序可以为我们做的另一件事。假设我们要检查我们是否已经创建了文件备份服务器上的“backups/results-2011-11-12.dat”。
我们可以安装下面的方式做，而不是登录然后输入`ls`，：

~~~
$ ssh ppju@exampleserver "ls results*"
Password: ********
~~~
{: .bash}

~~~ 
results-2011-09-18.dat  results-2011-10-28.dat
results-2011-10-04.dat  results-2011-11-11.dat
~~~
{: .output}

在这里，`ssh` 在我们的远程用户名之后接受参数并将它们传递给远程计算机上的shell。
（我们必须在它周围加上引号，使它看起来像一个单一的参数。）
由于这些参数是合法的命令，远程 shell 为我们运行 `ls results`并将输出发送回我们的本地 shell 进行显示。

## SSH密钥

一遍又一遍地输入我们的密码很烦人，
特别是如果我们要远程运行的命令处于循环中。为了避免总是输入密码，我们可以创建一个 [SSH 密钥]({{ page.root }}/reference/{{ site.index }}#ssh-key)告诉远程机器它应该永远信任我们。

SSH 密钥成对出现，一个与 GitHub 等服务共享的公钥，以及仅存储在您的计算机上的私钥。 如果键匹配，您被授予访问权限。

SSH 密钥背后的加密确保没有人可以通过公钥破解私钥。

使用 SSH 授权的第一步是生成您自己的密钥对。

您的机器上可能已经有一个 SSH 密钥对。 你可以检查看看是否home目录下存在一个.ssh隐藏目录，通过`ls`来查看目录内容。

~~~
$ cd ~/.ssh
$ ls
~~~
{: .bash}

如果你看到 `id_rsa.pub`，你已经有一个密钥对，不需要创建一个新的一个。

如果您没有看到 `id_rsa.pub`，请使用以下命令生成新密钥一对。 确保将 `your@email.com` 替换为您自己的电子邮件地址。

~~~
$ ssh-keygen -t rsa -C "your@email.com"
~~~
{: .bash}

当询问在哪里保存新密钥时，按<kbd>Return</kbd>键接受默认位置。

~~~
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ppju/.ssh/id_rsa):
~~~
{: .output}

然后，您将被要求提供一个可选的密码。 这可以用来使您的密钥更加安全，但如果您想要避免输入您的密码每次你可以通过按两次回车跳过它。

~~~
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
~~~
{: .output}

密钥生成完成后，您应该会看到以下确认信息：

~~~
Your identification has been saved in /home/ppju/.ssh/id_rsa.
Your public key has been saved in /home/ppju/.ssh/id_rsa.pub.
The key fingerprint is:
01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db your@email.com
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|        . E +    |
|       . o = .   |
|      . S =   o  |
|       o.O . o   |
|       o .+ .    |
|      . o+..     |
|       .+=o      |
+-----------------+
~~~
{: .output}

随机艺术图像是匹配键的另一种方法，但我们不需要它。

现在您需要将您的公钥副本放在您想要的任何服务器上。当使用SSH连接，使用密钥自动登录而不是使用用户名和密码交互式登录。

使用 `cat` 显示新公钥文件的内容：

~~~
$ cat ~/.ssh/id_rsa.pub
~~~
{: .bash}

~~~
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA879BJGYlPTLIuc9/R5MYiN4yc/YiCLcdBpSdzgK9Dt0Bkfe3rSz5cPm4wmehdE7GkVFXrBJ2YHqPLuM1yx1AUxIebpwlIl9f/aUHOts9eVnVh4NztPy0iSU/Sv0b2ODQQvcy2vYcujlorscl8JjAgfWsO3W4iGEe6QwBpVomcME8IU35v5VbylM9ORQa6wvZMVrPECBvwItTY8cPWH3MGZiK/74eHbSLKA4PY3gM4GHI450Nie16yggEg2aTQfWA1rry9JYWEoHS9pJ1dnLqZU3k/8OWgqJrilwSoC5rGjgp93iu0H8T6+mEHGRQe84Nk1y5lESSWIbn6P636Bl3uQ== your@email.com
~~~
{: .output}

复制输出的内容。

使用您的用户名和密码登录到远程服务器。

~~~
$ ssh ppju@examplehost
Password: ********
~~~
{: .bash}

将您复制的内容粘贴到 `~/.ssh/authorized_keys` 的末尾。

~~~
    pp> nano ~/.ssh/authorized_keys
~~~
{: .bash}

内容加入之后，注销远程计算机并再次尝试登录。 如果您正确设置了 SSH 密钥，您无需输入密码自动登录。

~~~
    pp> exit
~~~
{: .bash}

~~~
$ ssh ppju@examplehost
~~~
{: .bash}

## SSH 文件和目录

将我们的公钥复制到远程机器的示例，以便它然后可以在我们下一次 SSH 进入该远程机器时使用，假设我们已经有了一个目录`~/.ssh/`。

虽然远程服务器可能支持使用 SSH 登录，但您的家默认情况下，那里的目录可能不包含 `.ssh` 目录。

我们已经看到我们可以使用 SSH 远程运行命令机器，所以我们可以确保一切都按要求设置我们将我们的公钥副本放在远程机器上。

通过这个过程，我们可以突出一些典型的SSH 协议本身的要求，如对于 `ssh` 命令手册页中所述。

首先，我们检查我们在另一个远程上是否有一个 `.ssh/` 目录：

~~~
$ ssh ppju@examplehost "ls -ld ~/.ssh"
Password: ********
~~~
{: .bash}

~~~
    ls: cannot access /home/ppju/.ssh: No such file or directory
~~~
{: .output}

啊哦！ 我们应该创建目录； 并检查它是否存在（注：两条命令，用分号隔开）

~~~
$ ssh ppju@examplehost "mkdir ~/.ssh; ls -ld ~/.ssh"
Password: ********
~~~
{: .bash}

~~~
    drwxr-xr-x 2 ppju ppju 512 Jan 01 09:09 /home/ppju/.ssh
~~~
{: .output}

现在我们有了一个 dot-SSH 目录，用来放置 SSH 相关的文件，但我们可以看到默认权限允许任何人检查该目录中的文件。

对于安全的协议，这不是一件好事，所以推荐的权限是读/写/执行供用户使用，其他人无法访问。

让我们更改目录的权限：

~~~
$ ssh ppju@examplehost "chmod 700 ~/.ssh; ls -ld ~/.ssh"
Password: ********
~~~
{: .bash}

~~~
    drwx------ 2 ppju ppju 512 Jan 01 09:09 /home/ppju/.ssh
~~~
{: .output}

这样看起来好多了。

在上面的例子中，建议我们粘贴我们的公钥在 `~/.ssh/authorized_keys` 的末尾，但是如果我们在这台远程机器上没有`~/.ssh/`，我们可以简单地
复制我们的公钥作为初始的`~/.ssh/authorized_keys`，当然，我们将使用 `scp` 来执行此操作，即使我们没有尚未设置无密码 SSH 访问权限。

~~~
$ scp ~/.ssh/id_rsa.pub ppju@examplehost:.ssh/authorized_keys
Password: ********
~~~
{: .bash}

请注意，远程上 `scp` 命令的默认目标
machine 是主目录，所以我们不需要使用
简写 `~/.ssh/` 甚至是完整路径 `/home/ppju/.ssh/`
我们的主目录在那里。

在远程机器上，检查我们刚刚创建的文件的权限：

~~~
$ ssh ppju@examplehost "ls -l ~/.ssh"
~~~
{: .bash}

~~~
    -rw-r--r-- 2 ppju ppju 512 Jan 01 09:11 /home/ppju/.ssh/authorized_keys
~~~
{: .output}

虽然授权的密钥文件不被认为是高度敏感的，（毕竟，它包含公钥），我们更改权限以匹配手册页的建议。

~~~
$ ssh ppju@examplehost "chmod go-r ~/.ssh/authorized_keys; ls -l ~/.ssh"
~~~
{: .bash}

~~~
    -rw------- 2 ppju ppju 512 Jan 01 09:11 /home/ppju/.ssh/authorized_keys
~~~
{: .output}



{% include links.md %}
