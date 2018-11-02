---
title: C++ 的 Eigen 类库学习
date: 2018-10-12 00:03:18
tags: 编程 C++ 类库 
categories: 编程
---


## **一、Eigen 是什么**

Eigen 是一个 C++ 中的用于科学计算（矩阵）的类库，提供了非常方便的接口，最近我们小组需要做一个项目，里面涉及到很多的矩阵的计算，于是我打算使用这个类库结合我的 CLion 写代码


## **二、如何配置**

下载最新的 release 安装包 解压到项目的目录下（当然任何目录都可以，我习惯上解压到项目的目录下，这样比较方便管理），然后我们配置我们的 CMakeLists.txt


<!-- more -->

**CMakeLists.txt**
        
    
    cmake_minimum_required(VERSION 3.10)
    project(juzhen)
    
    set(CMAKE_CXX_STANDARD 11)
    
    
    include_directories(../eigen)//这里写你的解压目录
    
    
    add_executable(juzhen main.cpp)
    

**main.cpp**
    
    #include <iostream>
    #include <Eigen/Dense>
    
    using namespace Eigen;
    using namespace std;
    
    int main() {
        typedef Matrix<int,3,3> hh;
        MatrixXd m = MatrixXd::Random(3,3);
        //m = (m + MatrixXd::Constant(3,3,1.2)) * 50;
        MatrixXd c = MatrixXd::Constant(3,3,1)*10;
        hh x;
        cout << "m =" << endl << m << endl;
        cout << "c =" << endl << c << endl;
        cout << "x =" << endl << x << endl;
    }
    
这样就能运行啦


## **三、Eigen 基础**

### **1.Matrix 类**

#### **1.基本：**


在Eigen中，所有矩阵和向量都是Matrix模板类的对象。向量只是矩阵的一种特殊情况，有1行或1列。

**Matrix的三个必需模板参数是：**

    Matrix <typename Scalar，int RowsAtCompileTime，int ColsAtCompileTime>

**例如：** Matrix4f是浮动的4x4矩阵

    typedef Matrix <float，4,4> Matrix4f ;
    
假如：我想生成一个 int 型的 3x3 的矩阵

**示例代码：**

    #include <iostream>
    #include <Eigen/Dense>
    
    using namespace Eigen;
    using namespace std;
    
    int main() {
        typedef Matrix<int,3,3> hh;
        hh x;
        cout << "x =" << endl << x << endl;
    }

**结果：**

    x =
    2001839094         36          0
    2001791111    6880932 1275664310
            -1          4    6880948
        

#### **2.向量**

向量只是一种特殊的矩阵而已

**列向量：**

    typedef Matrix <float，3,1> Vector3f ;

**行向量：**

    typedef Matrix <int，1,2> RowVector2i ;


#### **3.动态值**

我们当然不满足在编译时就确定矩阵的大小，于是他给我们也提供了对应的方法

例如，便捷typedef MatrixXd，即具有动态大小的双精度矩阵，定义如下：

    typedef Matrix <double，Dynamic，Dynamic> MatrixXd ;
    
同样，我们定义了一个不言自明的typedef VectorXi如下：

    typedef Matrix <int，Dynamic，1> VectorXi ;

您可以完美地拥有例如具有动态列数的固定行数，如下所示：

    Matrix <float，3，Dynamic>
    
#### **4.构造函数**

默认构造函数始终可用，从不执行任何动态内存分配，也从不初始化矩阵系数。你可以做：

    Matrix3f a;
    MatrixXf b;

a 是一个3乘3的矩阵，具有未初始化系数的普通浮点数
b 是一个动态大小的矩阵，其大小目前是0乘0，并且其系数数组尚未分配。

**注意：**

    Matrix3i d;

这里代表声明了一个 Int 型的 3x3 的矩阵，也就是说 最后的f 代表的是 float 


对于矩阵，始终首先传递行数。对于矢量，只需传递矢量大小。它们使用给定的大小分配系数数组，但不自行初始化系数：

    MatrixXf a（10,15）;
    VectorXf b（30）;

