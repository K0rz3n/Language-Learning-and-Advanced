---
title: C语言编程进阶教程（详细）
date: 2018-9-23 15:13:18
tags:  编程 进阶 
categories: 编程
---

## **0X00 前言**

全文已经备份在我的github上，地址：https://github.com/K0rz3n/C-Advanced-programming/blob/master/C-Advanced.md

C 语言作为所有语言的基础地位是可想而知的，但是可能对于我 平时写的比较少，当初学的也不是很深入，但是很多看似平常的编程中其实涉及到很多理论性的东西，需要我们站在编译器的角度看问题，我越到后面发现这一块知识的空缺的危害，于是趁着还有时间，赶紧回炉重造一下，简单的记录一下，作为备忘。

## **0X01 数组做函数参数的退化问题**

<!-- more -->

### **初始程序：简单的排序**

    #include "stdlib.h"
    #include "stdio.h"
    #include "string.h"
    
    void printArray(int a[8]){
        int i =0;
        for (i = 0; i < 8; i++) {
            printf("%d ",a[i]);
        }
    }
    
    
    void sortArray(int a[8]){
        int i,j,tmp;
        for(i=0;i<8;i++){
            for(j=i+1;j<8;j++){
                if(a[i]>a[j]){
                    tmp = a[i];
                    a[i] = a[j];
                    a[j] = tmp;
                }
            }
        }
    }
    
    //排序
    void main() {
        int i,j,tmp;
        int a[] = {11, 2, 3, 4, 5, 6, 67, 788};
        //排序前
        printf("排序前:");
        printArray(a);
    
        //选择法进行排序
        //按住一个变量不变让另一个变量进行变化
        sortArray(a);
    
        //排序后
        printf("排序后:");
        printArray(a);
    
    }

### **抛出问题：**

但是这个程序实际上是不完美的，因为我们在程序中已经将数组的大小固定死了，实际上我们应该做的是传参数中的时候将数组的指针和数组的长度都传给对应的函数（**这就是我们下面要说的数组做函数参数的退化问题**）。


### **数组做函数参数的退化问题**

我们传递数组的时候传递的实际上是一个指向数组首地址的指针以及数组的长度，因此在这个程序中我们还需要设置一个数组长度的参数

于是乎，代码变成了下面这个样子：

    #include "stdlib.h"
    #include "stdio.h"
    #include "string.h"
    
    
    
    void printArray(int a[8],int num){
        int i =0;
        for (i = 0; i < num; i++) {
            printf("%d ",a[i]);
        }
    }
    
    
    void sortArray(int a[8],int num){
        int i,j,tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(a[i]>a[j]){
                    tmp = a[i];
                    a[i] = a[j];
                    a[j] = tmp;
                }
            }
        }
    }
    
    //排序
    void main() {
        int i,j,tmp,num;
        int a[] = {11, 2, 3, 4, 5, 6, 67, 788};
        num = sizeof(a)/sizeof(a[0]);
        //排序前
        printf("排序前:");
        printArray(a,num);
    
        //选择法进行排序
        //按住一个变量不变让另一个变量进行变化
        sortArray(a,num);
    
        //排序后
        printf("排序后:");
        printArray(a,num);
    }

### **进一步发问**

**我们形参中的 a[8] 中的“8” 真的有意义吗？**
不如我们把它去掉
    
    #include "stdlib.h"
    #include "stdio.h"
    #include "string.h"
    
    
    
    void printArray(int a[],int num){
        int i =0;
        for (i = 0; i < num; i++) {
            printf("%d ",a[i]);
        }
    }
    
    
    void sortArray(int a[],int num){
        int i,j,tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(a[i]>a[j]){
                    tmp = a[i];
                    a[i] = a[j];
                    a[j] = tmp;
                }
            }
        }
    }
    
    //排序
    void main() {
        int i,j,tmp,num;
        int a[] = {11, 2, 3, 4, 5, 6, 67, 788};
        num = sizeof(a)/sizeof(a[0]);
        //排序前
        printf("排序前:");
        printArray(a,num);
    
        //选择法进行排序
        //按住一个变量不变让另一个变量进行变化
        sortArray(a,num);
    
        //排序后
        printf("排序后:");
        printArray(a,num);
    
    }
    
### **进一步印证：**

**我们直接将其换成指针**

    #include "stdlib.h"
    #include "stdio.h"
    #include "string.h"
    
    
    
    void printArray(int *a,int num){
        int i =0;
        int num2 = 0;
        num2 = sizeof(a)/sizeof(a[0]);
        printf("传进来的参数的长度为：%d \n",num2);
        for (i = 0; i < num; i++) {
            printf("%d ",a[i]);
        }
        printf("\n");
    }
    
    
    void sortArray(int *a,int num){
        int i,j,tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(a[i]>a[j]){
                    tmp = a[i];
                    a[i] = a[j];
                    a[j] = tmp;
                }
            }
        }
    }
    
    //排序
    void main() {
        int i,j,tmp,num;
        int a[] = {11, 2, 3, 4, 5, 6, 67, 788};
        num = sizeof(a)/sizeof(a[0]);
        printf("实际的数组的长度为：%d\n",num);
        //排序前
        printf("排序前:");
        printArray(a,num);
    
        //选择法进行排序
        //按住一个变量不变让另一个变量进行变化
        sortArray(a,num);
    
        //排序后
        printf("排序后");
        printArray(a,num);
    
    }

**运行结果：**
    
    实际的数组的长度为：8
    排序前:传进来的参数的长度为：1
    11 2 3 4 5 6 67 788
    排序后:传进来的参数的长度为：1
    2 3 4 5 6 11 67 788


### **结论：**

以上的例子充分说明了，形参和实参并不是同一个类型，实参是一个数组，而形参是一个指针（两个a 的 数据类型不同），可以这样说，就算是你把形参中的*a 换成了a[8]，你在检查他的长度的时候他也依然是长度为1（也就是说，他长度为1并不是因为我把a[8]写成了*a 的缘故）

这实际上是C编译器帮我们优化的，因为如果形参也是一个数组，那么就意味着要将实参中的数组原封不动的拷贝到形参中，这无形中降低了C语言运行的效率（我们知道C语言就是以效率高著称），而直接在主调用函数和被调用函数中间通过指针去操纵内存效率时非常高的

**卖个关子：**

形参写在函数上和写在函数内是一样的，只不过写在函数上有着对外的属性而已。


> **对外的属性什么意思？**
> 
> 对外的属性就是指可以通过实参传递给形参的方式来初始化这个参数。


## **0X02 数据类型的本质的剖析**

### **1.为什么要引入数据类型的概念?**

为了方便的表示显示生活中的人事物

### **2.数据类型的本质**

（1）是固定大小内存的别名（告诉C编译器需要让他分配几个字节的空间）

### **3.一些数据类型的小问题**

**(1)如何计算数据类型的大小**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int a;
        int b[10];
    
        printf("sizeof(a):%d,sizeof(b):%d",sizeof(a),sizeof(b));
    }
    
**结果：**

    sizeof(a):4,sizeof(b):40

**（2）如何给数据类型取别名**

    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        char name[64];
        int age;
    }Teachers;
    
    void main(){
        Teachers xiaoming;
        xiaoming.age = 16;
    
        printf("%d",xiaoming.age);
    }

**（3）如何理解 void 类型以及 void指针**  

推荐一篇文章给大家，我认为讲的比较透彻

链接：https://pan.baidu.com/s/1JpekOYDeFyvcCKFmVYS9hw 密码：v11x



### **4. 数组数据类型**
### **举一个例子**
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int a;
        int b[10];
    
        printf("b:%d,b+1:%d,&b:%d,&b+1:%d",b,b+1,&b,&b+1);
    }

### **结果：**

    b:6422280,b+1:6422284,&b:6422280,&b+1:6422320

### **抛出问题：**

我们可以看到 b 和 &b 的结果是一样的，但是为什么 b+1 和 &b+1 的结果却不一样呢（b+1 加了4 但是 &b+1 加了 40）？

这说明 b 和 &b 所代表的数据类型不一样，**b 代表的是数组首元素的地址，而 &b 代表的是整个数组的地址**


### **数组数据类型**

这里涉及到三个知识点

**（1）数组类型
（2）数组指针
（2）数组类型和数组指针类型的关系**

在0X07节数组类型和多维数组的本质中我们会详细的剖析

### **5.函数数据类型**

在 0X13 节 函数指针中我会有比较详细的分析

## **0X03 变量本质的分析**

### **1.概念：**

既能读又能写的内存对象，如果是一旦声明就不能改了那就是常量

### **2.本质：**

**（1）变量是（一段连续）内存空间（地址）的别名**

我们可以将其理解为一个门牌号，比如一个人的住址是： 芯芯小区从东门进去的第3栋楼的25层左边的那个门，人们每次这么传递信息觉得非常的麻烦，于是给每栋楼每个门都编了号，后来我们就说住址是：芯芯小区3栋2501 （那么计算机中的物理地址就对应着这里的**“芯芯小区从东门进去的第3栋楼的25层左边的那个门”**，而变量对应的就是**“芯芯小区3栋2501”**）

**（2）那么变量的类型代表着什么？**

变量的类型决定着编译器将预留多大的内存空间，**变量一定要有类型**，没有类型编译器就会报错，认为这是无法操控的（具体可见上面我网盘链接分享的那篇文章）

**（3）简单解释一下& 和`*`**

有了上面两段的基础，我就来简单的解释一下&和*符号的含义：

①&符号作用是通过门牌号（变量名）得到原始的家庭住址（变量指向内存的地址）
②*符号作用是通过原始的家庭住址（变量指向内存的地址）得到家里面有什么人（变量的值）



**注意：**

**我们是通过变量往内存读写数据，而不是向变量读写数据**

### **3.通过变量修改内存有几种方法？**

**（1）直接**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    void man(void) {
        int a;
        a =10;
        printf("%d",&a);
    }i

**结果：**

    6422316

**（2）间接**
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    void main(void) {
        int a;
        a =10;
    
        *((int*)6422316) = 200;
        printf("%d",a);
    }

**结果：**

    200
    
### **4.变量的生命周期**

这一部分我们会在下面的内存四区模型的讲解中给读者讲述。

### **5.C编译器是如何管理两个函数变量之间的关系的**

这一部分我们会在下面的内存四区模型以及函数指针的讲解中给读者讲述。

## **0X04 程序的内存四区模型**

![图一][1]

![图二][2]


### **1.静态区模型**

**实例：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    char * getString1(){
        char *p1 = "abcdefgh1";
        return p1;
    }
    
    char * getString2(){
        char * p2 = "abcdefgh2";
        return p2;
    }
    
    void main(void) {
    
      char * p1 = NULL;
      char * p2 = NULL;
    
      p1 = getString1();
      p2 = getString2();
    
        //打印 p1 p2 地址的字符串
        printf("p1:%s\np2:%s\n",p1,p2);
    
        //打印p1 p2 的地址
        printf("p1:%d\nP2:%d\n",p1,p2);
    }


**结果：**
    
    p1:abcdefgh1
    p2:abcdefgh2
    p1:4214884
    P2:4214894

我们稍微改动一下，将第一个字符串也替换成和第二个字符串一样，那么，结果中后两个p1 、p2 的值会发生变化吗？（代码就不贴了，读者自己修改一下实验）

**结果：**

    p1:abcdefgh2
    p2:abcdefgh2
    p1:4214884
    P2:4214884
    
我们惊奇的发现下面两个p1、p2 居然地址变成了一个地址，下面我们来自己画一下这个程序的内存四区图来分析这个问题，在画图之前我么首先要记住下面几句无比经典的话：

> **1. 指针指向谁就将谁的地址赋值给指针**
> **2. 指针变量和其所指向的内存空间变量是两个不同的概念**
> **3. 理解指针的关键在于理解内存，没有内存哪来的指针**

首先是main 在栈区存放局部变量p1 p2，然后进入子函数，在静态区存储了字符串常量abcdefgh2，接着就是在堆区存放了一个局部变量p1,并将p1指向了该字符串的地址。

![此处输入图片的描述][3]

然后回到主函数,子函数中的p1 将这个地址给了主函数中的 p1，于是主函数中的p1 指向了该字符串的地址

![此处输入图片的描述][4]

然后子函数中的 p1 被自动的析构，现在就只有主函数中的p1指向了该字符串的地址了

![此处输入图片的描述][5]

当进入了第二个子函数以后，关键的点来了，编译器发现这次的字符串和上次的字符串是一样的，于是自动进行了优化，并没有再开辟一块空间存储相同的数据。（后面的步骤和前面的就一样了不再赘述）

![此处输入图片的描述][6]

![此处输入图片的描述][7]

![此处输入图片的描述][8]

### **2.堆区和栈区的理解** 

**1.堆区示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    char * getMem(int num){
        char * p1 = NULL;
        p1 = (char*)malloc(sizeof(char)*num);
        if(p1 == NULL){
            return NULL;
        }
        return p1;
    }
    
    int main() {
        char * tmp;
        tmp = getMem(10);
        if(tmp == NULL){
            return NULL;
        }
        strcpy(tmp,"111222");
        printf("%s",tmp);
    
    }


**下面的图解释了内存分配的过程**

![此处输入图片的描述][9]

![此处输入图片的描述][10]

![此处输入图片的描述][11]


**2.栈区示例代码：**


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    char * getMem2(){
        char buf[64];//临时变量存放在栈区
        strcpy(buf,"abcdefgh");
        return buf;
    }
    
    
    
    int main() {
        char * tmp;
        tmp = getMem2();
        printf("%s",tmp);
    
    }

**这里的返回值是一个不确定的值，如果编译器有保护的话返回的是一个 null ，为什么呢？**

因为，我们将数据保存在了栈区这个临时区域，在子函数返回的时候这一块区域是会被编译器自动析构的，因此主函数中的tmp 得到的位置在那个时候已经没有了原始的数据，而是一块未知的内存区域。（偷个小懒，图我就不画了，有兴趣的读者可以自己尝试画一下，也加深一下对这个概念的理解）

**注意：**这里有一点需要强调的就是return buf 并不是将内存中的64个内存块全部返回出来，返回出来的是内存块的首地址。


### **3.栈属性的理解**

栈是从高地址向低地址增长的，我们平时画的只是一个示意图，其实可以开口向上也可以开口向下，完全取决于你自己，但是只要保证一点就是：后入栈的变量地址低就可以了。

我们用代码说话：

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int main() {
        int a = 1;
        int b = 2;
        printf("&a:%d,&b:%d",&a,&b);
    }
    

**结果：**

    &a:6422316,&b:6422312

可以很清楚的看到后入栈的b的地址要比先入栈的a 的地址低，和我们的理解完全符合

**强调一个容易忽略的点：**

我们刚刚只是单纯的变量，如果我们是数组呢？会有不一样吗？我们用代码说话

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int a = 1;
        int b[2] ={1,2};
        printf("&a:%d,&b[0]:%d,&b[1]:%d",&a,&b[0],&b[1]);
    }
    
**结果：**

    &a:6422316,&b[0]:6422308,&b[1]:6422312

我们惊奇的发现 b[1] 的地址要高于 b[0] ，这说明数组的生长方向和栈的生长方向是刚好反过来的，而且由于栈是静态编译的，所以在编译的时候变量所指的内存空间的标号就定下来了


## **0X05 函数调用模型**

下面有一个gif 简单的说明了函数调用过程中栈区经历了哪些变化

![此处输入图片的描述][12]

> 思考几个问题：
> 
> **1. main 函数分配的内存空间在子函数中能用吗？**
> 
> 答案：可以的，因为main 函数在调用子函数的时候函数还没有运行完毕，那么内存空间就不会被析构，子函数当然可以使用
> 
> **2. 子函数中分配的内存能被主函数使用吗？**
> 
> 答案：我们需要分情况讨论，如果是在栈上分配的内存，它是临时的内存空间，当子函数返回到主函数中以后就被析构了，因此不能使用，但是如果是在堆上或者静态存储区分配的内存空间由于不会被自动析构，因此能够被主函数中使用
> 
> **3. 一个主程序由n个子程序组成，那么C编译器会建立几个堆区和几个栈区？**
> 
> 答案：一个，一个应用程序公用一个内存四区


## **0X06 指针铁律的强化**

### **一、指针也是一种数据类型**

**1）指针也是一个变量，也需要占用内存空间，指针保存的是另一个变量的内存地址**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    void* main(void) {
    
        int a =1;
        int * p1 = (int *) 0XFFFFFFFF;
        char****** p2 = (char ******) 0XFFFFFFFF;
        printf("sizeof(a):%d,sizeof(p1):%d,sizeof(p2):%d\n",sizeof(a),sizeof(p1),sizeof(p2));
    }
    
