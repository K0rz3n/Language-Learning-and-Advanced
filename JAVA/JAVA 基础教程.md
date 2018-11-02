---
title: JAVA 基础教程
date: 2018-10-8 01:28:18
tags: 编程 JAVA 基础 
categories: 编程
---

## **一、java 编程分为三个方向**

java se  Java 桌面开发
java ee  Java web开发
java me  Java 手机开发

Java se（包括面向对象，数据库编程等） 是 Java ee 的基础，Java ee 的中基部分是 servlet 和 jsp ，这两个属于 MVC 模式，java ee 的高级部分是 struts spring Hibernate Ejb 等，这些都是属于 ssh 框架

下面这幅图展示了java ee 的最终框架

**如图所示：**

![此处输入图片的描述][1]

<!-- more -->

## **二、一些前置知识**

### **1、jdk 中包括哪些内容**

(1) jre：java 的运行环境
(2) javac.exe java 的编译器  
(3) java.exe Java 的解释执行器
(4) java 的类库  3600 多个类


### **2. 用一个最简单的程序说话**

    public class hello{
    	public static void main(String args[]){
    		System.out.println("hello world!");
    	}
    }

(1)public 是公用的意思，在一个文件中只能有一个public 类
(2)hello 是类名，公共类的类名必须和文件名保持一致
(3)main 是函数的入口


### **3.java 编译的一些过程**

.java 文件经过 javac.exe 编译以后形成字节码文件 .class, 然后由解释执行器 java.exe  将字节码文件加载到java 虚拟机中并执行（Java 虚拟机就是形成的自己的运行平台目的是屏蔽不同操作系统的差异）


## **三、Java 基本数据类型**

### **1.java 的基本数据类型和c 、c++ 是一样的**

常见的有

整数：byte short in long(字节分别是 1,2,4,8)
浮点数: float double
布尔: boolean
字符：char (单个字符的类型，如果是多个字符在java中是一个类 String ，属于符合数据类型)

**注意：**

char 类型和整数进行加减运算的时候，我们直接将 char 类型看成是一个整形，运算结果再转化成字符型

### **2.基本数据类型的转换**

(1)数据类型能自动的从低精度向高精度转化，但是不能从高精度向低精度转化
(2)小数在java 中默认是double 类型的，于是我们直接写 float a = 3.4; 是会报错的，这样就是高精度向低精度了，我们应该 float a = 3.4f;
(3)高精度向低精度要强制类型转化  int a = (int)3.4;
(4)java 中不同精度的数据进行运算的时候会自动从低精度转化成高精度 3+4.4 得到的是 double 
(5)Java 从控制台接受的输入最后都是 String 类型，我们需要对其进行转换，通过 Float.parseFloat(a1)

**补充一个：Java 的输入输出**
import java.io.*
InputStreamReader isr = new ImputStreamReader(System.in);
BufferedReader br = new BufferedReader(isr);
System.out.println("请输入：");
String a1 = br.readLine();


## **四、Java 运算符**

由于和 C 语言的运算符保持一致，于是我这里不再重复介绍,

**注意：**
switch 后面条件表达式的数据类型应该和 case 后面的保持一致

## **五、Java 如何去构造一个类**

### **1.一个完整的类是什么样子的**

    package 包名；
    class 类名 extends 父类 implements 接口名{
        成员变量；
        构造方法；
        成员方法；
    }

### **2.如何去实例化一个类对象**

    类名 对象名 = new 类名();

### **3.可以把一个类赋值给另一个类**

一个类会根据它内部的属性和方法来分配一定的空间，实例化只是用数值填充这个空间，然后用变量去指向，赋值只不过是用另一个变量也指向那块空间而已

### **4.如何创建并调用一个类方法**
    
    访问控制修饰符 返回类型 方法名(参数){
        内容；
    }
    
调用方法的时候 Java 使用的是 对象.(点号)方法名(参数)的方法来调用的

> **注意：**
> 
> 1.当我们定义了同样名称的带参数的方法和不带参数的方法以后，我们在调用的时候不带参数 Java 虚拟机也能自动的找到我们不带参数的那个方法
> 2.但是如果我们只是单纯的返回类型不一样的话，java 虚拟机就很难区分了

### **5.如何创建一个类的构造方法**

**示例代码：**

    class Person{
    	int age;
    	String name;
    
    	public Person(int age,String name){
    		this.age = age;
    		this.name = name;
    	}
    
    	public Person(int age){
    		
    		this.age = age;
    	}
    }

>     
> **注意：**
> 1.构造方法名称必学和类名相同
> 2.构造方法可以有多个（参数不同），到时候初始化或根据你传入的参数调用不同的构造方法
> 3.当一个类没有自定义构造方法的时候就会在初始化的时候自动，创建一个空的构造方法，如果定义了，那个原始的就会被覆盖
>4.构造方法没有返回值


