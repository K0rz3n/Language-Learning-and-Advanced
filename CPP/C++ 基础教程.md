---
title: C++ 基础教程
date: 2018-10-11 11:58:18
tags: 编程 C++ 基础 
categories: 编程
---

## **一、第一个C++ 程序**

**示例代码：**

    #include "iostream"
    
    using namespace std; //使用标准的命名空间，在这个空间中有很多的标准定义
    
    int  main(){
    
        //cout 其实就代表那个黑色的输出框  << 表示输入  endl 表示 \n
        cout << "hello world" << endl;
        return 0;
    }
    
<!-- more -->
**结果：**

    hello world

**注意：**

cout cin  都要写在 << 或者 >> 的左边


## **二、通过一个程序理解面向对象**

我们想通过两种方式实现求解圆的周长和面积这一个小程序，来理解和区别面向对象和面向过程

### **1.面向过程：**

    #include "iostream"
    
    using namespace std; //使用标准的命名空间，在这个空间中有很多的标准定义
    
    int  main(){
    
        double r = 0;
        double s = 0;
        cout << "请输入圆的半径：";
        cin >> r;
        cout << "圆的半径是：" << r << endl;
        s = 3.14*r*r;
        cout << "圆的面积是：" << s << endl;
    
    }
    
### **2.面向对象：**

    #include "iostream"

    using namespace std; //使用标准的命名空间，在这个空间中有很多的标准定义
    class Circle{
    
    public:
        double m_r;
        double m_s;
    public:
        void setR(double r){
            m_r = r;
        }
    
        double getR(){
            return m_r;
        }
    
        double getS(){
            m_s = 3.14*m_r*m_r;
            return m_s;
        }
    
    };
    
    
    int main(){
        Circle c1;
        double r;
        cout << "请输入圆的半径:";
        cin >> r;
    
        c1.setR(r);
        cout << "圆的面积是：" << c1.getS() <<endl;
    }
    
### **3.总结分析**

1.类代码不是一步一步执行的
2.类是一个数据类型（是固定大小内存块的别名），定义类的时候是不会分配内存空间的，只有当实例化的时候才会分配内存空间

### **4.类中不写成员函数的错误模型**

    class Circle{
    
    public:
        double r;
        double pi = 3.1415926;
        double area = p1*r*r; 
    }
    
    int main(){
    
        Circle c1;
        cout << "请输入圆的半径："；
        cin >> c1.r;
        cout << "圆的面积：" << c1.area << endl;
    }
    

这个程序编译的时候会报错，后期版本的编译器不报错，但是拿到的是一个随机值，为什么？

**解释：**

在类对象初始化的时候，会给类对象分配内存，但是此时，r 是一个不确定的值，area 等号右面的语句也是在初始化的时候才会执行，这个执行的结果就是一个不确定的值，而后面我们再给 r 传值并不会影响 area ，因为他是属性，在引用的时候是不会执行的，他只会拿着之前的那个不确定的值给我们。（意思就是说，对象的方法在未调用的时候是不会执行的）


## **三、C++ 对 C 的扩展**

### **1.namespace 的出现**

#### **1.namespace 常识**

**如图所示：**

![此处输入图片的描述][1]

#### **2.namespace 的定义**

namespace 是各种标识符的可见范围，所谓 name 可以代表的是 符号常量、变量、宏、函数、结构、枚举、类和对象等 ，为了避免在大规模的程序设计和调用各种的库的时候出现命名冲突才引入了 namespace,std 是 c++ 的标准命名空间，c++ 标准程序库中的所有标识符都定义在std ，比如 iostream ,使用时使用 using namespace std; std::string;


#### **3.namespace 使用实例：**

**1.示例一：**

    #include <iostream>
    
    int main() {
        std::cout << "Hello, World!" << std::endl;
        return 0;
    }
    
我们看到这程序和之前程序的不同，我们没有定义 namespace 结果我们就需要使用 std::cout ，因为 cout 就定义在 std 中

**2.示例二：**

我们能自定义命名空间，还能在命名空间中嵌套定义命名空间

    #include "iostream"
    
    using namespace std;
    
    namespace namespaceA{
        int a = 10;
    }
    
    namespace namespaceB{
        int b = 20;
    
        namespace namespcaeC{
            struct Teacher{
                char name[32];
                int age;
            };
        }
    }
    
    int main() {
    
    
        cout << namespaceA::a << endl;
        cout << namespaceB::b << endl;
    
    }

**结果：**

    10
    20
    
    
#### **4.注意：**

C中的命名空间是全局的，为了和C区分开，C++ 规定不允许使用 带有 .h 的头文件


### **2.实用性加强**

C语言中的变量必须在作用域的开始定义，但是C++ 为了强调实用性可以在使用前定义


### **3.register 关键字增强**

#### **1.register 的作用是什么呢？**

请求编译器让变量直接放在寄存器中，速度更快

因为寄存器是没法取地址的，因此在C语言中下面的代码会报错

**实例：**
    
    void main(){
        register int a = 0;
        printf("&a = %d",&a);
    }

c++ 就比较特殊，它支持这个关键字，但是即使是不用这个关键字也会进行优化，但是c++ 编译器取 register 修饰的变量的地址的时候能取到（实际上它自动的将这个register 设置为无效）


### **4.变量检测增强**

C语言中有很多的灰色地带，比如下面的代码：

**示例代码：**

    int a ;
    int a = 100;
    
    int main(){
        printf("a = %d",a);
    }
    
这样的代码在C 程序中是不会报错的，甚至还能打印出100，但是在C++ 编译器看来这个是明显不合法的


### **5.struct类型的增强**

在C编译器看来，struct 并不是一个新的类型，比如我们定义了一个 struct Teacher{...}, 到函数中声明一个变量的时候不能直接是  Teacher xxx; 必须是 struct Teacher xxx; 但是在 C++ 编译器中是允许的。

**注意:**

在 C++ 中 struct 关键字和  class 关键字完成的功能是一样的，在struct 中也能加上 访问控制修饰符



### **6.C++ 中变量和函数都必须有类型**

也就是说，C++ 对类型的检查将更加严格

> **C 中有一些灰色地带：**
> 
> 1.函数定义的时候没有定义形参，但是调用的时候却能传递各种参数，还不报错
> 2.接受一个没有类型形参，传进去值还能打印

C++ 中变量和函数都必须有类型，C语言中的默认类型在C++ 中是不成立的

### **7.C++ 中新增 Bool 关键字**

1.bool 类型在内存中只占据一个字节的空间 sizeof(bool) == 1
2.bool 类型变量只有两个值，一或者零，不管赋值是多少

### **8.三目运算符在C 和 C++ 编译器中的表现**

在C语言中，表达式是不能做左值的，因为表达式的结果是放在寄存器中的或者说C编译器认为表达式的结果是一个变量的值。因此下面的代码就会报错

    int main(){
         int a = 10;
         int b = 20;
         
         (a  <b ? a :b) = 100;
         return 0;
    }


但是 C++ 编译器认为表达式的结果是变量本身，因此上面的代码在C++ 编译器中将会 给 a 赋值100

**补充:那么C++编译器是怎么实现的呢？**

**推演代码：**

    *(a < b ? &a :&b) = 100;

## **四、C 和 C++ 中的 const**

### **1.C语言中的 const 是一个冒牌货**

之前在 C 编程进阶的文章中说道，虽然我们给一个变量设置为 const 但是我们还是能通过指针间接地修改他的值（当然这个时候需要强制类型转换成 int* ）

但是C ++ 中 同样的代码，你用指针间接地修改是不能实现的，打印出来的还是原来的值

### **2.为什么呢？**