a 是一个10x15动态大小的矩阵，具有已分配但当前未初始化的系数。
b 是一个大小为30的动态大小向量，具有已分配但当前未初始化的系数

**注意：**

    RowVector2d e(1.0,2.0);

这个代表生成的是一个行向量（不加 row 是列向量）


#### **5.系数访问器**

**示例代码：**


    #include <iostream>
    #include <Eigen/Dense>
    
    using namespace Eigen;
    using namespace std;
    
    int main()
    {
        MatrixXd m(2,2);
        m(0,0)= 3;
        m(1,0)= 2.5;
        m(0,1)= -1;
        m(1,1)= m(1,0)+ m(0,1);
        std :: cout << "这是矩阵m：\n" << m << std :: endl;
    
        VectorXd v(2);
        v(0)= 4;
        v(1)= v(0) - 1;
        std :: cout << "这是向量v:\n" << v << std :: endl;
    }

**结果：**

    这是矩阵m：
      3  -1
    2.5 1.5
    这是向量v:
    4
    3


#### **6.逗号初始化**

**示例代码：**

    int main()
    {
        Matrix3f m;
        m << 1,2,3,
            4,5,6,
            7,8,9;
        std :: cout << m <<endl;
    }

**结果：**

    1 2 3
    4 5 6
    7 8 9


#### **7.调整**

可以通过rows（），cols（）和size（）检索矩阵的当前大小。这些方法分别返回行数，列数和系数数。调整动态大小矩阵的大小由resize（）方法完成。

**示例代码：**

    
    int main()
    {
    
        MatrixXd m(2,5);
        m.resize(4,3);
        std :: cout << "矩阵m的大小" << m.rows() << "x" << m.cols()<< std :: endl;
        std :: cout << "它有" << m.size()<< "系数" << std :: endl;
    
    
        VectorXd v(2);
        v.resize(5);
        std :: cout << "向量v的大小为" << v.size()<< std :: endl;
        std :: cout << "作为矩阵,v的大小" << v.rows()<< "x" << v.cols()<< std :: endl;
    
    }

**结果：**
    
    矩阵m的大小4x3
    它有12系数
    向量v的大小为5
    作为矩阵,v的大小5x1


#### **8.分配和调整大小**

这里实际上是个C++ 中等号的重载

Eigen自动调整左侧的矩阵大小，使其与右侧大小的矩阵大小相匹配。例如：

**示例代码：**

    int main()
    {
    
        MatrixXf a(2,2);
        std :: cout << "a is size " << a.rows() << "x" << a.cols()<< std :: endl;
        MatrixXf b(3,3);
        a = b;
        std :: cout << "a现在大小" << a.rows()<< "x" << a.cols()<< std :: endl;
    }
    
**结果:**

    a is size 2x2
    a现在大小3x3


## **四、矩阵和向量算数**

### **1.加减**

二元运算符+如 a+b
二元运算符 - 如 a-b
一元算子 - 如同 -a
复合运算符+ =如 a+=b
复合运算符 - =如 a-=b


**注意：**

运算符的左值和右值，必须要有相同的类型和相同数量的行列

**示例代码：**

    int main()
    {
    
        Matrix2d a;
        a << 1,2,
        3,4;
        MatrixXd b(2,2);
        b << 2,3,
        1,4;
        std :: cout << "a + b = \n" << a + b << std :: endl;
        std :: cout << "a  -  b = \n" << a  -  b << std :: endl;
        std :: cout << "做一个+ = b;" << std :: endl;
        a += b;
        std :: cout << "现在 a = \n" << a << std :: endl;
        Vector3d v(1,2,3);
        Vector3d w(1,0,0);
        std :: cout << "-v + w - v = \n" <<  -v + w - v << std :: endl;
    }

### **2.标量的乘除**