### **6.创建一个类变量**

我们有时候需要让每一个类对象都能共享某一个变量，这个时候类变量就应运而生

**定义方法：**

访问控制修饰符 static 变量名 = 值；


**访问方式：**

类名.类变量/对象名.类变量


**示例代码：**

    class Child{
    	int age;
    	String name;
    	static int totle=0; // 这个就是一个类变量，可以被各种的类对象共用
    	public Child(int age,String name){
    		this.age = age;
    		this.name = name;
    	}
    
    	public void  jionGame(){
    		totle++;
    		System.out.println("加入");
    	}
    }



### **7.创建一个类方法**

**1.为什么需要类方法：**

有些时候我们不希望这个类的每一个对象都能访问某个属于类的静态变量（并且这样做的确是很浪费空间的）

**2.类方法是属于所有对象实例的，他的创建方法如下：**

    访问控制修饰符 static  返回类型  方法名(){}

> **注意：**
> 
> 1.类方法中只能访问静态变量

**3.使用方法：**

类名.类方法名  /  对象名.类方法名


## **五、Java 面向对象编程**

### **1.面向对象编程有四大特征：**

#### **1.抽象：**

抽象就是将事物共有的特征提取出来形成一个类的方法


#### **2.封装：**

将抽象出来的数据和对数据的操作封装到一起，我们只希望通过我们封装的方法(提供给外界的接口对数据进行操作)，这就引出了

**访问控制修饰符：**

(1)public:对外公开
(2)protected：对子类以及同一个包中的类公开
(3)默认为空：仅仅向同一个包中的类公开
(4)private ：只有类本身能访问，不对外公开

**包：**

**包的作用：**

1.避免类名的冲突（不同的包中能定义相同的类名，不会打架）
2.很好的管理类
3.控制访问范围

**打包命令：**

    package com.xxx

文件开头有了这句话的时候，这个文件生成的字节码就能放在对应的包里

**包的命名：**

小写字母 用点号分割

**包的引入：**

import 包名

目的是引用包中的类

#### **3.继承：**

继承为了解决代码复用的问题，子类继承了父类以后就拥有了父类的属性和方法

    子类 extends 父类

**注意：**
    
    1.父类的 private 是不能被继承的
    2.子类最多继承一个父类


#### **4.多态：**

我们先来说说重载，和覆盖的概念

**什么是重载？**

重载就是为了实现一个目的就是使用相同的名字去调用不同的方法(当然方法的参数类型什么的不一样)，实际上就是做到了访问方式的统一

**注意：**

(1)方法名相同
(2)参数类型、个数、顺序至少一项不同
(3)方法的返回类型可以不同
(4)方法的修饰符可以不同


**什么是覆盖？**

其实就是子类对父类的方法的重写

> **注意：**
> 
> 1.子类的这个方法要和父类的名称、返回类型、参数一致
> 2.子类方法不能缩小父类方法的访问权限


我们现在来介绍多态：

多态就是子类继承了父类以后，我们可以通过 将子类的实例化对象赋值给父类的方式实例化子类对象

**示例代码：**


    public class Demo{
        public static void main(String[] args){
            Animal an = new Cat();
            an.cry();
            Animal bn = new Dog();
            bn.cry();
        }
    }
    
    public class Animal{
        public void cry（){
            System.out.println("动物叫")；    
        }
    }
    
    public class Cat extends Animal{
        public void cry（){
            System.out.println("猫叫")；    
        }
    }
    
    public class Dog extends Animal{
        public void cry（){
            System.out.println("狗叫")；    
        }
    }
    

我们清楚地看见，我们将子类的对象赋值给父类变量，并且一样能调用

**那么这种机制有什么用？**

我们其实希望在传参的时候保持一致，形参都是父类，这样就非常好的，举一个例子

**示例代码：**


    public class Demo {
        public static void main(String args[]){
            Master master = new Master();
            master.feed(new Dog(),new Bone());
            master.feed(new Cat(),new Fish());
    
        }
    }
    
    
    class Animal {
        String name;
        public void cry(){
            System.out.println("不知道怎么叫");
        }
        public void eat(){
            System.out.println("不知道吃什么");
        }
    }
    
    class Dog extends Animal{
        public void cry(){
            System.out.println("汪汪叫");
        }
        public void eat(){
            System.out.println("吃骨头");
        }
    }
    
    class Cat extends Animal{
    
        public void cry(){
            System.out.println("喵喵叫");
        }
        public void eat(){
            System.out.println("吃鱼");
        }
    }
    
    class Food {
        String name;
        public void getname(){
            System.out.println("不知道是什么");
        }
    }
    
    class Bone extends Food {
        public void getname(){
            System.out.println("骨头");
        }
    }
    
    class Fish extends Food {
        public void getname(){
            System.out.println("鱼");
        }
    }
    
    
    class Master{
        public void feed(Animal an,Food fd){//注意看这个方法
            an.cry();
            fd.getname();
        }
    }