C++ 编译器在检测到定义了const 常量以后，就会为这个常量在符号表中分配一块空间，并以键值对的形式存在，每次取的时候都会从符号表中取，当你对这个常量取地址的时候，C++ 编译器会为其重新分配一块内存空间，我们通过指针改变其值的时候改变的不过是那块新的内存空间，与符号表无关，因此最后的结果不会改变


![此处输入图片的描述][2]


**示例代码：**

    #include "iostream"
    using namespace std;
    
    int main() {
       const int a = 10;
       int * p = NULL;
       p = (int *)&a;
       *p =20;
       cout << "a 的值：" << a <<endl;
    
       cout << "*p的值：" << *p <<endl;
        system("pause");
    
    }
    
**结果：**

    a 的值：10
    *p的值：20

> **注意：**
> 
> C++ 中的 const 可能分配内存空间也可能不分配内存空间， (1)当const 常量为全局并且在其他文件中使用的时候 (2)使用&
> 操作符取 常量的地址



### **3.const 和 #define的相同之处**

C 和 C++ 编译器中普通变量是不能做数组下标的，但是 gcc 中支持

**示例代码：**

    #include "iostream"
    using namespace std;
    
    int main() {
    
        int a = 10;
        int b = 20;
        int array[a+b];
        system("pause");
    
    }

但是如果我们将变量生命成 const 就可以了，这个和#define 的功能是类似的

    #include "iostream"
    using namespace std;
    
    int main() {
    
        const int a = 10;
        const int b = 20;
        int array[a+b];
        system("pause");
    
    }


### **4.C++ 中 const 和 #define的不同之处**

const 常量是由编译器处理的，提供类型检查和作用域检查，宏定义是由预处理器处理的，单纯的文本替换

那么什么叫做作用域检查呢？作用域检查就是#define 是全局的，但是 const 只能在函数内部使用

#### **1.define 的使用** 


**示例代码：**

    #include "iostream"
    using namespace std;
    
    void f1(){
    #define a 10
        const int b = 20;
    }
    
    void f2(){
    
        printf("a = %d\n",a);
    }
    
    int main() {
        f1();
        f2();
        system("pause");
    }

**结果：**

    a = 10


#### **2.const 的使用**

**示例代码：**


    #include "iostream"
    using namespace std;
    
    void f1(){
    #define a 10
        const int b = 20;
    }
    
    void f2(){
    
        //printf("a = %d\n",a);
        printf("b = %d\n",b);
    }
    
    int main() {
        f1();
        f2();
        system("pause");
    
    }

**结果：**

     error: 'b' was not declared in this scope
        printf("b = %d\n",b);
    

## **五、C++ 中的引用**

### **1.引出：**

我们知道变量是内存空间的别名，那么一块内存空间只能有一个别名吗？能不能有多个呢？答案是可以的，这个技术在C++ 中就叫做引用

### **2.语法：**

    Type & name = var ;

name 在这里就是对 var 的引用，修改name 的值就是在修改 var 的值

**示例代码：**

    #include "iostream"
    using namespace std;
    
    int main() {
        int a = 10;
        int & b =  a;
    
        b = 100;
        cout << "b 的值为：" << b <<endl;
        cout << "a 的值为：" << a <<endl;
    
    }
    

**结果：**

    b 的值为：100
    a 的值为：100


**注意:**

引用是 C++ 的语法范畴，请不要用C 的理解方式去看这个语法

### **3.引用做函数参数：**

#### 1.引用必须依附于某一个变量

下面的语法是错误的：

**示例代码：**

    int main(){
        int & b ;
    }


#### **2.利用引用交换变量的值**

我们在C语言中交换变量的值一般是用指针作为函数参数实现的，在 C++ 中可以用引用的方式

**示例代码：**

    #include "iostream"
    using namespace std;
    
    void myswap(int & a ,int & b){
        int c;
        c =a;
        a = b;
        b = c;
    }
    
    
    
    int main() {
        int a = 10;
        int b = 20;
        myswap(a,b);
        cout << "a 的值为：" << a << endl;
        cout << "b 的值为：" << b << endl;
    
    }

**结果：**

    a 的值为：20
    b 的值为：10

#### **3.复杂数据引用做函数参数**

**示例代码：**
    
    #include "iostream"
    using namespace std;
    
    struct Teacher{
    
        int age;
    
    };
    
    void change(Teacher & PT){
        PT.age = 24;
    }
    
    int main() {
        struct Teacher t1;
        change(t1);
        cout << "T1 的年龄是：" << t1.age <<endl;
    }

**结果：**

    T1 的年龄是：24 

#### **4.引用的意义**

(1)引用作为别名存在，在一些场合能很好地替代指针
(2)引用相对于指针来讲有更好的可读性和实用性


#### **5.引用的本质**

**1.引用就是通一块内存空间的门牌号**

**示例代码：**

    #include "iostream"
    using namespace std;
    
    
    int main() {
    
        int a = 10;
        int & b = a;
        cout << "&a = " << &a <<endl;
        cout << "&b = " << &b << endl;
    }

**结果：**

    &a = 0x61ff18
    &b = 0x61ff18

**2.引用变量是否占据内存空间呢？**

**示例代码：**

    #include "iostream"
    using namespace std;
    
    struct Teacher{
        char name[64];//64
        int age;      //4
        int & a;      //?
        int & b;      //?
    
    };
    
    int main() {
        cout << "sizeof(Teacher) = " << sizeof(Teacher) <<endl;
    }

**结果：**

    sizeof(Teacher) = 76
    
我们可以清楚地看到，引用变量占据了4个字节的内存空间


#### **6.综上所述**

鉴于以下两点：

(1)引用在声明的时候必定要初始化，很像 const 常量
(2)引用变量也占据内存空间， 很像一个指针

于是我们其实就可以把引用看成是 一个常量指针  ： 
    
        Type * const p;

在使用的时候，引用会让人误以为是个别名没有占用空间，这其实是C++ 为了实用性的考虑故意设计的隐藏


### **4.函数的返回值是引用：**

当函数返回值为引用的时候，如果返回的是栈变量，可能会出现一些问题，记得不要作为其他引用的初始值（根据我在 C 编程进阶中讲的，栈区变量会在调用结束的时候被销毁，引用本身也是一个指针，只不过隐藏的比较深 ）

但是如果返回值是个全局变量或者静态变量，那么这个时候作为一个引用是没有问题的

**注意：**

返回值是引用的时候，返回的是变量本身，因此可以做左值

**示例代码：**

    
    #include "iostream"
    using namespace std;
    
    int & getA1(){
        static int a = 10;
        return a;
    }
    
    
    int main() {
    
        getA1() = 100;
        cout << "a 的值是:" << getA1() <<endl;
    
    
    }


**结果：**

    a 的值是:100


> **注意：**
> 
> 我这个变量是 static 如果是普通的变量实际上是会报错的

### **5.指针的引用**

引用一个指针就是相当于一个二级指针，就能直接操纵指针的指向

**示例代码:**

    #include "iostream"
    
    using namespace std;
    
    struct Teacher{
    
        char name[64];
        int age;
    
    };
    
    int getTeacher2(Teacher* & myp){
        //myp 是一个指针的引用，相当于是一个二级指针
        myp = (Teacher*)malloc(sizeof(Teacher));
        if(myp == NULL){
            return -1;
        }
    
        myp->age = 24;
    }
    
    void FreeTeacher(Teacher* pT1){
        if(pT1 == NULL){
            return;
        }
            free(pT1);
        }

    
    int main() {
        Teacher *pT1 = NULL;
        getTeacher2(pT1);
    
        cout << "Teacher 的年龄是：" << pT1->age <<endl;
    }