二元运算符*如 matrix*scalar
二元运算符*如 scalar*matrix
二元运算符/如 matrix/scalar
复合运算符* =如 matrix*=scalar
复合运算符/ =如 matrix/=scalar


**示例代码：**


    int main()
    {
    
        Matrix2d a;
        a << 1,2,
        3,4;
        Vector3d v(1,2,3);
        std :: cout << "a * 2.5 = \n" << a * 2.5 << std :: endl;
        std :: cout << "0.1 * v = \n" << 0.1 * v << std :: endl;
        std :: cout << "做v *= 2;" << std :: endl;
        v *= 2;
        std :: cout << "现在v = \n" << v << std :: endl;
    }

**结果：**

    a * 2.5 =
    2.5   5
    7.5  10
    0.1 * v =
    0.1
    0.2
    0.3
    做v *= 2;
    现在v =
    2
    4
    6

### **3.转置、共轭、伴随**

通过成员函数transpose（），conjugate（）和adjoint（）分别获得矩阵或向量的转置$ a ^ T $，共轭$ \ bar {a} $和伴随（即，共轭转置）。$ a ^ * $$ a $


**示例代码：**


    int main()
    {
    
        MatrixXf a = MatrixXf :: Random(2,2);
        cout << "这是矩阵a \n" << a << endl;
        cout << "这是矩阵a 的转置\n" << a.transpose()<< endl;
        cout << "这是矩阵a 的共轭\n" << a.conjugate()<<endl;
        cout << "这是矩阵a 的伴随\n" << a.adjoint()<< endl;

    }

**结果：**

    这是矩阵a
    -0.997497 -0.613392
     0.127171  0.617481
    这是矩阵a 的转置
    -0.997497  0.127171
    -0.613392  0.617481
    这是矩阵a 的共轭
    -0.997497 -0.613392
     0.127171  0.617481
    这是矩阵a 的伴随
    -0.997497  0.127171
    -0.613392  0.617481


### **4.矩阵 - 矩阵和矩阵 - 向量乘法**

**示例代码：**

    int main()
    {
    
        Matrix2d mat;
        mat << 1, 2,
                3, 4;
        Vector2d u(-1,1), v(2,0);
        std::cout << "Here is mat*mat:\n" << mat * mat << std::endl;
        std::cout << "Here is mat*u:\n" << mat*u << std::endl;
        std::cout << "Here is u^T*mat:\n" << u.transpose()*mat << std::endl;
        std::cout << "Here is u^T*v:\n" << u.transpose()*v << std::endl;
        std::cout << "Here is u*v^T:\n" << u*v.transpose() << std::endl;
        std::cout << "Let's multiply mat by itself" << std::endl;
        mat = mat*mat;
        std::cout << "Now mat is mat:\n" << mat << std::endl;
    
    }

**结果：**

    Here is mat*mat:
     7 10
    15 22
    Here is mat*u:
    1
    1
    Here is u^T*mat:
    2 2
    Here is u^T*v:
    -2
    Here is u*v^T:
    -2 -0
     2  0
    Let's multiply mat by itself
    Now mat is mat:
     7 10
    15 22


## **五、高级初始化**

### **1.将矩阵进行拼接**

请记住，必须先设置大小，然后才能使用逗号初始值设定项

**示例代码：**


    int main()
    {
    
        RowVectorXd vec1(3);
        vec1 << 1, 2, 3;
        std::cout << "vec1 = " << vec1 << std::endl;
        RowVectorXd vec2(4);
        vec2 << 1, 4, 9, 16;
        std::cout << "vec2 = " << vec2 << std::endl;
        RowVectorXd joined(7);
        joined << vec1, vec2;
        std::cout << "joined = " << joined << std::endl;
    
    }

**结果：**

    vec1 = 1 2 3
    vec2 =  1  4  9 16
    joined =  1  2  3  1  4  9 16


**我们可以使用相同的技术来初始化具有块结构的矩阵。**

