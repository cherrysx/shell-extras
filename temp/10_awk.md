---
title: AWK处理复杂数据
teaching: 120
exercises: 0
questions:
- "AWK是什么？"
- "如何使用awk处理数据？"
- "AWK和管道的区别？"
objectives:
- 解释为什么 AWK 有用，什么时候它比管道更好.
- 显示类似于命令“cut”的基本用法"
- 引入字段分隔符参数。
- 使用正则表达式来执行不同的指令。
- 引入 BEGIN 和 END 关键字。
- 使用 if then 结构来更改相同匹配正则表达式的行为。
keypoints:
- ""
---

## Prerequisite: shell and any programming language

If we need to count the number of lines in a file, we can use the previously
showed command for word counting wc

~~~ {.bash}
$ wc -l example.txt
~~~

As you probably remember, -l is an option that asks for the number of lines only.

However, wc counts the number of newlines in the file, if the last line does
not contain a carriage return (i.e. there is no emptyline at the end of the file),
the result is going be the actual number of lines minus one.
	
A workaround is to use Awk. Awk is command line program that takes as input a set
of instructions and one or more files. The instructions are executed on each line
of the input file(s).

The instructions are enclosed in single quotes or they can be read from a file.

Example:

~~~ {.bash}
$ awk '{print $0}' example.txt
~~~

This command has the same output of "cat": it prints each line from the example.fasta
file.

The structure of the instruction is the following:
- curly braces surround the set of instructions
- print is the instruction that sends its arguments to the terminal
- $0 is a variable, it means "the content of the current line"

As you can see, the file contains a table.

Awk automatically splits the processed line by looking at spaces: in our case it has
knowledge of the different columns in the table.

Each column value for the current line is stored into a variable: $1 for the first
column, $2 for the second and so on.

So, if we like to print only the second column from the table, we execute

~~~ {.bash}
$ awk '{print $2}' example.txt
~~~

We can also print more than one value, or add text to the printed line:

~~~ {.bash}
$ awk '{print "chr",$2,$4}' example.txt
~~~

The comma puts a space between the printed values. Strings of text should be enclosed in
double quotes. In this case we are printing the text "chr", the second and the fourth column
for each row in the table.

So, $0 is the whole line, $1 the first field, $2 the second and so on. What if we want
to print the last column, but we don't know its number? Maybe it is a huge table, or maybe
different lines have a different number of columns.

Awk helps us thanks to the variable NF. NF stores the number of fields (our columns) in the
row. Let's see for our table:

~~~ {.bash}
$ awk '{print NF}' example.txt
~~~

We can see that some lines contain 6 fields while others contain 7 of them.
Since NF is the number of the last field, $NF contains its value.

~~~ {.bash}
$ awk '{print "This line has",NF,"columns. The last one contains",$NF}' example.txt
~~~

> ## Field separator {.callout}
> Out there we have different file formats: our data may be comma separated (csv),
> tab separated (tsv), by semicolon or by any other character.

To specify the field separator, we should provide it at command line like:

~~~ {.bash}
$ awk -F "," '{print $2}' example2.txt
~~~

In this case , we are printing the second field in each line, using comma as separator.
Please notice that the character space is now part of the field value, since it is no
longer the separator.

> ## Matching lines {.callout}
> Maybe we would like to perform different instruction on different lines.

Awk allows you to specify a matching pattern, like the command grep does.

Let's look at the file content

~~~ {.bash}
$ awk '{print $0}' example.pdb
~~~

It seems an abriged PDB file. If we would like to print only lines starting with the word
"ATOM", we type:

~~~ {.bash}
awk '/^ATOM/ {print $0}' example.pdb
~~~

In this case, we specify the pattern before the instructions: only lines starting with the
text "ATOM". As you remember, ^ means "at the beginning of the line".

We can specify more that one pattern:

~~~ {.bash}
awk '/^ATOM/ {print $7,$8,$9} /^HEADER/ {print $NF}' example.pdb
~~~

In this case, we are printing the spatial coordinates of each atom.