**结果：**

    Teacher 的年龄是：24


### **6.常量的引用**

#### **1.什么叫常量引用**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    int main() {
       int a = 10;
       const int & x = a; 
       
    }

#### **2.常引用有什么用**

常引用可以让变量有只读属性

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    int main() {
       int a = 10;
       const int & x = a;
       x = 20;
    
    }

**结果：**
    
    error: assignment of read-only reference 'x'
        x = 20;
            ^
  
#### **3.常引用的初始化有两种情况**

**1.用变量初始化常量引用**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    int main() {
       int a = 10;
       const int & x = a;
    
    }


**2.用字面量初始化常量引用**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    int main() {
       const int & x = 10;
    
    }


按理讲常量是没有内存空间分配的，但是在这种情况下C++ 编译器会新创建一个内存空间，然后将这个常量放进去，再去引用


## **六、C++ 对 C 的函数的扩展**

### **1.inline 内联函数**

内联函数在C++ 中的作用就是代替 宏代码段的

#### **1.内联函数是什么样子的**

内联函数和普通函数的样子是一样的，就是在前面加一个 inline 

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    inline void test(){//这里是内联函数
        int a = 10;
        cout << "a 的值是：" << a <<endl;
    }
    
    int main() {
       test();
    }

**结果：**

    a 的值是：10


> **注意：**
> 
> (1)inline int myfunc(int a, int b); 必须和函数的定义放在一起，不能提前声明 (2)C++
> 编译器会将内联函数进行内联编译（会直接将函数体插入函数调用的地方） (3)内联函数没有普通函数调用的 压栈 出栈 跳转 等额外开销
> (4)内联函数是对编译器的一种请求，编译器可能会拒绝这种请求，
> (5)C++编译器自带优化，有些函数可能不是内联函数，但是比编译器还是会将其当成内联函数编译
> (6)内联函数的限制，内联函数中不能存在任何的循环语句，不能存在过多的条件判断语句

### **2.函数参数相关扩展**

#### **1.默认参数**

**1.概念：**

C++ 可以在函数声明的时候给函数的参数指定一个默认值，当这个参数没有传值的时候就会用这个默认值

**2.函数默认参数的规则**

(1)只有参数列表后面部分的参数才能设置默认值
(2)一旦在一个函数参数中使用默认值，那这个参数后面的参数都必须使用默认值


#### **2.函数的占位参数**

**1.概念：**

占位参数只有参数的类型声明，没有参数名的声明，一般情况下，函数内部不能使用占位参数，但是调用的时候参数的个数要写够


#### **3.默认参数和占位参数结合**

**示例代码：**


    #include "iostream"
    
    using namespace std;
    
    void test(int a,int b, int = 0){
    
        cout << "a = " << a << "b = " << b <<endl;
    
    }
    
    
    int main() {
       test(1,2);
    }
    

**结果：**

    a = 1b = 2

> **注意：**
> 
> (1)这样做是为以后的程序扩展留下线索 (2)兼容C中出现的


### **3.函数的重载**

#### **1.概念：**

用相同的函数名定义不同的函数（相同函数名和不同的参数搭配的时候含义是不同的）

#### **2.实现条件(至少满足下面一个)**

(1)函数参数个数不同
(2)函数参数类型不同
(3)函数参数顺序不同

#### **3.注意：**

当函数重载遇到默认参数的时候会出现二义性，这个是不允许的

#### **4.函数重载遇上函数指针**

我们知道，重载的函数特点是函数名是一样的，我们在使用函数指针的时候会只是将名字赋值给指针，那么我们还是在调用的时候根据函数的参数去区分究竟是哪个函数的

## **七、C++ 的面向对象**

### **1.类的概念：**

类的概念之前讲的太多了，这里就我的理解简单的说一下，类实际上就是 属性(变量) 和 方法(函数) 的一个整合体，代表的是一类事物（或者说是对一类事物的一种抽象）。一个类可以实例化多个对象。

### **2.类的封装**

我对封装的理解就是将 方法和属性封装起来，给可信的人提供可信的接口调用内部的方法和属性（做到访问控制）

### **3.类的访问控制修饰符**
(1)public : 可以在类的内部和外部访问
(2)private ：只能在类的内部访问，不能在外部访问
(3)protected: 能在自己和子类中访问


### **4.struct 和 class 关键字的区别**

用struct 关键字定义的类的属性默认是 public 的,class 默认是 private 

### **5.类的声明和实现分开**

**示例代码：**

**MyTeacher.h**

    
    #ifndef LEIDEFENGZHUANG_MYTEACHER_H
    #define LEIDEFENGZHUANG_MYTEACHER_H
    
    
    class MyTeacher {
    
    private:
        char m_name[64];
        int m_age;
    public:
        void setAge(int age);
        int  getAge();
    };
    
    
    #endif //LEIDEFENGZHUANG_MYTEACHER_H

**MyTeacher.cpp**
    
    #include "MyTeacher.h"
    
    void MyTeacher::setAge(int age) {
        this->m_age = age;
    }
    
    int MyTeacher::getAge() {
        return this->m_age;
    }

 **mian.cpp**
     
    #include "iostream"
    #include "MyTeacher.h"
    using namespace std;
    
    int main() {
    
        MyTeacher t1;
        t1.setAge(24);
        cout << "t1 老师的年龄是：" << t1.getAge() <<endl;
    
    }

**结果：**

    t1 老师的年龄是：24


### **6.对象的构造和析构**

#### **1.构造函数和析构函数的概念**

**1.构造函数：**

(1)C++ 中的类可以定义与类名相同的成员函数，这就是构造函数
(2)构造函数在定义的时候可能有参数
(3)构造函数没有任何类型的返回声明

**2.析构函数：**

析构函数是用来清理对象的 ，定义方法是  ~ClassName()

> **注意：** 
>  (1)析构函数没有参数也没有返回类型的声明 
>  (2)析构函数在对象被销毁的时候自动的调用


**示例代码：**


    #include "iostream"
    
    using namespace std;
    class Test{
    
    public:
    
        Test(){
    
            cout << "我是构造函数" <<endl;
        }
    
        ~Test(){
    
            cout << "我是析构函数" <<endl;
        }
    
    };
    
    void objplay(){
    
        Test t1;
    
    }
    
    
    int main() {
        objplay();
        return 0;
    }

**结果：**

    我是构造函数
    我是析构函数


#### **2.有参数和无参数的构造函数**

**1.调用有参数的构造函数**

一共有三种方法

**1.括号法：**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    
    class Test {
    public:
        int m_a;
        int m_b;
    
    public:
    
        Test() {
    
            cout << "我是无参数构造函数" << endl;
        }
    
        Test(int a, int b) {
            m_a = a;
            m_b = b;
            cout << "我是有参数构造函数" << endl;
        }
    
    };
    
    int main() {
        Test t1(1,2);
        return 0;
    }

**结果：**

    我是有参数构造函数


**2.等于号法**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    
    class Test {
    public:
        int m_a;
        int m_b;
    
    public:
    
        Test() {
    
            cout << "我是无参数构造函数" << endl;
        }
    
        Test(int a, int b) {
            m_a = a;
            m_b = b;
            cout << "我是有参数构造函数" << endl;
        }
    
        Test(int a ) {
            m_a = a;
            cout << "我是有一个参数的构造函数" << endl;
        }
    
    };
    
    int main() {
        Test t1 = (1,2);
        return 0;
    }

**结果：**

    我是有一个参数的构造函数

> **注意：**
> 
> 1.这里的 = 已经不是我们认识的等于号，c++ U对等于号的功能进行了加强
> 2.这里的括号是括号表达式，表达式的结果是最后一个逗号后面的内容、

