---
title: C++基础1
date: 2018-02-15 21:38:12
categories: 
    - 编程与生活
tags: 
    - C++&C
---

OOP的本质就是设计并扩展自己的数据类型。

1. 变量名命名时，以两个下划线或下划线和大写字母打头的名称被保留给实现（编译器及其使用的资源）使用，以一个下划线开头的名称被保留给，实现，用作全局标识符。

2. C++中对于名称长度没有限制，但是有些平台有长度限制。

3. C++赋值方式：
（大括号初始化器，适用于任何类型，等号可有可无）
    1. int x = 1;
    2. int x = {1};//将大括号初始化器用于单变量
    3. int x{1}；
    4. int x{};//置零
    5. int x = {1}；//置零

4. int类型被设定为对于目标计算机而言效率最高的长度。

5. cout 关于进制转换
std::cout;
std::endl;
std::hex;
std::oct;
code:
    using namespace std;
    int main()
    {
        int chest = 42;
        int waist = 42;
        int inseam = 42;

        cout << "chest = " << chest << " (decimal for 42)" << endl;
        cout << hex;
        cout << "waist = " << waist << " (hexadecimal for 42)" << endl;
        cout << oct;
        cout << "inseam = " << inseam << " (octal for 42)" << endl;
        return 0;
    }
(cout << hex;不会显示任何内容，只是会改变cout显示整数的方式。）

6. 数值类型
220L;
220LU == 220UL;
220ll == 220LL;
220ull == 220ULL == 220uLL == 220Ull;
(十六进制常用来表示内存地址，而内存地址是没有符号的，所以多用 "usigned int" 类型来表示十六位地址）

7. ‘字符’ “字符串”

8. cout.put();//cout的成员函数
显示一个字符；
    int main()
    {
        char ch = 'M';
        int i = ch;
        cout << "The ASCII code for " << ch << " is " << i << endl;

        cout << "Add one to the character code:" << endl;
        ch = ch + 1;
        i = ch;
        cout << "The ASCII code for " << ch << " is " << i << endl;

        cout << "Displaying char ch using cout.put(ch): ";
        cout.put(ch) //<< '!';
        //cout.put('!');//两个二选一即可；
        cout << endl << "Done" << endl;
        return 0;
    }

9. bool
bool is_ready = true; //"is_ready" assigned true or 1;

10. 创建符号常量
尽量使用const来创建，而非"#define";
const type name = value;
声明时没有提供值，则该常量的值将是不确定的，且无法修改。
    好处：
    1, 限定类型
    2, 限定范围

11. +d.dddE+n
指的是将小数点向右移动n位，
+换成~时，表示将小数点向左移动n位。

12. setf成员函数可以把强行把浮点数输出时用定点表示法
    int main()
    {
        cout.setf(ios_base::fixed, ios_base::floatfield);
        cout << "Integer division: 9/5 = " << 9 / 5 << endl;
        cout << "Floating-point division: 9.0 / 5.0 = ";
        cout << 9.0 / 5.0 << endl;
        cout << "Mixed division: 9.0 / 5 = " << 9.0 / 5 << endl;
        cout << "double constants: 1e7 / 9.0 = ";
        cout << 1.e7 / 9.0 << endl;
        cout << "float constants: 1e7f / 9.0f = ";
        cout << 1.e7f / 9.0f << endl;
        return 0;
    }
    
13. 浮点类型默认为double类型

14. 以{}方式初始化时进行的转换
使用大括号的初始化，陈作列表初始化，因为这种初始化，常用于给复杂的数据类型提供值列表，对类型转换更为严格，
此类初始化不允许对数据进行缩窄（narrowing），即使得变量类型有可能无法表示赋给它的值。

15. 整型提升（integral promotion）
例如将 bool,char,unsigned char,...值转换为 int 

16. 强制转换
(typeName) value
typeName (value)
static_cast<typeName> (value)//转换要求很严格

17. 运算符重载
使用相同符号，进行多种操作，叫做运算符重载(operator overloading)