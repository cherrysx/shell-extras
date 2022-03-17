---
title: "传输文件"
teaching: 10
exercises: 0
questions:
- "如何使用 wget、curl 和 lftp 传输文件？"
objectives:
- "如何使用wget下载文件"
- "如何使用curl访问Web应用/下载文件"
- "如何使用lftp访问Web应用/下载文件"
keypoints:
- "学习wget命令的参数和下载文件的方式"
- "学习curl命令的参数和下载文件的方式"
- "学习lftp命令的参数和下载文件的方式"
---

除了git，还有其他方法可以与远程文件进行交互。

的确，我们可以克隆整个git存储库，甚至可以使用以下命令克隆一个级别的git存储库：
`git clone --depth-1 repository_name`。git存储库中不存在的文件怎么办？ 如果我们想从shell下载文件
我们可以使用wget、curl 和 lftp 等工具。
## Wget

Wget 是一个为GNU项目开发的简单工具，它使用HTTP、HTTPS 和FTP协议下载文件。 它被用户广泛使用，并且可用于大多数Linux发行版。

要通过HTTP从Web下载本课程（位于https://www.yuanmadesign.com/shell2/03-file-transfer/index.html），我们只需输入：

~~~
$ wget https://www.yuanmadesign.com/shell2/03-file-transfer/index.html
~~~
{: .bash}

~~~
--2022-03-17 16:20:07--  https://www.yuanmadesign.com/shell2/03-file-transfer/index.html
Resolving www.yuanmadesign.com (www.yuanmadesign.com)... 175.24.122.92
Connecting to www.yuanmadesign.com (www.yuanmadesign.com)|175.24.122.92|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 14431 (14K) [text/html]
Saving to: ‘index.html’

100%[=====================================================================================================>] 14,431      --.-K/s   in 0.08s

2022-03-17 16:20:09 (172 KB/s) - ‘index.html’ saved [14431/14431]
~~~
{: .output}

或者，您可以添加更多参数选项，其形式为：

~~~
wget -r -np -D domain_name target_URL
~~~
{: .bash}

其中 -r 表示递归爬取到其他文件和目录，-np 表示避免爬到父目录， -D表示仅针对以下域名

对于我们的URL，它将是：

~~~
$ wget -r -np -D yuanmadesign.com https://www.yuanmadesign.com/shell2/03-file-transfer/index.html
~~~
{: .bash}

将检索限制为特定的扩展名我们可以使用 `-A` 选项后跟逗号分隔的列表：

~~~
wget -r -np -D yuanmadesign.com -A html https://www.yuanmadesign.com/shell2/03-file-transfer/index.html
~~~
{: .bash}

我们还可以克隆具有本地依赖项的网页：

~~~
$ wget -mkq target_URL
~~~
{: .bash}

如果我们不想为网站内容创建子目录，请添加 `-nH` 选项：

例如：

~~~
$ wget -mkq -np -nH -D example.com http://example.com
~~~
{: .bash}

`-m` 用于带有时间戳、无限递归深度和保存 FTP 目录设置的镜像
`-k` 转换链接以使其适合本地查看
`-q` 禁止输出到屏幕
  
## cURL

或者，我们可以使用 `cURL`。它支持更大范围的协议，包括基于普通邮件的协议，如pop3和smtp。

~~~
$ curl -o index.html https://www.yuanmadesign.com/shell2/03-file-transfer/index.html
~~~
{: .bash}

~~~
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
100 14005  100 14005    0     0  35170      0 --:--:-- --:--:-- --:--:--  105k
~~~
{: .output}

curl的输入格式为：

~~~
curl -o filename_for_local_machine target_url
~~~
{: .bash}

其中 `-o` 选项表示将输出写入文件而不是标准输出（屏幕），并且 file_name_for_local_machine 是您选择保存到本地机器的任何文件名，
target_URL 是文件的位置，即文件在网络上的 URL

删除 `-o` 选项，并遵循语法 `curl target_URL`将 url 的内容输出到屏幕。如果我们想增强我们拥有的功能，我们可以使用管道和过滤器部分的信息。

例如，我们可以输入
`curl https://www.yuanmadesign.com/shell2/03-file-transfer/index.html | grep 卷曲`
这会告诉我们这个 URL 确实包含字符串 curl。
我们可以通过使用 `-s` 选项将 curl 的输出限制为文件内容来使输出更清晰
（例如`curl -s https://www.yuanmadesign.com/shell2/03-file-transfer/index.html | grep curl`）。

如果我们只想要输出中的文本而不是 html 标签，我们可以使用 html 到文本解析器，例如 `html2text`。

~~~
$ curl -s https://www.yuanmadesign.com/shell2/03-file-transfer/index.html | html2text | grep curl
~~~
{: .bash}

使用 `wget`，我们可以通过键入以下内容获得相同的结果：

~~~
$ wget -q -D swcarpentry.github.io -O /dev/stdout https://www.yuanmadesign.com/shell2/03-file-transfer/index.html | html2text | grep curl
~~~
{: .bash}

`Wget` 提供了比 `curl` 更多的功能来检索整个目录。我们可以使用 `Wget` 首先检索整个目录，然后运行 `html2text` 和 `grep`找到一个特定的字符串。
`cURL` 仅限于检索一个或多个无法通过递归爬取目录获得的指定 URL。这种情况可以通过与其他 unix 工具结合使用来改善，但不如 `Wget` 好。

请通过在 shell 中键入 `man wget`、`man curl` 和 `man html2text` 来参考手册页以获取更多信息。 

## lftp

另一个选项是“lftp”。 它有很多功能，甚至可以做简单的bittorrent。

如果我们想在网站上检索 `index.html` 并在本地以文件名 `index.html` 保存它：

~~~
$ lftp -c get https://www.yuanmadesign.com/shell2/03-file-transfer/index.html
~~~
{: .bash}

如果我们想将 `.html` 打印到屏幕上：

~~~
$ lftp -c cat https://www.yuanmadesign.com/shell2/03-file-transfer/index.html
~~~
{: .bash}

要检索目录中具有特定扩展名的所有文件，我们可以键入：

~~~
$ lftp -c mget {URL for directory}/*.extension_name
~~~
{: .bash}

例如，要检索 extras 文件夹中的所有 `.html` 文件：

~~~
$ lftp -c mget https://www.yuanmadesign.com/shell2/03-file-transfer/*.html
~~~
{: .bash}

请通过在 shell 中键入“man lftp”来参考手册页以获取更多信息。

{% include links.md %}