**3.手动调用**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    
    class Test {
    public:
        int m_a;
        int m_b;
    
    public:
    
        Test() {
    
            cout << "我是无参数构造函数" << endl;
        }
    
        Test(int a, int b) {
            m_a = a;
            m_b = b;
            cout << "我是有两个参数构造函数" << endl;
        }
    
        Test(int a ) {
            m_a = a;
            cout << "我是有一个参数的构造函数" << endl;
        }
    
    };
    
    int main() {
        Test t1 = Test(1,2);
        return 0;
    }


**结果：**

    我是有两个参数构造函数


#### **3.copy （赋值）构造函数**

赋值构造函数，就是用一个对象去初始化另一个对象

**1. copy 构造函数的第一种调用情况**

在用一个对象**初始化**另一个对象的时候（注意是初始化不是赋值）

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    
    class Test {
    public:
        int m_a;
        int m_b;
    
    public:
    
        Test(int a, int b) {
            m_a = a;
            m_b = b;
            cout << "我是有两个参数构造函数" << endl;
        }
    
        Test(const Test & obj){
            m_b = obj.m_b;
            m_a = obj.m_a;
    
            cout << "我是赋值构造函数" << endl;
        }
    
    };
    
    int main() {
        Test t0(2,3);
        Test t1(1,2);
        Test t2 = t1; // 这句话就是使用 t1 去初始化对象 t2
        return 0;
    }


**结果：**

    我是有两个参数构造函数
    我是有两个参数构造函数
    我是赋值构造函数


虽然说是赋值构造函数，但是我们还是能进行一些个性化的操作

**比如像这样：**
    
       Test(const Test & obj){
            m_b = obj.m_b + 100;
            m_a = obj.m_a + 100;
    
            cout << "我是赋值构造函数" << endl;
        }

上面那个是第一种方法，下面我们介绍第二种方法

**2. copy 构造函数的第二种调用情况**

和第一种是一种请款，但是属于两种不同的表示方法

**示例代码：**

    int main() {
        Test t0(2,3);
        Test t1(1,2);
        Test t2(t1); // 这句话就是使用 t1 去初始化对象 t2
        return 0;
    }

**3. copy 构造函数的第三种调用情况**

实参对象去初始化形参对象会调用赋值构造函数

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class Location{
    private:
        int X;
        int Y;
    
    public:
        Location(int xx,int yy){
            X = xx;
            Y = yy;
            cout << "构造对象" <<endl;
        }
    
        Location(const Location & p){
            X = p.X;
            Y = p.Y;
            cout << "赋值构造语句" << endl;
        }
    
        ~Location(){
            cout << "析构语句" <<endl;
        }
    
        int GETX(){
            return X;
        };
    
        int GETY(){
            return Y;
        }
    
    };
    
    void f(Location p){
        cout << p.GETX() << endl;
    }
    
    int main(){
    
        Location a(1,2);
        f(a);
        return 0;
    }

**结果：**

    构造对象
    赋值构造语句
    1
    析构语句
    析构语句


**4. copy 构造函数的第四种调用情况**

这种方式和匿名对象有关

    Location g(){
        
        Location A(1,2);
        return A;
    }
    

这个函数返回的是一个对象A ，然后这个对象A 转化成一个匿名对象，这时候就会调用赋值（copy ）构造函数，而这个匿名对象究竟是否析构，关键看我们怎么接这个对象。

> **补充：有关匿名对象的去和留**
> 
> (1)如果用匿名对象初始话另一个同类型的对象，就相当于将匿名对象扶正，匿名对象不会析构
> (2)如果用匿名对象给另一个同类型的对象赋值，那么相当于生成一个新的对象，匿名对象会被析构#### 

**4.构造函数的调用规则**

(1)没有定义构造函数的时候会调用m的构造函数
(2)没有定义赋值构造函数的时候会调用默认的赋值构造函数
(3)当类中定义了任意的非拷贝构造函数的时候，C++编译器便不提供默认构造函数（也就是你写了就必须给我用）


#### **4.对象的初始化列表**

**1.原因：**

如果我们有一个类的类成员，他本身就是一个类对象，并且这个类只有一个带参数的构造函数，没有默认的构造函数，那我们初始化外面这个类的对象的时候就需要调用里面这个类的带参数的构造函数，没有就会报错

**示例代码：**
    
    #include "iostream"
    
    using namespace std;
    
    class A{
    private:
        int a;
    
    public:
        A(int _a) {
            a = _a;
        }
    };
    
    class B{
    
    private:
        int b1;
        int b2;
        A a1;
        A a2;
    
    public:
    
    
    };
    
    
    int main() {
        B objb;
        return 0;
    }


于是 C++ 提供了初始化列表对成员变量进行初始化，语法规则如下：

    Constructor::Constructor():m1(v1),m2(v1,v2),m3(v3)

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class A{
    private:
        int a;
    
    public:
        A(int _a) {
            a = _a;
        }
    };
    
    class B{
    
    private:
        int b1;
        int b2;
        A a1;
        A a2;
    
    public:
        B():a1(1),a2(2){// 注意这一行
            
        }
    };

    int main() {
        B objb;
        return 0;
    }
    
    

> **补充:**
> 
> 1.调用顺序，先执行里面的类的构造函数，然后再执行自己的构造函数，析构的顺序和构造相反
> 2.如果类里面const 属性的话，必须要在列表后加上这个的初始化
> 3.初始化列表不止在有参数和无参数后面可以跟，在赋值构造函数后面也可以跟


### **7.深拷贝和浅拷贝**

浅拷贝就是将指针变量指向的内存地址的值进行了拷贝，但是并没有对内存空间进行拷贝(也就是说，没有重新的开辟一块新的内存空间)

C++ 中，对象的初始化默认是浅拷贝，这就会出现一个非常严重的问题，当我们拷贝的某一个对象销毁的时候就会调用析构函数，析构函数就会将内存空间（这块内存空间是多个对象共用的）进行析构(销毁)，那么原始对象销毁的时候就销毁的是一个空的地址的值，这样就会出现 double free 的问题

解决方法就是手动写copy 函数，重新分配内存空间

> **注意：**
> 
> 等号操作默认也是浅拷贝，解决方法就是显示的重载等号操作符（这个我们在后面会讲到）


### **8.对象的动态建立以及释放**

#### **1.new 和 delete 的基本语法**

**1.概念：**

new 和 delete 是 C++ 的语法，很像 malloc 和 free (或者可以说是malloc 和 free 的简化，因为他们是操作符而不是函数)，C++ 中建议不要使用 malloc 和 free 而使用 new 和 delect ，  new 可以创建基础类型变量 、分配数组变量、分配类对象等

new int; //就是开辟一个存放整数的存储空间，并且返回一个指向该空间的指针
new int(110); //就是开辟一个存放整数的存储空间，并初始化为110，并且返回一个指向该空间的指针
new char[10]; //开辟一个存放字符数组的空间，并返回首地址
float *p = new float(3.1415926);

> **注意：**
> 
> 1.new 和 malloc 一样，是动态分配堆内存
> 2.new 动态分配内存的时候如果分配的空间不足，就会分配失败返回一个NULL 
> 3.用 new 分配数组空间的时候不能分配初始值


**2.分配基础变量**

**示例代码：**
    
    
    #include "iostream"
    
    using namespace std;
    
    int main() {
        //分配基础变量
        int* p = new int(30);
        cout << "*p 的值为： " << *p << endl;
        delete p;
        return 0;
    }
    
**结果：**

    *p 的值为： 30
    
    
