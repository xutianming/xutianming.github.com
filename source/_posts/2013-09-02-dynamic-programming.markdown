---
layout: post
title: "动态规划"
date: 2013-09-02 19:53
comments: true
categories: 算法基础
---

当遇到复杂问题的时候，我们很自然的想要降低问题的复杂度和规模。在《算法导论》中，作者将动态规划和分治算法都当做降低问题规模，将复杂问题转化为子问题的方法。不同的是，分治算法划分出的子问题都是独立的，而动态规划中的子问题相互之间是非独立的，高层的子问题（离原问题比较近）往往依赖于低层子问题。所以假如不利用动态规划，自底向上计算这类问题的时候，会有大量重复的计算，从而使算法的效率低。

那么，动态规划的本质就是建立一个表，存储子问题的结果，在计算高层问题的时候，直接利用表中的结果。这就是动态规划的基本思想。动态规划往往被用来计算最优化问题，利用的理论是子问题最优化理论，也就是一个最优化解的路径上的所有子问题，也都是当前最优化的。但是，这已经不是动态规划的核心了，我之前就在理解动态规划的时候，犯了这个错误。在试图利用动态规划去解决问题的时候，我往往纠结于去寻找这样的最优化子结构。其实有时候，这种最优化子结构并不好找甚至没有，但是也可以利用动态规划去解。动态规划的基本思想，还是很朴素的。

最近在TopCoder上读了一个黄名大牛写的[动态规划进阶手册](http://www.topcoder.com/tc?d1=tutorials&d2=dynProg&module=Static)深受启发。文中指出，动态规划应该这么描述：动态规划算法往往建立在一个递归方程和这个方程的初始状态之上（很像那种找递推公式的问题）。每个子问题，都建立在之前发现并计算好的问题上。动态规划问题往往都有多项式级别的复杂度，因此比暴力搜索和回溯算法效率要高（这一点我还不是特别明白，文中有提出，多项式复杂度的问题都可以用动态规划来解决）。

动态规划问题的解决过程是分为两步：定义问题的状态（或者说是子问题），发现如何又一个状态转移到下一个状态（也就是定义状态转移方程，是上文指出的“递归方程”），更准确的说，如何利用已有的子状态，计算下一个状态，因为有时候不是从i转移到i+1。

举例说明：

给定N个硬币，价值分别为（v1，v2，v3...vN），给定价值和S。求解价值和为S的硬币集合，使其包含的硬币个数最少，每种价值的硬币可以使用任意多个。

问题分析：

在这个问题中我们要求解的结果是价值和S，那么定义“状态”为当前价值和为i（i<S），对于所有的状态j（j>i），要利用i来求解，而对于状态j(j<i)，要在计算状态i之前计算好。在实践中，我发现子状态往往是针对要求解的那个问题的。

对于状态i，我们假设，所有的子状态j（j<i）都已经计算出结果来了。那么i如何利用子结果来计算。对于所有价值小于i 的硬币（循环遍历）j，使用此硬币，使价值和变为i-j，由于i-j<i，那么此子问题，我们应该已经计算好了，假设硬币数为m，则i状态的结果应为m+1。遍历过程中，我们把最小的那个结果存给状态i。

基于这种想法，我们需要从状态0开始，到状态s逐一计算。也就是动态规划中所谓的，从顶向下思考，从底向上计算。这里有个思考陷阱，千万不要想如何从i计算i+1，而是如何利用已有的所有子状态j(j<=i)计算i+1。就写这篇博客的过程，我因为想当然认为这样，差点进入死胡同。

这是动态规划最基本的思考过程。

一般情况下，我们要遍历所有的比i低的状态j，但是并不是所有的j都能转换到i。在这个硬币问题中取决于，是否有个硬币k，使得S[j]+vk=S[i]。这就是状态转移的条件，所以在实践中，我们一般要判断状态j能否转移到条件i，在上例中没有判断的原因是，我们根据硬币价值遍历，遍历到的都肯定满足转移的条件。其他问题，比如无向图中，状态节点j能否转移到节点i取决于i和j之间是否有一条边。这就要判断了，当然假如存储图结构的时候，存储了所有指向节点i的入节点j，就可以遍历这个节点集了，但是对于自底向上的动态规划解决方案中，很少出现。

更复杂一点，状态转移不是一维的，变成二维甚至多维（目前只见过二维）。这种问题，通过比较好的定义子状态，也很好理解。

还有其他的情况下，限制条件所需的信息和我们所求的结果并不一致。比如赋权图中，从节点j到节点i的最短路径，在权值和小于s的前提下。我们最终关心的是路径长度，但是状态转移的限制条件是权值和，这时候需要一个二维数组来存储。

更复杂的情况，乍一看并不是动态规划问题，但是和我们以前遇到过的动态规划问题很像，这时候，我们需要一些转变，把问题转换成以前的问题的升级版去求解。这是最难的，菜鸟级别的我暂时还没什么感受。留在日后补充吧。