**结果：**

    sizeof(a):4,sizeof(p1):4,sizeof(p2):4
    

> **思考：为什么不同类型的指针的大小是一样的？**
> 
> 答：我们知道指针代表的是
> 某块固定大小的内存的首地址，而地址是有地址编号的，地址编号是由CPU进行编址的，因此32位系统都是4字节，64位系统是8字节（当然其实并不只是和CPU的位数有关，还和操作系统和编译器的位数有关，总之是取它们三个的最小值）

    
**2）*p 是操作内存**

①指针变量在声明的时候*表示我们现在声明的是指针变量
②指针变量在使用的时候*表示操作指针所指向内存空间中的值
③指针变量在初始化的时候表示将等号右边的地址赋值给该指针变量

*p 是通过地址找到内存（p的值），并操纵内存中的值

（1）*p 放在左边是操纵p指向内存中的值
    
    a = 10;
    *p = NULL;
    p = &a;
    *p = 20;//将a的值间接的改成20

***p 就像一把钥匙，通过一个地址&a 去改变a 所标识的内存空间的值**

（2）*p 放在右边是从内存中取值
    
    c = 10;
    c = *p;

**3）指针变量和它指向的内存块是两个不同的概念**

**含义一：**

给 p 赋值， p = 0x1111; ，p+1 或者 p++ 只会改变指针变量的值而不会改变指针变量所指向的内容（也就是说只是让指针指向了另一块内存空间而已）

**含义二：**

给 *p 赋值 *p = 'a'; 不会改变指针变量的值，而是改变了指针指向的内存空间的值

**特别注意：当通过指针变量修改内存空间的值的时一定要保证内存空间能够被修改，如果不能修改就会出问题**

**示例程序：**

    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    char* getString(){
    
        char* tmp = NULL;
        tmp = "abcdefgh";
        return tmp;
    }
    
    void* main(void) {
    
        char* p = getString();
        *(p+2) = 'r';
        printf("String:%s",p);
    }
    

这段代码会运行出错，因为字符串常量是存储在静态区的，静态区的数据时受操作系统保护的

**4）指针是一种数据类型，是指它指向的内存空间的数据类型**

**（1）如何看待下面的语句？**

    int getASD1(char*p1);
    int getASD2(char**p2);
    int getASD2(char***p2);
    int getASD2(char (*p4)[30]);
    int getASD2(char (*p5)[10][30]);

可能很多人看了会有这样的疑问，我们在使用指针作为函数的形参的时候究竟是把 char* 看成一个整体还是把 *p 看成一个整体呢？

其实我们思考问题的角度有问题，我们应该从编译器的角度去看这个问题，在编译器看来只要是带*号的就是指针，指针就是代表着地址的标号，在32位平台下就分配4个字节的空间，而当我们在使用指针指向的内存空间的时候才关心指针指向的内存空间是一维的还是二维的。

**（2）指针的步长是由其所指的内存空间的数据类型决定的**

    char* p = NULL;
    *p ='a';
    p++  =>  (unsiged char)p +sizeof('a')
    

### **补充：野指针**

什么是野指针？
野指针就是指向未知内存区域的指针，在无意中操纵这种指针是非常危险的，会直接造成程序的崩溃，下面举一个野指针的例子：

**示例代码：**


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        char* p = NULL;
        p = (char*)malloc(100);
        if(p == NULL){
            return -1;
        }
        strcpy(p, (const char *) "xxxx");
        printf("%s",p);
    
        //第一次释放
        if(NULL != p){
            free(p);
        }
    
        //重复释放
        if(NULL != p){
            free(p);
        }
        return 0;
    }
    
**内存四区图：**

![此处输入图片的描述][13]


**解释：**

我们要非常清楚一点，指针变量和内存区域是两个完全不同的概念，当时用free()函数销毁p指针指向的内存区域的时候，p指针的值并没有改变，还是原来的0xaa11 ,只不过是这个时候 0xaa11 已经不是我们当时malloc()分配的内存空间了，于是下次再进行判段 p是不是为 NULL的时候 P肯定不是 NULL，于是乎还会在 free一次，但是这一次我们也不知道free的是什么东西了，于是程序 就会崩溃， 这就是野指针的巨大危害。


**修改：**

我们只需要在每次free(）以后将 指针p的值归位就行了。


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        char* p = NULL;
        p = (char*)malloc(100);
        if(p == NULL){
            return -1;
        }
        strcpy(p, (const char *) "xxxx");
        printf("%s",p);
    
        //第一次释放
        if(NULL != p){
            free(p);
            p = NULL;
        }
    
        //重复释放
        if(NULL != p){
            free(p);
            p = NULL;
        }
        return 0;
    }
    
        
**总结：**

为了避免出现野指针的情况我们建议以下几点：
（1）定义指针的时候初始化成NULL
（2）释放指针所指的内存空间的时候将指针重新指向NULL

### **二、间接赋值（*p）是指针存在的最大意义**

这其实也是C语言指针的一个精髓所在，我们能在程序之间传递地址来操纵同一块内存空间

**（1）从0级指针到1级指针的技术推演过程**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int geta(int b){
        b = 100;
        return b;
    }
    
    int getp(int* p){
        *p = 1000;
        return *p;
    }
    
    
    void* main() {
        int a = 1;
        a = geta(a);
        printf("a:%d\n",a);
        a = getp(&a);
        printf("a:%d\n",a);
    
    }

**结果：**

    a:100
    a:1000
    
**简单的解释：**

可能原来我们对此有些疑惑，但是我相信如果你对我之前画的内存四区图真的理解了的话，现在应该是非常清晰的的，b是在栈区新分配的一块内存空间，与main函数中的a 之间没有任何联系，于是无论你怎么修改 b 对a 来讲都是没有影响的，但是如果你传递的是a 的地址的话，通过地址间接地操控 a 变量成为可能。


**（2）从一级指针到二级指针的技术推演过程**

二级指针可以从一级指针类比而来，一级指针是普通变量的指针（地址），他可以通过对该地址取*间接地修改变量的内容，那么二级指针就是一级指针的指针（地址），他可以通过对该地址取*间接地修改一级指针的内容（一级指针指向的地址）

**示例代码1：**

     void* main() {
    
        char* p1 = NULL;
        char** p2 = NULL;
    
        //直接赋值
        p1 = (char*)0X11;
        p2 = (char**)0X22;
        
        //间接赋值
        p2 = &p1;
        *p2 = (char*)0X12;
    }
    
**示例代码2：**
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"

    void* getp(char** p){
        *p = (char*)0X0011;
    }

     void* main() {
    
        char* p1 = NULL;
        char** p2 = NULL;
    
        //直接赋值
        p1 = (char*)0X11;
        p2 = (char**)0X22;
        
        //间接赋值，传进去指针p1的地址
        getp(&p1);

    }
    
**再次强调一下，p1 和 p2 都是变量，一个是代表普通变量的地址编号，另一个代表指针变量的地址编号，一个通过*操纵普通变量的值，另一个通过*操纵指针变量的值**

**（3）总结一下间接赋值成立的三个条件：**

1）定义两个变量，一个实参一个形参
2）将实参取地址扔给形参，建立关联
3）通过形参取*修改实参的值

**（4）间接赋值的应用场景**

**场景一：** 1 、2、3 都写在一个函数内
**场景二：** 1、2 写在一个函数内 3 写在另一个函数内======》函数间传递指针
**场景三：** 1 写在一个函数内，2、3 写在另一个函数内 ======》 C++ 中存在这种情况


**场景一示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int main() {
        char buf1[128] = "12312414nkj1b4jk1b232131";
        char buf2[128] = {0};
        char* p1 = buf1;
        char* p2 = buf2;
    
        while('\0' != *p1){
            *p2 = *p1;
            p1++;
            p2++;
        }
    
        printf("buf2:%s",buf2);
        return 0;
    }

**结果：**

    buf2:12312414nkj1b4jk1b232131


**注：其他的示例代码我就不写了，读者可以自己尝试完成（其实前面类似的都有了）**

**（5）间接赋值推论**

1）用1级指针（形参），间接的修改0级指针（实参）的值
2）用2级指针（形参），间接的修改1级指针（实参）的值
3）用3级指针（形参），间接的修改2级指针（实参）的值

以此类推......

### **三、理解指针必须和内存四区相结合**

**指针的输入和输出特性**

**out ：**

在主函数中分配内存(栈/malloc/静态)，然后把地址甩出去给被调用函数，让他来操控这一块内存

**in：**

在被调用函数中分配内存（malloc/静态），然后主函数接收被调用韩散户甩进来的内存地址，并进行操控


### **四、应用指针必须和函数调用相结合**


图


### **五、一级指针的典型用法**

**（1）数组**

前面的内容我们已经涉及到这一部分，于是这里不再赘述（详情可见**0X01 数组做函数参数的退化问题**）

**（2）字符串**

> **注意：**
> 1. C语言中字符串是以 \0结尾的
> 2. C语言中没有字符串类型，我们可以通过字符数组来模拟字符串
> 3. 字符串的内存分配可以在堆、栈和静态区上（**很重要**）

**①字符串赋值的前两个方法：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int main() {
    
        //1.指定长度
        //如果长度给多了，那么编译器会在多余的空间自动补零
        char buf[128] = {'a','b','c','d','e','f'};
        //如果你的长度不够，就会报错
        //char buftest[2] = {'a','v','c'};
    
        //2.不指定长度
        char buf2[] = {'a','b','c','d','e','f'};
        //但是这种形式并不是标准的C风格的字符串，因为没有以0结尾，只能说是一个6个字节的字符数组
    
    
        return 0;
    }
    
**②字符串赋值的第三个方法：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int main() {
    
        int len = 0;
        int size = 0;
        char buf[] = "abcdef";//这样赋值会把结尾的0赋值进去
    
        len = strlen(buf);
        size = sizeof(buf);
    
        printf("字符串的长度为：%d,数组的数据类型的大小：%d",len,size);
        return 0;
    }
    
**结果:**

    字符串的长度为：6,数组的数据类型的大小：7
    

**③通过数组下标和指针来操纵字符串**

上面已经有过实例了，我这里就不再演示



> **补充两个小问题：**

> **1.[] 和 *p 的关系**
> 
> （1）他们的用法是类似的，只不过[] 更符合程序员的阅读习惯
> 
> buf[i] ==> buf[0+i] ==> *(buf+i)
> 
> （2）但是buf 和 *p 有着非常本质区别
> 
> buf 从他出现的第一次开始就是一个指针常量，他的值是能被改变的，但是 *p
> 却是一个指针变量，值可以随意改变，这个非常的重要，也是很多初学者一直要犯的错误。
> 
> **2.那么为什么设计编译器的人要把他设置成一个指针常量而不是变量呢？**
> 
> 因为buf
> 这个数组是在栈区开辟的空间，当函数运行完毕的时候这一块空间将被析构，如果你改变了sbuf（这一块空间的首地址）那么到时候编译器就不知道该从哪里开始析构内存空间了。


**④字符串一级指针的内存模型的建立**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        char buf[10] = "abcdef";
        char buf2[] = "bbb";
        char* p1 = "11111";
        char* p2 = malloc(100);
        strcpy(p2,"zxczxc");
    
        return 0;
    }

**下面我用内存四区图的形式给大家展示一下（代码区我们暂时不考虑）**

![此处输入图片的描述][14]


**⑤字符串作为函数参数实现copy**

原来我们是这么写字符串的copy的

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
    
        char buf[] = "welcome to XDSEC";
        char buf2[128];
        int i =0;
        for(i=0;*(buf+i) != '\0';i++){
            *(buf2+i) = *(buf+i);
        }
        buf2[i] = '\0';
        printf("buf2:%s",buf2);
    
        return 0;
    }

**注意一下:这里循环中并没有将\0 copy进去，最后一定要将\0单独的添加，否则就是一个bug代码**

但是现在我们要学会能使用自定义的接口，直接传入两个指针作为参数，间接的实现字符串的copy

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    void* str_copy(char *from,char *to){
        for(;'\0' != *from;from++,to++){
            *to = *from;
        }
        *to = '\0';
    }
    
    
    int main() {
    
        char buf[] = "welcome to XDSEC";
        char buf2[128];
        str_copy(buf,buf2);
        printf("buf2:%s",buf2);
    
        return 0;
    }
    
这个代码写的有一些臃肿，我们可以进一步修改一下

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    

    void* str_copy(char *from,char *to){
        while('\0' != *from){
            *to++ = *from++;
        }
        *to = '\0';
    }
    
    
    int main() {
    
        char buf[] = "welcome to XDSEC";
        char buf2[128];
        str_copy(buf,buf2);
        printf("buf2:%s",buf2);
    
        return 0;
    }

这段代码能达到同样的效果，但这里面牵扯出一个问题

> `*` 和 ++ 谁的优先级高：
答案是：++ 的优先级要高于 * 的优先级，但是由于这里是后++，必须先执行操作，再执行++ ,因此， *to++ = *from++; 等同于 *to = *from; from++; to++; 

但是现在遇到一个很棘手的问题，每次我们都必须单独的拷贝\0，这是非常蛋疼的，万一就忘了呢，你说是不是，于是我们必须对代码进行进一步改进。
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
        
    
    void* str_copy(char *from,char *to){
        while((*to++ = *from++) !='\0');
    }
    
    
    int main() {
    
        char buf[] = "welcome to XDSEC";
        char buf2[128];
        str_copy(buf,buf2);
        printf("buf2:%s",buf2);
    
        return 0;
    }
    
这段代码在循环中进行了首先的copy，然后才判拷copy 的是不是 \0n ,是的话就退出循环，然而这个时候已经将\0 copy进去了，这其实也是 strcpy() 函数的源码实现的方案。


**再优化的话我们可以这样：**


    void* str_copy(char *from,char *to){
        while(*to++ = *from++);
    }


虽然功能实现了，但是这段代码并不够健壮，我们还需要解决几点问题：

> **问题一：**
> 
> 如果我们在调用api函数的时候忘记了在主函数中分配内存怎么办？那我们传进去一个空的指针，往空的指针拷贝数据肯定是会出现程序崩溃的，于是我们需要对传入的指针进行非空的检测，如果为空那么直接返回。不进行拷贝操作。

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int  str_copy(char *from,char *to){
        if(from == NULL || to == NULL){
            return -1;
        }
        while((*to++ = *from++));
        return 0;
    }
    
    int main() {
    
        char buf[] = "welcome to XDSEC";
    
        char * buf2 = NULL;//注意，这里现在传入的是一个空指针
    
        int res = 0;
        res = str_copy(buf,buf2);
        if(res != 0){
            printf("str_copy function has some error, please check and try again!");
            return -1;
        }
        printf("buf2:%s",buf2);
        return 0;
    }
    
 **结果：**

    str_copy function has some error, please check and try again!

> **问题二：**
> 
> 我们现在在API函数中如果想打印这个字符串实际上是不可行的，因为我们直接操纵了形参的值，形参现在指向的位置已经是字符串的末尾了，于是乎，我们应当在API函数中创建一个临时指针变量去接收形参的值，然后改变的是临时的指针变量，使得形参依然指向字符串的一开始。

**示例代码：**
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int  str_copy(char *from,char *to){
        char* str_from = from;
        char* str_to = to;
        if(str_from == NULL || str_to == NULL){
            return -1;
        }
        while((*str_to++ = *str_from++));
        return 0;
    }
    
    int main() {
    
        char buf[] = "welcome to XDSEC";
        char buf2[128];
        int res = 0;
        res = str_copy(buf,buf2);
        if(res != 0){
            printf("str_copy function has some error, please check and try again!");
            return -1;
        }
        printf("buf2:%s",buf2);
        return 0;
    }
    
### **六、字符串指针的实际应用模型**

因为这一节太重要了，内容也非常的多，我就单独把它拿出来放在和众多铁律相同的地位来给大家演示

**（1）strstr while dowhile 模型**
利用这个模型求一个字符串在另一个字符串中出现的次数

strstr()  函数的作用是返回后一个参数在前一个参数中出现的位置（也就是返回的是一个指针）

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int count = 0;
        char* p = "1234abcdjnvvvsdabcdfmr234u9abcdfjjew";
    
        do{
            p = strstr(p, (const char *) "abcd");
            if(p != NULL){
                count++;
                p = p + strlen((const char *) "abcd");
            }else{
                break;
            }
        }while(*p != '\0');
    
        printf("一共出现了：%d次",count);
        return 0;
    }