**2.分配数组变量**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    int main() {
    
        //分配数组
        int * pArray = new int[10];
        pArray[1] = 1;
        delete [] pArray;    // 注意这里的删除方式 要加一个 []
        
        return 0;
    }
        

 **3.分配对象**     
 
 **示例代码:**
 
     #include "iostream"
    
    using namespace std;
    
    
    class Test{
    
    private:
        int m_a;
    public:
        Test(int a){
            m_a = a;
            cout << "构造函数" << endl;
        }
    
        ~Test(){
            cout << "析构函数" << endl;
        }
    };
    
    
    int main() {
    
    
        //分配对象
        Test *pT = new Test(10);
        delete pT;
    
        return 0;
    }
        
**结果：**
    
    构造函数
    析构函数
    

#### **2.和 malloc() 、free() 的区别**

malloc 和 free 在分配对象内存的时候都不能自动调用构造和析构方法，但是 new 和 delete 可以，这说明 malloc 和 free 更加底层一点

**注意：**

new 的空间可以用 free 释放 同理，malloc的空间可以用 delete 释放


## **八、静态成员变量和成员函数**

### **1.静态成员变量**

把一个类的成员生命成 static 时，这个类无论有多少个对象被创建，这些对象都共享这个 static  成员，准确的说静态成员是属于类的

**示例代码：**
    
    #include "iostream"
    
    using namespace std;
    
    class B{
    
    private:
        int a;
        int b;
        static int c;
    
    public:
    
    };
    
    
    int B :: c = 10;//注意这里，静态属性一定要在类的外面单独声明
    
    int main() {
    
        B b1,b2,b3;
        return 0;
    }
    

任何对象对静态属性的修改都将作用于全局

**示例代码：**


    #include "iostream"
    
    using namespace std;
    
    class B{
    
    private:
        int a;
        int b;
        static int c;
    
    public:
        void getC(){
            cout << "c :" << c <<endl;
        }
    
        void addC(){
            c = c+1;
        }
    };
    
    
    int B :: c = 10;
    
    int main() {
    
        B b1,b2,b3;
        b1.getC();
        b2.addC();
        b3.getC();
        return 0;
    }
    

**结果：**

    c :10
    c :11


### **2.静态成员方法**

静态成员函数提供不依赖于类数据结构的共同操作，没有this指针，外部调用的时候可以通过 类::类函数 的方式或者通过对象调用

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class B{
    
    private:
        int a;
        int b;
        static int c;
    
    public:
        void getC(){
            cout << "c :" << c <<endl;
        }
    
        void addC(){
            c = c+1;
        }
    
        static void testC(){
    
            cout << "我是静态成员" << endl;
    
        }
    };
    
    
    int B :: c = 10;
    
    int main() {
    
        B b1,b2,b3;
        b1.getC();
        b2.addC();
        b3.getC();
    
        b3.testC();
        B::testC();
    
        return 0;
    }

**结果：**
    
    c :10
    c :11
    我是静态成员
    我是静态成员


**注意:**

静态成员变量中不能调用非静态属性或者函数，因为非静态的特定对象的，用静态的去调用就没法分清是属于哪个对象的，于是就会报错



## **九、C++ 面向对象模型初探**

### **1.问题抛出**

当我们用一个具体的对象调用类中的某一个方法的时候 C++ 编译器是怎么知道是那一个类调用的哪一个方法呢？


### **2.揭开面纱**

C++ 中的成员变量和成员函数是分开存储的

**1.成员变量：**

(1)普通成员变量：存储于对象中，与struct 对象有着相同的内存布局和字节对齐方式
(2)static 成员变量：存储在全局数据区中

**2.成员函数**

存储在代码段中

那么究竟是怎么区分不同对象对相同代码段的调用的呢？

答案是： this 指针，这个指针在方法中是隐藏的，但是不代表没有，用对象调用的时候就会把这个this 指针指向调用函数的变量

