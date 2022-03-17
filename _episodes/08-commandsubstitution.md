---
title: 命令替换
teaching: 15
exercises: 0
questions:
- "什么是命令替换？"
- "怎么使用命令替换？"
objectives:
- "了解关于参数的灵活性的必要性"
- "使用命令替换即时生成参数的值"
- "了解管道/重定向和命令替换运算符之间的区别"
keypoints:
- "通过命令替换即时生成循环变量列表"
- "通过命令替换即时生成变量"
---

## 介绍

在 *循环* 主题中，我们看到了如何通过让计算机完成重复性工作来提高生产力。
通常，这涉及对一整套文件执行相同的操作，例如：

~~~{.bash}
$ cd data/pdb
$ mkdir sorted
$ for file in cyclo*.pdb; do
>     sort $file > sorted/sorted-$file
> done
~~~

在此示例中，shell 为我们生成要循环的事物列表
结束，使用我们在*管道和过滤器*主题中看到的通配符机制。 这导致
`cyclo*.pdf` 被替换为 `cyclobutane.pdb cyclohexanol.pdb
环丙烷.pdb 乙基环己烷.pdb` 在循环开始之前。

另一个例子是所谓的*参数扫描*，您可以多次运行同一个程序
有不同的论点。 这是一个虚构的例子：

~~~{.bash}
$ for cutoff in 0.001 0.01 0.05; do
>   run_classifier.sh --input ALL-data.txt --pvalue $cutoff --output results-$cutoff.txt
> done
~~~

在第二个示例中，要循环的内容：`"0.001 0.01 0.05"` 由您拼写出来。

> ## 循环遍历字符串中的单词
>
> 在前面的示例中，您可以通过放置截止值使您的代码更整洁和自我记录
> 在单独的字符串中：
> ~~~
> $cutoffs="0.001 0.01 0.05"
> $ 用于 $cutoffs 中的截止； 做
> run_classifier.sh --input ALL-data.txt --pvalue $cutoff --output results-$cutoff.txt
> 完成
> ~~~
> 这是因为，就像文件名通配符一样，`$cutoffs` 被替换为 `0.001 0.01 0.05`
> 在循环开始之前。
{: .callout}

但是，您并不总是事先知道*要循环的内容*
超过。 很可能它不是一个简单的文件名模式（在
在这种情况下您可以使用通配符），或者它不是一个小的已知集合
值（在这种情况下，您可以像所做的那样明确地写出它们
在第二个例子中）。 因此，如果您可以循环，那就太好了
文件名或文件中包含的单词。 假设那个文件
`cohort2010.txt` 包含要迭代的文件名，然后它
能够说类似的话会很高兴：

~~~
# (imaginary syntax)
$ for file in [INSERT THE CONTENTS OF cohort2010.txt HERE]
> do
>    run_classifier.sh --input $file --pvalue -0.05 --output $file.results
> done
~~~

## 命令替换

这将比
依靠通配符机制。 因此，我们需要的是一个
实际上将 `[` 和 `]` 之间的一切替换为
输入文件的所需名称，就在循环开始之前。 谢天谢地，
这种机制是存在的，它被称为**命令替换操作符**
（以前使用**反引号运算符**编写）。 它看起来很像前面的片段：

~~~ {.bash}
# (actual syntax)
$ for file in $(cat cohort2010.txt)
> do
>    run_classifier.sh --input $file --pvalue -0.05 --output $file.results
> done
~~~

它的工作原理如下：`$(` 和 `)` 之间的所有内容都是
作为 Unix 命令执行，命令的标准输出替换为
从 `$(` 到并包括 `)` 的所有内容，就在循环之前
开始。 为方便起见，命令输出中的换行符为
替换为简单的空格。

### 反引号运算符
在遗留代码中，您可能会看到相同的构造，但带有
不同的语法。 它以反引号 ` ` ` 开始和结束（不是
与单引号 `'` 混淆！）。 反引号的工作原理
与 `$(` 和 `)` 完成的命令替换相同。 然而它的使用
不鼓励，因为反引号不能嵌套。

## 例子
好的。 回想一下*管道和过滤器*主题，`cat` 打印内容
其参数（文件名）到标准输出。 所以，如果内容
文件“cohort2010.txt”看起来像

~~~
patient1033130.txt 
patient1048338.txt 
patient7448262.txt 
.
.
.
patient1820757.txt
~~~

然后构造

~~~
$ for file in $(cat cohort2010.txt)
> do
>     ...
> done
~~~