但是现在我们需要将该功能抽象成一个API接口，放在main函数外运行。

> **技巧点播：** 
>我们在创建API函数的时候绝对不要说API函数给返回一个值，然后main函数去接收，这样写说明你没有掌握C语言的精髓，C语言的精髓就是通过指针去操纵变量，于是我们应该用指针做函数参数将我们的运算结果甩出来，而不是傻傻的将API函数的运行结果以返回值的形式进行传递。

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int  getCount(char* p,char* sub,int* count){
        char* temp_p = p;
        int res = 0;
        if(p == NULL || sub == NULL || count == NULL){
            res = -1;
            printf("func getCount() err %d",res);
             return res;
        }
        do{
            temp_p = strstr(temp_p, sub);
            if(temp_p != NULL){
                *count = *count+1;
                temp_p = temp_p + strlen(sub);
            }else{
                break;
            }
        }while(*temp_p != '\0');
        return res;
    }
    
    int main() {
        int count = 0;
        int res = 0;
        char* p = "1234abcdjnvvvsdabcdfmr234u9abcdfjjew";
        char* sub = "abcd";
        res = getCount(p,sub,&count);
        if(res != 0){
            printf("func getCount() err:%d \n",res);
            return res;
        }
        printf("一共出现了：%d次",count);
    
        return 0;
    }


**这里面有几点我需要提示一下：**

（1）并不是说所有的指针形参都需要给一个临时的指针变量来接，如果这个参数传递进来并没有要改变 的需求，我们也不用浪费空间去创建临时指针变量。
（2）API函数应该有一个返回值，这个返回值我们一开始初始化为0， 在运行过程中我们进行一些判段，出现问题我们就直接返回（这样返回值就不是0），在主函数中我们接收这个返回值，然后进行判段，从而决定是否 返回0还是直接打印一个异常，并将异常返回。

 **（2）两头堵模型**

利用两头堵模型求字符串中去除两端空白的的字符串的长度
 
    #include "ctype.h"
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        char* p = "    zxczcz      ";
        int i = 0;
        int j = strlen(p)-1;
        int n = 0;
        while(isspace(p[i])){
            i++;
        }
        while(isspace(p[j])){
            j--;
        }
    
        n = j-i+1;
    
        printf("字符串的长度为：%d",n);
        return 0;
    }
    
将该功能封装成一个接口

    #include "ctype.h"
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int getCount(char* p,int* count){
        char* temp_p = p;
        int ncount = 0;
        if(p == NULL|| count == NULL){
            return -1;
        }
        int i = 0;
        int j = strlen(temp_p)-1;
    
        while(isspace(temp_p[i])){
            i++;
        }
        while(isspace(temp_p[j])){
            j--;
        }
    
        ncount = j-i+1;
        *count = ncount;
        return 0;
    }
    
    
    int main() {
        int count = 0;
        int res = 0;
        char* p = "    zxczcz      ";
        res = getCount(p,&count);
        if(res != 0){
            printf("func getCount has something wrong please check and try again");
            return res;
        }
        printf("字符串的长度为：%d",count);
        return 0;
    }

进一步，如果我还要你去除字符串前后的空格呢？

    #include "ctype.h"
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int getCount(char* p,int* count){
        char* temp_p = p;
        int ncount = 0;
        if(p == NULL|| count == NULL){
            return -1;
        }
        int i = 0;
        int j = strlen(temp_p)-1;
    
        while(isspace(temp_p[i])){
            i++;
        }
        while(isspace(temp_p[j])){
            j--;
        }
    
        ncount = j-i+1;
        *count = ncount;
        return 0;
    }
    
    int trimSpace(char* str,char* newstr){
        char* temp_p = str;
        int ncount = 0;
        if(temp_p == NULL|| newstr == NULL){
            return -1;
        }
        int i = 0;
        int j = strlen(temp_p)-1;
    
        while(isspace(temp_p[i])){
            i++;
        }
        while(isspace(temp_p[j])){
            j--;
        }
    
        ncount = j-i+1;
        strncpy(newstr,temp_p+i,ncount);
        newstr[ncount] = '\0';//这一步是非常重要的，请大家务必不要忘记
        return 0;
    
    }
    
    int main() {
        int count = 0;
        int res = 0;
        char* p = "    zxczcz      ";
        char buf[1024] = {0};
        res = getCount(p,&count);
        if(res != 0){
            printf("func getCount has something wrong please check and try again\n");
            return res;
        }
        printf("字符串的长度为：%d\n",count);
        res = trimSpace(p,buf);
        if(res != 0){
            printf("func trimSpace() has something wrong please check and try again\n");
            return res;
        }
        printf("去除两端空格后的字符串为：%s\n",buf);
        return 0;
    }
    
当然了，我们也可以直接往原始字符串所在空间进行拷贝，而不是新建一块内存空间（但是需要保证原始字符串所在的内存空间能够被修改，我们这个程序由于原始的字符串是存储在静态存储区的，因此这一块空间是只读的而不可写，因此这种方法在该案例中不能使用，但是如果原始字符串写在栈区，那么可读可写，就没有任何问题）

**综合三个函数的实现：**
    
    #include "ctype.h"
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int getCount(char* p,int* count){
        char* temp_p = p;
        int ncount = 0;
        if(p == NULL|| count == NULL){
            return -1;
        }
        int i = 0;
        int j = strlen(temp_p)-1;
    
        while(isspace(temp_p[i])){
            i++;
        }
        while(isspace(temp_p[j])){
            j--;
        }
    
        ncount = j-i+1;
        *count = ncount;
        return 0;
    }
    
    int trimSpace(char* str,char* newstr){
        char* temp_p = str;
        int ncount = 0;
        if(temp_p == NULL|| newstr == NULL){
            return -1;
        }
        int i = 0;
        int j = strlen(temp_p)-1;
    
        while(isspace(temp_p[i])){
            i++;
        }
        while(isspace(temp_p[j])){
            j--;
        }
    
        ncount = j-i+1;
        strncpy(newstr,temp_p+i,ncount);
        newstr[ncount] = '\0';
        return 0;
    
    }
    
    
    int trimSpace2(char* str){
        char* temp_p = str;
        int ncount = 0;
        if(temp_p == NULL){
            return -1;
        }
        int i = 0;
        int j = strlen(temp_p)-1;
    
        while(isspace(temp_p[i])){
            i++;
        }
        while(isspace(temp_p[j])){
            j--;
        }
    
        ncount = j-i+1;
        strncpy(str,temp_p+i,ncount);
        str[ncount] = '\0';//在这个函数里如果这句话没有的话，你就会看到意想不到的结果
        return 0;
    
    }
    
    
    
    int main() {
        int count = 0;
        int res = 0;
        char* p = "    zxczcz      ";
        char p1[100] = "      efdsfdsfdfd    ";
        char buf[1024] = {0};
        res = getCount(p,&count);
        if(res != 0){
            printf("func getCount has something wrong please check and try again\n");
            return res;
        }
        printf("字符串的长度为：%d\n",count);
        res = trimSpace(p,buf);
        if(res != 0){
            printf("func trimSpace() has something wrong please check and try again\n");
            return res;
        }
        printf("去除两端空格后的字符串为：%s\n",buf);
    
        res = trimSpace2(p1);
        if(res != 0){
            printf("func trimSpace() has something wrong please check and try again\n");
            return res;
        }
        printf("去除两端空格后的字符串为：%s\n",p1);
        return 0;
    }

> **注意：** 实际上上面的代码还是能进行优化的，因为去除两边空格的API接口没有必要实现计算字符串长度API接口同样的功能，可以直接调用计算字符串长度的API接口

 **（3）字符串逆序模型**

**方法一：使用两个指针内容互换的方式**


**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int main() {
        char p[] = "abcdefgh";
        int len = strlen(p);
        char* p1 = p;
        char* p2 = p1 + len - 1;
        while(p1<p2){
            char c = *p1;
            *p1 = *p2;
            *p2 = c;
            p1++;
            p2--;
        }
    
        printf("反转后的字符串为：%s\n",p);
        return 0;
    }

我们可以将其封装成接口

**示例代码：**


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int re_str(char* str1 ,char* str2){
        char* p1 = str1;
        char* p2 = str2;
        int res = 0;
        if(str1 == NULL || str2 == NULL){
            res = -1;
        }
    
        int len = strlen(p1);
        p2 = p1 + len - 1;
        while(p1<p2){
            char c = *p1;
            *p1 = *p2;
            *p2 = c;
            p1++;
            p2--;
        }
    
        return 0;
    
    }
    
    int main() {
        int res;
        char p[] = "abcdefgh";
        char buf[1024] = {0};
        res = re_str(p,buf);
        if(res != 0){
            printf("func re_str() has something wrong,please check and try again");
            return res;
        }
        printf("反转后的字符串为：%s\n",p);
        return 0;
    }

**方法二：使用递归逆序的方法**

这里利用了一个字符串入栈出栈的操作，当入栈的时候是顺序入栈的话，出栈就是逆序的。

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int re_str(char* tmp){
        if(tmp == NULL){
            return -1;
        }
        if(*tmp == '\0'){
            return 0;
        }
        re_str(tmp+1);
        printf("%c",*tmp);
        return 0;
    }
    
    
    int main() {
        int res = 0;
        char* p = "abcdefegh";
        res = re_str(p);
        if(res != 0){
            printf("func re_str has something wrong , please check and try again");
            return res;
        }
        return 0;
    }

递归操作不得不说是学习过程中的一座大山，那么究竟该如何理解呢？

> **首先需要理解几点：** 
>（1）函数返回时会返回调用他的函数中，并从调用函数的下一句开始执行
> （2）每次递归传递给函数形参相当于再次创建了一个变量，原始的变量没有消失，并将在后面函数的返回后进行打印等
> （在这个例子中就是再次创建了一个新的指针指向了字符串的下一个字母，而原来的指针并没有消失，并将在函数返回时打印上一个字母的过程中发挥关键作用。）

 **递归内存的图示：**

**（1）入栈模型示意图：**

![此处输入图片的描述][15]

**（2）函数调用模型示意图：**

![此处输入图片的描述][16]

**我们现在加以延伸：**

如果我们不只是想单纯的打印这个字符而是要将其存储在一个内存空间中下次继续使用怎么办？

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    char g_buf[1000];
    
    int re_str(char* tmp){
        if(tmp == NULL){
            return -1;
        }
        if(*tmp == '\0'){
            return 0;
        }
        re_str(tmp+1);
    
        strncat(g_buf,tmp,1);//每次追加一个字符到指定内存空间
        return 0;
    
    }
    
    int main() {
        int res = 0;
        char* p = "abcdefegh";
        memset(g_buf,0,sizeof(g_buf));
        res = re_str(p);
        if(res != 0){
            printf("func re_str has something wrong , please check and try again");
            return res;
        }
        printf("%s", (char *) g_buf);
        return 0;
    }

但是由此又出现了一个新的问题：我们现在操控的是一个全局变量，但是如果多线程同时操纵这个全局变量的话，为了变量不被错误篡改，我们就面临着加锁的问题，  那我们能不能不用全局变量实现呢？

当然可以，我们之前就说过了使用指针传递参数并间接操纵是C语言的精髓

**示例代码：**


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    
    int re_str(char* tmp,char* mybuf){
        if(tmp == NULL || mybuf == NULL){
            return -1;
        }
        if(*tmp == '\0'){
            return 0;
        }
        re_str(tmp+1,mybuf);
        strncat(mybuf,tmp,1);
        return 0;
    
    }
    
    
    int main() {
        int res = 0;
        char* p = "abcdefegh";
        char mybuf[1024] = {0};
        res = re_str(p,mybuf);
        if(res != 0){
            printf("func re_str has something wrong , please check and try again");
            return res;
        }
    
        printf("%s", (char *) mybuf);
        return 0;
    }


### **七、一级指针常见错误模型**

**错误一：对传入的参数的存在性判断出错**

错误写法：

    if(*from == '\0' || t*o == '\0'){
        xxx;
    }

正确写法：

    if(from == NULL || to == NULL){
        xxx;
    }

>**错误原因：**

> 没有理解指针变量和指针变量指向内存空间的值不是一个概念这句话。我们需要判段传进来的指针是不是一个空指针，而不是判段这个指针指向的内存空间的值是不是0，实际上这个值可以是一万个0，没有任何关系。


**错误二：数组越界**

错误写法：

    char buf[3] = "abc";

正确写法：

    char buf[4] = "abc";

>**错误原因：**

> 没有理解字符串的末尾自带一个\0占据一个字节


**错误三：malloc内存的释放错误**

错误案例：

1.malloc 成功赋值给一个指针变量，但是当你已经改变了指针指向的地址以后，不还原就直接free() 那么这个时候free() 的是一个 未知的内存，程序直接爆炸。
2.你free() 内存以后并没有将指针赋值为空，这样会有下次free()同一块内存的风险。

解决办法：

1.最好用一个临时指针变量去接这块地址，修改临时的指针变量，到时候free()的时候直接free()原始的字符串指针变量
2.在 free() 指针变量指向额内存以后一定要记得将指针变量赋值为NULL.

> **错误原因：**
> 
> 没有理解指针变量和指针变量指向内存空间的值不是一个概念这句话


**错误四：函数变量的生命周期判断错误**

如果我们在子函数中生命的变量是在栈区，那么这个空间的值在返回到主函数中是会被解析器自动析构的，也就是无法通过指针进行传递的，但是在静态区或堆区就可以通过指针传递。

> **错误原因：**
> 
> 使用者没有理解内存四区的 以及概念以及通过指针传递的实际上是一块内存空间。

**错误五：*p++**

如果想改变p 指向内存空间的值的大小，不能直接写 *p++ ,因为++ 比 * 的优先级来得高，直接这样写会先改变指针的指向，于是我们应该 （*p）++

> **错误原因：**
> 
> 不了解 ++ 和 * 之间的优先级关系


### **八、const 修饰符专题**

**我们一定要分清楚一下的几种情况（非常重要）：**

1.const int a;
2.int const b;
3.const char* a;
4.char* const b;
5.const char* const e;

**解释：**

1.第一种和第二种是完全一样的意思，就是这个变量不能被修改
2.要分清第三和第四的区别

- 第三个的意思是：指针所指向的地址中的值不能被修改 
- 第四个的意思是：指针所指向的地址不能被修改

3.第五个以此类推：指针指向的变量的地址不能被修改，该变量的值也不能被修改（只读）


**还有一个重点：**

我们一开始这样写：

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    int main() {
        const int a = 0;
        a = 10;
        return 0;
    }

结果:

    error: assignment of read-only variable 'a'

编译器告诉你 a 是 read only 的类型，不能修改，但是真的不能修改吗？我们看下面的程序
    
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    int main() {
        const int a = 0;
        int* p = (int *) &a;
        *p = 100;
        printf("a:%d",*p);
        return 0;
    }

结果：

    a:100   

也就是说我们能绕过 const 来修改变量的值


### **九、二级指针输入输出模型概念**

**1.回顾一下我们之前讲的二级指针的概念：**

我们之前是通过一级指针来类比二级指针的，我们说我们是通过一级指针变量间接的修改0级指针变量的值（普通变量的值 ） ，因为我们有时候需要在子函数中去直接修改主函数中变量的值，同样的我们是通过二级指针间接的修改一级指针变量的值（换句话说就是改变一级指针的指向），同样我们也有这样的应用场景：有时候我们会在子函数中用 malloc 动态分配空间，并且将其给主函数使用，那么主函数中的一级指针的指向就需要在子函数中动态的改变，因此我们就使用二级指针 来间接的修改主函数一级指针的指向，于是就使用二级指针实现这样的功能。

>**两个要点：**
> 指针做输入：主函数分配内存 
>指针做输出：子函数分配内存

**2.二级指针的输出模型：**

**示例代码：**


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int getMem(char** myp1,int* mylen1, char** myp2,int* mylen2){
    
        char* tmp1 = NULL;
        char* tmp2 = NULL;
        tmp1 = (char*)malloc(100);
        if(tmp1 == NULL){//这个判断一定是要在分配内存以后再写
            return -1;
        }
        strcpy(tmp1,"asdasdasdasdasd");
        *mylen1 = strlen(tmp1);
        *myp1 = tmp1;//通过*间接地修改一级指针变量的值
    
        tmp2 = (char*)malloc(100);
        if(tmp2 == NULL){//这个判断一定是要在分配内存以后再写
            return -2;
        }
        strcpy(tmp2,"qweqweqweqweqwe");
        *mylen2 = strlen(tmp2);
        *myp2 = tmp2;
        return 0;
    }
    
    // free 函数
    int getMem_free(char** myp){
        if(myp == NULL){//判断这个一级指针是不是还存在
            return -1;
        }
        free(*myp);//free 这个一级指针指向的内存空间
        *myp = NULL;
    }
    
    
    
    
    int main() {
        int res = 0;
        char * p1 = NULL;
        int len1 = 0;
    
        char * p2 = NULL;
        int len2 = 0;
    
        res = getMem(&p1,&len1,&p2,&len2);
        if(res != 0){
            printf("func getMem() has something wrong please check and try again");
            return res;
        }
    
        printf("p1:%s,len1:%d\n",p1,len1);
        printf("p2:%s,len2:%d\n",p2,len2);
    
        getMem_free(&p1);
        getMem_free(&p2);
    
        return 0;
    }