我们看到 Master 在传递参数时的形参是 Animal 这个父类，但是实参是 Cat 或者 Dog 的子类


### **2.抽象类和接口：**

#### **1.抽象类：**

为什么需要抽象类、

当父类的某方法不能确定的时候，我们就将其定义为一个抽象类，使用 abstruct 修饰符修饰

> **注意：**
> 
> 1.当某一个类中有抽象方法的时候这个类也就是一个抽象类，必须要在前面加上 abstruct修饰符
> 2.抽象类吃的子类必须将父类的所有抽象方法全部实现
> 3.抽象类是不用实现里面的抽象方法的，只用定义，让子类去实现好了
> 4.抽象类不能被实例化
> 5.抽象类不一定要包含抽象方法，但是包含抽象方法的类一定是抽象类


#### **2.接口：**

接口就是给出一些没有内容的方法，封装到一起，到某个类要使用的时候再根据具体情况将这些方法写出来

**格式:**

    class 类名 implements 接口名{
        变量；
        方法;
    }

**注意：**

1.当一个类实现了一个接口就要把这个接口的全部方法实现
2.接口不能被实例化
3.接口中的所有方法都不能有主体
4.一个类能实现多个接口
5.接口中的变量不能加任何的修饰符（默认就是 static 的），于是在开发中常常将比较常用的变脸放在接口中作为全局变量使用，访问形式：

    接口名.变量名
6.一个接口不能继承其他的类但是能继承其他接口
7.接口是对java 单继承的一种弥补，



### **3.final：**

final 能用来修饰变量或者方法

**使用条件：**

(1)当父类的某个方法不希望被子类覆盖的时候就可以使用 final 来修饰
(2)当不希望某个类的变量的值被修改就可以使用 final 修饰，但是这种情况必须赋初值
(3)当不希望类被继承的时候可以使用final 来修饰


## **六、Java 数组**

### **1.先介绍一下Java的位运算符**

按位与：&
按位或：|
按位亦或：^
按位取反: ~

### **2.一维数组：**

**形式：**

    数据类型  数组名[] = new 数据类型[元素个数]；

**注意：**

1.除了有普通类型的数组以外，还能有对象数组（反正是一个类型）
2.对象数组在定义以后，赋值时必须再次为每个对象分配空间

### **3.二维数组：**

形式 ：

数据类型 数组名 [][] = new 数据类型 [大小][大小]


## **七、Java 集合类**

### **1.为什么需要集合类**

我们知道数组的长度是不能动态改变的，但是集合类可以做到


### **2.Java 的集合类：**

#### **(1)List 结构的集合类：**

    ArrayList LinkList Vector  Stack
    
**区别：**

1.ArrayList 是异步的 Vector 是同步的 
2.数据增长ist方面，ArrayList 在空间不够的时候会自增长 50%  Vector 会增长 100%
    
**示例代码：**

    package jihe;
    import java.util.*;
    
    public class test3 {
        public static void main(String args[]){
    
            ArrayList al = new ArrayList();//创建一个集合
            Clerk clerk1 = new Clerk(10,"zhangfei",1000);
            al.add(clerk1);//向集合中添加元素
            System.out.println(al.size());
    
            Clerk temp = (Clerk) al.get(0);//取出集合中的元素
            temp.getname();
            al.remove(0);//删除集合中的元素
        }
    }
    
    
    class Clerk {
        private int age;
        private String name;
        private float sal;
    
        public Clerk(int age,String name,float sal){
            this.age = age;
            this.name = name;
            this.sal = sal;
        }
    
        public void getname(){
            System.out.println(this.name);
        }
    }


再给一个比较常见的应用代码：