**实例代码：**

    int main()
    {
    
        MatrixXf matA(2, 2);
        matA << 1, 2, 3, 4;
        MatrixXf matB(4, 4);
        matB << matA, matA/10, matA/10, matA;
        std::cout << matB << std::endl;
    
    }

**结果：**

      1   2 0.1 0.2
      3   4 0.3 0.4
    0.1 0.2   1   2
    0.3 0.4   3   4


更高级的填充：

**示例代码：**


    int main()
    {
    
        Matrix3f m;
        m.row(0) << 1, 2, 3;
        m.block(1,0,2,2) << 4, 5, 7, 8;
        m.col(2).tail(2) << 6, 9;
        std::cout << m;
    
    }
    
**结果：**

    1 2 3
    4 5 6
    7 8 9


### **2.特殊矩阵**


矩阵和数组都有特殊方法 Zero()


**示例代码：**

    
    int main()
    {
        std::cout << "A fixed-size array:\n";
        Array33f a1 = Array33f::Zero();
        std::cout << a1 << "\n\n";
        std::cout << "A one-dimensional dynamic-size array:\n";
        ArrayXf a2 = ArrayXf::Zero(3);
        std::cout << a2 << "\n\n";
        std::cout << "A two-dimensional dynamic-size array:\n";
        ArrayXXf a3 = ArrayXXf::Zero(3, 4);
        std::cout << a3 << "\n";
    
    }

**结果：**

    A fixed-size array:
    0 0 0
    0 0 0
    0 0 0
    
    A one-dimensional dynamic-size array:
    0
    0
    0
    
    A two-dimensional dynamic-size array:
    0 0 0 0
    0 0 0 0
    0 0 0 0
    

**类似地:**

1.静态方法Constant（value）将所有系数设置为value。如果需要指定对象的大小，则附加参数将在value参数之前，如MatrixXd::Constant(rows, cols, value)。

2.方法Random（）用随机系数填充矩阵或数组。

3.可以通过调用Identity（）获得单位矩阵; 此方法仅适用于Matrix，而不适用于Array，因为“单位矩阵”是线性代数概念。

4.LinSpaced方法（大小，低，高）仅适用于向量和一维数组;

它产生一个指定大小的向量，其系数在low和之间等间隔high。



**Eigen定义了实用函数**

如setZero（），MatrixBase :: setIdentity（）和DenseBase :: setLinSpaced（），可以方便地执行此操作。以下示例对比了构造矩阵的三种方法

使用静态方法和赋值，使用静态方法和逗号初始化程序，或使用setXxx（）方法。


**示例代码：**


    int main()
    {
    
        const int size = 6;
        MatrixXd mat1(size, size);
        mat1.topLeftCorner(size/2, size/2)     = MatrixXd::Zero(size/2, size/2);
        mat1.topRightCorner(size/2, size/2)    = MatrixXd::Identity(size/2, size/2);
        mat1.bottomLeftCorner(size/2, size/2)  = MatrixXd::Identity(size/2, size/2);
        mat1.bottomRightCorner(size/2, size/2) = MatrixXd::Zero(size/2, size/2);
        std::cout << mat1 << std::endl << std::endl;
    
    
        MatrixXd mat2(size, size);
        mat2.topLeftCorner(size/2, size/2).setZero();
        mat2.topRightCorner(size/2, size/2).setIdentity();
        mat2.bottomLeftCorner(size/2, size/2).setIdentity();
        mat2.bottomRightCorner(size/2, size/2).setZero();
        std::cout << mat2 << std::endl << std::endl;
    
    
        MatrixXd mat3(size, size);
        mat3 << MatrixXd::Zero(size/2, size/2), MatrixXd::Identity(size/2, size/2),
                MatrixXd::Identity(size/2, size/2), MatrixXd::Zero(size/2, size/2);
        std::cout << mat3 << std::endl;
    
    }


