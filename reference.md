---
layout: reference
---

## 词汇表

{:auto_ids}

remote login
:   remote login一般指远程登录。 远程登录（rlogin）是一个 Linux 命令，它允许授权用户进入网络中的其它 Linux 机器并且就像用户在现场操作一样。一旦进入主机，用户可以操作主机允许的任何事情，比如：读文件、编辑文件或删除文件等。

remote login server
: remote login server一般指远程登录服务器。该服务器运行远程登录服务（sshd等）

SSH daemon
: SSH守护进程。SSH守护程序sshd 提供对Linux服务器的安全、加密访问。

secure shell
: SSH（Secure Shell）是一种通用的、功能强大的、基于软件的网络安全解决方案。计算机每次向网络发送数据时，SSH都会自动对其进行加密。数据到达目的地时，SSH自动对加密数据进行解密。整个过程都是透明的，使用OpenSSH工具将会增进你的系统安全性。谈到网络安全访问，相信大家首先想到的就是安全Shell，也就是Secure Shell，通常简写为SSH。这是因为SSH安装容易、使用简单，而且比较常见，一般的Unix系统、Linux系统、FreeBSD系统都附带有支持SSH的应用程序包。

SSH key
: SSH key提供了一种与Linux服务器的通信的方式，通过这种方式，能够在不输入密码的情况下，将Linux服务器作为自己的remote端服务器，进行命令的执行操作。

SSH protocol
: ssh是一个加密传输协议，它有三个模块，传输层协议作为基础协议，验证协议和连接协议平行的建立在其上，虽然验证在连接之前进行，但是两个协议之间并没有联系。身份验证主要有两种方式，一种是用户名密码，另一种是公钥验证。其中公钥验证还用到了数字签名。在数据传输过程中采用非对称加密算法。

command
: Shell命令

user name
: Linux用户名，Linux系统是一个多用户多任务的分时操作系统，任何一个要使用系统资源的用户，都必须首先向系统管理员申请一个账号，然后以这个账号的身份进入系统。用户的账号一方面可以帮助系统管理员对使用系统的用户进行跟踪，并控制他们对系统资源的访问；另一方面也可以帮助用户组织文件，并为用户提供安全性保护。每个用户账号都拥有一个唯一的用户名和各自的口令。用户在登录时键入正确的用户名和口令后，就能够进入系统和自己的主目录。

user ID
: Linux用户ID，每个用户分配一个用户ID，普通用户从1000开始，系统用户0～999, 0是root用户的ID。

user group
: Linux用户组。每个用户都有一个用户组，系统可以对一个用户组中的所有用户进行集中管理。不同Linux 系统对用户组的规定有所不同，如Linux下的用户属于与它同名的用户组，这个用户组在创建用户时同时创建。用户组的管理涉及用户组的添加、删除和修改。组的增加、删除和修改实际上就是对/etc/group文件的更新。

user group name
: Linux用户组名

user group ID
: Linux用户组ID，每个用户组分配一个用户组ID，普通用户组从1000开始，系统用户0～999, 0是root用户组的ID。

access control lists
: 访问控制表

search path
: Linux搜索路径