**示例代码：**

    
    import java.util.*;
    import java.io.*;
    public class demo3 {
        public static void main( String args[]) throws Exception{
    
            EmpManager em = new EmpManager();
            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
            //一个简易的菜单
            while(true){
                System.out.println("请选择一个你要进行的操作");
                System.out.println("1.添加一个雇员");
                System.out.println("2.显示雇员信息");
                System.out.println("3.修改雇员薪水");
                System.out.println("4.删除一个雇员");
                System.out.println("5.退出系统");
    
                String operType = br.readLine();
    
                if(operType.equals("1")){
                    System.out.println("请输入编号：");
                    String empNo =  br.readLine();
                    System.out.println("请输入名字：");
                    String name = br.readLine();
                    System.out.println("请输入薪水");
                    float sal = Float.parseFloat(br.readLine());
    
                    Emp emp = new Emp(empNo,name,sal);
    
                    em.addEmp(emp);
    
                }
                else if(operType.equals("2")){
                    System.out.println("请输入编号：");
                    String empNo =  br.readLine();
                    em.showInfo(empNo);
    
                }
                else if(operType.equals("3")){
                    System.out.println("请");
    
                }
                else if(operType.equals("4")){
    
                }
                else if(operType.equals("5")){
                    System.exit(0);
                }
    
    
            }
    
        }
    }
    
    
    class EmpManager {
    
        private ArrayList al = null;
    
        public EmpManager(){
    
            this.al = new ArrayList();
        }
    
        public void addEmp(Emp emp){
            al.add(emp);
        }
    
        public void showInfo(String empNo){
    
            for(int i=0;i<al.size();i++){
                Emp emp = (Emp)al.get(i);
                if(emp.getEmpNo().equals(empNo)){
                    System.out.println("该员工的编号是:"+emp.getEmpNo());
                    System.out.println("该员工的名字是:"+emp.getName());
                    System.out.println("该员工的薪水是:"+emp.getSal());
                }
            }
        }
    
        public void updateSal(String empNo,float newSal){
            for(int i=0;i<al.size();i++){
                Emp emp = (Emp)al.get(i);
                if(emp.getEmpNo().equals(empNo)){
                    emp.setSal(newSal);
                }
            }
        }
    
        public void delEmp(String empNo){
            for(int i=0;i<al.size();i++){
            Emp emp = (Emp)al.get(i);
            if(emp.getEmpNo().equals(empNo)){
                al.remove(emp);//al.remove(i);
            }
        }
    }
    
    
    }
    
    
    class Emp{
        private String empNo;
        private String name;
        private float sal;
    
    
        public Emp(String empNo, String name, float sal){
            this.empNo = empNo;
            this.name = name;
            this.sal = sal;
    
        }
    
        public String getEmpNo() {
            return empNo;
        }
    
        public void setEmpNo(String empNo) {
            this.empNo = empNo;
        }
    
        public String getName() {
            return name;
        }
    
        public void setName(String name) {
            this.name = name;
        }
    
        public float getSal() {
            return sal;
        }
    
        public void setSal(float sal) {
            this.sal = sal;
        }
        
    }
    
    


#### **(2)Map结构的集合类**

    HashMap  HashTable
    
**区别：**

1.HashMap 是异步的  HashTable 是同步的（线程安全）
2.HashMap   接受空值作为 key 或者 value HashTable 不能

常见用法代码实例

**示例代码：**

    import java.util.*;
    public class demo {
        public static void main(String args[]){
    
            HashMap hm = new HashMap();
            Emp emp1 = new Emp("S001","张三",12.5f);
            Emp emp2 = new Emp("S002","李四",13.9f);
            hm.put("S001",emp1);
            hm.put("S002",emp2);
    
            if(hm.containsKey("S002")){
                System.out.println("有该员工");
                Emp emp = (Emp)hm.get("S002");
                System.out.println("该员工的名字是："+emp.getName());
            }
            else{
                System.out.println("没有该员工");
            }
    
            Iterator it = hm.keySet().iterator();//迭代key 
            while(it.hasNext()){
                String key = it.next().toString();//因为返回来是一个对象，所以我们需要 toString()
                Emp emp = (Emp)hm.get(key);
                System.out.println(emp.getName());
                System.out.println(emp.getEmpNo());
                System.out.println(emp.getSal());
            }
    
    
        }
    }
    
    
    class Emp{
        private String empNo;
        private String name;
        private float sal;
    
    
        public Emp(String empNo, String name, float sal){
            this.empNo = empNo;
            this.name = name;
            this.sal = sal;
    
        }
    
        public String getEmpNo() {
            return empNo;
        }
    
        public void setEmpNo(String empNo) {
            this.empNo = empNo;
        }
    
        public String getName() {
            return name;
        }
    
        public void setName(String name) {
            this.name = name;
        }
    
        public float getSal() {
            return sal;
        }
    
        public void setSal(float sal) {
            this.sal = sal;
        }
    
    
    }
    


#### **(3)set 结构的集合类**

    HashSet  TreeSet

#### **(4)Queue 结构的集合类**

    Queue 接口


## **八、Java 泛型**

### **1.定义：**

泛型的本质是参数化类型，就是将所操作的数据类型指定成一个参数，这中参数化类型可以用在 类、接口和方法的创建中，分别称为泛型类、泛型接口和泛型方法

### **2.为什么要用泛型**

没有泛型的情况下，通过对类型 Object 的引用实现参数类型的“任意化”，但是这样就必须要强制类型转化，也就是说开发者要预先知道参数的类型，这是很困难的，容易造成类型转化错误。


### **3.泛型的优点**

(1)类型安全
(2)向后兼容
(3)层次清晰
(4)效率较高


**示例代码：**

    public class Demo {
        public static void main(String args[]){
          Gen<String> gen1 = new Gen<String>("aaa");
          gen1.showTypeName();

        }
    }
    
    
    class Gen<T>{
        private T o;
        public Gen<T>(T a){
            o = a;
        }
    
        public void showTypeName(){
            System.out.println("类型是："+o.getClass().getName());
        }
    }
    


