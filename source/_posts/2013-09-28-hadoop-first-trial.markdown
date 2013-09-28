---
layout: post
title: "Hadoop环境搭建小结"
date: 2013-09-28 15:37
comments: true
categories: Hadoop
---

这两天一直在忙着搭Wikipedia Miner的环境，其中用到了Java和Hadoop相关。Java因为以前做过TopCoder，对于环境配置、Ant使用什么的还比较熟悉，也没有遇到什么大的问题。主要就是Hadoop了，虽然以前实习的时候，也多少接触过Hadoop，用过Hive写过一些Hql什么的，但是只是一些皮毛吧。自己搭建Hadoop环境还是头一回，这次要在我的Mac上，搭一个单机器的双节点伪分布式环境。具体的步骤，参考了官方的手册。

[Wikipedia Miner搭建手册](http://sourceforge.net/apps/mediawiki/wikipedia-miner/index.php?title=Extraction)

[Apache Hadoop环境搭建手册](http://hadoop.apache.org/docs/stable/single_node_setup.html#PseudoDistributed)

[OSX Hadoop环境搭建手册](http://wiki.apache.org/hadoop/Running_Hadoop_On_OS_X_10.5_64-bit_%28Single-Node_Cluster%29)

步骤基本上是结合这几个看的，第一次搭建，就没考虑太多安全性，合理性。先搭个能用的环境体验下再说。具体步骤列举如下：

1. 安装JDK并配置JAVA_HOME(PATH,CLASSPATH在MAC上好像是不需要配置的)，[参考](http://blog.csdn.net/johnstrive/article/details/7791451)
2. 开启mac的远程登录功能，也就是教程里的ssh。[参考](http://stackoverflow.com/questions/12369172/how-to-install-sshd-on-mac)
3. 通过生成dsa秘钥等工作，ssh localhost可以不输入密码。[参考](http://wiki.apache.org/hadoop/Running_Hadoop_On_OS_X_10.5_64-bit_%28Single-Node_Cluster%29)中讲ssh那段有。
4. 下载安装Hadoop，然后配置。[参考](http://hadoop.apache.org/docs/stable/single_node_setup.html#PseudoDistributed)
5. 运行Hadoop。

上面列举出了我配置过来所参考的资料，我当然没有一次成功，每一步都有点小波折的，上面的资料都是我使用的，经过验证的方法。

接着我启动Hadoop，上传我的文件。总是抛异常，就像这个[问题](http://stackoverflow.com/questions/10097246/no-data-nodes-are-started)里描述的，SO上有很多类似问题，解决方法也都一样，删除dfs里面的，name和data文件，然后重新format。但是我照做了就没成功，我也没有去读那些log，但是运气比较好的，我发现其实我的Hadoop根本就没有启动任何datanode成功。于是根据提示消息，我找到了这个[问题](http://stackoverflow.com/questions/6307244/hadoop-namenode-format-returns-a-java-net-unknownhostexception)。最终我通过在hosts文件中，加了别名，解决了这个问题。

看起来很简单的过程，折腾了得有好几个钟头。到现在为止，hadoop基本上没问题了。但是总体的Wikipedia Miner的环境，还没搭建成功，还得解决各种Exception。等可以顺利使用Wikipedia Miner之后，我还要好好研究下，Wikipedia Miner的hadoop job部分是怎么写的。



