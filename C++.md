# 一、C++基础

面向对象编程技术的讲解

## 0. 零碎知识

`int main(int argc,int argv[]) argc` 为函数参数数量 `argv[]`每个指针指向命令行一个字符

枚举类型：用法 `enum <类型名> {<枚举常量表>};`  

​	枚举常量代表该枚举类型的变量可能取的值，编译系统为每个枚举常量指定一个整数值，默认状态下，这个整数就是所列举元素的序号，序号从0开始。 可以在定义枚举类型时为部分或全部枚举常量指定整数值，在指定值之前的枚举常量仍按默认方式取值，而指定值之后的枚举常量按依次加1的原则取值。 各枚举常量的值可以重复。枚举常量只能以标识符形式表示，而不能是整型、字符型等文字常量。

```c++
enum week {Sun=7, Mon=1, Tue, Wed, Thu, Fri, Sat};
//枚举常量Sun,Mon,Tue,Wed,Thu,Fri,Sat的值分别为7、1、2、3、4、5、6
```



## 1. const修饰指针

### 常量指针

`const`在*左边，修饰 `*p`，表示`*p`不可修改

```c++
const int *p=&a; const int* i;int const* j; //指针指向可以修改，指向的值不可修改
```

### 指针常量

`const`在*右边，修饰 p，表示`p`不可修改  

```c++
int * const p=&a;//指针指向不可以修改，指向的值可修改
			    //常量指针，必须初始化  *在const之前表示指针是一个常量，不变的是指针本身的值，
```

### 既修饰指针又修饰常量

```c++
const int * const p=&a;//指针指向及指向的值均不可修改
```

利用指针作为函数参数，可以修改实参的值

# 二、C++提高



## 1. 内存分区模型

代码区：存放二进制代码 ，存放CPU执行的机器指令，是共享的（对需要频繁被执行的程序，内存中有一份代码即可）、只读的

全局区 ：存放全局变量、静态变量（`static`关键字修饰）和常量（字符串常量及`const`修饰的全局变量），程序结束后由操作系统释放

​				不在全局区中的主句：局部变量、`const`修饰的局部变量（局部常量）

栈区 ：由编译器自动分配释放，存放函数的参数、局部变量；不要返回局部变量地址

堆区 ：由程序员分配和释放，程序员不释放则由操作系统回收  主要使用new来开辟

```c++
int *p=new int(10); //创建一个指向10的指针  
delete p;
int *arr=new int[20]; //创建一个指向20个整形数据的数据
delete[] s;
```



##  2. 引用

基本语法： `数据类型 &别名=原名`     引用必须要初始化，一旦初始化即不可修改；

必须引用合法内存空间，10在常量区，故不能 `int &a=10;`

```c++
int a=10;	int &b=a;	
int c=20; 	b=c;//赋值操作，而不是更改引用
```

### 本质

