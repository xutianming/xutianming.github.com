---
layout: post
title: "MapReduce初试"
date: 2013-10-29 20:46
comments: true
categories: 机器学习
---

最近在尝试做一个行业关键词分类的项目，训练数据百万级，测试数据千万。数据量不大，但是我的机器跑来已经相当吃力了，于是想用一些简单的Hadoop知识，体验一下这个目前业内最流行的分布式计算框架。

以前接触过很多分布式计算的知识，也学习过MapReduce。甚至实习期间，还多少使用到了hadoop的hive。但是一直都流于表面和概念，没有机会实践，甚至都不知道一个MapReduce程序如何来写，这次终于有机会尝试。

体验的过程并不顺利，为了简单起见，我使用Python和Streaming。参考网上的博客，很快就写出了MapReduce程序。这里的心得是，编写MapReduce程序并不比普通的编程难，关键是把算法分布式化，思考清楚，在Map和Reduce的过程中的数据流。这里分别附上我写的简单的Map，Reduce程序，作为以后工作的模板。

Mapper.py

    #!/usr/bin/env python
	import math
    import sys

    features = []

    fin = open("feature.txt","r")
    for line in fin:
        winfo = line.split("\t")
        word = winfo[0].strip()
        features.append(word)
    fin.close()


    for line in sys.stdin :
	    document = []
	    info = line.strip().split("\t")
	    for i in range(2,len(info)):
		    document.append(info[i].strip())
		for feature in features:
		    if feature in document :
			    print '%s\t%s' % (feature, 1)

Reducer.py

    #!/usr/bin/env python
    import math
    import sys

    current_word = None
    current_count = 0
    word = None

    for line in sys.stdin:
	    line = line.strip()
		word,count = line.split('\t')

	try:
		count = int(count)
	except ValueError:
		continue

	if current_word == word:
		current_count += count
	else :
		if current_word :
			print '%s\t%s' % (current_word,current_count)
		current_count = count
		current_word = word

	if current_word == word:
		print '%s\t%s' % (current_word, current_count)

这一个统计DocumentFrequence的MapReduce程序。下面就一一描述下在编写这个程序中遇到的问题和解决方案。

1. 首先注意到Mapper中需要读入一个文件。一开始不懂文件应该怎么才可以被程序读到。尝试过放在HDFS和改变各种文件路径。其实只要在执行hadoop任务的时候，用`-file`参数指定就好了。
2. 关于失败提示。`# of failed Map Tasks exceeded allowed limit`,一开始的时候总是遇到。出现这个job失败提示的原因在于mapper程序有问题。执行失败，超过限度，job就会被杀死。所以mapper要做好本地测试。使用`cat input|python mapper.py|sort|reducer.py`可以对程序进行基本的测试。再就是在本地的hadoop平台上先跑一下，假如说没问题了，可以跑个5%，一般就可以提交到线上的集群了。
3. 关于Streaming命令的各个参数。除了使用`-mapper`，`-reducer`之外。还可以利用`-D`选项，指定一些hadoop的运行参数，比如mapper和reducer的数量。这里有个比较重要的技巧。就是假如我们的执行逻辑中，只需要mapper不需要reducer。有几种办法，一个是不指定reducer，默认使用`IdentityReducer`，mapper的输入直接转化为reducer的输出；二是设置reducer数量为0，`-setReduceTasks=0`，有几个mapper即有几个输出文件。
4. 最重要的一点，程序效率。上面给出的两个模板很高效。9GB的输入文件，十分钟即可在线上集群中完成。但是一开始并不是这样的。这个问题困扰了我整个周末。一开始，我想当然以为既然hadoop了，程序效率就不是短板了，我效率低，mapper分块小一点也很快完成，于是编程序的时候就很不在意效率。用list查找什么的，各种遍历循环，怎么好写怎么来。于是一开始我的程序测试好提交之后，job执行很慢，可以理解。令人崩溃的是，总是莫名其妙失败，我一度认为是线上集群不稳定弱爆了。后来在乐乐的提示下改进了程序的效率，list改用了hash，提高查找效率，去掉了几个循环。于是job很顺利也很迅速执行完了。事后分析，我觉得线上的集群可能做了很多的限制，我之前的写法，由于消耗太多的资源，由于本身优先级不高，就总是被delay，就会有莫名其妙的失败。所以mapreduce程序中，效率也是非常重要的，每一个task都高效执行，那么整个job都能很快完成，大大提高效率，节省时间啊！终于深刻认识到程序效率的重要性了。
5. 最后一点，我还没有很好的解决。就是mapreduce程序的调试。hadoop实战这本书上有介绍debug的方法，我没怎么看懂，另外简单的程序，使用print大法打印一些调试信息和程序运行信息，很有利于快速定位bug。目前还不知道如何打印。如果在本地的hadoop的话，可以输出到`stderr`上，会最终输出到logs目录下的日志里。但是线上集群的话，就没有结点的访问权限，自然没法查看日志。所以我就想能否通过`Reporter`来打印一些消息。还没有尝试过，需要实践来验证。