### **3.this 指针**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class B{
    
    private:
        int a;
        int b;
        
    public:
        
        B(int a ,int b){
            this->a = a;
            this->b = b;
        }
        
        
    };
    
    int main() {
        B test(1,2);
        return 0;


### **4.修饰成员函数的const 修饰的是谁**

修饰成员函数的const 可以放在这个函数后面，他修饰的都是 this 指针指向的内存空间，也就是属性不能修改

**示例代码：**

下面的代码会报错


    #include "iostream"
    
    using namespace std;
    
    class B{
    
    private:
        int a;
        int b;
    
    public:
    
        B(int a ,int b){
            this->a = a;
            this->b = b;
        }
    
        void Test(int a ,int b) const{
            this->a = 100;
            this->b = 300;
    
        }
    
    
    };
    
    int main() {
        B test(1,2);
        test.Test(1,2);
        return 0;
    }
    

## **十、友元**


### **1.友元函数：**

#### **1.概念：**

友元函数是类中的一个全局函数，他是类的好朋友，通过他能修改类的私有属性(破坏了类的封装)，就和 JAVA 中的反射破坏了类的封装一样

**示例代码：**

下面的 test 函数在类的外面，是不能操纵类的私有属性的，于是会报错

    #include "iostream"
    
    using namespace std;
    
    
    class A{
    
    private:
        int a;
        int b;
    
    public:
        A(int a , int b){
    
            this->a = a;
            this->b = b;
        }
    
    
    };
    
    
    void test(A* pT){
    
        cout << pT->a <<endl;
    }
    
    int main() {
    
        A t(1,2);
        return 0;
    }

但是，如果我们在类里面将这函数声明为友元（类的好朋友），那么久不会报错了

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    
    class A{
    
    private:
        int a;
        int b;
    
    public:
        friend void test(A* pT);
        A(int a , int b){
    
            this->a = a;
            this->b = b;
        }
    
    
    };
    
    
    void test(A* pT){
    
        cout << pT->a <<endl;
    }
    
    int main() {
    
        A t(1,2);
        return 0;
    }


### **2.友元类**

#### **1.概念：**

如果类 B 是类 A 的好朋友，那么如果在类B 中有 A 对象，那么可以直接对七进行修改

> **注意：**
> 
> (1)如果 B 类是A 类的友元类，那么B中的所有成员函数都是A 的友元函数 
> (2)友元类通常设计为一种对数据操作或者类之间传递消息的辅助类

**示例代码：**


    #include "iostream"
    
    using namespace std;
    
    
    class A{
    
    private:
        int a;
        int b;
    
    public:
        friend class B; //注意这里将 B 定义为 A 的好朋友
        A(int a , int b){
    
            this->a = a;
            this->b = b;
        }
    };
    
    
    class B{
        
    private:
        A TestA;
    public:
        
        void setA(int a ){
            
            TestA.a = a;
        }
    };
    
    
    int main() {
    
        A t(1,2);
        return 0;
    }

## **十一、运算符重载**

### **1.为什么要运算符重载**

对于基础的数据类型，编译器已经知道如何进行运算了，但是对于复杂的数据类型，编译器却不知道应该怎么办，于是我们就需要运算符的重载，这是C++ 编译器给我们提供的对自定义数据类型进行运算的方式

### **2.如何实现：**

**我们可以自定义一个函数实现这样的功能**：

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    
    class Complex{
    private:
        int a;
        int b;
        friend Complex myAdd(Complex & c1, Complex & c2);
    public:
        
        Complex(int a=0,int b =0){
            this->a = a;
            this->b = b;
        }
    
        void printCom(){
    
            cout << this->a << " + " << this->b << "i" <<endl;
    
        }
    };
    
    
    Complex myAdd(Complex & c1, Complex & c2){
    
        Complex tmp(c1.a+c2.a,c1.b+c2.b);
        return tmp;
    
    }
    
    int main() {
        Complex c1(1,2),c2(3,4);
        Complex c4 = myAdd(c1,c2);
        c4.printCom();
    
        return 0;
    }
    

**结果：**

    4 + 6i


**然后我们使用 运算符重载技术**

**示例代码：**

    #include "iostream"

    using namespace std;
    
    class Complex{
    private:
        int a;
        int b;
        friend Complex operator+(Complex & c1, Complex & c2);
    public:
    
        Complex(int a=0,int b =0){
            this->a = a;
            this->b = b;
        }
    
        void printCom(){
    
            cout << this->a << " + " << this->b << "i" <<endl;
    
        }
      
    };
    
    
    Complex operator+(Complex & c1, Complex & c2){
    
        Complex tmp(c1.a+c2.a,c1.b+c2.b);
        return tmp;
    
    }
    
    int main() {
        Complex c1(1,2),c2(3,4);
        Complex c4 = c1 + c2;
        c4.printCom();
    
        return 0;
    }

**结果：**

    4  + 6i


注意：

(1)并不是所有的运算符都能进行运算符重载，比如 . :: .* ?: sizeof 不能进行重载
(2)运算符重载后基本语义不变

    ① 不改变运算符的优先级
    ② 不改变运算符的结合性
    ③ 不改变运算符所需要的操作数
    ④ 不能创建新的运算符
    


### **3.定义运算符重载函数名的步骤**

(1)承认操作符重载是一个函数，写出函数名称
(2)根据操作数，写出函数参数
(3)根据业务完善函数的返回值，实现函数的业务


### **4.二运算符重载的两种方法**

#### **1.友元函数完成二元运算符的重载**

    Complex operator+(Complex  & c1, Complex & c2);

调用的时候就要这样调用

    Complex c3 =c1 + c2;


#### **2.成员函数完成二元运算符的重载**
   
   Complex operator+(Complex & c2);
  
 调用的时候要这样引用：
 
     Complex c3 = c1.operator+(Complex & c2);
    
    
### **5.一运算符重载的两种方法**

#### **1.友元函数完成一元运算符的重载**

前置++ 

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class Complex{
    private:
        int a;
        int b;
        friend Complex operator+(Complex & c1, Complex & c2);
        friend Complex & operator++(Complex & c1);
    public:
    
        Complex(int a=0,int b =0){
            this->a = a;
            this->b = b;
        }
    
        void printCom(){
    
            cout << this->a << " + " << this->b << "i" <<endl;
        }
    };
    
    Complex & operator++(Complex & c1){
    
        c1.a++;
        c1.b++;
        return c1;
    
    }
    
    int main() {
        Complex c1(1,2),c2(3,4);
        ++c1;
        c1.printCom();
        return 0;
    }

**结果：**

    2 + 3i
    

#### **2.成员函数完成一元运算符的重载**

前置--

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class Complex{
    private:
        int a;
        int b;
        friend Complex operator+(Complex & c1, Complex & c2);
        friend Complex & operator++(Complex & c1);
    public:
    
        Complex(int a=0,int b =0){
            this->a = a;
            this->b = b;
        }
    
        void printCom(){
    
            cout << this->a << " + " << this->b << "i" <<endl;
    
        }
    
    
        Complex & operator--(){
    
            this->a --;
            this->b --;
            return *this;
    
        }
    };
    
    
    
    int main() {
        Complex c1(1,2),c2(3,4);
        --c1;
        c1.printCom();
    
        return 0;
    }


**结果：**

    1 + 2i
    

### **6.怎么实现一元运算符后置++ --重载**

我们知道，后置++ -- 是要先运行 在 ++ 或者 --  ，于是我们返回的就不是一个引用而是一个对象，那么是加上和这个和前置 ++ -- 就只有返回值不同，所以直接写是会报错的，于是乎，C++ 又给我们提供了一个新的东西叫做 占位符语法

实际上就是在 参数的最后写上一个 int 


#### **1.友元函数完成一元运算符的重载**


**示例代码：**
    
    #include "iostream"
    
    using namespace std;
    
    
    class Complex{
    private:
        int a;
        int b;
    
        friend Complex operator++(Complex & c1,int);
    public:
    
        Complex(int a=0,int b =0){
            this->a = a;
            this->b = b;
        }
    
        void printCom(){
    
            cout << this->a << " + " << this->b << "i" <<endl;
    
        }

    
    };
    
    Complex operator++(Complex & c1,int){
    
        Complex tmp = c1;
        c1.a++;
        c1.b++;
        return tmp;
    
    }
    
    
    int main() {
        Complex c1(1,2),c2(3,4);
    
        c1++;
        c1.printCom();
    
        return 0;
    }
    
    
**结果：**

    2 + 3i


#### **2.成员函数完成一元运算符的重载**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    
    class Complex{
    private:
        int a;
        int b;
        friend Complex operator+(Complex & c1, Complex & c2);
        friend Complex & operator++(Complex & c1);
        friend Complex operator++(Complex & c1,int);
    public:
    
        Complex(int a=0,int b =0){
            this->a = a;
            this->b = b;
        }
    
        void printCom(){
    
            cout << this->a << " + " << this->b << "i" <<endl;
    
        }
    
    
    
        Complex operator--(int){
            Complex tmp = *this;
            this->a--;
            this->b--;
            return tmp;
        }
    
    };
    
    int main() {
        Complex c1(1,2),c2(3,4);
        c1--;
        c1.printCom();
    
        return 0;
    }

**结果：**

    1 + 2i


### **7.重载 = 操作符**

我们之前说过，= 操作符是默认使用浅拷贝的，我们现在需要重载他，实现我们想要的深拷贝

**示例代码：**

    #include <cstring>
    #include "iostream"
    
    using namespace std;
    
    
    class Name{
    private:
        int m_len;
        char* m_p;
    public:
        Name(const Name & obj1){
    
            m_len = obj1.m_len;
            m_p = (char*) malloc(m_len+1);
            strcpy(m_p,obj1.m_p);
    
        }
    
        ~Name(){
    
            if(m_p != NULL){
                free(m_p);
                m_p = NULL;
                m_len = 0;
            }
        }
    
       Name& operator=(Name & obj){
    
            //先吧旧的内存释放
            if(this->m_p != NULL){
                delete[] m_p;
                m_len = 0;
            }
    
            //根据obj 分配内存大小
    
            this->m_len = obj.m_len;
            this->m_p = new char [this->m_len+1];
    
            //给 obj 赋值
            strcpy(m_p,obj.m_p);
            return *this;
        }
    
    };
    
    void objplaymain(){
    
        Name obj1((const Name &) "abcdefg");
        Name obj3((const Name &) "obj3");
        obj3 = obj1;
    
    }
    
    int main() {
        objplaymain();
        return 0;
    }


## **十二、继承**

### **1.继承的基本语法：**

**示例代码：**


    #include "iostream"
    
    using namespace std;
    class Parent{
    
    private:
        int a;
        int b;
    
    public:
    
        void print(){
            cout << "a : " << this->a <<endl;
        }
    };
    
    class Child : protected Parent{
    
    private:
        int c;
    };
    
    int main() {
        Child C1 ;
        return 0;
    }

### **2.继承的重要说明：**

(1)子类拥有父类的所有成员变量和成员函数
(2)子类就是一种特殊的父类
(3)子类对象可以当做父类对象使用（多态的概念，当形参是父类的时候，可以传子类进去）
(4)子类可以拥有父类没有的方法和属性

### **3.类的访问控制：**

类的继承方式会影响父类里面的属性在子类中的访问控制

#### **(1)以公有的形式去继承父类的属性**

所有的访问控制符在子类中保持不变，public 还是public protected 还是 protected private 还是 private 但是要注意，**如果父类是 private 属性，不管子类怎么样的方式继承，都是不能访问父类的成员的**

#### **(2)以私有的形式去继承父类的属性**

不管父类的访问控制是什么样子的，在子类中都会变成 private 

#### **(3)以保护的形式去继承父类的属性**

除了 public 方式在子类中变成了 protected 以外，其余保持不变


### **4.继承中的构造和析构：**

#### **1.类型兼容性原则**

类型兼容性原则是指在需要基类对象的任何地方，都可以使用公有派生类的对象来代替，通过公有继承，派生类得到基类中除了构造函数和析构函数之外的所有成员，因公有派生类能解决所有基类能解决的问题。

> **类型兼容规则中指的替代：**
> 
> (1)子类对象能当做父类对象使用 
> (2)子类对象能直接赋值给父类对象 
> (3)子类对象能直接初始化父类对象 
> (4)父类指针能直接指向子类对象
> (5)父类引用能直接引用子类对象


#### **2.继承中构造和析构的调用原则**

**1.首先要知道两个重要的概念：**

(1)在子类对象构造的时候需要调用父类的构造函数对其继承过来的成员进行初始化
(2)在子类对象进行析构的时候需要调用父类的析构函数对其继承雇来的对象进行清理

**2.构造和析构的调用原则**

(1)子类对象在创建时首先要调用父类对象的构造方法
(2)父类的构造函数执行以后会调用子类的构造函数
(3)当父类的构造函数有参数的时候，需要在子类的初始化列表中显示的调用
(4)析构函数的调用顺序和构造函数的调用顺序相反

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class Parent{
    
    private:
        int a;
        int b;
    
    public:
    
        Parent(int a ,int b){
            this->a = a;
            this->b = b;
            cout << "父类构造函数" << endl;
        }
    
        void printP(){
    
            cout << "我是爹" <<endl;
    
        }
    
        ~Parent(){
    
            cout << "父类析构函数" << endl;
    
        }
    
    };
    
    
    class Child : public Parent{
    
    private:
        int c;
    
    public:
    
        Child(int a,int b,int c):Parent(a,b){
           this->c = c;
           cout << "子类构造函数" << endl;
    
        }
    
        void printC(){
    
            cout << "我是儿子" << endl;
    
        };
    
        ~Child(){
    
            cout << "子类析构函数" << endl;
    
        }
    };
    
    void objplay(){
    
        Child c1(1,2,3);
    }
    
    int main() {
        objplay();
        return 0;
    }

**结果：**
    
    父类构造函数
    子类构造函数
    子类析构函数
    父类析构函数



#### **3.继承中同名成员变量的处理方法**

1.当子类成员变量和父类成员变量同名的时候，子类依然从父类中继承同名成员，并且在子类中默认调用的是子类的成员变量，如果想访问父类中的同名变量。请使用域分辨符::
2.同名成员存储在内存中的不同位置

#### **4.派生类中的 static 关键字**

(1)基类定义的静态成员，将被所有的派生类共享
(2)派生类中访问静态成员，用一下的形式显示的说明

    类名 :: 成员

或者
    
    对象名.成员
    

## **十三、多继承**
    
### **1.最基本的多继承**


**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class Base1{
    
    private:
        int a;
    public:
        Base1(int a){
    
            this->a = a;
        }
    
        void print1(){
    
            cout << "a = " << this->a <<endl;
    
        }
    
    };
    
    class Base2{
    
    private:
        int b;
    
    public:
        Base2(int b){
    
            this->b = b;
    
        }
    
        void print2(){
    
            cout << "b = " << this->b <<endl;
    
        }
    
    
    };
    
    class B : public Base1,public Base2{
    
    private:
        int c;
    
    public:
        B(int b1,int b2,int c):Base1(b1),Base2(b2){
            this->c = c;
        }
    
        void print3(){
    
            cout << "c = " << this->c <<endl;
    
        }
    
    };
    
    
    
    int main() {
        B b1(1,2,3);
        b1.print1();
        b1.print2();
        b1.print3();
    
        return 0;
    }
    
    
### **2.二义性和虚继承的解决方案**

### **1.虚继承的概念**

如果一个派生类从多个基类派生，而这些基类有一个共同的基类，那么对这个基类中的名字进行访问的时候就会出现二义性，因为最底层的这个类不知道访问的是上面哪个类中的属性（属性都是从最基类中继承的，名字是一样的）

于是就产生了虚继承的概念，就是在中间的两个类继承最基类的时候加上 :virtual 关键字，这样在中间两个类继承最底层的那个类的时候就不会去继承两次而是一次，这样就不会出现那样的问题了



**示例代码：**

    #include "iostream"
    
    using namespace std;
    class Baseend{
    
    public:
    
        int d;
        void printBB(){
    
            cout << "d = " << this->d <<endl;
    
        }
    };
    
    class Base1:virtual public Baseend{
    private:
    
        int a;
    
    public:
        Base1(int a){
    
            this->a = a;
        }
    
        void print1(){
    
            cout << "a = " << this->a <<endl;
    
        }
    };
    
    class Base2:virtual public Baseend{
    
    private:
        int b;
    
    public:
        Base2(int b){
    
            this->b = b;
    
        }
    
        void print2(){
    
            cout << "b = " << this->b <<endl;
    
        }
    
    
    };
    
    class B : public Base1,public Base2{
    
    private:
        int c;
    
    public:
        B(int b1,int b2,int c):Base1(1),Base2(2){
            this->c = c;
        }
    
        void print3(){
    
            cout << "c = " << this->c <<endl;
    
        }
    
    };
    
    int main() {
        B b1(1,2,3);
        b1.d = 100;
        b1.printBB();
        return 0;
    }
    
    
**结果：**

    d = 100


## **十四、多态**

### **1.引言：**

**封装、继承、多态 是面向对象的三大境界，为什么这么说？**

(1)封装打破了函数的框架，将变量和函数结合在一起，可以使用对象的属性和对象的方法
(2)继承实现了代码的复用，后来人能使用前人写的代码
(3)多态也是代码的复用，只不过多态是更高的层次，实现了复用后人写的代码

### **2.问题发现：**

父类中被重写的函数还是会继承给子类，默认情况下，子类中重写的函数将隐藏父类中的函数，然后我们通过作用于分辨符:: 访问到父类中的函数，

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class A{
    
    private:
        int a;
    
    public:
        A(int a){
            this->a = a;
        }
    
        void print(){
    
            cout  << "父类" << endl;
    
        }
    };
    
    class B:public A{
    
    private:
        int b;
    
    public:
    
        B(int b):A(1){
    
            this->b = b;
    
        }
    
        void print() {
    
            cout << "子类" << endl;
        }
    
    };
    
    
    
    int main(){
    
        B bb(100);
        bb.print();
    
    }
    
    
**结果：**

    子类

    
**但是我们如果用指针**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class A{
    
    private:
        int a;
    
    public:
        A(int a){
            this->a = a;
        }
    
        void print(){
    
            cout  << "父类" << endl;
    
        }
    };
    
    class B:public A{
    
    private:
        int b;
    
    public:
    
        B(int b):A(1){
    
            this->b = b;
    
        }
    
        void print() {
    
            cout << "子类" << endl;
        }
    
    };
    
    
    
    int main(){
    
        A * pT = NULL;
        A p1(20);
        B p2(100);
        pT = &p1;
        pT->print();
        pT = &p2;
        pT->print();
    
    }


**结果：**
    
    父类
    父类


**解释：**

你会发现指针没法识别子类的函数，始终调用的是父类的函数，这其实也是编译器的一种安全的做法，因为在静态编译的时候编译器无法知道应该调用哪个函数，为了安全于是选择了调用父类的函数，毕竟这个函数肯定没问题

### **3.新的需求：**

我传入一个父类对象，调用的就是父类的函数，我传入的是子类对象，得到的就是子类的函数


### **4.面向对象中的多态：**

根据具体的对象类型决定最终的函数调用目标，也就是同样的调用语句有着不用的表现形态，C++ 为我们提供了多态

我们只要在父类对应的函数前面写上 virtual，父类写了 virtual 子类就可以不用写（默认已经写好了）

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class A{
    
    private:
        int a;
    
    public:
        A(int a){
            this->a = a;
        }
    
        virtual void print(){//注意这里的 virtual
    
            cout  << "父类" << endl;
    
        }
    };
    
    class B:public A{
    
    private:
        int b;
    
    public:
    
        B(int b):A(1){
    
            this->b = b;
    
        }
    
        void print() {
    
            cout << "子类" << endl;
        }
    
    };
    
    
    
    int main(){
    
        A * pT = NULL;
        A p1(20);
        B p2(100);
        pT = &p1;
        pT->print();
        pT = &p2;
        pT->print();
    
    }
    
    
**结果：**

    父类
    子类
    


有了这种操作以后，我们就能在函数的形参中定义父类指针，传入一个子类地址，然后就能调用子类的方法，正所谓应用未来，这是一种固定的接口，传什么调什么，就是这么厉害

### **5.实现多态的三个条件：** 

(1) 要有继承
(2) 要有虚函数重写
(3) 要有一个固定的平台（父类指针或者父类引用指向子对象）

### **6.多态的理论基础：**

#### **静态联编和动态联编**

1.联编：

是一个程序块、代码块 之间相互关联的过程

2.静态联编：

指的是程序的匹配链接在编译阶段实现（也成为早期匹配），重载函数使用的是就是静态联编、

3.动态联编：

指的是程序的联编推迟到运行时进行，又称为晚期联编，switch 和 if 语句就是动态联编的例子

结合我们多态的例子

如果我们不写 virtual 关键字将会是静态联编



### **7.虚析构函数：**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class A{
        
    public:
        
        A(){
    
            cout << "A 构造" << endl;
        }
    
        ~A(){
    
            cout << "A 析构" << endl;
    
        };
    };
    
    
    class B:public A{
    
    public:
    
        B(){
    
            cout << "B 构造" << endl;
        }
    
        ~B(){
    
            cout << "B 析构" << endl;
        };
    };
    
    class C:public B{
    
    public:
    
        C(){
    
            cout << "C 构造" << endl;
        }
    
        ~C(){
    
            cout << "C 析构" << endl;
    
        };
    };
    
    void objdele(A* p){
    
        delete(p);
    
    }
    
    int main() {
    
        C *myp = new C;
        objdele(myp);
        return 0;
    }