**结果：**
    
    p1:asdasdasdasdasd,len1:15
    p2:qweqweqweqweqwe,len2:15


**3.二级指针的输入模型**

**（1）二级指针做输入的第一种内存模型**

指针数组模式

**示例代码:**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
    
        int num = 0;
        int i = 0;
        int j = 0;
        char* tmp = NULL;
        char* myArray[] = {"eaaaaa","cbbbb","sccccccccc","add"};
        //myArray[0]、myArray[1]都是指针变量，他们有自己的地址，他们的值是字符串的首地址
        num = sizeof(myArray)/sizeof(myArray[0]);
    
        //打印(排序前)
        for(i=0;i<num;i++){
            //printf("%s\n",myArray[i]);
            printf("排序前：\n%s\n",*(myArray+i));
        }
        
        //排序
    
        for(i=0;i<num;i++){
            for(j=i;j<num;j++){
                //因为字符串的地址是静态地址，是不能改变的，我们只能修改指针变量的指向实现排序
                if(strcmp(*(myArray+i),*(myArray+j))> 0){
                    tmp = *(myArray + i);
                    *(myArray+i) = *(myArray+j);
                    *(myArray+j) = tmp;
                }
            }
        }
    
        //打印(排序后)
        for(i=0;i<num;i++){
            //printf("%s\n",myArray[i]);
            printf("排序后:\n%s\n",*(myArray+i));
        }
    
        return 0;
    }
    
**结果：**

    排序前：
    eaaaaa
    cbbbb
    sccccccccc
    add
    排序后:
    add
    cbbbb
    eaaaaa
    sccccccccc

现在我们对不同的功能进行函数封装

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int printArray(char** myArray,int num){
        //使用二级还是一级指针要看传进来的是普通变量的地址还是指针变量的地址
        //你想操纵指针变量的值肯定是需要传递指针变量的地址，这样才能间接使用
        int i = 0;
        for(i=0;i<num;i++){
            //printf("%s\n",myArray[i]);
            printf("%s\n",*(myArray+i));//指针变量的地址加上*代表指针变量的值
        }
        return 0;
    }
    
    
    int sortArray(char** myArray,int num){
        int i =0;
        int j =0;
        char* tmp = NULL;
        for(i=0;i<num;i++){
            for(j=i;j<num;j++){
                if(strcmp(*(myArray+i),*(myArray+j))> 0){
                    tmp = *(myArray + i);
                    *(myArray+i) = *(myArray+j);
                    *(myArray+j) = tmp;
                }
            }
        }
        return 0;
    }
    
    
    int main() {
    
        int num = 0;
        int i = 0;
        int j = 0;
        char* tmp = NULL;
        char* myArray[] = {"eaaaaa","cbbbb","sccccccccc","add"};
        num = sizeof(myArray)/sizeof(myArray[0]);//这里可以重点关注一下是如何求数组长度的
    
        //打印(排序前)
        printf("排序前：\n");
        printArray(myArray,num);
        //排序
        sortArray(myArray,num);
    
        printf("排序后:\n");
        printArray(myArray,num);
    
        return 0;
    }
    
**结果：**

    排序前：
    eaaaaa
    cbbbb
    sccccccccc
    add
    排序后:
    add
    cbbbb
    eaaaaa
    sccccccccc
    

**注意点:**

**1.理解为什么函数传参数的时候使用的是二级指针**

因为C语言没有字符串类型，字符串都使用字符串的首地址表示的，现在我们把多个字符串进行了封装，变成了一个字符串数组，换句话说就是一个指针数组（myArray[0]、myArray[1]都是指针变量，他们有自己的地址，他们的值是字符串的首地址），于是我们首先要用二级指针接收指针变量的地址，然后间接修改的指针变量的值（也就是指针指向的那个的内存空间的地址） 。

**2.malloc() 分配内存的检测和释放问题**

以后一定要检查是否分配成功了，使用完成以后记得要释放指针指向的内存空间，使用free() 函数，这里面放的是内存空间的地址（或者说是一级指针的值），然后我们还要记得将指针的值（指针指向的内存空间）赋值为NULL这个地址，防止下次出现重复free() 。

**3.指针还是数组？**

这里我操纵数组全部使用的是指针的比较，没有使用[]，其实[]完全也是可以的，我只不过是想演示一下指针的操作而已，让大家都有一个认识，不要只会看[]，不过不得不承认[]的可读性要好得多。

**4.我们排序时操纵的究竟是什么**

因为字符串是存储在静态存储区的，地址是不能改变的，因此我们只能改变指针变量的指向的地址（也就是改变指针变量的值）来实现排序。


**（2）二级指针做输入的第二种内存模型**

多维数组模式

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int i = 0,j = 0,num = 4;
        char tmpBuf[30];
        char myBuf[30];
        char myArray[10][30] = {"xxx", "asdada", "111111", "cccccc"};
    
        printf("排序前：\n");
        for(i=0;i<num;i++){
            printf("%s\n",myArray[i]);//myArray[i] 等同于 *(myArray+i)
        }
    
        for(i=0;i<num;i++){
            for(j=i;j<num;j++){
                if(strcmp(myArray[i],myArray[j])>0){//这个是一个常见的错误
                    strcpy((char *) tmpBuf, myArray[i]);
                    strcpy(myArray[i],myArray[j]);
                    strcpy(myArray[j], (char *) tmpBuf);
    
                }
            }
        }
        printf("排序后：\n");
        for(i=0;i<num;i++){
            printf("%s\n",myArray[i]);
        }
        return 0;
    }
    

**结果：**
    
    排序前：
    xxx
    asdada
    111111
    cccccc
    排序后：
    111111
    asdada
    cccccc
    xxx


我们将功能进行简单的封装：

**示例代码：**


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int printArray(char myArray[10][30],int num){
        int i =0;
        for(i=0;i<num;i++){
            printf("%s\n",myArray[i]);//myArray[i] 等同于 *(myArray+i)
        }
        return 0;
    }
    
    int sortArray(char myArray[10][30],int num){
        int i = 0,j = 0;
        char tmpBuf[30];
        for(i=0;i<num;i++){
            for(j=i;j<num;j++){
                if(strcmp(myArray[i],myArray[j])>0){//这里比较容易犯错误
                    strcpy((char *) tmpBuf, myArray[i]);
                    strcpy(myArray[i],myArray[j]);
                    strcpy(myArray[j], (char *) tmpBuf);
    
                }
            }
        }
    
    }
    
    int main() {
        int i = 0,j = 0,num = 4;
        char tmpBuf[30];
        char myBuf[30];
        char myArray[10][30] = {"xxx", "asdada", "111111", "cccccc"};
    
        printf("排序前：\n");
        printArray(myArray,num);
        sortArray(myArray,num);
        printf("排序后：\n");
        printArray(myArray,num);
    
        return 0;
    }
    

**（3）二级指针做输入的第三种内存模型**

全部自己动态的使用malloc()分配

自己 malloc() 一块内存空间用来存放**指针变量**，再让每一个指针变量都指向一个用 malloc分配的内存空间


首先在看代码的前面提一个要求：

**对于二级或者多级指针我们要求理解的深，但是思考的少，什么意思？**

**（1）理解的深：**就是我随便问你一个代码，你都能非常清楚地知道这个是谁的值，谁的地址
**（2）思考的少：**我们在写代码的时候千万不要每次看到一个和指针有关的东西就开始分析，其实没有必要，这样做反而会大大降低我们写代码的速度，而且很有可能自己写着写着就把自己绕进去了。我们应该都能在写代码的时候清楚地知道几个关键的原则

**原则：**

1.我们如果要在子函数操纵的是一级指针的值，那么我们传递参数的时候形参肯定是二级指针，传进去的肯定是一级指针的地址。
2.我们如果要在子函数操纵的是二级指针的值，那么我们传递参数的时候形参肯定是三级指针，传进去的肯定是一级指针的地址。
3.子函数中只能改变的是比形参低一级的指针的值，永远也不要想能改和形参同级的指针的值，因为即使你改了，你改的也是形参的值（形参和实参是在不同内存空间的，即使你把传进来的形参的值置为空还是没有改变实参的值）


**代码示例：**

 下面代码的排序使用的是交换指针的方式

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        char** p = NULL;
        int num = 5;
        int i = 0,j = 0;
        char* tmp = NULL;
    
        //初始化
        p = (char**)malloc(sizeof(char*)*num);//二级指针存放的是一级指针的地址
        for(i=0;i<num;i++){
            p[i] = (char*)malloc(sizeof(char)*100);//一级指针存放的是零级指针的地址
            sprintf(p[i],"%d%d%d",i,i+1,i+2);
        }
    
        //打印
        printf("排序前:\n");
        for(i=0;i<num;i++){
            printf("%s\n",p[i]);
        }
    
        //排序
        for(i=0;i<num;i++){
            for(j=i+i;j<num;j++){
                if(strcmp(p[i],p[j])<0){
                    tmp = p[i];
                    p[i] = p[j];
                    p[j] = tmp;
                }
            }
        }
    
        //打印
        printf("排序后:\n");
        for(i=0;i<num;i++){
            printf("%s\n",p[i]);
        }
    
    
        //释放内存
        for(i=0;i<num;i++){
            if(p[i] != NULL){
                free(p[i]);
                p[i] = NULL;//将一级指针指向的地址清空
            }
        }
    
        if(p != NULL){
            free(p);//释放一级指针占用的内存空间
            p = NULL;//清空二级指针的值
        }
        
        return 0;
    }
    

下面内存空间的值的做法进行排序

**示例代码：**
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        char** p = NULL;
        int num = 5;
        int i = 0,j = 0;
        char* tmp = NULL;
        char tmpbuf[100] = {0};
    
        //初始化
        p = (char**)malloc(sizeof(char*)*num);
        for(i=0;i<num;i++){
            p[i] = (char*)malloc(sizeof(char)*100);
            sprintf(p[i],"%d%d%d",i,i+1,i+2);
        }
    
        //打印
        printf("排序前:\n");
        for(i=0;i<num;i++){
            printf("%s\n",p[i]);
        }
    
        //排序
        for(i=0;i<num;i++){
            for(j=i+i;j<num;j++){
                if(strcmp(p[i],p[j])<0){
                    strcpy(tmpbuf,p[i]);
                    strcpy(p[i],p[j]);
                    strcpy(p[j],tmpbuf);
                }
            }
        }
    
        //打印
        printf("排序后:\n");
        for(i=0;i<num;i++){
            printf("%s\n",p[i]);
        }
    
    
        //释放内存
        for(i=0;i<num;i++){
            if(p[i] != NULL){
                free(p[i]);
                p[i] = NULL;
            }
        }
    
        if(p != NULL){
            free(p);
            p = NULL;
        }
    
        return 0;
    }
    
        
**结果：**

    排序前:
    012
    123
    234
    345
    456
    排序后:
    456
    345
    234
    123
    012

现在我们将各个功能进行封装，封装就意味着需要传递参数，还是坚持那个原则，如果我们要操纵变量的值的时候还是要入指针，但是如果只是为了输出或者其他，而没有改变值的话，可以就直接传入就行了。

**错误示例代码：**

**问题我在注释中已经指明，正确的代码在下一小节中会有**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    char** getMem(int num){
        int i = 0;
        char ** p = NULL;
        p = (char**)malloc(sizeof(char*)*num);
        if(p == NULL){
            return NULL;
        }
        for(i=0;i<num;i++){
            p[i] = (char*)malloc(sizeof(char)*100);
            sprintf(p[i],"%d%d%d",i,i+1,i+2);
        }
        return p;
    }
    
    void* sortArray(char** p,int num){
        int i = 0,j = 0;
        char* tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(strcmp(p[i],p[j])<0){
                    tmp = p[i];
                    p[i] = p[j];
                    p[j] = tmp;
                }
            }
        }
    
    }
    
    void* printArray(char** p,int num){
        int i = 0;
        for(i=0;i<num;i++){
            printf("%s\n",p[i]);
        }
    }
    
    void* memFree(char** p, int num){
        int i =0;
        for(i=1;i<num;i++){
            if(p[i] != NULL){
                free(p[i]);
                p[i] = NULL;
            }
        }
    
        if(p != NULL){
            free(p);
            p = NULL;//问题在在这里，看到了吗，这个函数我们传进来的是二级指针，我们是不能改变这个二级指针的值的，因为这个是临时变量，你改了也没用，还有就是我们一直建议不要直接修改参数的值，防止以后再用的时候出现问题
        }
    
    }
    
    int main() {
        char** p = NULL;
        int num = 5;
    
        //初始化
        p = getMem(num);
    
        //打印
        printf("排序前:\n");
        printArray(p,num);
    
        //排序
        sortArray(p,num);
        
        //打印
        printf("排序后:\n");
        printArray(p,num);
    
        //释放内存
        memFree(p,num);
        return 0;
    }
    

**（4）二级指针做输入的第三种内存模型示意图：**

**再回忆一下二级指针的三种模型：**

（1） 一阶指针数组
（2）二阶字符串数组
（3）malloc 动态分配

**示例代码：**


    void main(){
        int i =0;
        char* p1[] = {"111","222","333"};//一阶指针数组
        char p2[3][17] = {"111","222","333"};//二阶字符串数组
        char** p3 = malloc(sizeof(char*)*3);//手动二阶内存
        for(i=0;i<3;i++){
            p3[i] = (char*)malloc(sizeof(char)*10);
            sprintf(p3[i],"%d%d%d",i+1,i+1,i+1);
        }
    }

**图示：**

![此处输入图片的描述][18]

**注意：**

p1+1 和 p3+1 都是移动一个指针的距离（是由指向的地址的内容的长度决定的），因此他们的步长是一样的。但是 p2+1 移动的距离和数组的长度有关也就是说如果我们声明的时候是 p2[10][30] 那么即使我们字符串只有4个字节，p2+1 还是会移动30个字节。


**4.玩转多级指针**

**回忆一句话：**

要想在被调用函数中间接的修改实参的值，我们要把实参的地址转进去，形参变成实参指针数+1 级指针，然后在被调用函数中使用 *  间接地修改值

**那么我们什么情况下需要用到三级指针呢？**

当我们想在被调用函数中修改主函数中传递进去的二级指针的值的时候，我们就会在被调用函数的参数中使用三级指针

好，现在我们就用上面的例子做修改来给大家演示一下使用三级指针在子函数中直接动态分配内存而不是在主函数中接收返回值的方式。


**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int  getMem(char*** p,int num){
        int i = 0;
        char** tmp = NULL;
        if(p == NULL){
            return -1;
        }
        tmp = (char**)malloc(sizeof(char*)*num);
        if(tmp == NULL){
            return -1;
        }
        for(i=0;i<num;i++){
            tmp[i] = (char*)malloc(sizeof(char)*100);
            sprintf(tmp[i],"%d%d%d",i,i+1,i+2);
        }
        *p = tmp;
        return 0;
    }
    
    
    void* sortArray(char** p,int num){
        int i = 0,j = 0;
        char* tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(strcmp(p[i],p[j])<0){
                    tmp = p[i];
                    p[i] = p[j];
                    p[j] = tmp;
                }
            }
        }
    
    }
    
    void* printArray(char** p,int num){
        int i = 0;
        for(i=0;i<num;i++){
            printf("%s\n",p[i]);
        }
    }
    
    
    int  memFree(char*** p, int num){
        int i =0;
        char** tmp = NULL;//创建临时的指针变量，不直接修改参数的值
        if(p == NULL){
            return -1;
        }
        tmp = *p;
        for(i=0;i<num;i++){
            if(tmp[i] != NULL){
                free(tmp[i]);
                tmp[i] = NULL;
            }
        }
        free(tmp);
        *p = NULL;//通过三级指针将二级指针的值清空
        return 0;
    }
        
    
    int main() {
        char** p = NULL;
        int num = 5;
    
        //初始化
        getMem(&p,num);
        
        //打印
        printf("排序前:\n");
        printArray(p,num);
    
        //排序
        sortArray(p,num);
    
        //打印
        printf("排序后:\n");
        printArray(p,num);
    
        //释放内存
        memFree(&p,num);
        return 0;
    }

