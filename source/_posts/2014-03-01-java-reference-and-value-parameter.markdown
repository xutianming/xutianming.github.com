---
layout: post
title: "Java值传递和引用传递"
date: 2014-03-01 21:25
comments: true
categories: 编程语言
---

网上一搜Java引用传递，会出来很多博客，会举出好多例子来说明Java的引用传递。当然有一小部分博客说，Java是没有引用传递的。最近在做项目的过程中，我也遇到了这些问题，看这些中文博客更是一头雾水。只有经过了自己的实践，才有了可以让自己信服的结论：

Java里没有在C++意义上的那种引用传递，全是值传递。只是在Java中，对象分为基本类型和引用类型。典型的基本类型，包含所有的基本类型（primitive）；而对象等高级类型，属于引用类型。基本类型作为参数传递的时候，就是普通意义上的值传递，没啥争论，而引用类型作为参数的时候，传递的是本对象的引用的值。所以一概是值传递，而没有C++意义上的引用传递。下面也用代码来说明。

    public void setA(ArrayList<Double> a) {
        ArrayList<Double> b = new ArrayList<Double>();
        b.add(2.0);
        a.add(4.0);
        a=new ArrayList<Double>(b);
        a.add(3.0);
    }

上面这段代码，运行之后，a里面只多了4.0。也就是说，只有`a.add(4.0)`起了作用。按照我们上面的理论，a是原数组的一个引用，指向原数组。那么`a.add(4.0)`自然可以影响到原数组。下面的`new ArrayList`语句，改变了a的指向，a指向一个新的数组。这时候再`a.add(3.0)`就是影响的新建的这个数组了。函数调用结束，a被还原指向原来的数组（这就是值传递的机制，原来数组的引用在函数调用之前保存在堆栈中，复制一份作为函数参数传递进去，函数调用结束恢复）。所以运行之后，a里只有4.0。

那么什么是引用传递呢。其实这是C++里的概念，就是传递的是引用。同样的代码用C++写。

    void setA(vector<double> a) {
        a.push_back(1.0);
        vector<double> b;
        b.add(2.0);
        a=b;
    }
    int main() {
        vector<double> a;
        setA(a);
        cout<<a.size()<<endl;
        return 0;
    }

在这段代码里不论setA里做什么，a都不变。因为是值传递。那么C++的引用传递是什么呢？

    void setA(vector<double> &a) {
        a.push_back(1.0);
        vector<double> b;
        a=b;
    }
    
    int main() {
        vector<double> a;
        a.add(1.0);
        setA(a);
        cout<<a.size()<<endl;
        return 0;
    }

这样就能改变a的值了。 

最后附上我所说的结论的证明。

http://stackoverflow.com/questions/40480/is-java-pass-by-reference

Java中的reference type 和 C++中的reference type是指的不同的东西的，在Java中，引用类型指的是Java对某一系列类型的处理方式，而在C++中，引用类型是一种切实的类型，和指针一样，可以与其他类型组合出一些复合类型。