## **九、Java 异常**

### **1.Java 用两种方法处理异常**

1.在发生异常的地方直接处理
2.将异常抛给调用者，让调用者处理

### **2.异常的分类**

**1.检查性异常：**

程序本身没有问题，外在环境的问题导致程序不能正常编译，比如打开一个不存在的 socket 端口

**2.运行期异常**

说明程序本身有Bug

### **3.异常的处理**

#### **1. try catch**


    try{
        可能存在异常的语句；
    }catch(Exception e){
        e.printStackTrace();
    }
    

#### **2.finally**

finally 属于万能的保险，可以将其放在 catch 之后，这个语句块一定会执行，通常用在文件资源的关闭上

**以下情形 finally 不会被执行：**

(1)finally 块中发生了异常
(2)程序所在的线程死亡
(3)前面的代码中使用了 System.exit()
(4)关闭CPU


**3.throws 将异常抛给调用者**

这个的使用就在方法的后面跟上  throws Exception 就行了，然后再调用函数中得 try  catch 中捕获


## **十、java 的反射机制**

### **1.反射机制中的类：**

java.lang.Class
java.lang.reflect.Constructor
java.lang.reflect.Field
java.lang.reflect.Methods
java.lang.reflect.Modifier

如果我们把第一个看成一个整体的话，后面的几个就分别是这个类的 构造方法、属性、方法、修饰符

### **2.反射机制的作用**

1.反编译： .class -> .java
2.通过反射机制访问Java 的属性和方法和构造方法等

### **3.获取 Class 的三种方式**：


**示例代码1：**

**reflectTest.java**


     package test;
    
    //获取class 类型对象的三种方式
    public class reflectTests {
        public static void main(String[] args) throws ClassNotFoundException {
            //方法一
            Class c1 = Class.forName("test.Employee");//这里一定要写全路径 引用保存内存地址指向堆中的对象，该内存地址表示的是整个类
    
            //方法二
            //Java 中每个类型都有class 属性
    
            Class c2 = Employee.class;
    
            //方法三
            // 任何一个对象都有 getClass 方法
            Employee em = new Employee();
            Class c3 = em.getClass();
    
            // 双等于号两边如果是引用对象的话，比较的是地址
            System.out.println(c1 == c2);
            System.out.println(c3 == c2);
    
    
        }
    }


**Employee.java**
    
    package test;
    
    public class Employee {
        //Field
        private String name;
    
        //Constructor
        public Employee(){}
    
    
        public Employee(String name){
            this.name = name;
        }
        //Methods
        public void work(){
            System.out.println(name+"在工作");
        }
    }
    

**结果：**
    
    true
    true
    
        

**示例代码2：**

     Class c4 = int.class;// 这个C4 就代表 int 类型
     

#### **注意 forName 和 xx.getClass 的区别**

**forName 示例代码：**

    package test2;
    
    public class test02  {
        public static void main(String[] args) throws Exception{
            Class.forName("test2.A");
        }
    }
    
    
    class A{
        static{
            System.out.println("test A");
        }
    }
    
**结果：**

    test A

可以看到我们的静态块被执行了，说明这个是将 A.class 装载到JVM 的过程中

**A.getClass 示例代码：**

    package test2;
    
    public class test02  {
        public static void main(String[] args) throws Exception{
            Class c = A.class;
        }
    }
    
    
    class A{
        static{
            System.out.println("test A");
        }
    }
    
    
结果是没有运行


### **4.通过Class 类型的对象创建class 对象**：

**test3.java**

    package test3;
    public class test3 {
        public static void main(String[] args) throws Exception{
            Class c = Class.forName("test3.Employee");
            Object o = c.newInstance();//调用无参数构造方法
            System.out.println(o);
    
        }
    }
    
**Employee.java**
    
    package test3;
    
    public class Employee {
        //Field
        private String name;
    
        //Constructor
        public Employee(){
            System.out.println("构造方法调用成功");
        }
    
    
        public Employee(String name){
            this.name = name;
        }
        //Methods
        public void work(){
            System.out.println(name+"在工作");
        }
    }
    

**结果：**

    构造方法调用成功
    test3.Employee@4554617c

可见成功的创建了这个对象，调用了无参数构造方法


### **5.可变长参数：**

#### **1.一般形式：**

**示例代码：**


    package test4;
    
    public class changeLen {
        public static void ma(int... a){
            System.out.println("Test");
        }
        public static void main(String[] args){
            ma(1);
            ma(1,2);
            ma(1,2,3);
            ma(1,2,3,4,5,6,7,8,9,0);
    
        }
    }
    
**结果：**

    Test
    Test
    Test
    Test


#### **2.存在精确匹配：**