**结果：**

    A 构造
    B 构造
    C 构造
    A 析构


你会发现我们传进去的是子类的指针，但是调用的却是父类的析构函数，这就造成了内存泄漏。

**为什么？**

原因很简单，下面的这个函数并不满足多态成立的三个条件，也就不能实现多态，因此就是静态联编，根据形参去释放，这里形参是父类，自然也就是父类

    void objdele(A* p){
    
        delete(p);
    
    }
    
**解决：**

我们只要在根类中的析构函数前加一个 virtual 就成了动态联编，就能实现多态了

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class A{
        
    public:
        
        A(){
    
            cout << "A 构造" << endl;
        }
    
        virtual ~A(){
    
            cout << "A 析构" << endl;
    
        };
    
    };
    
    
    class B:public A{
    
    public:
    
        B(){
    
            cout << "B 构造" << endl;
        }
    
        ~B(){
    
            cout << "B 析构" << endl;
    
        };
    };
    
    class C:public B{
    
    public:
    
        C(){
    
            cout << "C 构造" << endl;
        }
    
        ~C(){
    
            cout << "C 析构" << endl;
    
        };
    };
    
    void objdele(A* p){
    
        delete(p);
    }
    
    int main() {
    
        C *myp = new C;
        objdele(myp);
        return 0;
    }



