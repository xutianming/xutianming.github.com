---
layout: post
title: "C++输入输出总结"
date: 2013-08-07 09:23
comments: true
categories: 算法基础
---
输入：
1 . 未说明有多少个input block

    while(scanf("%d %d",&a,&b)!=EOF) 
    {    ...    }
scanf返回的是输入的变量的个数，EOF=-1表示没有输入
    while(cin>>a>>b) {    ...    }2 . 说明有N个input block    

    scanf("%d",&n) ; 	for( i=0 ; i<n ; i++ )     {...} 
		cin >> n;	for( i=0 ; i<n ; i++ )     { .... } 3 . 输入是一整行的字符串
    C  
    char buf[20];     gets(buf); 

    // 如果用string buf;来保存：
    getline( cin , buf );     // 如果用char buf[ 255 ]; 来保存：     cin.getline( buf, 255 );
`scanf(“ %s%s”,str1,str2)`，在多个字符串之间用一个或多个空格分隔；若使用gets函数，应为gets(str1); gets(str2); 字符串之间用回车符作分隔。通常情况下，接受短字符用scanf函数，接受长字符用gets函数。而getchar函数每次只接受一个字符，经常c=getchar()这样来使用。getline 是一个函数，它可以接受用户的输入的字符，直到已达指定个数，或者用户输入了特定的字符。它的函数声明形式（函数原型）如下：	`istream& getline(char line[], int size, char endchar = '\n');`不用管它的返回类型，来关心它的三个参数：
* char line[]： 就是一个字符数组，用户输入的内容将存入在该数组内。* int size : 最多接受几个字符？用户超过size的输入都将不被接受。* char endchar :当用户输入endchar指定的字符时，自动结束。默认是回车符。例如：char name[4];cin.getline(name,4,'\n');由于 endchar 默认已经是 '\n'，所以后面那行也可以写成：cin.getline(name,4);输出：
1 . 输出之后换行    C:    {     ....     printf("%d\n",ans);     }     C++:    {     ...     cout << ans << endl;     }
2 . 每个输出之间有空行
      while(scanf("%d %d",&a, &b) != EOF) 	        printf("%d\n\n",a+b);    
    C++:    cout << ans << endl << endl;
3 . 输出结束之后输出空行
    C:    for (k=0;k<count;k++) { 	  while (…) {     	     printf(" %d\n",result);       
      }       
      if (k!=count-1) printf("\n");     
    }     C++	// 类似，输出语句换一下即可。

在学习了以上基础之后，我在HDUOJ上做了十几个题目来练习，也犯了不少错误，总结如下，以避免以后再犯，提高编程的成功率和效率：
* 未说明有多少个输入块时，scanf要和EOF比较，这个和cin的使用不同，不然的话OJ上会超时
* 同样的道理，也不要用`while(true)`来代替`while(cin>>a)`，也会在输入结束的时候，仍然执行从而超时
* 再就是输出有换行，要输出两个`endl`的时候，要注意假如指定输入个数，最后一次输出之后，不要再换行的。没指定有多少个输入的时候，则每次输出都换行。
另外，我还犯了其他一些低级错误，比如数据类型定义错误等等，这些错误自己调试的时候，不会出错，但是OJ上指定的某些测试用例肯定是没法通过的。所以要格外小心，OJ也不会告诉你哪个测试用例挂了，就很浪费时间，为了提高效率，写程序的时候就得多注意下，看清题目。