---
layout: wiki
title: Shell 命令简介
categories: Shell
description: Shell Command  简介
keywords: Shell Command
---

**目录**

* TOC
{:toc}

### 写在前面

本文主要总结 Shell 的一些常用命令，包含一些小技巧，属于入门性质，不定期会有更新（说的跟真的一样）。另外，不要随便执行下面的命令，除非你真的清楚它在干什么（这是血的教训）。

### man

哪个命令是最重要的？当然是 man 了。通过 man 查询时，会输出很多内容，如果想在输出结果中搜索的话，方法是：

```
* 按一个反斜杠 / ,然后输入想要搜索的内容
* 按 return
* n 可以跳到下一个结果，N 跳到上一个结果，不过都是按行跳的
```

默认的是没有 C++ 库函数的 man page 的，需要我们额外安装。

```
sudo apt-get install manpages manpages-dev manpages-posix manpages-posix-dev
sudo apt-get install libstdc++6-4.2-doc
```

现在执行命令 man std::vector 就可以看到结果了。

### compgen

```
compgen -u  显示所有用户名
compgen -g  显示所有组
```

### netstat

用于显示与IP、TCP、UDP和ICMP协议相关的统计数据，一般用于检验本机各端口的网络连接情况

```
-a或–all 显示所有连线中的Socket
-n或–numeric 直接使用IP地址，而不通过域名服务器
-p或–programs 显示正在使用Socket的程序识别码和程序名称
-t或–tcp 显示TCP传输协议的连线状况
-u或–udp 显示UDP传输协议的连线状况
```

实例：

```
sudo netstat -atunp | more
```

### diff

diff 用来比较两个文件的不同，隐含的意思是，如何将第一个文件修改成第二个文件。当要执行修改时，还需要通过重定向输出一个脚本，例如：

```
diff -e file1.txt file2.txt > my-ed-script.txt
```

这个文件还缺少 w 命令，再执行下面的语句添加 w：

```
echo "w" >> my-ed-script.txt
```

my-ed-script.txt 里面已经多了一个 w,下面调用 ed 命令

```
ed - file1.txt < my-ed-script.txt
```

注意空格， “-” 告诉 ed 从 stdin 读取 。下面再看 file1.txt, 发现它和file2.txt 一模一样了.


### echo

echo 可以查看一些值，比如

```
echo $SHELL
```

echo 可以将内容写入文件，>> 代表在下一行添加，> 代表覆盖原文件，再添加。当添加一些特殊符号时，需要注意.具体地，可以体会下面的例子：

```
If you want to have quotes, then you must escape them using the backslash character.

echo "I am \"Finding\" difficult to write this to file" > file.txt
echo "I can \"write\" without double quotes" >> file.txt

 The same holds true if you i.e. also want to write the \ itself, as it may cause side effects. So you have to use \\

Another option would be to use ` instead of quotes.

echo 'I am "Finding" difficult to write this to file' > file.txt 
echo 'I can "write" without double quotes' >> file.txt

However in this case variable substition doesn't work, so if you want to use variables you have to put them outside.

echo "This is a test to write $PATH in my file" >> file.txt
echo 'This is a test to write '"$PATH"' in my file" >> file.txt
```

### more

more 命令显示文件内容，每次显示一屏。配合管道使用。

```
* 按 Space 键，显示文本的下一屏内容
* 按 Enter 键，只显示文本的下一行内容
* 按斜线符（/），接着输入一个模式，可以在文本中寻找下一个相匹配的模式
* 按 H 键，显示帮助屏，该屏上有相关的帮助信息。
* 按 B 键，显示上一屏内容。
* 按 Q 键，退出 more 命令。
```

### less

与 more 命令一样，less 命令也用来分屏显示文件的内容。但是二者存在差别：less 命令允许用户向前或向后浏览文件，而 more 命令只能向前浏览。用 less 命令显示文件时，用 PageUp 键向上翻页，用 PageDown 键向下翻页。要退出 less ，应按 Q 键。 less 有几种格式和很多选项，这里不做详述。

### head

命令在屏幕上显示指定文件的开头若干行。

```
head -5 file  显示文件file的前5行
head -v file  显示文件file的内容，并且给出文件名标题
head -q file  显示文件file的内容，但不列出文件名标题
```

### tail

tail命令在屏幕上显示指定文件的末尾若干行。

```
tail file        显示文件file的最后10行
tail +20 file    显示文件file的内容，从第20行至文件末尾
tail -c 10 file  显示文件file的最后10个字符
```
### touch

可以修改指定文件的时间标签或者创建一个空文件。

```
touch file 在当前目录下建立一个空文件 file
```

然后，利用ls -l命令可以发现文件 file 的大小为0，表示它是空文件。