**结果：**

    A 构造
    B 构造
    C 构造
    C 析构
    B 析构
    A 析构


### **8.重载与重写和重定义：**


#### **1.重载**

(1)重载必须在同一个类中进行
(2)重载是在编译器编译期间根据参数的类型和个数决定函数的调用
(3)子类无法重载父类的函数，名称一样父类的函数将被覆盖，想要调用父类的函数就要显示的使用域控制符
#### **2.重写**

(1)重写必须发生在父类和子类之间
(2)父类与子类中的函数必须有完全相同的原型
(3)使用virtual 声明以后会产生多态，如果不使用 virtual 那叫**重定义**



## **十五、纯虚函数和抽象类**

### **1.概念**

(1)纯虚函数是一个在基类中声明的函数，但是咋基类中没有定义，并要求任何的派生类都定义自己的版本
(2)纯虚函数的说明形式

    virtual 类型 函数名(参数) = 0;

(3)一个具有纯虚函数的基类成为抽象类

### **2.注意：**

(1)抽象类不能建立对象
(2)抽象类不能作为返回类型
(3)抽象类不能作为参数类型
(4)可以声明抽象类的指针
(5)可以声明抽象类的引用


### **3.一个结合多态的小例子**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    class Figure{
    
    public:
        virtual void getArea() = 0;
    };
    
    class Circle : public Figure{
    
    private:
        int a;
        int b;
    public:
    
        Circle(int a ,int b){
            this->a = a;
            this->b = b;
    
        }
        virtual void getArea(){
    
            cout << "圆的面积是 : " << 3.14*this->a*this->a <<endl;
        }
    
    };
    
    class Tri : public Figure{
    
    private:
        int a;
        int b;
    public:
        Tri(int a ,int b){
            this->a = a;
            this->b = b;
    
        }
        virtual void getArea(){
    
            cout << "三角的面积是 : " << 0.5*this->a*this->b <<endl;
        }
    
    };
    
    
    void objplay(Figure* p){
    
        p->getArea();
    
    }
    
    int main() {
    
        Circle c(10,20);
        objplay(&c);
        Tri t(20,30);
        objplay(&t);
        return 0;
    }


**结果：**

    圆的面积是 : 314
    三角的面积是 : 300

### **4.抽象类模拟java 的接口**

**示例代码：**

    #include "iostream"
    
    using namespace std;
    
    
    class Interface1{
    
    public:
        virtual int add(int a ,int b) = 0;
        virtual void print() = 0;
    
    };
    
    class Interface2{
    
    public:
        virtual int mutl(int a ,int b) = 0;
        virtual void print() = 0;
    
    };
    
    
    class Parent{
    
    private:
        int a;
    
    public:
        int getA(){
    
            this->a = 0;
            return a;
    
        }
    };
    
    class Child:public Parent,public Interface1,public Interface2{
    
    private:
        int a;
        int b;
    public:
        Child(){
    
            this->a = a;
            this->b = b;
    
        }
        virtual int add(int a ,int b){
    
            cout <<  a+b <<endl;
    
        }
        virtual void print(){
    
            cout << "Child print()函数执行" <<endl;
    
        }
        virtual int mutl(int a ,int b){
    
            cout <<  a*b <<endl;
    
         }
    
    };
    
    int main() {
        Child c1;
        Interface1 *t1 = &c1;
        t1->add(1,2);
        Interface2 *t2 = &c1;
        t2->mutl(3,4);
        return 0;
    }


**结果：**
    
    3
    12


  [1]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/namespace%20%E5%B8%B8%E8%AF%86.png
  [2]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/const%20%E7%A4%BA%E6%84%8F%E5%9B%BE.png