当存在精确匹配的时候，会优先调用精确匹配的函数，实例如下：

**示例代码：**

    package test4;
    
    public class changeLen {
        public static void m1(int... a){
            System.out.println("Test");
        }
    
        public static void m1(int i){
            System.out.println(i);
        }
    
        public static void main(String[] args){
            m1(1);
            m1(1,2);
            m1(1,2,3);
            m1(1,2,3,4,5,6,7,8,9,0);
    
        }
    }

**结果：**
    
    1
    Test
    Test
    Test


#### **3.可变长参数可等同看成数组**

**示例代码：**

    
    package test4;
    
    public class changeLen {
        public static void m1(int... a){
            System.out.println("Test");
        }
    
        public static void m1(int i){
            System.out.println(i);
        }
    
        public static void m2(String... args){
            for(int i = 0;i<args.length;i++){
                System.out.println(args[i]);
            }
        }
    
    
    
        public static void main(String[] args){
            m1(1);
            m1(1,2);
            m1(1,2,3);
            m1(1,2,3,4,5,6,7,8,9,0);
            m2("体育","音乐","美食","养生");
    
        }
    }

**结果：**

    1
    Test
    Test
    Test
    体育
    音乐
    美食
    养生


> **注意：**
> 
> 同样，在 m2 的参数中可以直接传入一个数组

当然我们就能通过这种方式批量的创建对象了

**示例代码：**
    
      public static void m3(Class... args) throws Exception {
            for(int i = 0;i<args.length;i++){
                Class c = args[i];
                System.out.println(c.newInstance());
            }
        }
    

> **注意:**
> 
> 1.可变长参数只能出现一次，且只能出现在参数列表的最后一位



### **6.IO 和 Properties 的联合应用：**

**示例代码：**

**test5.java**

    package test5;
    
    import java.io.FileInputStream;
    import java.io.FileNotFoundException;
    import java.io.IOException;
    import java.util.Properties;
    
    public class test5 {
        public static void main(String[] args) throws IOException {
            Properties p = new Properties();
            FileInputStream fis = new FileInputStream("E:\\programme\\JavaOJ\\test_ref\\src\\test5\\info");
            p.load(fis);
            fis.close();
    
            String v = p.getProperty("username");
            System.out.println(v);
        }
    }

**info**

    username=K0rz3n

**结果:**

    K0rz3n

> **注意：**
> 
> 1.这种方法常用于配置文件的读取，像以上的特殊内容的配置文件又叫做 属性文件，他规范来讲是以 .properties 结尾的
> 2.key 和 value 之间可以使用 等号 冒号 空格
> 3.如果有多个谁出现在第一个就按照谁做分隔符
>4.我们能将 value 改成类，**使用反射机制动态的创建类对象,这其实是反射机制的核心**


### **7.反射机制获取类的所有属性：**

#### **1.使用getFields()**

**test6.java** 

    package test6;
    import java.lang.reflect.*;
    public class test6 {
        public static void main(String[] args) throws Exception{
            Class c = Class.forName("test6.User");
            Field[] fs = c.getFields();
            System.out.println(fs.length);
            System.out.println(fs[0].getName());
        }
    }
    
    
**User.java**

    package test6;
    
    public class User {
        private String id;
        public int age;
        protected String addr;
        boolean sex;
    }
    

**结果：**
    
    1
    age


我们发现只输出了 Public 属性

#### **2.使用getDeclaredFields()**

**示例代码1：获取所有属性**

**test6.java** 

    package test6;
    import java.lang.reflect.*;
    public class test6 {
        public static void main(String[] args) throws Exception{
            Class c = Class.forName("test6.User");
            Field[] fs = c.getDeclaredFields();
            for(Field field:fs){
                System.out.println(field.getName());
            }
    
        }
    }

**User.java**

    package test6;
    
    public class User {
        private String id;
        public int age;
        protected String addr;
        boolean sex;
    }

**结果：**

    id
    age
    addr
    sex

**示例代码2：获取所有属性类型**

**test6.java** 

    package test6;
    import java.lang.reflect.*;
    public class test6 {
        public static void main(String[] args) throws Exception{
            Class c = Class.forName("test6.User");
            Field[] fs = c.getDeclaredFields();
            for(Field field:fs){
                Class type = field.getType();
                 System.out.println(type.getSimpleName());
            }
        }
    }

**User.java**

    package test6;
    
    public class User {
        private String id;
        public int age;
        protected String addr;
        boolean sex;
    }

**结果：**
    
    String
    int
    String
    boolean
    
**示例代码3：获取所有属性修饰符**

**test6.java**

    package test6;
    import java.lang.reflect.*;
    public class test6 {
        public static void main(String[] args) throws Exception{
            Class c = Class.forName("test6.User");
            Field[] fs = c.getDeclaredFields();
            for(Field field:fs){
                int i = field.getModifiers();
                String strModifier = Modifier.toString(i);
                System.out.println(strModifier);
    
            }
        }
    }