**结果：**

    0 0 0 1 0 0
    0 0 0 0 1 0
    0 0 0 0 0 1
    1 0 0 0 0 0
    0 1 0 0 0 0
    0 0 1 0 0 0
    
    0 0 0 1 0 0
    0 0 0 0 1 0
    0 0 0 0 0 1
    1 0 0 0 0 0
    0 1 0 0 0 0
    0 0 1 0 0 0
    
    0 0 0 1 0 0
    0 0 0 0 1 0
    0 0 0 0 0 1
    1 0 0 0 0 0
    0 1 0 0 0 0
    0 0 1 0 0 0



### **3.用作临时对象**

静态方法如Zero（）和Constant（）可用于在声明时或在赋值运算符的右侧初始化变量。您可以将这些方法视为返回矩阵或数组; 实际上，它们**返回所谓的表达式对象**，在需要时可以计算矩阵或数组。


**示例代码：**
    
    int main()
    {
    
        MatrixXd m = MatrixXd::Random(3,3);
        m = (m + MatrixXd::Constant(3,3,1.2)) * 50;
        cout << "m =" << endl << m << endl;
        VectorXd v(3);
        v << 1, 2, 3;
        cout << "m * v =" << endl << m * v << endl;
    }

**结果：**
    
    m =
    10.1251 90.8741 45.0291
    66.3585 68.5009 99.5962
    29.3304 57.9873  92.284
    m * v =
    326.961
    502.149
    422.157

## **六、数组**


### **1.概念：**

Array是一个类模板，采用与Matrix相同的模板参数

    Array <typename Scalar，int RowsAtCompileTime，int ColsAtCompileTime>

我们采用的形式是ArrayNt形式的typedef代表一维数组，其中N和t是大小和标量类型。对于二维数组，我们使用ArrayNNt形式的typedef。

**下表显示了一些示例：**

    Array<float,Dynamic,1>                            ArrayXf 
    Array<float,3,1>                                  Array3f 
    Array<double,Dynamic,Dynamic>                     ArrayXXd 
    Array<double,3,3>                                 Array33d 
    
### **2.访问Array中的值**

括号运算符被重载以提供对数组系数的写和读访问，就像使用矩阵一样。此外，<<操作符可用于初始化数组（通过逗号初始化程序）或打印它们。

**示例代码：**

    
    #include <iostream>
    #include <Eigen/Dense>
    
    using namespace Eigen;
    using namespace std;
    
    int main() {
    
        ArrayXXf  m(2,2);
    
        // assign some values coefficient by coefficient
        m(0,0) = 1.0; m(0,1) = 2.0;
        m(1,0) = 3.0; m(1,1) = m(0,1) + m(1,0);
    
        // print values to standard output
        cout << m << endl << endl;
    
        // using the comma-initializer is also allowed
        m << 1.0,2.0,
                3.0,4.0;
    
        // print values to standard output
        cout << m << endl;
    }
    
    
**结果：**

    1 2
    3 5
    
    1 2
    3 4


### **3.加减**

添加和减去两个数组与矩阵相同。如果两个阵列具有相同的大小，则该操作有效，并且以系数方式进行加法或减法。

数组还支持表单的表达式，该表达式为数组中的array + scalar每个系数添加标量。这提供了一种不能直接用于Matrix对象的功能。

**示例代码：**

    #include <Eigen/Dense>
    #include <iostream>
    using namespace Eigen;
    using namespace std;
    int main()
    {
      ArrayXXf a(3,3);
      ArrayXXf b(3,3);
      a << 1,2,3,
           4,5,6,
           7,8,9;
      b << 1,2,3,
           1,2,3,
           1,2,3;
           
      // Adding two arrays
      cout << "a + b = " << endl << a + b << endl << endl;
      // Subtracting a scalar from an array
      cout << "a - 2 = " << endl << a - 2 << endl;
    }

**结果：**

    a + b =
     2  4  6
     5  7  9
     8 10 12
    
    a - 2 =
    -1  0  1
     2  3  4
     5  6  7
    
    
### **4.数组乘法**