这里相对可能难度较大，要求深刻理解内存四区的的概念，理解 p[i] 和 *(p+i) 的等价关系等，我自己也想了一会，也希望大家结合之前的例子仔细的思考一下什么时候应该用二级指针什么时候应该用三级指针，以及他们应该怎么用。

**思考：**

> 我们在写代码的时候有些东西需要注意可以让代码更加简直健壮，就是函数很多都是属于一个入口多个出口的函数，什么意思呢？就是说入口就是函数的调用，出口就是函数的返回（函数的返回在正常和非正常情况是不同的，或者说在不同情况下有不同的返回值），所以很有可能在内存分配到一般的情况下就返回了，那么这个时候，我们分配到一半的的内存就没有机会得到释放，那么时间长了就会出现内存的泄露，于是我们最好在出现问题的时候使用一个goto：END，然后我们在函数的最后使用一个END 标记一个函数块来统一的释放内存。


**5.多级指针避免野指针的策略**

（1）初始化的时候如果不直接赋值就赋值为 NULL
（2）接收指针参数的时候首先要判段是不是NULL，不是NULL才能继续运行
（3）释放内存空间的时候不要忘了在 free掉指针指向的内存空间之后将指针的值重新置为NULL
（4）二级指针分配内存空间的时候记得将二级指针的值初始化为0，再来分配一级指针，因为如果我们在一级指针的分配过程中出现问题，提前返回，那么我们在 goto 到 END (也就是我们能强制让他一个出口的那个释放内存模块)以后要根据二级指针的值是不是0来判段有没有分配要不要释放一级指针的内存，然后再把二级指针的内存全部释放掉。

## **0X07 数组类型和多维数组的本质**

### **一、数组的概念：**
1.元素类型看：数组是**相同类型**变量的有序集合
2.从内存角度：是**连续的**一大片内存空间

### **二、数组的初始化：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int a[] = {1,2};
        int b[100] = {1,2,3};//这些都是在编译器静态编译的时候就确定的
        int c[200] = {0};
        memset(c,0,sizeof(c));//在编译后才显示地重置内存块
    
        return 0;
    }

### **三、数组名的技术盲点**

（1）数组首元素的地址和数组的地址是两个不同的概念
（2）数组名代表着数组首元素的地址，是一个**指针常量**

**简单的解释一下这个问题：**数组定义以后在静态编译的时候就已经分配好内存了，是不会再进行改变的，因此编译器将数组的名字作为一个指针常量

（3）数组首元素的地址和数组的地址是相等的
（4）对数组名取地址规定得到的是整个数组的地址
（5）如何表达数组这种数据类型

### **四、数组数据类型**

数组数据类型是一种非常规的数据类型，我们要换一种思考角度（我们类比指针的数据类型，指针的数据类型是它指向的内存的内容的数据类型）

（1）数组的数据类型是由数组元素的数据类型和数组的长度共同决定的
（2）比如  int array[10] 的数据类型是  int[10]

> **回忆：**
> 
> 类型的本质：固定大小的内存块的别名

**1.使用typedef 给数组设置别名**

先解释一下，数组的特殊之处：

数组这种数据类型一开始就不单纯，别的数据类型都是单纯的，比如 int  char 啥的，但是数组这个数据类型一开始就是带着别名的（标签），那么别名是什么，我们以下面这个最常见的数组为例：

int (array)[10]  ----------->  数据类型实际上就是 int[10] ，这个array 就是 int[10]的别名

有了这个基础我们看下面的例子：

array a ------------>  int a[10]

相当于给 array  又找了一个别名替换了之前 array 的名字

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int i = 0;
        typedef int (myArray)[10];
        myArray array = {0};
    
        for(i=0;i<10;i++){
            array[i] = i+1;
        }
    
        for(i=0;i<10;i++){
            printf("%d\n",array[i]);
        }
        return 0;
    }

**结果：**

    1
    2
    3
    4
    5
    6
    7
    8
    9
    10

我们可以再验证一下之前的判段：array 和 &array 的数据类型是不一样的， 何以见得？请看代码：

**示例代码：**
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int i = 0;
        typedef int (myArray)[10];
        myArray array = {0};
    
        for(i=0;i<10;i++){
            array[i] = i+1;
        }
        printf("array:%p,array+1:%p\n",array,array+1);
        printf("&array:%p,&array+1:%p\n",&array,&array+1);
        return 0;
    }

**结果：**

    array:0061FF04,array+1:0061FF08
    &array:0061FF04,&array+1:0061FF2C

可以清楚的看到  array 虽然和 &array 代表的都是数组的首地址，但是array+1 移动4个字节，&array+1 移动40个字节 

**2.定义一个数组指针**

**(1)方法一：通过数组类型来定义数组指针**

数组指针就是一个指向数组类型变量的指针，那么我们应该如何定义呢？我们还是做一个类比：

我们原来这么写：

int* a ----------------> 定义一个指向 Int类型变量的指针 a ，那我们假如现在需要定义一个指向数组类型的变量的指针 b 应该怎么写？ 我想聪明的你应该立刻能反应过来
    
    typedef int (arrayType)[10];
    arrayType* b;

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int i = 0;
        typedef int (myArray)[10];
        myArray array = {0};
        myArray* p = NULL;
        for(i=0;i<10;i++){
            array[i] = i+1;
        }
    
        p = &array;
    
        for(i=0;i<10;i++){
            printf("%d\n",(*p)[i]);
        }
    
        return 0;
    }
    

**（2）方法二：声明一个数组指针类型**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    int main() {
        int i =0;
        typedef int (myArray)[10];
        typedef int (*pointer)[10];
        myArray  array={0};
        pointer p = NULL;
        p  = &array;
        for(i=0;i<10;i++){
            (*p)[i] = i;
            printf("%d",(*p)[i]);
        }
        return 0;
    }

**（3）方法三：直接声明一个数组指针变量**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int i = 0;
        int (*pointer)[10];
        int myArray[10] = {0};
        pointer = &myArray;
        for(i=0;i<10;i++){
            (*pointer)[i] = i;
            printf("%d\n",(*pointer)[i]);
        }
        return 0;
    }


### **五、多维数组的本质**

二维数组名其实和指向一维（低维）数组的指针是等价的，我们以下面的代码为例

**示例代码：**
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int i = 0,j = 0;
        int a[3][19] ={0};
        int(*pointer)[5] = NULL;
        for(i=0;i<3;i++) {
            for (j = 0; j < 5; j++) {
                a[i][j] = i + j;
            }
        }
        pointer = a;
        for(i=0;i<3;i++){
            for(j=0;j<5;j++){
                printf("%d",pointer[i][j]);
            }
            printf("\n");
        }
    
        printf("a:%p,a+1:%p\n",a,a+1);
        printf("&a:%p,&a+1：%p\n",&a,&a+1);
        return 0;
    }

**结果：**

    01234
    12345
    23456
    a:0061FEE8,a+1:0061FEFC
    &a:0061FEE8,&a+1：0061FF24
    
可以看到我们可以直接用一维数组指针变量来代替二维数组的数组名进行操纵数组

如果还有些抽象我们可以看下面这个示意图：

**示意图：**

![此处输入图片的描述][20]

我们再做一个简单的分析：

如果说 a 是二维数组的名字，将每一个低维都看成是一个数组（假设存在第0行），**如下图：**

![此处输入图片的描述][21]

1.(a+i) 表示整个第i行的地址
2.*(a+i) 表示第i行首元素的地址
3.*(a+i)+j 表示第i行第j列的地址，等价于 &a[i][j]
4.*(*(a+i)+j) 第i行第j列元素的值

### **六、多维数组做函数参数的退化问题**

既然我们已经知道了多维数组的本质，那么我们就进行函数封装并进行传递

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    void* printArray(int(*p)[5]){
        int i = 0,j = 0;
        for(i=0;i<3;i++){
            for(j=0;j<5;j++){
                printf("%d",*(*(p+i)+j));
            }
            printf("\n");
        }
    }
    
    int main() {
        int a[3][22] = {0};
        int(*p)[5] = NULL;
        int i = 0,j = 0;
        p = a;
        for(i=0;i<3;i++){
            for(j=0;j<5;j++){
                *(*(p+i)+j) = i+j;
            }
        }
    
        printArray(a);
        return 0;
    }


### **七、证明二维数组在内存空间上是连续的**

虽然我们在理解的时候将二维数组理解成好几个数组，但是实际上在内存中二维数组的存储是连续的， 那么怎么证明呢？那我们只要用指针连续打印就行了

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    void* printArray(int* array,int num){
        int i = 0;
        for(i =0;i<num;i++){
            printf("%d",array[i]);
        }
    }
    
    int main() {
        int a[3][23] = {0};
        int(*p)[5] = NULL;
        int i = 0,j = 0;
        p = a;
        for(i=0;i<3;i++){
            for(j=0;j<5;j++){
                *(*(p+i)+j) = i+j;
            }
        }
    
        printArray((int*)a,15);
        return 0;
    }


结果：

    012341234523456

### **八、数组做函数参数的等价关系**

char a[30]  ======>  char* p
char* a[30] ======> char** p
char a[10][30] ======> char (*p)[30]

### **九、指针数组的应用场景**

这里面涉及到一些比较绕的知识，我们再来强化一下：

比如我们定义：

    char* ppp[] = {"11","222","333"};

**几个点需要明确的：**

（1）ppp 相当于是一个二级指针，为啥呢？因为这是一个指针数组，数组里面的元素全部是指针， 而ppp是数组的地址，于是是指针的指针，就是二级指针。
（2）*(ppp+i) 得到的才是一级指针
（3）我们如何计算指针数组的长度，我们的方法是使用数组的sizeof值去除以一级指针的sizeof值（一级指针的sizeof值为4），如下

    num = sizeof(ppp)/sizeof(*ppp);/sizeof(ppp)/sizeof(ppp[0]);
（4）ppp+1 移动的是4字节，但是 &ppp+1移动的是12字节

**应用：命令行参数**

可能有人已经注意到了main函数有时会带一些奇奇怪怪的参数，但是main函数作为函数的入口是被谁调用并且传参的呢？

**答案：**当你双击一个应用程序的时候，操作系统首先会为这个程序分配内存，main 函数是操作系统和程序交互的入口，操作系统会把我们对这个程序的一些操作通过参数的形式传递给应用程序（比如说我们在命令行传递给函数的参数）

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main(int argc,char* argv[],char** env){
        int i = 0;
        printf("====================argv======================\n");
        for(i=0;i<argc;i++){
            printf("%s\n",argv[i]);
        }
    
        printf("=====================env=======================\n");
    
        for(i=0;env[i]!=NULL;i++){
            printf("%s\n",env[i]);
        }
    
        return 0;
    }
    
**简单的解释：**
（1）argc 会自动计算出传入的参数的个数
（2）argv[] 这个指针数组会接收命令行以空格传递的参数
（3）env 会将环境变量的所有的值全部收入囊中（env会自动在最后一个环境变量的后面加一个 NULL/0/'\0' 这样方便判断结束，我们称这种能力叫做指针数组的自我结束能力）

### **十、指针数组的自我结束能力：**

指针数组预定俗成在最后添加NULL/0/'\0'，这样就能很方便的实现遍历的结束而不用计算指针的个数

**先来举一个反例：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int i = 0;
        char* ppp[] = {"111","222","3333"};
        for(i=0;ppp[i]!=NULL;i++){
            printf("%s\n",ppp[i]);
        }
        return 0;
    }

这段代码运行完以后就会宕掉，因为原始的指针数组不具有自我结束能力，于是我们需要手动添加一个结束标志，可以是 0 '\0' 或者 NULL

其实0才是最原始的，\0只是因为在字符串里面才需要转义，而 NULL 是在宏定义中规定了等于0

**代码示例:**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int i = 0;
        char* ppp[] = {"111","222","3333",0};
        for(i=0;ppp[i]!=NULL;i++){
            printf("%s\n",ppp[i]);
        }
        return 0;
    }
    

## **0X08 结构体在项目开发中的实际应用**

### **一、回忆一下结构体的一些基本概念**

如何定义结构体并初始化：

**示例代码1：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    struct Teacher{//数据类型是固定大小内存块的别名，但是现在还是没有分配内存
        int id;
        int age;
        char name[64];
    };
    
    
    int main() {
        struct Teacher t1;//这里开始分配内存
        return 0;
    }

**示例代码2：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{//数据类型是固定大小内存块的别名，但是现在还是没有分配内存
        int id;
        int age;
        char name[64];
    }Teacher;
    
    int main() {
        Teacher t1;
        return 0;
    }
    

### **二、结构体定义变量的三种方法：**

**第一种：通过类型定义：**

上面代码已经演示过，不在赘述

**第二种：定义结构体的同时定义变量：**

    struct Teacher{
        int id;
        int age;
        char name[64];
    }t1,t2;//定义类型的同时定义变量
    
    
**第三种：使用匿名结构体**

    struct {
        int id;
        int age;
        char name[64];
    }t1,t2;//定义一个匿名结构体
    
### **三、结构体初始化变量的三种方法：**

**第一种：定义完以后初始化**
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    struct Teacher{
        int id;
        int age;
        char name[64];
    };
    
    
    int main() {
        struct Teacher t1 = {1,23,"hhh"};
        return 0;
    }

**第二种：直接在定义过程中初始化：**

    struct {
        int id;
        int age;
        char name[64];
    }t1 = {1,23,"K0rz3n"};
    
**或者**

    struct Teacher{
        int id;
        int age;
        char name[64];
    }t1 = {1,23,"K0rz3n"};
    

### **四、结构体的（.）操作**

结构体变量用来操纵自己的属性的符号

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
    }Teacher;
    
    int main() {
        Teacher t1;
        t1.age = 23;
        strcpy(t1.name,"sam hello");
        printf("name:%s\n",t1.name);
        return 0;
    }

那么t1.是什么含义呢？有没有操纵内存呢？

> 答： t1.age 实际上是在计算 age 相对于 t1 的偏移量，是CPU在运算，并没有操纵内存，操纵内存的时间是在等于号赋值的时候


### **五、结构体的（->）操作**

结构体指针变量用来操纵指针所指向的对象的属性的操作符

**示例代码:**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
    }Teacher;
    
    int main() {
        Teacher t1;
        Teacher t2 = {24,"XDSEC"};
        Teacher* p = NULL;
        t1.age = 23;
        strcpy(t1.name,"sam hello");
        printf("name:%s\n",t1.name);
        p = &t2;
        printf("t2.age:%d\nt2.name:%s\n",p->age, p->name);
        return 0;
    }

**结果：**

    name:sam hello
    t2.age:24
    t2.name:XDSEC
    
那么p->是什么含义呢？有没有操纵内存呢？

> 答： p->age 实际上是在计算 age 相对于 t2 的偏移量，是CPU在运算，并没有操纵内存，操纵内存的时间是在等于号赋值的时候


### **六、结构体元素做参数&&结构体指针做参数**

**1.编译器支持直接的 = 赋值操作**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
    }Teacher;
    
    int main() {
        Teacher t1 = {25,"Tom"};
        Teacher t2;
        t2 = t1;
        printf("age:%d\nname:%s\n",t2.age,t2.name);
    
        return 0;
    }

**结果：**

    age:25
    name:Tom

**2.我们做参数直接传进函数试一试：**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
    }Teacher;
    
    void* copyTeacher(Teacher to ,Teacher from){
        to = from;
        printf("toage:%d\ntoname:%s\n",to.age,to.name);
    
    }
    
    int main() {
        Teacher t1 = {25,"Tom"};
        Teacher t2;
        Teacher t3;
        t2 = t1;
        printf("t2age:%d\nt2name:%s\n",t2.age,t2.name);
        copyTeacher(t3,t1);
        printf("t3age:%d\nt3name:%s\n",t3.age,t3.name);
        return 0;
    }
    
**结果：**
    
    t2age:25
    t2name:Tom
    toage:25
    toname:Tom
    t3age:0
    t3name:

发现我们直接传进去并没有用，其实也很好理解，我们传进去的时候创建了新的栈空间，于是修改的也不是原来的栈空间，于是我们还是需要使用指针来改变原来地址的值

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
    }Teacher;
    
    void* copyTeacher(Teacher* to ,Teacher from){
        *to = from;
        printf("toage:%d\ntoname:%s\n",(*to).age,(*to).name);
    
    }
    
    int main() {
        Teacher t1 = {25,"Tom"};
        Teacher t2;
        Teacher t3;
        t2 = t1;
        printf("t2age:%d\nt2name:%s\n",t2.age,t2.name);
        copyTeacher(&t3,t1);
        printf("t3age:%d\nt3name:%s\n",t3.age,t3.name);
        return 0;
    }