将扩展为

~~~
$ for file in patient1033130.txt patient1048338.txt patient7448262.txt ... patient1820757.txt
> do
>     ...
> done
~~~

（请注意，换行符已被简单的空格所取代）。

这个例子使用``$(cat somefilename)``来提供参数给
`for variable in ... do ... done`-构造，但任何输出
也可以使用命令，甚至管道。 例如，如果
`cohort2010.txt` 包含几千名患者，但您只想
尝试前两个进行测试运行，您可以使用`head`命令来
获取其参数的前几行，如下所示：

~~~ {.bash}
$ for file in $(cat cohort2010.txt | head -n 2)
> do
>     ...
> done
~~~

被扩展为

~~~ {.bash}
$ for file in patient1033130.txt patient1048338.txt
> do
>     ...
> done
~~~

仅仅因为`cat队列2010.txt | head -n 2` 产生
命令替换后的`patient1033130.txt patient1048338.txt`。

`$(` 和 `)` 之间的所有内容都由 shell 逐字执行，所以
`head` 命令的`-n 2` 参数也按预期工作。

### **重要的**

回顾 Unix 使用的 *Loops* 和 *Shell Scripts* 主题
用于分隔命令、选项（标志）和参数的空格 /
论据。 出于同样的原因，命令（或
反引号内的管道）产生 *clean* 输出：单个单词
输出在单个命令和空格中效果最好 - 或
换行符分隔的单词最适合迭代的列表
循环。

> ## 根据时间戳生成文件名
>
> “即时”创建文件名会很有用。 例如，如果
> 一些名为“质量控制”的程序会定期运行（或
> 不可预测）可能需要提供时间戳作为
> 将所有输出文件分开的参数，如下所示：
> 
> ~~~
> qualitycontrol --inputdir /data/incoming/  --output qcresults-[INSERT TIMESTAMP HERE].txt
> ~~~
> 
> 让 `[INSERT TIMESTAMP HERE]` 工作是命令替换的工作
> 运算符。 您需要的 Unix 命令是 `date` 命令，它为您提供
> 使用当前日期和时间（试试看）。
>
> 在当前形式中，它的输出对于生成文件名的用处不大
> 因为它包含空格（正如我们现在所知，应该
> 最好避免在文件名中使用）。 您可以调整“日期”的格式
> 非常详细，例如去掉空格：
> 
> ~~~
> $ date +"%Y-%m-%d_%T"
> ~~~
> 
>（试试看）。
>
> 编写将您选择的文件复制到新文件的命令
> 其名称包含时间戳。 通过执行命令 a 来测试它
> 几次，在调用之间等待几秒钟（使用向上箭头
> 键以避免必须重新键入命令）
{: .callout}
<!-- solution: cp file file.$(date +"%Y-%m-%d_%T") -->


> ## 文件扩展名
>
> 当运行带有某个输入文件的分析程序时，它
> 通常要求输出与输入具有相同的名称，但具有
> 不同的文件扩展名，例如
>
> ~~~
> $ run_classifier.sh --input patient1048338.txt --pvalue -0.05 --output patient1048338.results
> ~~~
>
> 这里的一个好技巧是使用 Unix `basename` 命令。 它需要一个字符串（通常是文件名），
> 并去掉给定的扩展名（如果它是输入字符串的一部分）。 例子：
> ~~~ {.bash}
> $ basename patient1048338.txt    .txt
> ~~~
> gives
> ~~~ {.output}
> patient1048338
> ~~~
>
>
> 编写一个使用命令替换运算符和
> `basename` 命令将每个 `*.pdb` 文件排序到一个
> 对应的 `*.sorted` 文件。 也就是说，使循环执行
> 以下：
>
> ~~~
> $ sort ammonia.pdb > ammonia.sorted
> ~~~
> 但对于 `.pdb` 文件中的 *each*。
{: .callout}
<!-- solution: for file in *.pdb; do sort $file > $(basename $file .pdb).sorted; done -->

## 结束语

命令替换运算符为我们提供了一个
强大的新“管道”，使我们能够连接“小
碎片，松散地在一起”以保持 Unix 哲学。它是远程的
类似于 `|` 运算符，因为它连接两个
程式。 但也有明显的区别：`|` 连接
一个命令的标准输出到另一个命令的标准输入，
其中 `` $(command) `` 被“就地”替换到外壳中
脚本，并始终为其他命令提供参数、选项和参数。