**User.java**

    package test6;
    
    public class User {
        private String id;
        public int age;
        protected String addr;
        boolean sex;
    }
    
**结果：**

    private
    public
    protected


**示例代码4：综合，反编译类的所有属性**

**示例代码：**

    package test6;
    import com.sun.org.apache.xerces.internal.util.SynchronizedSymbolTable;

    import java.lang.reflect.*;
    public class test6 {
        public static void main(String[] args) throws Exception{
            Class c = Class.forName("test6.User");
            Field[] fs = c.getDeclaredFields();
            StringBuffer sb = new StringBuffer();
            sb.append(Modifier.toString(c.getModifiers()) + " class "+c.getSimpleName()+"{\n");
            for(Field field:fs){
                sb.append("\t");
                sb.append(Modifier.toString(field.getModifiers()) + " ");
                sb.append(field.getType().getSimpleName() + " ");
                sb.append(field.getName() + ";\n");
            }
            sb.append("}");
            System.out.println(sb);
        }
    
    }

**User.java**

    package test6;
    
    public class User {
        private String id;
        public int age;
        protected String addr;
        boolean sex;
    }


**结果：**
    
    public class User{
    	private String id;
    	public int age;
    	protected String addr;
    	 boolean sex;
    }
    

### **8.反射机制修改某个特定的属性：**

**示例代码：**

    package test7;
    
    import java.lang.reflect.*;
    
    public class test7 {
    
        public static void main(String[] args) throws Exception {
            //获取类
            Class c = Class.forName("test7.User");
            //获取类的属性
            Field idF = c.getDeclaredField("id");
            Object o = c.newInstance();
    
            //打破封装，能访问到私有属性
    
            idF.setAccessible(true);
            idF.set(o,"100");
            System.out.println(idF.get(o));
    
    
        }
    }

**结果：**

    100
    

**注意：**

反射机制打破了对象的封装性，导致对象的属性不安全

## **十一、I/O 流操作**

### **1.文件流的概念：**

文件程序中是以流的形式操作的

1.输入流：文件到内存
2.输出流：内存到文件


### **2.文件流的分类**

#### **1.字节流：**

可用于读写二进制类型的文件以及任何类型的文件

(1)输入流：InputStream
(2)输出流：OutputStream

#### **2.字符流：**

只能用来读写文本文件

(1)输入流：Reader
(2)输出流：Writer

### **3.I/O的基本操作**

**示例代码：**

    package filetest;
    
    import java.io.*;
    import java.util.List;
    
    public class demo1 {
        public static void main(String args[]){
            
            //检测文件的属性
            
            File f = new File("d:/abc.txt");
            System.out.println("文件的路径是："+f.getAbsolutePath());
            System.out.println("文件的大小是："+f.length()+"byte");
        
            //创建文件
            
            File f  = new File("d:\\create.txt");
            if(!f.exists()){
                try {
                    f.createNewFile();
                    System.out.println("文件创建成功");
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }else{
                System.out.println("文件已经存在不能重复创建");
            }
            
            //创建文件夹
            
             File f = new File("d:\\ff");
             if(f.isDirectory()){
                System.out.println("该文件夹已经存在");
                }else{
                    f.mkdir();
                    System.out.println("文件夹创建成功");
                }
            }
            
            //遍历文件夹下面的文件
            
                File f = new File("D:\\phpstudy");
                if(f.isDirectory()){
                    File list[] = f.listFiles();
                    for(int i=0;i<list.length;i++){
                        System.out.println(list[i].getName());
                    }
                }
        }

    }


### **4.文件的基本操作**


#### **1.从文件中读取内容**

f 文件对象没有读的能力，于是需要使用 FileInputStream 进行包装

