---
title: C++基础3
date: 2018-02-15 21:38:47
categories: 
    - 编程与生活
tags: 
    - C++&C
---

1. 未初始化的数组的内容是未定义的，且其末尾的空字符是不确定的，有可能在限定字节外。
2. 未初始化的 string 对象长度自动设定为0。
3. wchar_t, char16_t, char32_t 三种字符串
wchar_t title[] = L"Chief Astrogator";
char16_t name[] = u"Felonia Ripova";
char32_t car[] = U"Humber Super Snipe";

4. 原始(raw)字符串
cout << R"(l"s" / \sajfl; " "" lafjlj.)" << endl;
原样输出；
上面的方式有不足，会把第一个 ' )" ' 认为是字符串到此结尾；
cout << R"+*("(dkshfk)"), jsfjldsjf.)+*" << endl;
输入时会忽视特殊字符的特殊用途，回车会被添加到原始字符串中；
可以使用 Ru UR 等配合wchar_t 等类型的原始字符串；

5. 结构
C: struct inflatable goose;
C++: inflatable vincent;
*访问类成员函数的点号方式即使从访问结构成员变量的方式衍生而来；
--------------------------
<string>
struct test
{
	std::string name;
	flaot volume;
	double price;
};
struct inflatable
{
	char name[20];
	float colume;
	double prince;
};
int main()
{
	inflatable guest = 
	{
		"lasfj",
		1.88,
		29.99
	};
	
	inflatable pal1 = {"sakdfh", 2.5, 3.0};
	
	inflatable pal2 {"sakdfh", 2.5, 3.0};

	inflatable pal3 {};//全部字节置零**不允许格式上的缩窄转换；
	...
	return 0;
}
---------------------
(the same struct) 
choice = bouquet;
---------------------
struct perks
{
	int sdlkfj;
	double skdf;
	char a[20];
} mr_dlasjfl =
{
	7, 
	23.0,
	"dsafhk"
};//分开写的话可能更便于阅读；
--------------------
struct 
{
	int x;
	int y;
} position;
//只有position这一个这种类型的结构，后面也将无法创建；
--------------------
struct widget
{
	char brand[20];
	int type;
	union id
	{
		long id_num;
		char id_char[20];
	} id_val;
};
widget prize;
cin >> prize.id_val.id_char;
-----------------------
struct widget
{
	char brand[20];
	int type;
	union//匿名共用体(anonymous union)
	{
		long id_num;
		char id_char[20];
	} id_val;
};
widget prize;
cin >> prize.id_char;

6. enum 枚举类型
可用于设置符号常量，枚举量(enumerator)；
enum {a, b, c, d}//0,1,2,3
枚举只定义了赋值运算符，没有定义算术运算；

7. 指针使用是一定要先确定地址；
int* pt;
pt = (int*) 0xB8000000//地址；

8. new 运算符
指针的真正用武之地在于在运行阶段分配未命名的内存以存储值；
C中可以使用库函数malloc()来分配；
C++中可以使用new运算符；
传统通过内存名称来访问该 int：
int higgens;
int* pt = &higgens;
新方式：
int* pn = new int;
//此处pn指向的是内存是没有名称的，可以说指向一个数据对象，指的是为数据项分配的内存块；

9. delete 运算符
使得用完的内存归还内存池；
与 new 搭配使用。
int* ps = new int;
delete ps;
//仅仅释放ps所指向的内存；
*一定要配对使用 new 与 delete,否则会发生内存泄漏(memory leak)，也就是说，被分配的内存再也无法使用。
如果泄露严重，则程序会不断寻找更多内存而中止。
int* ps = new int;
delete ps;
delete ps;//已释放的内存块再进行释放操作，这样的出的结果是不确定的，意味着什么都会发生，但是对于空指针而言则是安全的
int jugs = 5;
int* pi = %jugs;
delete pi;//delete只能用来释放由new分配的内存。