首先，当然你可以用一个标量乘以一个数组，这与矩阵的工作方式相同。数组与矩阵根本不同的是，当你将两个数组相乘时。矩阵将乘法解释为矩阵乘积，并且数组将乘法解释为系数乘积。因此，当且仅当它们具有相同的尺寸时，两个阵列可以相乘。

**示例代码：**
    
    #include <iostream>
    #include <Eigen/Dense>
    
    using namespace Eigen;
    using namespace std;
    
    int main() {
    
        ArrayXXf a(2,2);
        ArrayXXf b(2,2);
        a << 1,2,
                3,4;
        b << 5,6,
                7,8;
        cout << "a * b = " << endl << a * b << endl;
    }
    
**结果：**
    
    a * b =
     5 12
    21 32
    

### **5.其他系数运算**

例如，.abs（）方法取每个系数的绝对值，而.sqrt（）计算系数的平方根。如果你有两个相同大小的数组，你可以调用.min（.）来构造数组，其系数是两个给定数组的相应系数的最小值。以下示例说明了这些操作。

**示例代码：**

    #include <iostream>
    #include <Eigen/Dense>
    
    using namespace Eigen;
    using namespace std;
    
    int main() {
    
        ArrayXf a = ArrayXf::Random(5);
        a *= 2;
        cout << "a =" << endl
             << a << endl;
        cout << "a.abs() =" << endl
             << a.abs() << endl;
        cout << "a.abs().sqrt() =" << endl
             << a.abs().sqrt() << endl;
        cout << "a.min(a.abs().sqrt()) =" << endl
             << a.min(a.abs().sqrt()) << endl;
    
    }

**结果：**
    
    a =
    -1.99499
    0.254341
    -1.22678
     1.23496
    0.340037
    a.abs() =
     1.99499
    0.254341
     1.22678
     1.23496
    0.340037
    a.abs().sqrt() =
     1.41244
    0.504323
      1.1076
     1.11129
    0.583127
    a.min(a.abs().sqrt()) =
    -1.99499
    0.254341
    -1.22678
     1.11129
    0.340037


## **七、在数组和矩阵表达式之间转换**

什么时候应该使用Matrix类的对象？何时应该使用Array类的对象？您不能对数组应用Matrix运算，也不能对矩阵应用Array运算。因此，如果你需要进行矩阵乘法等线性代数运算，那么你应该使用矩阵; 如果你需要进行系数运算，那么你应该使用数组。但是，有时它并不那么简单，但您需要同时使用Matrix和Array操作。在这种情况下，您需要将矩阵转换为数组或反向转换。无论选择将对象声明为数组还是矩阵，都可以访问所有操作。

矩阵表达式有一个**.array（）方法**，可以将它们“转换”为数组表达式，因此可以轻松应用系数方法。相反，数组表达式具有**.matrix（）方法**。与所有Eigen表达式抽象一样，这没有任何运行时成本（假设您让编译器进行优化）。即.array（）和.matrix（）可被用作右值和作为左值。

Eigen禁止在表达式中混合矩阵和数组。例如，您无法直接添加矩阵和数组; 该规则的例外是赋值运算符：允许将矩阵表达式赋给数组变量，或者将数组表达式赋给矩阵变量。

**以下示例说明如何通过使用.array（）方法对Matrix对象使用数组操作**

**示例代码1：**

    #include <iostream>
    #include <Eigen/Dense>
    
    using namespace Eigen;
    using namespace std;
    
    int main() {
    
        MatrixXf m(2,2);
        MatrixXf n(2,2);
        MatrixXf result(2,2);
        m << 1,2,
                3,4;
        n << 5,6,
                7,8;
        result = m * n;
        cout << "-- Matrix m*n: --" << endl << result << endl << endl;
        result = m.array() * n.array();
        cout << "-- Array m*n: --" << endl << result << endl << endl;
        result = m.cwiseProduct(n);
        cout << "-- With cwiseProduct: --" << endl << result << endl << endl;
        result = m.array() + 4;
        cout << "-- Array m + 4: --" << endl << result << endl << endl;
    }
    