**示例代码：**
    
    package filetest;
    
    import java.io.*;
    import java.util.List;
    
    public class demo1 {
        public static void main(String args[]){
            File f = new File("d:\\create.txt");
            FileInputStream fis = null;
            try {
                fis = new FileInputStream(f); // 对f 进行的包装，让其有读的功能
                byte bytes[] = new byte[1024];//字节数组就相当于是一个缓存
                int n =0;
                try {
                    while((n = fis.read(bytes))!=-1){// n 表示实际读取到的字节数
                        String s = new String(bytes,0,n);// 把字节转化成 string
                        System.out.println(s);
    
    
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
    
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            }finally {
                //关闭文件流
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
    
            }
        }
    
    
#### **2.写内容到文件**

**示例代码：**

    package filetest;
    
    import java.io.*;
    import java.util.List;
    
    public class demo1 {
        public static void main(String args[]){
            File f = new File("d:\\dd.txt");
            FileOutputStream fos = null;
    
            try {
                fos = new FileOutputStream(f);
                String s = "HELLO WORLD\r\n";
                String s1 = "世界你好";
                try {
                    fos.write(s.getBytes());
                    fos.write(s1.getBytes());
    
                } catch (IOException e) {
                    e.printStackTrace();
                }
    
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            }
        }
    
#### **3.实现图片的拷贝**

**示例代码：**

    package filetest;
    
    import java.io.*;
    import java.util.List;
    
    public class demo1 {
        public static void main(String args[]){
            File f = new File("d:\\solved.bmp");
            File f1 = new File("e:\\solved.bmp");
            if(!f1.exists()){
                try {
                    f1.createNewFile();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            FileInputStream fis = null;
            FileOutputStream fos = null;
            try {
                fis = new FileInputStream(f);
                fos = new FileOutputStream(f1);
    
                byte bytes[] = new byte[1024];
                int n =0;
                try {
                    while((n = fis.read(bytes))!=-1){
                        String s = new String(bytes,0,n);
                        fos.write(bytes);
                        System.out.println("拷贝成功");
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            }finally {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    

#### **4.文件字符流**

使用字符一次能读两个字节

**示例代码：**

    package filetest;
    
    import java.io.*;
    import java.util.List;
    
    public class demo1 {
        public static void main(String args[]){
    
            File f = new File("d:\\hh.txt");
            File f1 = new File("e:\\hh.txt");
            FileReader fr = null;
            FileWriter fw = null;
            try {
                fr = new FileReader(f);
                fw = new FileWriter(f1);
    
                char[] c = new char[1024];
                int n =0;
                while((n =fr.read(c))!=-1){
                    fw.write(c,0,n);
                }
    
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }finally {
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                try {
                    fw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
       }    
       

#### **5.缓冲字符流**

缓冲字符流能一行一行的读取，这个特性深受广大程序员的喜爱

**示例代码：**
    
    package filetest;
    
    import java.io.*;
    import java.util.List;
    
    public class demo1 {
        public static void main(String args[]){
    
            BufferedReader br = null;
            BufferedWriter bw = null;
    
            try {
                FileReader fr = new FileReader("d:\\hh.txt");
                FileWriter fw = new FileWriter("e:\\aa.txt");
                br = new BufferedReader(fr);
                bw = new BufferedWriter(fw);
                String s = "";
                while((s = br.readLine())!=null){
                   // System.out.println(s);
                    bw.write(s+"\r\n");
                }
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }finally {
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                try {
                    bw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

## **十二、网络编程基础**

**java 网络通信的示意图如下：**

![此处输入图片的描述][2]

### **实现客户端和服务器互发信息**

**server端**

**示例代码：**

    package net;
    
    import java.io.*;
    import java.net.*;
    
    public class server1 {
        public static void main(String args[]) {
            server1 s1 = new server1();
        }
    
        public server1() {
            try {
                ServerSocket ss = new ServerSocket(9999);
                System.out.println("服务端正在监听9999端口");
                Socket s = ss.accept();//这个函数返回一个 sockect 连接，是阻塞的
                InputStreamReader isr = new InputStreamReader(s.getInputStream());
                BufferedReader br = new BufferedReader(isr);
                PrintWriter pw = new PrintWriter(s.getOutputStream(), true);
                InputStreamReader isr2 = new InputStreamReader(System.in);
                BufferedReader br2 = new BufferedReader(isr2);
    
    
                while (true) {
                    String info = br.readLine();
                    System.out.println("客户端发来：" + info);
                    if(info == "bye"){
                        s.close();
                        break;
                    }
                    System.out.println("你想对客户端说：");
                    String response = br2.readLine();
                    pw.println(response);
                }
    
            } catch (IOException e) {
                e.printStackTrace();
            }
    
        }
    }


**Client 端**

**示例代码：**


    package net;
    
    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStreamReader;
    import java.io.PrintWriter;
    import java.net.Socket;
    
    public class client1 {
        public static void main(String args[]){
            client1 c1 = new client1();
        }
    
        public client1(){
            try {
                Socket s = new Socket("127.0.0.1",9999);
                PrintWriter pw = new PrintWriter(s.getOutputStream(),true);//向 socket 写数据，true 表示你即时刷新
                InputStreamReader isr = new InputStreamReader(System.in);
                BufferedReader br = new BufferedReader(isr);
                InputStreamReader isr2 = new InputStreamReader(s.getInputStream());
                BufferedReader br2 = new BufferedReader(isr2);
                while(true){
                    System.out.println("请输入你想对服务器说的话：");
                    String info = br.readLine();
                    pw.println(info);
                    if(info == "bye"){
                        s.close();
                        break;
                    }
                    System.out.println("服务器响应：");
                    String response = br2.readLine();
                    System.out.println(response);
    
                }
    
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }


  [1]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/JAVA%20EE%20server%20%E7%A4%BA%E6%84%8F%E5%9B%BE.png
  [2]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/Java%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B.png