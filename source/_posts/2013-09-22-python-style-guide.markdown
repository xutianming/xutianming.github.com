---
layout: post
title: "Python Style Guide"
date: 2013-09-22 14:01
comments: true
categories: 编程语言
---

写Python一段时间了，由于从初学到现在，一直还在熟悉语法和各种数据结构，以事先功能为主，自觉代码写的非常丑陋，现在也写了不少Python代码了，是时候挑战下习惯，遵循合适的代码风格，写简单易读的代码，Be Pythonic!!

本文主要参考了《[Google Python Style Guide](http://google-styleguide.googlecode.com/svn/trunk/pyguide.html)》、《[How can I learn to effectively write Pythonic code](http://programmers.stackexchange.com/questions/119913/how-can-i-learn-to-effectively-write-pythonic-code)》、《[Code Like a Pythonista: Idiomatic Python](http://python.net/~goodger/projects/pycon/2007/idiomatic/handout.html)》三篇文章，摘取了其中我现在可能需要遵循的，因为有些做比较大的系统的项目才用到的，暂时就没写。我主要以写小算法和小工具为主。

一、编码风格：

1、不要使用分号，也不要利用分号把两个命令放在一行

2、每行代码不超过80（特殊情况下，尽量不要把链接分开两行放）

3、少用括号，条件语句和return语句不要用括号（特殊情况下，利用括号来进行断行可以）

4、缩进用四空格（一般在文本编辑器里设置tab转四空格）

5、空格使用。语法符号之后用空格，但是括号、花括号、方括号之后不要用。逗号冒号之后有个空格，但是之前不要有。赋值号前后有空格，但是在使用默认参数的时候，不要加。

6、一系列的赋值或者相同的操作。没必要用空格来使赋值号对齐。

7、字符串的使用。尽量使用string format和'%'而不是'+'。在循环中构造字符串的时候，不要使用'+'和'+='，而是把所有的字符串先存在一个list里，然后在循环结束之后，用join函数。（这一点需要格外注意，具体原因原文有）

8、多行string尽量不要用三个连续的引号，因为会破坏整体的缩进。使用join函数拼接。

9、file和socket使用完毕要显式关闭

10、临时的实现临时插入的代码，使用TODO注释

11、import的组织。每行一个，不要一次import多个。按照标准库、第三方库、程序特有的，这一顺序进行引用，统一级别按照字母顺序排序。

12、命名规范。module_name, package_name, ClassName, method_name, ExceptionName, function_name, GLOBAL_CONSTANT_NAME, global_var_name, instance_var_name, function_parameter_name, local_var_name。大部分使用了小写和下划线。

13、所有的脚本都应该是可import的，并且脚本被import之后，不能对当前脚本的功能有所影响。所以，脚本都应该有主函数！

    def main():
    	…
    if __name__ == '__main__':
    	main()

不然的话，主程序的逻辑，会在被import的时候被执行。

14、注释。使用正确的注释方法。见原文。