**结果：**
    
    t2age:25
    t2name:Tom
    toage:25
    toname:Tom
    t3age:25
    t3name:Tom


### **七、结构体数组做函数参数：**
    
**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
    }Teacher;
    
    
    void* printArray(Teacher* p,int num){
        int i = 0;
        for(i=0;i<num;i++){
            printf("your name is :%d\n",p[i].age);
        }
    }
    
    int main() {
        int i =0;
        int num = 3;
        Teacher array[num];
        for(i=0;i<num;i++){
            printf("\nplease enter your age:");
            scanf("%d",&(array[i].age));
        }
    
        printArray(array,num);
        return 0;
    }

**结果：**

    please enter your age:18
    18
    
    please enter your age:12
    12
    
    please enter your age:13
    13
    your name is :18
    your name is :12
    your name is :13


我们现在将其按照老师的年龄进行排序：

**示例代码：**


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
    }Teacher;
    
    
    void* printArray(Teacher* p,int num){
        int i = 0;
        for(i=0;i<num;i++){
            printf("your name is :%d\n",p[i].age);
        }
    }
    
    void* sortArray(Teacher* p, int num){
        int i = 0,j = 0;
        Teacher tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(p[i].age > p[j].age){
                    tmp= p[j];
                    p[j] = p[i];
                    p[i] = tmp;
                }
            }
        }
    }
    
    int main() {
        int i =0;
        int num = 3;
        Teacher array[num];
        for(i=0;i<num;i++){
            printf("\nplease enter your age:");
            scanf("%d",&(array[i].age));
        }
    
        printf("排序前:\n");
        printArray(array,num);
        sortArray(array,num);
        printf("排序后:\n");
        printArray(array,num);
    
        return 0;
    }
    
通过malloc 的方式初始化结构体数组

**示例代码：**
    
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
    }Teacher;
    
    
    void* printArray(Teacher* p,int num){
        int i = 0;
        for(i=0;i<num;i++){
            printf("your name is :%d\n",p[i].age);
        }
    }
    
    void* sortArray(Teacher* p, int num){
        int i = 0,j = 0;
        Teacher tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(p[i].age > p[j].age){
                    tmp= p[j];
                    p[j] = p[i];
                    p[i] = tmp;
                }
            }
        }
    }
    
    Teacher* createTeacher(int num){
        Teacher* tmp = NULL;
        tmp = (Teacher*)malloc(sizeof(Teacher)*num);
        if(tmp == NULL){
            return NULL;
        }
        return tmp;
    }
    
    
    int freeTeacher(Teacher** p){
        if(p == NULL){
            return -1;
        }
        free(p);
        *p = NULL;
        return 0;
    }

    
    int main() {
        int i =0;
        int num = 3;
    
        Teacher* tmp = NULL;
        tmp = createTeacher(num);
        for(i=0;i<num;i++){
            printf("\nplease enter your age:");
    
            scanf("%d",&(tmp[i].age));
        }
    
        printf("排序前:\n");
    
        printArray(tmp,num);
    
        sortArray(tmp,num);
        printf("排序后:\n");
    
        printArray(tmp,num);
        return 0;
    }


调用二级指针：

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
    }Teacher;
    
    
    void* printArray(Teacher* p,int num){
        int i = 0;
        for(i=0;i<num;i++){
            printf("your name is :%d\n",p[i].age);
        }
    }
    
    void* sortArray(Teacher* p, int num){
        int i = 0,j = 0;
        Teacher tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(p[i].age > p[j].age){
                    tmp= p[j];
                    p[j] = p[i];
                    p[i] = tmp;
                }
            }
        }
    }
    
    int createTeacher(Teacher** p,int num){
        Teacher* tmp = NULL;
        tmp = (Teacher*)malloc(sizeof(Teacher)*num);
        if(tmp == NULL){
            return -1;
        }
        *p = tmp;
        return 0;
    }
    
    
    int freeTeacher(Teacher** p){
        if(p == NULL){
            return -1;
        }
        free(p);
        *p = NULL;
        return 0;
    }
    
    
    int main() {
        int i =0;
        int num = 3;
        Teacher *tmp = NULL;
        res = createTeacher(&tmp,num);
        if(res != 0){
            printf("func createTeacher has somethong wrong , please check and try again");
            return -1;
        }
        for(i=0;i<num;i++){
            printf("\nplease enter your age:");
            scanf("%d",&(tmp[i].age));
        }
    
        printf("排序前:\n");
        printArray(tmp,num);
        sortArray(tmp,num);
        printf("排序后:\n");
        printArray(tmp,num);
        freeTeacher(&tmp);
        return 0;
    }

### **八、结构体套一级指针**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
        char* aliasname;
    }Teacher;
    
    
    void* printArray(Teacher* p,int num){
        int i = 0;
        for(i=0;i<num;i++){
            printf("your name is :%d\n",p[i].age);
        }
    }
    
    void* sortArray(Teacher* p, int num){
        int i = 0,j = 0;
        Teacher tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(p[i].age > p[j].age){
                    tmp= p[j];
                    p[j] = p[i];
                    p[i] = tmp;
                }
            }
        }
    }
    
    
    
    int createTeacher(Teacher** p,int num){
        int i = 0;
        Teacher* tmp = NULL;
        tmp = (Teacher*)malloc(sizeof(Teacher)*num);
        if(tmp == NULL){
            return -1;
        }
        memset(tmp,0,sizeof(Teacher)*num);
        for(i=0;i<num;i++){
            tmp[i].aliasname = (char*)malloc(128);
        }
    
        *p = tmp;
        return 0;
    }
    
    
    
    int freeTeacher(Teacher* p,int num){//这里的free 函数使用的是一级指针
        int i;
        if(p == NULL){
            return -1;
        }
        for(i=0;i<num;i++){
            if(p[i].aliasname != NULL){
                free(p[i].aliasname);
            }
        }
        free(p);
        return 0;
    }
    
    
    int main() {
        int i =0;
        int num = 3;
        int res = 0;
        Teacher *tmp = NULL;
        res = createTeacher(&tmp,num);
        if(res != 0){
            printf("func createTeacher has something wrong , please check and try again");
            return -1;
        }
        for(i=0;i<num;i++){
            printf("\nplease enter your age:");
            scanf("%d",&(tmp[i].age));
        }
    
        printf("排序前:\n");
        printArray(tmp,num);
        sortArray(tmp,num);
        printf("排序后:\n");
        printArray(tmp,num);
        freeTeacher(tmp,num);
        return 0;
    }
    
**内存示意图：**



现在我们挑战一下使用二级指针来free内存，我这里就不再解释，如果有不理解还要参考之前我讲的内存以及上面的内存示意图：

![此处输入图片的描述][24]

**示例代码：**


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
        char* aliasname;
    }Teacher;
    
    
    void* printArray(Teacher* p,int num){
        int i = 0;
        for(i=0;i<num;i++){
            printf("your name is :%d\n",p[i].age);
        }
    }
    
    void* sortArray(Teacher* p, int num){
        int i = 0,j = 0;
        Teacher tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(p[i].age > p[j].age){
                    tmp= p[j];
                    p[j] = p[i];
                    p[i] = tmp;
                }
            }
        }
    }
    
    int createTeacher(Teacher** p,int num){
        int i = 0;
        Teacher* tmp = NULL;
        tmp = (Teacher*)malloc(sizeof(Teacher)*num);
        if(tmp == NULL){
            return -1;
        }
        memset(tmp,0,sizeof(Teacher)*num);
        for(i=0;i<num;i++){
            tmp[i].aliasname = (char*)malloc(128);
        }
    
        *p = tmp;
        return 0;
    }
    
    
    int freeTeacher(Teacher** p,int num){
        int i;
        if(p == NULL){
            return -1;
        }
        for(i=0;i<num;i++){
            if((*p)[i].aliasname != NULL){
                free((*p)[i].aliasname);
            }
        }
        free(*p);
        free(p);
        return 0;
    }
    
    int main() {
        int i =0;
        int num = 3;
        int res = 0;
        Teacher *tmp = NULL;
        res = createTeacher(&tmp,num);
        if(res != 0){
            printf("func createTeacher has something wrong , please check and try again");
            return -1;
        }
        for(i=0;i<num;i++){
            printf("\nplease enter your age:");
            scanf("%d",&(tmp[i].age));
        }
    
        printf("排序前:\n");
        printArray(tmp,num);
        sortArray(tmp,num);
        printf("排序后:\n");
        printArray(tmp,num);
        freeTeacher(&tmp,num);
        return 0;
    }
    

### **九、结构体套二级指针**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int age;
        char name[64];
        char* aliasname;
        char** stuname;
    }Teacher;
    
    
    void* printArray(Teacher* p,int num){
        int i = 0;
        for(i=0;i<num;i++){
            printf("your name is :%d\n",p[i].age);
        }
    }
    
    void* sortArray(Teacher* p, int num){
        int i = 0,j = 0;
        Teacher tmp;
        for(i=0;i<num;i++){
            for(j=i+1;j<num;j++){
                if(p[i].age > p[j].age){
                    tmp= p[j];
                    p[j] = p[i];
                    p[i] = tmp;
                }
            }
        }
    }
    
    int createTeacher(Teacher** p,int num){
        int i = 0,j = 0;
        Teacher* tmp = NULL;
        char** sp = NULL;
        tmp = (Teacher*)malloc(sizeof(Teacher)*num);
        if(tmp == NULL){
            return -1;
        }
        memset(tmp,0,sizeof(Teacher)*num);
        for(i=0;i<num;i++){
            tmp[i].aliasname = (char*)malloc(128);
            sp = (char**)malloc(num*sizeof(char*));//打造二级指针
            for(j=0;j<num;j++){
                sp[j] = (char*)malloc(120);
            }
            tmp[i].stuname = sp;
        }
    
        *p = tmp;
        return 0;
    }
    
    
    int freeTeacher(Teacher* p,int num){
        int i,j = 0;
        char** pn = NULL;
        if(p == NULL){
            return -1;
        }
        for(i=0;i<num;i++){
            if(p[i].aliasname != NULL){
                free(p[i].aliasname);
            }
            if(p[i].stuname != NULL){
                pn = p[i].stuname;
                for(j=0;j<num;j++){
                    if(pn[j] != NULL){
                        free(pn[j]);
                    }
                }
                free(pn);
                p[i].stuname = NULL;
            }
        }
        
        free(p);
    
        return 0;
    }
    
    
    int main() {
        int i = 0,j = 0;
        int num = 3;
        int res = 0;
        Teacher *tmp = NULL;
        res = createTeacher(&tmp,num);
        if(res != 0){
            printf("func createTeacher has something wrong , please check and try again");
            return -1;
        }
        for(i=0;i<num;i++){
            printf("\nplease enter your age:");
            scanf("%d",&(tmp[i].age));
            for(j=0;j<num;j++){
                printf("\nplease enter the student age:");
                scanf("%s",tmp[i].stuname[j]);
            }
        }
    
    
    
        printf("排序前:\n");
        printArray(tmp,num);
        sortArray(tmp,num);
        printf("排序后:\n");
        printArray(tmp,num);
        freeTeacher(tmp,num);
        return 0;
    }

**内存模型：**

![此处输入图片的描述][25]


### **十、结构体的深拷贝与浅拷贝**

当结构体中存在指针变量，并进行整体的赋值操作给另一个结构体变量的时候，默认情况下拷贝只能将指针变量的值拷贝过去，但是并不会真正的将指向的内存空间再复制一份，也就是说最终的结果只是两个指针变量指向了同一块内存空间。

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        char name[64];
        int age;
        char* pname;
    }Teacher;
    
    void* teacherCopy(Teacher* to,Teacher* from){
        *to = *from;
    }
    
    int freeTeacher(Teacher* p){
        if(p->pname == NULL){
            return -1;
        }
        free(p->pname);
        p->pname = NULL;
        return 0;
    }
    
    int main() {
        Teacher t1;
        Teacher t2;
        int res;
        strcpy(t1.name,"tom");
        t1.age = 20;
        t1.pname = (char*)malloc(100);
        strcpy(t1.pname,"xxxxxx");
        teacherCopy(&t2,&t1);
        freeTeacher(&t1);
        freeTeacher(&t2);
        return 0;
    }

因此，如果我们对一块内存释放两次就会出现程序崩溃，解决的办法就是显示的分配内存实现深cpoy

**示例代码：**


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        char name[64];
        int age;
        char* pname;
    }Teacher;
    
    void* teacherCopy(Teacher* to,Teacher* from){
        *to = *from;
        to->pname = (char*)malloc(100);
        strcpy(to->pname,from->pname);
    }
    
    int freeTeacher(Teacher* p){
        if(p->pname == NULL){
            return -1;
        }
        free(p->pname);
        p->pname = NULL;
        return 0;
    }
    
    int main() {
        Teacher t1;
        Teacher t2;
        int res;
        strcpy(t1.name,"tom");
        t1.age = 20;
        t1.pname = (char*)malloc(100);
        strcpy(t1.pname,"xxxxxx");
        teacherCopy(&t2,&t1);
        freeTeacher(&t1);
        freeTeacher(&t2);
        return 0;
    }


### **十一、结构体的高级话题_偏移量**

#### **1.基础演示：**

结构体内部的元素都是可以通过相对于结构体的偏移量来访问的

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "stdio.h"
    
    typedef struct Teacher{
        char name[64];//64
        int age;//4
        int p;//4
        char *pname2;//4
    }Teacher;
    
    
    int main() {
        Teacher t1;
        Teacher* p = NULL;
        int offsize = (int)&(p->age);
        printf("%d\n",offsize);
        return 0;
    }

**结果：**

    64

当然，更深入一点的话我们还能把下面这句

    int offsize = (int)&(p->age);

改成这样：

    int offsize = (int)&(((Teacher*)0)->age);

**原理是什么呢？**

其实我们就是将0处的地址按照结构体的方式进行解析，那么自然age 的相对偏移量就是64 了

**有什么用呢？**

我们可以通过这个地址找到结构体的起始位置的偏移量，简单地说就是“透过小孔看世界”。


#### **2.实际地址和相对地址**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "stdio.h"
    
    typedef struct Teacher{
        char name[64];//64
        int age;//4
        int p;//4
        char *pname2;//4
    }Teacher;
    
    
    int main() {
        int i = 0;
        char* pp;
        Teacher t1;
        //Teacher* p = NULL;
        Teacher* p = &t1;
        int offsize1 = (int)&(p->age);
        int offsize2 = (int)&(((Teacher*)0)->age);
        printf("实际地址：%d\n",offsize1);
        printf("相对地址：%d\n",offsize2);
        return 0;
    }


**结果:**

    实际地址：6422292
    相对地址：64


## **0X09 C语言文件操作**

### **一、文件的分类：**

1.文本文件：文本编辑器打开是可见字符的文件
2.二进制文件：文本编辑器打开全部是不可见字符

### **二、文操作API 的分类：**

（1）文件读写API 

1.fgetc、fputc 按照字符的形式读写文件
2.fgets、fputs 按照字符串的形式（行）读写（配置）文件
3.fread、fwrite 按照块读写文件（大数据块迁移）

### **三、文件按字符读写操作**

**1.fputc 按字符写**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        int i = 0;
        FILE *fp = NULL;
        char* p = "D:/1.txt";
        char a[10] = "abcdefghi";
        fp = fopen(p,"w+");
        if(fp == NULL){
            printf("func fopen has something wrong please check and try again");
            return -1;
        }
        for(i=0;i<strlen(a);i++){
            fputc(a[i],fp);
        }
        fclose(fp);
    
        return 0;
    }

**2.fgetc 按字符读**

**示例代码:**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main(){
        char t;
        FILE* fp = NULL;
        char* p = "D:/1.txt";
        fp = fopen(p,"r+");
        if(fp == NULL){
            printf("func has something wrong ,please check and try again");
            return -1;
        }
        while(!feof(fp)){
            t = fgetc(fp);
            printf("%c",t);
        }
        return 0;
    }

### **四、文件按行读写操作：**

1.fputs 按行写操作：

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        FILE* fp =NULL;
        char* p = "D:/1.txt";
        char a[10] = "jklmnopqr";
        fp = fopen(p,"w+");
        if(fp == NULL){
            printf("func has something wrong please check and try again");
            return -1;
        }
        fputs(a,fp);
        if(fp != NULL){
            fclose(fp);
        }
        return 0;
    }

