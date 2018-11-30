---
title: C++基础2
date: 2018-02-15 21:38:32
categories: 
    - 编程与生活
tags: 
    - C++&C
---

1. 有效的下标值
int a[10];
a[10] = 10;

2. 数组初始化
只有定义数组时才可以初始化；
int a[2] = {1,2};
可以用下标分别给数字元素初始化；
int a[2];
a[0] = 1;
a[1] = 2;
部分初始化时，未言明部分自动初始化为零；
//将数组整体初始化为零，可以显示的将数组第一个元素初始化为零即可
//int a[5] = {0};
可以不明确指定数组元素数目；
int a[] = {1,2,3};
int num_elements = sizeof things / sizeof ( int);
初始化时可以省略"=";
int a[2] {1,2};
//大括号内可以空白，全部元素置零；
//int a[2] {};
//列表示初始化是不允许缩窄的，要保证数据无误；
//int a[] = {1,3.4};

3. C-风格字符串
以空白符结尾，'\0'，ascii = 0；
int a[2] = {'a', 'b'}; //不是字符串
int a[2] = {'a', '\0'}; //是字符串
//字符串常量(string constant)或字符串字面值(string literal);
char a[10] = "god is girl";
char a[] = "god is girl";
//C++对字符串长度无限制；

4. 带引号的字符串
cout << “sdlfjl" "sldfjlj" << endl;//可以在中间处隔行；
cout << “sdlfjlsldfjlj" << endl;

5. strlen()与sizeof();
<cstring>（老式实现为string.h）
提供了很多关于字符串相关的其他函数的声明；
strlen();//只计算可见的字符数
sizeof();//算出整个数组的长度
==>>int a[strlen(strings) + 1];

6. cin小缺陷
cout << "Your name" << endl;
char name[10];
cin >> name;
//输入"Lart Pang";
cout << "Your university" << endl;
char u_name[10];
//想要输DUT；
cin >> u_name;
...
//结果，name[10] == "Lart";
//u_name[10] == "Pang";
==>>
//cin 使用空白（空格，制表，换行符）来确定字符串结尾
//此处，name中被放入Lart，之后的Pang仍在输入队列中，被存到了u_name中；

7. 读取行输入
<iostream>
getline();//到 '\n', 然后停止读取并删掉 '\n'；
get();// 不删；

8. cin.getline(name, 20);
//name为要存储输入行的数组名；
//20为要读取的字符数；
//一行最多读取19个字符；  
//在存储字符串时用空字符替换换行符；
cin.getline(name, 20).getline(u_name. 20);
//连续读入两行；

9. cin.get(name, 20);
由于在第一次cin.get();读完后，输入队列中还存在换行符，所以紧跟在其后的另一个cin.get();可能会直接吃掉换行符；
所以可以使用下面的两种方法来解决：
cin.get(name, 20);
cin.get();
cin.get(u_name, 20);
---------------------
cin.get(name, 20).get();//cin会返回一个cin对象；
cin.get(u_name, 20);
---------------------
(cin >> name).get();//cin后也会残留'\n'；
==>>
get(),getline()的性质上的差异，使得两者有了一些奇特的作用，getline()使用起来明显方便，但是get()的特性使得操作者，可以明确读取停止的原因，是因为数组填满还是读完了一整行；
---------------------
稍微特别用法：
getline(cin ,str);
将数据读入到string对象中；
这里的getline不是类方法；

10. string类
<string>
using namesapce std;
string str1;
string str2 = "asd";
//string str2 = {"asd"};
//string str2 {"asd"};
cin >> str1;
cout << str1 << str2[2] << str2 << str1[2] << endl;
//string对象和字符数组之间主要区别，可以讲string对象声明为简单变量而不是数组；
//string对象能在初始化时自动调节大小；

11. string简单操作
string str3;
str3 = str1 + str2;
str3 = str1;
str1 += str2;//字符串拼接；
int len1 = str1.size();
//str1是string类中的一个对象，而size()是string类的一个方法。
//方法是一个函数，只能通过其所属的类的对象进行调用；
//C++中的类，对象，方法
//C函数使用参数来确定要使用那个字符串，而C++string类对象，是使用对象名和句点运算符来指出要使用那个字符串；
int len2 = strlen(str1);