**结果：**

    -- Matrix m*n: --
    19 22
    43 50
    
    -- Array m*n: --
     5 12
    21 32
    
    -- With cwiseProduct: --
     5 12
    21 32
    
    -- Array m + 4: --
    5 6
    7 8
    

类似地，如果array1和array2是数组，那么表达式array1.matrix() * array2.matrix()计算它们的矩阵乘积。


**示例代码2：**

    #include <iostream>
    #include <Eigen/Dense>
    
    using namespace Eigen;
    using namespace std;
    
    int main() {
    
        MatrixXf m(2,2);
        MatrixXf n(2,2);
        MatrixXf result(2,2);
        m << 1,2,
                3,4;
        n << 5,6,
                7,8;
    
        result = (m.array() + 4).matrix() * m;
        cout << "-- Combination 1: --" << endl << result << endl << endl;
        result = (m.array() * n.array()).matrix() * m;
        cout << "-- Combination 2: --" << endl << result << endl << endl;
    
    }

**结果：**

    -- Combination 1: --
    23 34
    31 46
    
    -- Combination 2: --
     41  58
    117 170

求矩阵的行列式


## **八、实例应用**

### **1. 求矩阵的行列式**

    m.determinant()

**注意:**矩阵m要是double 类型，要不然报错


下面是我写的一个生成 32x32 位 01 可逆矩阵的类，这里面我在生成32位矩阵的时候使用的是矩阵的拼接技术，但是这样就会出现大量的数组，并且不能用循环实现，很尴尬。