​	本质上是C++内部实现的一个[指针常量](#指针常量) ：指针的指向不可修改，指向的值可以修改；

```c++
int a = 10;
int& ref = a;//    编译器自动转换为 int* const ref = &a;
ref=20 		//	  编译器自动转换为*ref=20
```

### 常量引用

### 引用做函数参数/返回值

​	函数传参时，可以利用引用技术让形参修饰实参；

​	使用引用的方式传递参数就不会再拷贝出来一份数据

```c++
void mySwap01(int a, int b){  //值传递，形参改变，实参不变
    int temp = a;    a = b;    b = temp;
}
void mySwap02(int* pa, int* pb){//地址传递，实参改变
    int temp = *pa;    *pa = *pb;    *pb = temp;
}void mySwap03(int& a, int& b){//引用传递，实参改变，优化指针修改实参
    int temp = a;    a = b;    b = temp;
}
int main(){
    swqp01(a,b);	swap02(&a,&b);	swap03(a,b);
}
```

​	不要返回局部变量引用

​	如果函数的返回值是引用，这个函数调用可以作为左值

```c++
int& test(){
    static int a = 10;//静态变量，存放在全局区，不会被释放
    return a;
}
int& ref = test();//ref是别名 a是原名
test()=1000;
```

### 

用于修饰形参，防止误操作

```c++
int& ref = 10;//错误  引用必须引一块合法的内存空间 
const int& ref = 10;//正确   编译器自动修改  int temp = 10; const int& ref = temp;
```



```c++
void showValue(const int& val){  //形参不会改变外部实参
    val = 1000;//语句报错
    cout << "val= " << val << endl;
}
```

## 3.函数提高

### 函数默认参数 / 占位参数

语法：`返回值类型 函数名（形参=默认值）` 

如果某个位置已经有了默认参数，那么从这个位置往后都必须有默认值；

如果函数声明有默认参数，函数实现就不能有默认参数；声明和实现只能有一个有默认参数

占位参数语法：`返回值类型 函数名（形参=默认值）` 还可以有默认参数

```c++
int add(int a, int b = 20, int c = 30){  //默认参数
    return a + b + c;
}

int func(int a,int =10){	//占位参数
    return a;
}
```

### 函数重载

函数名可以相同，提高复用性

满足条件：同一个作用域下，函数名相同，函数参数类型、个数、或顺序不同  

函数的返回值不可以作函数重载的条件

```c++
//引用作为重载条件
void func(int &a){   
    cout << "func (int &a)调用 " << endl;
}
void func(const int &a){	//此处a可理解为为10的引用
    cout << "func(const int &a) 调用 " << endl;
    cout << "num of a is " << a << endl;
}
int main(){
    int a=10;
    func(a);//调用无const的版本
    func(10);//调用有const版本     const int &a=10; 相当于临时建一个变量t=10,让&a=t; 
}
```

# 4.类和对象

面向对象的三大特征：封装、继承、多态；万事万物皆为对象

例子：

​	人可以作为对象，属性有姓名、年龄、身高、体重……  行为有走、跑、跳

## 封装

### 意义

将属性和行为作为一个整体；将属性和行为加以权限控制

类中的属性和行为统称为成员；属性又称为成员属性、成员变量；行为又称为成员函数、成员方法

```c++
const double PI = 3.14;
class Circle
{
public:
	int m_r;//属性 半径
	double calculateZC() {  //行为(一般为函数) 获取周长
		return 2 * PI * m_r;
	}
};
int main()
{
	Circle c1;
	c1.m_r = 10;
	cout << "Zhouchagn is " << c1.calculateZC() << endl;
	return 0;
}
```



```c++
#include <iostream>
#include<string>
using namespace std;
class Student {
public:
	string m_Name;
	int m_ID;
	void showStu()	{
		cout << "姓名: " << m_Name << "  学号： " << m_ID << endl;
	}
	void setName(string name) {
		m_Name = name;
	}
	void setID(int num) {
		m_ID = num;
	}
};
int main()
{
	Student stu;
	stu.setName("张三");
	stu.setID(201712842);
	stu.showStu();
}
```

访问权限：	`public`：成员，类内可以访问，类外可以访问   

`protected`：成员，类内可以访问，类外不可访问  儿子也可以访问父亲中的保护内容

`private`：    成员，类内可以访问，类外不可访问  儿子不可以访问父亲的私有内容

`struct`默认访问权限为公共、`class`默认访问权限为公有

### 成员属性设为私有

优点：可以自己控制读写权限；对于写权限，我们可以检测数据的有效性

```c++
class Person {
public:
	void setName(string name) {
		m_Name = name;
	}
	string getName() {
		return m_Name;
	}
	int getAge() {
		m_Age = 21;
		return  m_Age;
	}
	void setAge(int age)
	{
		if (age > 150 || age < 0)
		{
			cout << "error age" << endl;
			return;
		}
		m_Age = age;
	}
	void setLover(string lover)	{
		m_Lover = lover;
	}
private:
	string m_Name; //可读可写
	int m_Age;	//只读
	string m_Lover;//
};
```

利用成员函数判断是否相等只需要传入一个参数；利用全局函数判断是否相等需要传入两个参数

```c++
class Cube {
public:
	bool isSameByClass(Cube &c) {
	}

}；
bool isSame(Cube &c1, Cube &c2) { //使用引用的方式传递参数就不会再拷贝出一份数据
}
int main()
{
	Cube c1, c2;
	isSame(c1, c2);
	c1.isSameByClass(c2);
}
```



## 

```c++
class Point {
public:
	void setX(float x)	{		m_X = x;	}
	void setY(float y)	{		m_Y = y;	}
	float getX()	{		return m_X;	}
	float getY()	{		return m_Y;	}
private:
	float m_X;	float m_Y;
};

class Circle {
public:
	void setR(float R)	{		m_R = R;	}
	float getR() {		return m_R;	}
	void setCenter(Point center)	{		m_center = center;	}
	Point getCenter()	{		return m_center;	}
private:
	int m_R;	Point m_center;
};

```

把类的声明放到 头文件中：https://www.bilibili.com/video/BV1et411b73Z?p=105

## 对象的初始化和清理

## C++对象的初始化和清理