2.fgets 按行读操作：

**示例代码：**


    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        FILE* fp =NULL;
        char* p = "D:/1.txt";
        char* pn = NULL;
        char buf[1024];
        fp = fopen(p,"r+");
        if(fp == NULL){
            printf("func has something wrong please check and try again");
            return -1;
        }
        while(!feof(fp)){
            pn = fgets(buf,1024,fp);
            if(pn == NULL){
                goto END;
            }
            printf("%s",buf);
        }
    
        END:
            if(fp != NULL){
                fclose(fp);
            }
    
        return 0;
    }

这里是一行一行的读取数据（每一行都将\n读了进去，于是我们打印的时候完全不需要自己写\n），当然我们已经规定了了总共最大的读取长度是1024自字节。

### **五、文件按照块进行读写操作**

**1.fwrite 的写操作**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        FILE* fp = NULL;
        char* p = "D:/1.txt";
        int res = 0;
        char* str = "i love you";
        fp = fopen(p,"wb");
        if(fp == NULL){
            printf("func has something wrong please check and try again");
            fclose(fp);
            return -1;
        }
        res = fwrite(str,strlen(str),1,fp);
        if(res != 1){
            printf("func has something wrong please check and try again");
            fclose(fp);
            return -2;
        }
    
        fclose(fp);
        return 0;
    }

**fwrite()的参数的解释：**

1.第一个参数是你想向文件中写的字符串首地址或者数组首地址
2.第二个参数是你每次操作一块的长度
3.第三个参数是操作的次数
4.第四个参数是文件句柄
5.返回值是写成功的次数

**2.fwrite 的读操作**

**示例代码：**
    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    int main() {
        FILE* fp = NULL;
        char* p = "D:/1.txt";
        char str[100] = {0};
        fp = fopen(p,"rb");
        if(fp == NULL){
            printf("func1 has something wrong please check and try again");
            fclose(fp);
            return -1;
        }
        fread(str,sizeof(str),1,fp);
        printf("%s",str);
        fclose(fp);
        return 0;
    }

### **六、配置文件读写**

#### **设计一个接口需要注意的几点：**

1.理清楚功能点（我需要提供哪些接口）
2.分清楚输入还是输出（是主函数分配内存还是子函数分配内存）

#### **设计理念：**

**接口要紧，模块要松**

**什么意思呢？**

接口能做的事绝不会抛给上层应用完成，比如说我的写功能和修改功能在接口的封装上是重复的，我们就应该在一个接口中实现，根据用户的提供的信息判断用户是写还是修改，而不是说让用户自己判断然后调用不同的接口，再比如，我们不应该在接口中传递文件句柄，如果传递文件句柄，那么使用者还要去打开文件，万一要是网络句柄，那么还要学网络，这对上层是很不友好的，我们不能这么做，能在接口中实现的都要在接口中实现。



### **七、大文件加解密的实现**

由于文件比较大，我们不能一下子将其加到内存中进行加密，那样对内存的消耗比较大，于是我们可以采用分块加密的方式，每次加密一部分然后拼接起来。


## **0X10 动态链接库的封装和设计**

**先了解一下基本的名称：**

1.socketclient.lib 是资源 描述文件，用来描述 socketclient.dll 这个文件
2.socketclient.dll 是动态链接库文件，是函数二进制码的集合，里面有很多函数的函数体（动态库有自己的一套规范，在windows 下面有一套，linux 下面也有一套）

**了解一下基本的概念：**

1. 编译器是通过资源描述文件 xxx.lib 去寻找动态链接库xxx.dll（我们在编译以前需要将lib配置进编译器的链接器中的输入中）
2.动态库要有能力把函数导出来供其他函数调用，（dll 是由c和h组成的）实际上是在 dll 中的 c 文件中实现的

**导出函数用到的关键字：**

__declspec(dllexport)

导入函数实际上不用我们单独写，程序会为我们自动调用


## **0X11 内存泄露的原理以及检测**

C语言的malloc 是不能自动析构内存的，操作不当就会产生内存泄漏的问题，内存泄露并不会对程序的功能造成影响，但是长此以往，计算机的内存将被耗尽。内存泄露有许多的检测工具，比如mtrace 再比如 memwatch



## **0X12 链表**

### **一、结构体中套结构体**

结构体中可以嵌套其他结构体

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Student{
        int age;
        char name[64];
    }Student;
    typedef struct Teacher{
        int age;
        char name[64];
        Student xiaoming;
    }Teacher;
    
    
    int main() {
        return 0;
    }

**注意：**

但是结构体中不能嵌套自己，因为编译器会首先判断结构体的大小，如果大小能正常分配的话就能编译通过（因为数据类型的本质是固定大小的不内存的别名，只有知道大小了才能分配内存），但是嵌套自己就会陷入一个无法计算大小的死循环。

### **二、链表基础**

**1.链表的概念：**

链表是一种在内存上非连续的存储结构，由一系列的节点组成的，节点可以在运行时动态生成，节点之间通过指针链接，于是每个节点包括两个部分，一个是数据域另一个是指针域。

**2.链表的组成以及特点**

（1）链表的每一个节点都是一个结构体
（2）链表有两个域，数据域和指针域
（3）链表的指针是一个指向自己类型结构体的指针
（4）链表的特点是非顺序存储

**3.静态链表的创建和遍历**

静态链表意思就是固定大小长度的链表

**（1）创建**

**示例代码:**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int data;
        struct Teacher* next;
    }Teacher;
    int main() {
        Teacher t1,t2,t3;
        t1.data = 1;
        t2.data = 2;
        t3.data = 3;
    
        t1.next = &t2;
        t2.next = &t3;
        t3.next = NULL;
        return 0;
    }
    
**注意：**

静态链表的一个非常重要的缺点就是他所有的内容都是在栈区存储的，如果你想在子函数中创建然后返回给主函数，那么在返回后内存就会被自动析构，于是主函数得到的将会是一个空指针

**（2）遍历：**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Teacher{
        int data;
        struct Teacher* next;
    }Teacher;
    
    int main() {
        Teacher *p =NULL;
        Teacher t1,t2,t3;
        t1.data = 1;
        t2.data = 2;
        t3.data = 3;
    
        t1.next = &t2;
        t2.next = &t3;
        t3.next = NULL;
    
        p = &t1;
        while(p){
            printf("%d\n",p->data);
            p = p->next;
        }
        return 0;
    }

**结果：**

    1
    2
    3

**4.链表的分类**

（1）带头节点的链表和不带头节点的链表
（2）单向链表、双向链表、循环链表
（3）静态链表、动态链表

**5.链表的元素分类：**

1.头结点  pHead
2.当前节点 pCurrent
3.前趋节点 pPrior
4.后继节点 pNext
5.新建节点 pMalloc

**6.建立带有头结点的单向链表**

**添加节点的思路：**

1.循环创建新节点并 malloc 分配内存
2.新节点加入链表
3.新节点变成当前节点

**引入三个辅助指针变量：**
1.pHead 用来指向头结点，最后返回值需要用到
2.pM 用来作为新分配节点的指针，在添加节点过程中有用
3.pCurrent 始终指向当前节点，在新节点的插入以后不断的更新，指向新的节点



**链表的创建与遍历：**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Node{
        int data;
        struct Node* next;
    }SLIST;
    
    //创建链表
    SLIST* createList(){
        int data =0;
        SLIST* pHead;
        SLIST* pCur;
        SLIST* pM;
        pHead = (SLIST*)malloc(sizeof(SLIST));
        pCur = pHead;
        if(pHead == NULL){
            return NULL;
        }
        pHead->data = 0;
        pHead->next = NULL;
        printf("\nplease enter your data:");
        scanf("%d",&data);
        while(data != -1){
            pM = (SLIST*)malloc(sizeof(SLIST));
            if(pM == NULL){
                return NULL;
            }
            pM->data = 0;
            pM->next = NULL;
            pM->data = data;
            pCur->next = pM;
            pCur = pM;
            printf("\nplease enter your data:");
            scanf("%d",&data);
        }
        return pHead;
    }
    
    //遍历链表
    int listPrint(SLIST* pHead){
        SLIST* tmp = NULL;
        if(pHead == NULL){
            return -1;
        }
        tmp = pHead->next;
        printf("\nBegin\t\n");
        while(tmp){
            printf("%d\n",tmp->data);
            tmp = tmp->next;
        }
        printf("End\t\n");
        return 0;
    }
    
    //插入节点
    int nodeInsert(SLIST* pHead, int x ,int y ){
        return 0;
    }
    
    //删除节点
    int nodeDel(SLIST* pHead , int x){
        return 0;
    }
    
    //链表销毁
    
    int listDistory(SLIST* pHead){
        return 0;
    }
    
    
    int main() {
        int res = 0;
        SLIST* pHead = NULL;
        pHead = createList();
        //res = nodeInsert(pHead,20,19);
        res = listPrint(pHead);
    
    
        return 0;
    }


**7.链表的插入**

