---
layout: post
title: "为控件增加鼠标悬浮提示"
date: 2013-08-26 14:21
comments: true
categories: [数据可视化,CodeSegment]
---

上星期阅读了《Interactive Data Visualization for the Web》一书之后，便开始尝试使用D3js了。以前一直没怎么写过前端和界面，个人对于坐标位置布局什么的相当没感觉，总是乱乱的。所以这次写来，大部分也都是用的别人写好的现成的东西，在这个基础上做些改动。

今天做的改动之一就是为标签Text增加鼠标悬浮事件，提示改概念的定义。

效果图如下：

![Imgur](http://i.imgur.com/0E53nrC.jpg)

这段代码参考了StackOverflow上的这个[帖子](http://stackoverflow.com/questions/10805184/d3-show-data-on-mouseover-of-circle)

    // Show defination of the current item
    function show_defination(d) {
        d3.select(this)
            .append("svg:title")
            .text(function(d) {return d.def;})
            .attr("x",function(d) {return d.x+10;})
            .attr("y",function(d) {return d.y+10;})
    }

我添加的代码如上，绑定在text控件的mouseover时间上。```on("mouseover",show_defination);```

另外我还发现了一个很好的在线校验json的[网站](http://jsonlint.com/)



