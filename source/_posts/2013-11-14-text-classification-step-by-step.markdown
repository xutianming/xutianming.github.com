---
layout: post
title: "行业关键词分类项目总结1"
date: 2013-11-14 10:24
comments: true
categories: [文本挖掘,机器学习]
---

在过去的一个月里，完成了一个行业关键词分类的项目，100w的训练数据，1000w的测试数据，33个分类。项目期限已经到了，但是我们的准确率目前只有88%，尝试了很多方法，遇到很多困难，也走过弯路。

项目结束并且效果不好，自然要寻找原因。一方面是等待比赛完全结束，公布冠军的解决方法，来开阔下眼界；另一方面就是更广泛的读一些相关的资料，做一些在项目过程中没有精力去做的阅读和学习工作。

首先简单描述下项目的过程。这次项目提供了33个分类，和一些要分类的关键词。每个关键词，提供了10个搜索引擎的自然排序的结果，以及购买这些关键词的用户。一部分已经标注的训练数据，和大量要分类的测试数据，分类效果的评估用的是宏平均(Macro-average)准确率。接下来，我们做了如下尝试：

1. 利用提供的文本数据进行文本分类。其中包括了利用lucene的IKAnalyzer进行分词、利用Chi-square进行特征选择、利用tf-idf作为权重生成文本的向量表示形式、利用Liblinear进行线性核的分类。第一次准确率87%。由于以上工作都是在Hadoop上完成的，加上学习和适应Hadoop平台的时间，其实只要一天的工作量的事情，做了2周（比赛承办方提供的Hadoop集群的限制实在是太多了）。
2. 利用一些人工制定的规则，挖掘一些有着高区分度的词，利用这些词去修正上述文本分类的结果。这一步，把准确率提高到了88.5%。这也是我们最好的成绩了，本来可以利用这种方法获得更多的准确率的提升的，但是感觉可学习的东西很多，不想把更多的时间放在对数据的理解上。
3. 利用关键词的用户购买信息去做关键词分类。这样做是建立在“购买关键词的用户相似，那么关键词倾向于属于同一类”这一假设的基础上的。所以想对<关键词-用户>向量，应用分类算法。这一矩阵存在着维度过高（600w维）和过于稀疏的问题（很少有两个用户买同一个关键字）。所以推断直接分类既不可行，效果也不好。
4. 这一条详细讲一下用户矩阵的使用。首先想到的是对用户聚类，以降低<关键词-用户>向量的维度和稀疏性，使用k-means。但是由于上面所说的稀疏性问题，所以聚类效果也不好。于是想到利用第一次文本分类的结果，补充训练数据，另外首先对关键词聚类（已经聚好了，33类），这样<用户-关键词>向量维度33维，并且不稀疏。可以使用k-means对用户进行聚类了，分别制定k为100和1000，进行聚类。在使用的过程中，发现了一个问题，k-means一般是基于距离的，而在这个项目的用户中，和质心距离相等的两个点，并不一定相同，距离计算过程中把所有的维度（关键词类别）等同看待，而现实中，我们购买了不同维度（类别）的关键词的用户，虽然距离相等，但是是属于不同类的。基于上述思考，我放弃了使用k-means，按照自己的想法，对用户进行了聚类。得到了2w类左右的用户。用户聚类之后，就达到了对<关键词-用户>矩阵的降维和去稀疏的目的。可以应用分类算法了。
5. 利用上述的方法，利用用户对关键词分类。但是结果非常不理想。原因在于，我们在第3步里的假设是不对的。也就是说，假如几个关键词，被几个相同或者相似的用户购买，并不代表关键词属于同一类。分析bad case可以看出，比如公司转让，服装公司转让被分入了服装类，房地产公司转让被分入了房地产类。但是这些关键词应该是被一个公司转让中介性质的用户购买了。所以从用户角度去分类，是不可行的。
6. 经过上面的尝试，时间已经过去了3周。我们放弃了使用用户购买信息，试图提高文本分类的效果。首先我们发现，需要利用多词模式去提高特征质量，比如，搬家是个特征词，公司不是，但是搬家公司，是个非常有区分度的特征。由于没有事先的积累，也找不到现成的工具，我们自己根据经验和对数据的理解，写了bi-gram和tri-gram的map-reduce组合算法。使用频率对多词模式进行筛选，然后加入特征集训练。这一步，问题很多，因为大部分是拍脑袋定的，感觉很不科学，接下来，我可能会对这方面做一些针对性的阅读。
7. 重复了文本分类的步骤，做了第二版的分类器，但是由于多词模式的筛选和使用都有些问题，所以这一版分类器没有取得预期的进步。
8. 时间到了最后一周，感觉也没有什么特别好的点子了。偶然之中发现了Multi-class 分类相关的知识，比如one-vs-rest，one-vs-one等等。以为发现了新的方法，于是我们用Hadoop又做了一个one-vs-rest的多类分布式分类器，主要的思路是参考的别人的，花了很多时间去读别人的代码以及了解liblinear的各项接口和参数。这段时间算是对SVM和Hadoop编程有了更进一步的知识。无奈，最终发现，其实本来Liblinear做多类分类，就是用的one-vs-rest。而且我们的分类器，最终效果也不好。

这就是过去一个月中，我们进行的所有工作。学到了很多知识，对SVM，NB分类器，对Hadoop的java接口和Streaming以及map-reduce的思想都有了进一步的认识，并且可以顺利使用了。虽然最终结果并不好，但是对于缺乏经验的我们来说，算是一个很好的经历。下篇文章里，我再结合一些书本上的知识，讲一些关于这个项目的思考，和可能的改进方向。