涉及到的三个指针
1.pM 新创建的节点
2.pHead 头节点
3.pCur 当前节点
4.pPrior 上一个节点

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Node{
        int data;
        struct Node* next;
    }SLIST;
    
    //创建链表
    SLIST* createList(){
        int data =0;
        SLIST* pHead;
        SLIST* pCur;
        SLIST* pM;
        pHead = (SLIST*)malloc(sizeof(SLIST));
        pCur = pHead;
        if(pHead == NULL){
            return NULL;
        }
        pHead->data = 0;
        pHead->next = NULL;
        printf("\nplease enter your data:");
        scanf("%d",&data);
        while(data != -1){
            pM = (SLIST*)malloc(sizeof(SLIST));
            if(pM == NULL){
                return NULL;
            }
            pM->data = 0;
            pM->next = NULL;
            pM->data = data;
            pCur->next = pM;
            pCur = pM;
            printf("\nplease enter your data:");
            scanf("%d",&data);
        }
        return pHead;
    }
    
    //遍历链表
    int listPrint(SLIST* pHead){
        SLIST* tmp = NULL;
        if(pHead == NULL){
            return -1;
        }
        tmp = pHead->next;
        printf("\nBegin\t\n");
        while(tmp){
            printf("%d\n",tmp->data);
            tmp = tmp->next;
        }
        printf("End\t\n");
        return 0;
    }
    
    //插入节点
    int nodeInsert(SLIST* pHead, int x ,int y ){
        SLIST* pM,*pCur,*pPre;
        pPre = pHead;
        pCur = pPre->next;
        pM = (SLIST*)malloc(sizeof(SLIST));
        if(pM == NULL){
            return -1;
        }
        pM->next = NULL;
        pM->data = y;
        while(pCur){
            if(pCur->data == x){
                pM->next = pCur;
                pPre->next = pM;
                break;
            }
            pPre = pCur;
            pCur = pCur->next;
        }
    
        return 0;
    }
    
    //删除节点
    int nodeDel(SLIST* pHead , int x){
        return 0;
    }
    
    //链表销毁
    
    int listDistory(SLIST* pHead){
        return 0;
    }
    
    
    int main() {
        int res = 0;
        SLIST* pHead = NULL;
        pHead = createList();
        res = nodeInsert(pHead,20,19);
        if(res != 0){
            printf("func nodeInsert has something wrong please check and try again");
            return res;
        }
        res = listPrint(pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
    
        return 0;
    }
    

**8.链表的删除与销毁**

销毁内存的时候一定要使用如果传入的是一级指针那么我们就应该使用二级指针来接，要不然就会出问题

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Node{
        int data;
        struct Node* next;
    }SLIST;
    
    //创建链表
    SLIST* createList(){
        int data =0;
        SLIST* pHead;
        SLIST* pCur;
        SLIST* pM;
        pHead = (SLIST*)malloc(sizeof(SLIST));
        pCur = pHead;
        if(pHead == NULL){
            return NULL;
        }
        pHead->data = 0;
        pHead->next = NULL;
        printf("\nplease enter your data:");
        scanf("%d",&data);
        while(data != -1){
            pM = (SLIST*)malloc(sizeof(SLIST));
            if(pM == NULL){
                return NULL;
            }
            pM->data = 0;
            pM->next = NULL;
            pM->data = data;
            pCur->next = pM;
            pCur = pM;
            printf("\nplease enter your data:");
            scanf("%d",&data);
        }
        return pHead;
    }
    
    //遍历链表
    int listPrint(SLIST* pHead){
        SLIST* tmp = NULL;
        if(pHead == NULL){
            return -1;
        }
        tmp = pHead->next;
        printf("\nBegin\t\n");
        while(tmp != NULL){
            printf("%d\n",tmp->data);
            tmp = tmp->next;
        }
        printf("End\t\n");
        return 0;
    }
    
    //插入节点
    int nodeInsert(SLIST* pHead, int x ,int y ){
        SLIST* pM,*pCur,*pPre;
        pPre = pHead;
        pCur = pPre->next;
        pM = (SLIST*)malloc(sizeof(SLIST));
        if(pM == NULL){
            return -1;
        }
        pM->next = NULL;
        pM->data = y;
        while(pCur){
            if(pCur->data == x){
                pM->next = pCur;
                pPre->next = pM;
                break;
            }
            pPre = pCur;
            pCur = pCur->next;
        }
    
        return 0;
    }
    
    //删除节点
    int nodeDel(SLIST* pHead , int y){
        SLIST* pM,*pCur,*pPre;
        pPre = pHead;
        pCur = pPre->next;
        while(pCur){
            if(pCur->data == y){
                pPre->next = pCur->next;
                break;
            }
            pPre = pCur;
            pCur = pCur->next;
        }
        if(pCur == NULL){
            printf("don't find the %d node ,please check and try again",y);
            return -1;
        }
    
        return 0;
    }
    
    //链表销毁
    
    int listDistory(SLIST** p){
        SLIST* tmp = *p;
        if(NULL == p){
            return -1;
        }
        while(*p != NULL){
            tmp = (*p)->next;
            free(*p);
            *p = tmp;
        }
        return 0;
    }
    
    int main() {
        int res = 0;
        SLIST* pHead = NULL;
        pHead = createList();
        res = nodeInsert(pHead,20,19);
        if(res != 0){
            printf("func nodeInsert has something wrong please check and try again");
            return res;
        }
        res = listPrint(pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
    
        res = nodeDel(pHead,19);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
        res = listPrint(pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
    
        res = listDistory(&pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
        res = listPrint(pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
        return 0;
    }


**9.链表的逆序操作**

1.链表的逆置也是一个节点一个节点的逆置
2.逆置的起点不应该在链表的末尾，因为链表是单项的，如果你从末尾开始逆置为了得到前一项的位置，必须要不断的溯源，一直到链表的开始。
3.从前往后逆置，我们还是要知道链表是单项的，为了防止指针逆置以后把后面的值丢掉我们必须创建一个辅助指针变量保存下一个节点的地址
4.我们和以前一样依然需要两个辅助的指针变量，这样看来我们就需要三个辅助指针变量

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    typedef struct Node{
        int data;
        struct Node* next;
    }SLIST;
    
    //创建链表
    SLIST* createList(){
        int data =0;
        SLIST* pHead;
        SLIST* pCur;
        SLIST* pM;
        pHead = (SLIST*)malloc(sizeof(SLIST));
        pCur = pHead;
        if(pHead == NULL){
            return NULL;
        }
        pHead->data = 0;
        pHead->next = NULL;
        printf("\nplease enter your data:");
        scanf("%d",&data);
        while(data != -1){
            pM = (SLIST*)malloc(sizeof(SLIST));
            if(pM == NULL){
                return NULL;
            }
            pM->data = 0;
            pM->next = NULL;
            pM->data = data;
            pCur->next = pM;
            pCur = pM;
            printf("\nplease enter your data:");
            scanf("%d",&data);
        }
        return pHead;
    }
    
    //遍历链表
    int listPrint(SLIST* pHead){
        SLIST* tmp = NULL;
        if(pHead == NULL){
            return -1;
        }
        tmp = pHead->next;
        printf("\nBegin\t\n");
        while(tmp != NULL){
            printf("%d\n",tmp->data);
            tmp = tmp->next;
        }
        printf("End\t\n");
        return 0;
    }
    
    //插入节点
    int nodeInsert(SLIST* pHead, int x ,int y ){
        SLIST* pM,*pCur,*pPre;
        pPre = pHead;
        pCur = pPre->next;
        pM = (SLIST*)malloc(sizeof(SLIST));
        if(pM == NULL){
            return -1;
        }
        pM->next = NULL;
        pM->data = y;
        while(pCur){
            if(pCur->data == x){
                pM->next = pCur;
                pPre->next = pM;
                break;
            }
            pPre = pCur;
            pCur = pCur->next;
        }
    
        return 0;
    }
    
    //删除节点
    int nodeDel(SLIST* pHead , int y){
        SLIST* pM,*pCur,*pPre;
        pPre = pHead;
        pCur = pPre->next;
        while(pCur){
            if(pCur->data == y){
                pPre->next = pCur->next;
                break;
            }
            pPre = pCur;
            pCur = pCur->next;
        }
        if(pCur == NULL){
            printf("don't find the %d node ,please check and try again",y);
            return -1;
        }
    
        return 0;
    }
    
    //链表逆置
    
    int listReverse(SLIST* pHead){
        if(pHead == NULL || pHead->next->next == NULL || pHead->next ==NULL){
            return -1;
        }
        SLIST* pPer = pHead->next;
        SLIST* pCur = pHead->next->next;
        SLIST* tmp = NULL;
        while(pCur){
            tmp = pCur->next;
            pCur->next = pPer;
            pPer = pCur;
            pCur = tmp;
        }
        pHead->next->next = NULL;
        pHead->next = pPer;
        return 0;
    
    
    
    }
    
    //链表销毁
    
    int listDistory(SLIST** p){
        SLIST* tmp = *p;
        if(NULL == p){
            return -1;
        }
        while(*p != NULL){
            tmp = (*p)->next;
            free(*p);
            *p = tmp;
        }
        return 0;
    }
    
    int main() {
        int res = 0;
        SLIST* pHead = NULL;
        pHead = createList();
        res = nodeInsert(pHead,20,19);
        if(res != 0){
            printf("func nodeInsert has something wrong please check and try again");
            return res;
        }
        res = listPrint(pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
    
        res = nodeDel(pHead,19);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
        res = listPrint(pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
    
        res = listReverse(pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
        res = listPrint(pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
        res = listDistory(&pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
        res = listPrint(pHead);
        if(res != 0){
            printf("func listPrint has something wrong please check and try again");
            return res;
        }
    
        return 0;
    }



**10.传统链表和非传统链表**

**传统链表的缺点：**
（1）和具体的结构绑定，不通用
（2）链表逻辑结构试图包含业务逻辑结构
（3）业务数据和链表逻辑的耦合性太高

**改进:**

于是我们对链表进行了改进，既然我们不能让链表包含万事万物，那我们干脆就不包含，我们让万事万物来包含我。

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    typedef struct Node{//定义的一个单纯地链表节点
        struct Node * next;
    }Node;
    
    
    typedef struct Teacher{//将这个链表节点植入一个结构体中
        int age;
        char name[64];
        struct Node node; 
    }Teacher;
    
    int main() {
        printf("Hello, World!\n");
        return 0;
    }
    
那么这样我们就能很快地构建一个静态链表

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    typedef struct Node{
        struct Node * next;
    }Node;
    
    
    typedef struct Teacher{
        int age;
        char name[64];
        struct Node node;
    }Teacher;
    
    int main() {
        Teacher t1,t2,t3;
        t1.node.next = &(t2.node);
        t2.node.next = &(t3.node);
        t2.node.next = NULL;
        return 0;
    }
    
那么假如我们只知道指针的地址，我们怎么得到节点中其他元素？回忆一下我们在讲结构体的时候讲过的，结构体中的每一个都有自己的相对偏移量，我们知道一个的偏移量就能求出其他元素的偏移量。

**改进：**

但是人总是很懒，每次都算偏移量真的很烦人，怎么办呢？我们想了一个办法，对这个链表进行改进。
我们如果把链表的指针域放在第一个那么是不是意味着我们每次指向下一个节点的时候我们其实也同时指向了这个指针域，这样我们就不用再算偏移量了。

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    typedef struct Node{
        struct Node * next;
    }Node;
    
    
    typedef struct Teacher{
        struct Node node;
        int age;
        char name[64];
    
    }Teacher;
    
    int main() {
        Teacher t1,t2,t3;
        t1.node.next = &(t2.node);
        t2.node.next = &(t3.node);
        t2.node.next = NULL;
        return 0;
    }


## **0X13 函数指针**

### **一、概念：**

函数指针揭示了一个概念，就是：函数也是有地址的。我们可通过这个地址带来找到这个函数并对其进行操控。

### **二、定义**

函数指针的定义我们要拿我之前说的数组指针的定义来类比学习，我们函数指针的类型是严格按照函数的返回类型定义的，我们函数指针的括号中有没有参数也是严格按照原函数是否有参数定义的。

**我们首先使用的是最简单的方法，就是直接声明一个函数指针**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    void test(){
        printf("hello world!");
    }
    int main() {
        void (*p)() = NULL;
        p = test;
        p();
        return 0;
    }
    


当然我们还可以将这个函数写的更复杂：

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    void test(){
        printf("hello world!");
    }
    
    int maxValue(int a , int b){
        return a > b? a : b ;
    }
    int main() {
        int res = 0;
    
        int (*p)(int,int) = NULL;
        p = maxValue;
        res =  p(19,20);
        printf("%d\n",res);
        return 0;
    }

**补充一下函数指针的另一种定义方法（依然是模仿数组的定义）：**

**1. 下面是先定义函数类型在定义函数指针的方法：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    void test(){
        printf("hello world!");
    }
    
    int maxValue(int a , int b){
        return a > b? a : b ;
    }
    int main() {
        int res = 0;
        typedef int (func)(int,int);
        func *p = maxValue;
        res =  p(19,20);
        printf("%d\n",res);
    
    }
    
**2. 下面是先定义函数指针类型再定义指针：**

先定义了函数的指针类型，然后用这个类型去定义一个指针，并且对指针赋值。

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    void test(){
        printf("hello world!");
    }
    
    int maxValue(int a , int b){
        return a > b? a : b ;
    }
    int main() {
        int res = 0;
        typedef int(*point)(int,int);
        point p = maxValue;
        res =  p(19,20);
        printf("%d\n",res);
    }


**定义函数指针数组：**

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int func1(){
        printf("func1\n");
    }
    
    int func2(){
        printf("func2\n");
    }
    
    int func3(){
        printf("func3\n");
    }
    
    int func4(){
        printf("func4\n");
    }
    
    int main() {
        int res = 0;
        int i = 0;
    
        int(*parray[4])();
        parray[0] = func1;
        parray[1] = func2;
        parray[2] = func3;
        parray[3] = func4;
    
        for(i=0;i<4;i++){
            parray[i]();
        }
    
    }

**结果：**

    func1
    func2
    func3
    func4


**区别：**

**函数指针和指针函数的区别：**

1.函数指针是指向函数的指针
2.指针函数是返回值为指针 


### **三、函数指针做函数参数（回调函数）**

**函数指针做函数参数的最佳实践就是回调函数，怎么理解回调函数呢？**

其实很简单，我们首先需要创建一个模具，这个模具有两个参数（以最简单的为例），其中有一个是函数指针参数，另一个是普通的指针参数。这个模具的作用就是将这个普通参数指向的变量作为参数传递给这个函数指针指向的函数。于是我们相当于提供了一个接口，传递什么就调用什么，而不用担心传递的是不是符合函数的要求（因为我们已经把适配的函数也传递进去了）那么我们传进去的这个函数就叫做回调函数。 

**示例代码：**

    #include "stdio.h"
    #include "stdlib.h"
    #include "string.h"
    
    
    int callbackTest(int *a, int *b, int(*p)(int,int)) {
        return p(*a,*b);
    }
    
    int addTest(int a , int b){
        return a+b;
    }
    
    int main() {
        int a = 10;
        int b = 11;
        int res = 0;
        res = callbackTest(&a,&b,&addTest);
        printf("%d\n",res);
        return 0;
    }
    

**结果：**

    21


## **0X14 预处理**

### **一、基本概念：**

C语言对源程序的处理顺序是： 

1.预处理
2.编译
3.汇编
4.链接

预处理是程序代码进行编译以前由预处理器对程序源码进行的处理，整个过程不对程序源码的语法进行解析

### **二、文件包含指令年策略：**

**1.原理：**
include 是文件包含指令，在预处理的过程中会将被包含的文件全部加载进主文件中

 **图示:**

![此处输入图片的描述][26]

**2.<> 和 "" 的区别**
(1) <> 意味着系统会直接在指定的目录下寻找头文件
(2) "" 系统会先在c文件的目录下寻找，没有找到才会去指定目录

### **三、宏定义**

#### **1.普通宏定义：**

如果我们经常用一个数值，我们为了方便就能直接将其定义为一个不变的变量

    int const num = 100;

但是这样做有一个缺陷，就是在不支持c99的编译器中我们还是不能直接在数组的定义中直接使用num ，因为名义上num 还是一个变量，而在c99中数组的定义中是不能出现变量的，于是我们还是要使用宏定义

    #define NUM 100（注意这里没有分号）
    
定义完成以后，在预处理的时候，编译器会将代码中所有的 NUM 都替换成 100 这个常量

**一些注意点：**

1.宏名一般都是大写，以便区分于变量
2.宏定义可以是常数或者是表达式
3.宏定义不做语法检测，只有在编译器解析到被宏展开的语句的时候才会报错
4.宏定义不是C语言，因此不需要结尾的分号
5.宏名的有效范围是从定义到本源文件结束
6.可以使用 #undef 终止宏定义的作用域
7.宏定义中可以引用已经定义过的宏名
8.宏定义不重视作用域，从宏定义开始的行到文件末尾都是宏的作用域，我们可以用 #undef 来卸载这个宏
9.宏定义是没有数据类型的

#### **2.宏函数定义：**
    
    #define SUM(x,y) ((x)+(y))
    
**一些注意点：**

1.用括号括住每一个参数并括住函数的整体定义
2.宏名不能有空格，但是替换字符串可以有空格
3.大写字母表示宏的函数名

### **四、条件编译**

#### **1.基本概念：**

正常情况下，源程序中所有的行都对会参加编译，但是有时候希望对部分源程序只在特定的条件下编译，而在其他条件下不编译

#### **2.几种情况：**

**(1)测试存在**

    #ifdef  标识符
        程度段1
    #else
        程序段2
    #endif
    
**(2)测试不存在**

    # ifndef
        程序段1
    #else 
        程序段2
    #endif

**(3)根据表达式：**

    #if 表达式
        程序段1
    #else
        程序段2
    #endif

那这些有什么用呢？我们可以用它来防止头文件被重复包含

**示例代码：**

    #ifndef xxxx
    #define xxxx
    //宏定义
    //结构体
    //...
    
    #endif
    
#### **3.特殊的宏**

C编译器给我们提供了几个特殊的宏，我们无需自己定义就能直接使用

`__FILE__` 宏所在文件的文件名
`__LINE__` 当前所在的行号
`__DATE__` 代码编译的日期
`__TIME__` 代码编译的时间


这个我们可以在报错的时候使用这种内定的宏，实现返回文件名以及出错行数


## **0X15 库的封装和使用**

### **一、基本概念：**

库是已经写好的、成熟的、可复用的代码，在我们开发应用的过程中，我们经常会遇到一些公共代码需要反复使用，我们就可以将其编译为库文件

库可以看做是.o文件的集合，我们将这些文件进行打包压缩以后行程一个库文件

### **二、实现静态库**

**那我们怎么才能自己写一个静态库呢？**

我们新建一个项目，在头文件中写一个 .h 文件 在源文件中写一个 .c 文件 ，这两个文件的文件名要一样，.h 文件中只进行函数的声明，.c 中要包含.h文件并写上函数的实现，我们在项目的属性中选择静态库lib，然后重新生成项目，那么在与该目录同级的目录中就生成了一个 .lib  文件，我们再给别人用的时候除了要给Lib还要把.h 文件同时给他，因为没有.h对方也不知道你这个 lib 文件里面可用哪些函数

**(以上方法是针对VS 的，其他编译器可能有所不同)**


### **三、静态库的优缺点**

1.静态库对函数库的链接是放在编译时期完成的，而静态库在程序的链接阶段就被完全复制到了程序中
2.程序运行时与库再无瓜葛，移植方便
3.但是浪费了空间和资源
4.后期的修改和维护比较困难


### **四、动态库应运而生**

只有在程序运行的时候才会去加载动态库中的部分函数，而不是像静态库一眼不管三七二十一全部加载。

动态库的基本创建方式和静态库一样，就是我们需要添加一个导出函数,在 .h 文件的函数声明以前写这个

    __declspec(dllexport)

然后我们就能在项目外使用这个函数了

**区别：**

静态库和动态库的lib 文件不同，动态库的lib 只存储着导出函数和一些变量的声明，具体实现在.dll文件中

**如何引用动态库？**

将我们生成的动态库 .dll .lib .h 放到C源程序的目录下
在C文件的头部添加这样一段话

    #program comment(lib,"./mydll.lib");

程序的寻找过程是这样的，首先检查 lib 文件中的导出函数，然后去dll 中寻找实现。

## **0X16 结语**

花了一段时间仔细的梳理了一下C语言的种种，推翻之前自己对C语言的肤浅认识，完善了自己的编程的知识体系，主要还是训练自己对指针的理解和运用，还谈论了一些C语言基于指针的高级话题，比如数组指针、 函数指针、回调函数等，可能篇幅有些长，但是代码都是我手撸的，图是我手画的，当然其中还夹杂着自己的一些理解，除了自己备忘以外，也希望能对和我有相同目的的人有所帮助。


  [1]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%86%85%E5%AD%98%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B%E7%A4%BA%E6%84%8F%E5%9B%BE.png
  [2]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%9B%9B%E5%8C%BA%E7%9A%84%E8%AF%A6%E7%BB%86%E5%88%86%E6%9E%90.png
  [3]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BB%A3%E7%A0%81%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B1.png
  [4]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BB%A3%E7%A0%81%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B2.png
  [5]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BB%A3%E7%A0%81%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B3.png
  [6]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BB%A3%E7%A0%81%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B4.png
  [7]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BB%A3%E7%A0%81%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B5.png
  [8]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BB%A3%E7%A0%81%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B6.png
  [9]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%A0%86%E5%8C%BA%E7%9A%84%E7%90%86%E8%A7%A31.png
  [10]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%A0%86%E5%8C%BA%E7%9A%84%E7%90%86%E8%A7%A32.png
  [11]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%A0%86%E5%8C%BA%E7%9A%84%E7%90%86%E8%A7%A33.png
  [12]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%87%BD%E6%95%B0%E8%B0%83%E7%94%A8.gif
  [13]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E9%87%8E%E6%8C%87%E9%92%88%E6%A8%A1%E5%9E%8B.png
  [14]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%B8%80%E7%BA%A7%E6%8C%87%E9%92%88%E7%9A%84%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B.png
  [15]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E9%80%92%E5%BD%92%E5%85%A5%E6%A0%88%E6%A8%A1%E5%9E%8B.png
  [16]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E9%80%92%E5%BD%92%E5%87%BD%E6%95%B0%E8%B0%83%E7%94%A8%E6%A8%A1%E5%9E%8B.png
  [17]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BB%A3%E7%A0%81%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B2.png
  [18]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BA%8C%E7%BA%A7%E6%8C%87%E9%92%88%E5%81%9A%E8%BE%93%E5%85%A5%E7%9A%84%E7%AC%AC%E4%B8%89%E7%A7%8D%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B%E7%A4%BA%E6%84%8F%E5%9B%BE.png
  [19]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BB%A3%E7%A0%81%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B3.png
  [20]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%A4%9A%E7%BB%B4%E6%95%B0%E7%BB%84%E6%9C%AC%E8%B4%A8.png
  [21]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BA%8C%E7%BB%B4%E6%95%B0%E7%BB%84%E7%9A%84%E5%A4%9A%E7%A7%8D%E8%A1%A8%E7%A4%BA.png
  [22]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BB%A3%E7%A0%81%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B3.png
  [23]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E4%BB%A3%E7%A0%81%E5%9B%9B%E5%8C%BA%E6%A8%A1%E5%9E%8B3.png
  [24]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E7%BB%93%E6%9E%84%E4%BD%93%E5%A5%97%E4%B8%80%E7%BA%A7%E6%8C%87%E9%92%88%E5%86%85%E5%AD%98%E5%88%86%E5%B8%83.png
  [25]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E7%BB%93%E6%9E%84%E4%BD%93%E4%BA%8C%E7%BA%A7%E6%8C%87%E9%92%88%E7%9A%84%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B.png
  [26]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E9%A2%84%E5%A4%84%E7%90%86include.png