**示例代码：**


    #include <iostream>
    #include <Eigen/Dense>
    #include <cstdlib>
    #include <ctime>
    #include <cmath>
    
    using namespace Eigen;
    using namespace std;
    typedef Matrix <double,32,32> Matrix32d;
    
    
    class cM32{
    
    public:
    
        Matrix32d create(){//返回一个可逆的32x32 随机 01 矩阵
    
            Matrix32d m = createPT32();
            if(check(m)) {
                cout << "可逆 " << "行列式为： " << m.determinant() << endl;
                return m;
            }else{
                cout << "不可逆" << endl;
                create();
            }
        }
    
    
    private:
    
        Matrix32d createPT32(){//创建一个随机的32x32 的01矩阵
            Matrix32d m5;
            Matrix2d* arrayM =  new Matrix2d[256];
            for(int i=0;i<256;i++){
                arrayM[i] = create2();
            }
    
            m5 << arrayM[0], arrayM[1], arrayM[2], arrayM[3], arrayM[4], arrayM[5], arrayM[6], arrayM[7], arrayM[8], arrayM[9], arrayM[10], arrayM[11], arrayM[12], arrayM[13], arrayM[14], arrayM[15], arrayM[16],
                    arrayM[17], arrayM[18], arrayM[19], arrayM[20], arrayM[21], arrayM[22], arrayM[23], arrayM[24], arrayM[25], arrayM[26], arrayM[27], arrayM[28], arrayM[29], arrayM[30], arrayM[31], arrayM[32],
                    arrayM[33], arrayM[34], arrayM[35], arrayM[36], arrayM[37], arrayM[38], arrayM[39], arrayM[40], arrayM[41], arrayM[42], arrayM[43], arrayM[44], arrayM[45], arrayM[46], arrayM[47], arrayM[48],
                    arrayM[49], arrayM[50], arrayM[51], arrayM[52], arrayM[53], arrayM[54], arrayM[55], arrayM[56], arrayM[57], arrayM[58], arrayM[59], arrayM[60], arrayM[61], arrayM[62], arrayM[63], arrayM[64],
                    arrayM[65], arrayM[66], arrayM[67], arrayM[68], arrayM[69], arrayM[70], arrayM[71], arrayM[72], arrayM[73], arrayM[74], arrayM[75], arrayM[76], arrayM[77], arrayM[78], arrayM[79], arrayM[80],
                    arrayM[81], arrayM[82], arrayM[83], arrayM[84], arrayM[85], arrayM[86], arrayM[87], arrayM[88], arrayM[89], arrayM[90], arrayM[91], arrayM[92], arrayM[93], arrayM[94], arrayM[95], arrayM[96],
                    arrayM[97], arrayM[98], arrayM[99], arrayM[100], arrayM[101], arrayM[102], arrayM[103], arrayM[104], arrayM[105], arrayM[106], arrayM[107], arrayM[108], arrayM[109], arrayM[110], arrayM[111],
                    arrayM[112], arrayM[113], arrayM[114], arrayM[115], arrayM[116], arrayM[117], arrayM[118], arrayM[119], arrayM[120], arrayM[121], arrayM[122], arrayM[123], arrayM[124], arrayM[125], arrayM[126],
                    arrayM[127], arrayM[128], arrayM[129], arrayM[130], arrayM[131], arrayM[132], arrayM[133], arrayM[134], arrayM[135], arrayM[136], arrayM[137], arrayM[138], arrayM[139], arrayM[140], arrayM[141],
                    arrayM[142], arrayM[143], arrayM[144], arrayM[145], arrayM[146], arrayM[147], arrayM[148], arrayM[149], arrayM[150], arrayM[151], arrayM[152], arrayM[153], arrayM[154], arrayM[155], arrayM[156],
                    arrayM[157], arrayM[158], arrayM[159], arrayM[160], arrayM[161], arrayM[162], arrayM[163], arrayM[164], arrayM[165], arrayM[166], arrayM[167], arrayM[168], arrayM[169], arrayM[170], arrayM[171],
                    arrayM[172], arrayM[173], arrayM[174], arrayM[175], arrayM[176], arrayM[177], arrayM[178], arrayM[179], arrayM[180], arrayM[181], arrayM[182], arrayM[183], arrayM[184], arrayM[185], arrayM[186],
                    arrayM[187], arrayM[188], arrayM[189], arrayM[190], arrayM[191], arrayM[192], arrayM[193], arrayM[194], arrayM[195], arrayM[196], arrayM[197], arrayM[198], arrayM[199], arrayM[200], arrayM[201],
                    arrayM[202], arrayM[203], arrayM[204], arrayM[205], arrayM[206], arrayM[207], arrayM[208], arrayM[209], arrayM[210], arrayM[211], arrayM[212], arrayM[213], arrayM[214], arrayM[215], arrayM[216],
                    arrayM[217], arrayM[218], arrayM[219], arrayM[220], arrayM[221], arrayM[222], arrayM[223], arrayM[224], arrayM[225], arrayM[226], arrayM[227], arrayM[228], arrayM[229], arrayM[230], arrayM[231],
                    arrayM[232], arrayM[233], arrayM[234], arrayM[235], arrayM[236], arrayM[237], arrayM[238], arrayM[239], arrayM[240], arrayM[241], arrayM[242], arrayM[243], arrayM[244], arrayM[245], arrayM[246],
                    arrayM[247], arrayM[248], arrayM[249], arrayM[250], arrayM[251], arrayM[252], arrayM[253], arrayM[254], arrayM[255];
            return m5;
        }
    
        Matrix2d create2(){//创建 2x2 的01随机矩阵
            Matrix2d m1;
            m1(0,0) = rand()%2;
            m1(1,0) = rand()%2;
            m1(0,1) = rand()%2;
            m1(1,1) = rand()%2;
            return m1;
        }
    
        int check(Matrix32d m){//检查是不是可逆的
    
            if(fabs(m.determinant())){
                return 1;
            }else{
                return 0;
            }
        }
    
    };
    
    int main(){
        srand((unsigned int)time(NULL));
        cM32 *m = new cM32;
        for(int i=0;i<1000;i++){
            m->create();
    
        }
    }
    
    
    
    
    
    
    
    
    
    









