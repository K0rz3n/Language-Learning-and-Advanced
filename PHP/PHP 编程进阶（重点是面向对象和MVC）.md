---
title: PHP 编程进阶（重点是面向对象和MVC）
date: 2018-10-5 22:44:18
tags: 编程 PHP 进阶 
categories: 编程
---

## **一、面向对象的基本概念**

### **1.类和对象**

类是描述一类事物的抽象名称

    class 类名{
        类的描述
    }


对象是一个具体的事物，他必然隶属于某个类

    $obj = new 类名();
<!-- more -->

**示例代码：**
    
    <?php 
    class Dog{
    	var $color = "";
    	function speak(){
    		echo "wangwang";
    	}
    }
    
    $dog1 = new Dog();
    
    $dog1 -> color = "red";
    
    $dog1 -> speak();
   
  

### **2.属性和方法：**

属性本质就是变量，只不过失去了自由，现在只能被这个类或者这个类实例化的对象访问 对象 -> 属性,用于描述这个类的一些共同的特征信息

方法本质就是函数，只不过失去了自由，现在只能被这个类或者这个类实例化的对象访问 对象 -> 方法，用于描述这个类的一些共同的行为动作

### **3.对象的创建形式：**

**1.方法一：**

    $obj = new 类名();

**2.方法二：**
    
    $a = "类名"；
    
    $obj = $a();

**这就是我们所说的“可变类”**

**3. 方法三:**

    $obj = new 类名();
    $obj1 = new $obj();

这个就比较神奇了，我们用一个对象去创建了这个对象所属类的另一个对象

**4.方法四：**

    $obj = new self;

创建本类


### **4.对象的使用：**

使用属性就将其看成是变量，可以取值赋值或者 unset()、 isset()
使用方法就是一个函数

### **5.对象的值传递**

我们知道变量的传值是值传递，只有在使用了& 符号以后才是引用传递，那么我们来做一个实验：

**示例代码：**
    
    <?php
    
    class Dog{
       var $p1 =1;
    }
    
    $t1 = new Dog();
    $t2 = $t1;
    
    echo "t1中的p1 ".$t1->p1."<br>";
    echo "t2中的p1 ".$t2->p1."<br>";
    
    $t1->p1 = 11;
    
    echo "赋值后t1中的p1 ".$t1->p1."<br>";
    echo "赋值后t2中的p1 ".$t2->p1."<br>";
    
    
**结果：**
    
    t1中的p1 1
    t2中的p1 1
    赋值后t1中的p1 11
    赋值后t2中的p1 11
    
我们发现好像并不是这样了，这是为什么呢？

**解释：**

对象的的赋值和普通变量的赋值是有差别的，我们在普通变量赋值的时候这个变量是直接拿到的值，但是对象赋值传递的是**编号**，（我们在生成一个新的对象的时候本省就不是将对象直接给变量，而是将对象的编号赋值给变量，然后变量通过这个编号间接地操纵这个对象，这个编号能通过 var_dump() 看到，但是并不能选择，因此如果我们两个变量拿到的都是同一个编号的话自然他们操纵的就是同一个变量了，我们有上面的结果也就不奇怪了）

**如下图所示：**

![此处输入图片的描述][1]

![此处输入图片的描述][2]

那我们使用引用传递呢？

**示例代码：**
    
    <?php
    
    class Dog{
       var $p1 =1;
    }
    
    $t1 = new Dog();
    $t2 = $t1;
    
    echo "t1中的p1 ".$t1->p1."<br>";
    echo "t2中的p1 ".$t2->p1."<br>";
    
    $t1->p1 = 11;
    
    echo "赋值后t1中的p1 ".$t1->p1."<br>";
    echo "赋值后t2中的p1 ".$t2->p1."<br>";
    
    $t2 = &$t1;
    $t1->p1 = 111;
    
    echo "赋值后t1中的p1 ".$t1->p1."<br>";
    echo "赋值后t2中的p1 ".$t2->p1."<br>";

**结果：**

    t1中的p1 1
    t2中的p1 1
    赋值后t1中的p1 11
    赋值后t2中的p1 11
    赋值后t1中的p1 111
    赋值后t2中的p1 111

**那么赋值的示意图是什么样子的呢？**

**如图所示：**

![此处输入图片的描述][3]



**结论：**

对于对象来讲，传递的都是编号，操纵的也都是编号，如果我们unset() 一个变量，只是对象和编号之间的连线断了，并不影响另一个变量，但是如果我们给引用赋值的对象赋值一个数字，这样就相当于断了编号与原始对象之间的联系，由于引用赋值，用的是一条线，那么另一个也不能用了，但是直接赋值由于是独立的两条线就没有影响。


### **6.静态属性和静态方法**

静态属性和静态方法是只隶属于类的属性和方法,应该通过类来调用

#### **定义格式：**

static 属性名[=xxx]；
static function 方法名(){xxx};


#### **访问格式：**

    类名：：$属性名
    类名：：方法名

我们有了静态属性和静态方法，调用的时候就不必new 对象了，可以直接使用类名调用

**示例代码：**

    <?php
    
    class Dog{
       static $p1 =1;
       static function hello(){
           return "helo world";
       }
    }
    
    echo Dog :: hello()."<br>";
    echo Dog :: $p1;
    
**结果：**
    
    helo world
    1


### **7.this 和 self 关键字**

$this 代表当前类的对象
self  代表类本身

如果你学过Python 的面向对象的话，这里的 this 就相当于 python 中的 self 这里的 self 就是 python 中的 cls

self 在类内部改变静态变量的值很有用
$this 不能用在静态方法中，因为调用静态方法的是类而不是类对象

### **8.类常量**

就是常量，只能放在一个类中，只能用该类去调用，并且这个常量在声明的时候必须赋值

    class 类名{
        const 常量名 = 常量值；
    }


### **9.构造方法**

#### **1.概念：**

就是在一个类进行实例化的时候会自动调用的方法，使用一个固定的名字 `__construct`

构造方法通常是用来简化我们对象属性的初始化工作的

### **10.析构方法**

#### **1.概念：**

就是在一个对象被销毁的时候自动调用的方法，使用一个固定的名字 `__destruct`

#### **2.那么什么时候对象会被销毁呢？**

(1)当网页代码执行结束的时候，所有的对象都会被销毁，（变量会按照生成的顺序逆序的销毁）
(2)当一个对象没有变量引用的时候（引用计数机制）


## **二、类的继承**

### **1.基本概念：**

如果一个类B 自动拥有了另一个类 A 的特征信息，那么我们就说 B 继承了 A 

**示例代码：**

    <?php
    
    class A{
        function speak(){
            echo "wangwang"."<br>";
        }
    }
    
    class B extends A{
        function move(){
            echo "i can move<br>";
        }
    }
    
    $dog = new B();
    $dog->speak();
    $dog->move();

**结果:**

    wangwang
    i can move

### **2.派生**

派生是继承的另一个角度的说法，比如 B 继承了 A, 那么就说 A 派生了 B 

### **3.单继承**

PHP 和大多数的面向对象的语言都是单继承模式，C++ 支持多继承， Python 也支持多继承


### **4.访问控制修饰符**

修饰符就是放在一个属性或者方法的前面用来表明这个属性或者方法的可访问程度的关键字。在 php 中只有三个

public : 谁都能访问，var 在修饰属性的时候和 public 是一样的 
protected ： 只有自己家族内部可以访问（具有继承关系的多个类之间）
private ： 只有自己可以访问

**注意：**
对于属性必须使用修饰符
对于方法可以省略修饰符，省略默认为public 


**示例代码：**
    
    <?php
    
    class Person{
        public $name;
        protected $age;
        private $password;
    
        function __construct($name,$age,$pass){
            $this->name = $name;
            $this->age = $age;
            $this->password = $pass;
        }
    
    }
    
    $xiaoming = new Person("小明",18,"123456");
    echo $xiaoming->name;
    echo $xiaoming->age;
    echo $xiaoming->password;

**结果：**

这段代码只能打印出一个小明，然后就会报错

这验证了我们的修饰符的作用，我们想要访问只能让他自己和我们说

**示例代码：**

    <?php
    
    class Person{
        public $name;
        protected $age;
        private $password;
    
        function __construct($name,$age,$pass){
            $this->name = $name;
            $this->age = $age;
            $this->password = $pass;
        }
    
        function showInfo(){
            echo $this->name."<br>";
            echo $this->age."<br>";
            echo $this->password;
        }
    
    }
    
    $xiaoming = new Person("小明",18,"123456");
    $xiaoming->showInfo();

**结果：**

    小明
    18
    123456

同样如果我们使用一个类去继承这个 Person 类，我么还是这样访问，我们就会发现没法打印出password ，因为是 private 的只能自己类访问 


### **5.构造方法和析构方法在继承中的表现**

如果子类没有就会自动调用父类的
如果子类有就调用自己的，但是我们能在子类的方法中人为的调用父类的，形式为： parent :: 对应父类方法

**示例代码：**

    <?php
    
    class A{
        function __construct(){
            echo "父类A的构造方法<br>";
        }
    }
    
    class B extends A{
        function __construct(){
            echo "子类B的构造方法<br>";
            parent :: __construct();
        }
    }
    
    $b = new B();

**结果：**
    
    子类B的构造方法
    父类A的构造方法
    
### **6.parent 关键字**

parent  关键字用来表示某一个类的父类（或者说父类的对象），类似于Python 中的 super()，常常在子类中去访问父类的方法或者属性，他的使用形式只有一个

    parent :: 父类的属性或者方法

常常用于子类不想重写父类已经出现过得构造方法，就用这个避免重写，只要添加新的就可以了

**示例代码：**
    
    <?php
    
    class Person{
        public $name = " ";
        public $age = " ";
        function __construct($name,$age){
            $this -> name = $name;
            $this -> age = $age;
        }
        function showInfo(){
            echo "xxxx";
            echo "yyyy";
        }
    }
    
    class Teacher extends Person{
        public $edu;
        function __construct($name, $age ,$edu)
        {
            parent::__construct($name, $age);
            $this -> edu = $edu;
        }
    
        function showInfo()
        {
            parent::showInfo(); // TODO: Change the autogenerated stub
            echo "zzzz";
        }
    }


### **7.重写**

重写就是子类重新定义父类继承给自己的属性或者方法

#### **1.重写的要求**

(1)子类重写上级类成员时，访问控制修饰符不能权限更低（意思是可以更开放）

父类： public 子类: public 
父类： protected 子类: protected/public
父类： private  子类： 不能重写


(2) 子类重写父类的方法是要求形参保持一致



### **8.final class 和 final method**

最终类：定义一个类，这个类不允许别的类去继承

最终方法：定义一个方法，这个方法不允许下级类去重写

## **三、设计模式**

### **1.什么叫设计模式**

是面向对象程序设计中“常见任务”中的代码模式的经验总结

### **2.工厂模式**

工厂模式就是设计这样一个类（F），该类可以接受一个参数，这个参数代表某个类名（B），然后这个类（F）就能生成所传入的类（B）所对应的对象

可见工厂类的目的就是生产各种不同类的对象

**示例代码：**
    
    <?php
    
    class Factory{
        static function getObject($class_name){
            $obj = new $class_name();
            return $obj;
        }
    }


### **3.单例模式**

设计一个类，让这个类只能得到一个单例对象，那么这个类就叫做单例类，生成出来的对象就叫做单例对象

那么怎么实现呢？我们的想法就是，我们自定义一个实例化的接口，并且在接口中做判断，这样就能控制只能实例化一次

**示例代码：**

    <?php
    
    class Single{
        static $s = null;
        function __construct(){
        }
        function getOne(){
            if(empty(self::$s)){
                self::$s = new self();
                return self::$s;
            }
            else{
                self::$s;
            }
        }
    }

## **四、抽象类和抽象方法**

### **1.抽象类**

#### **1.概念：**

抽象类就是一个不能实例化的类

形式：
    
    abstruct class 类名{
        类成员
    }

#### **2.不能实例化，那么抽象类有什么作用？**

用于定义一些类的共同上级类，让这些类都具有某种共同的特征（其实就是强制子类实现某种属性或者方法）



### **2.抽象方法**

抽象方法是一个什么都不做的方法，只有函数头没有函数体，形式：

abstruct function 方法名 (形参);

和抽象类类似，抽象方法主要用于规定子类一定要实现的方法，但是没有规定怎么实现


### **3.抽象类和抽象方法的关系**

(1)一个类中有抽象方法那么这个类必须声明为抽象类
(2)一个类继承一个抽象类那么就必须实现所有的抽象方法，除非这个类也是作为一个抽象类
(3)子类实现父类的抽象方法的时候访问控制修饰符权限不能降低（可以更加开放），并且形参需要一致

## **五、重载技术 overloading**

### **1.概念**

php 中的重载指的是，当我们使用对象（或者类）去访问一些不存在的属性或者方法的时候，就会使用某些预先定义好的特殊方法来应对这种情况

可见PHP 的重载是内部为了应对非法使用属性或者方法的措施

### **2.重载的分类**

1.属性重载
2.方法重载

### **3.属性重载：**

属性就是变量，于是属性也就有和变量一样的四种操作 取值 赋值 isset()判断  unset()销毁
于是，属性重载就是在类中预先定义了四种特殊方法，来应对这四种对属性的可能错误的操作

    __set($name,$value)
    
    __get($name)
    
    __isset($name)
    
    __unset($name)


#### **1.`__set($name,$value)`**

在给一个不存在的属性赋值时候会自动调用，$name 会自动赋值为那个不存在的属性，$value 会赋值为那个不存在的属性的值，我们就能利用这个特性实现动态属性（用什么就实现什么，无需提前定义）

**示例代码：**

    <?php
    
    class A{
        public $p1 = 1;
        public $prop_list = array();
        function __set($name, $value)
        {
            // TODO: Implement __set() method.
            $this->prop_list[$name] = $value;
        }
    }
    
    $a = new A();
    $a->p1 = 11;
    $a->p2 = 22;
    var_dump($a);

**结果：**


    object(A)[1]
      public 'p1' => int 11
      public 'prop_list' => 
        array (size=1)
          'p2' => int 22
         

#### **2.`__get($name)`**

在给一个不存在的属性取值时候会自动调用

**示例代码：**

    <?php
    
    class A{
        public $p1 = 1;
        public $prop_list = array();
        function __set($name, $value)
        {
            // TODO: Implement __set() method.
            $this->prop_list[$name] = $value;
        }
    
        function __get($name)
        {
            // TODO: Implement __get() method.
            if(!empty($this->prop_list[$name])) {
                return $this->prop_list[$name];
            }else{
                return "该属性不存在";
            }
        }
    
    }
    
    $a = new A();
    $a->p1 = 11;
    $a->p2 = 22;
    echo $a->p2;

**结果:**

    22

#### **3.`__isset($name)`**

对一个不存在的属性使用 isset() 方法的时候调用

**示例代码：**

    class A{
        public $p1 = 1;
        public $prop_list = array();
        function __set($name, $value)
        {
            // TODO: Implement __set() method.
            $this->prop_list[$name] = $value;
        }
    
        function __get($name)
        {
            // TODO: Implement __get() method.
            if(!empty($this->prop_list[$name])) {
                return $this->prop_list[$name];
            }else{
                return "该属性不存在";
            }
        }
    
        function __isset($name)
        {
            // TODO: Implement __isset() method.
            if(isset($this->prop_list[$name])){
                return true;
            }else{
                return false;
            }
        }
    
    }
    
    $a = new A();
    $a->p1 = 11;
    $a->p2 = 22;
    echo isset($a->p2);
    
**结果：**

    1

#### **4.`__unset($name)`**

对一个不存在的属性使用 unset() 方法的时候调用

**示例代码：**

    <?php
    
    class A{
        public $p1 = 1;
        public $prop_list = array();
        function __set($name, $value)
        {
            // TODO: Implement __set() method.
            $this->prop_list[$name] = $value;
        }
    
        function __get($name)
        {
            // TODO: Implement __get() method.
            if(!empty($this->prop_list[$name])) {
                return $this->prop_list[$name];
            }else{
                return "该属性不存在";
            }
        }
    
        function __isset($name)
        {
            // TODO: Implement __isset() method.
            if(isset($this->prop_list[$name])){
                return true;
            }else{
                return false;
            }
        }
    
        function __unset($name)
        {
            // TODO: Implement __unset() method.
            if(isset($this->prop_list[$name])){
                unset($this->prop_list[$name]);
            }else{
                echo "error";
            }
    
        }
    
    }
    
    $a = new A();
    $a->p1 = 11;
    $a->p2 = 22;
    unset($a->p2);


### **4.方法重载：**

方法重载涉及到两个函数  __call()  __callstatic()

当对一个对象未定义的方法进行调用的时候会自动调用 __call($name,$arguments)
当对一个类未定义的静态方法进行调用的时候会自动调用 __callstatic($name,$arguments)

    $name 不存在的函数名
    
    $arguments 所有参数组成的数组


#### **1.`__call($name,$arguments)`**

**示例代码：**

    <?php
    
    class A{
        function __call($name, $arguments)
        {
            // TODO: Implement __call() method.
            if($name == "eat"){
                $num = count($arguments);
                if($num ==1){
                    $this->hezhou($arguments[0]);
                }
                if($num ==2){
                    $this->chifan($arguments[0],$arguments[1]);
                }
            }
        }
    
        function hezhou($zhou){
            echo "zheng zai he $zhou <br>";
        }
    
        function chifan($fan,$tools){
            echo "zheng zai yong $tools chi $fan <br>";
        }
    }
    
    $p = new A();
    $p->eat("zhou");
    $p->eat("fan","kuaizi");


**结果：**

    zheng zai he zhou
    zheng zai yong kuaizi chi fan 


#### **2.`__callstatic($name,$arguments)`**

这个完全类似，我就不重复讲了

## **六、接口**

### **1.概念：什么是接口**

接口是比抽象类更抽象的一种类似类的结构，接口中只有两种成员，一种是常量，另一种是抽象方法

**形式：**

    interface class A{
        const PI = 3.14;
        function B(); //接口中的方法都是抽象方法，不用写 abstract
    }

### **2.为什么需要接口**

面向对象的单继承是对现实世界多继承现象的一种妥协（为了不使的代码过于复杂），但是还是不免有多继承的情形需要描述，于是接口技术就成了对这种妥协的一种弥补（接口能够实现多继承），只不过我们队接口的继承换了一个名字叫做“实现”---> implements
    
    class 类名 implements 接口名{
        成员定义
    }
    
    
**示例代码：**

    <?php
    
    interface Player{//音乐播放器接口
        function play();
        function next();
        function prev();
        function stop();
    }
    
    interface USBset{//USB接口
        const WIDTH = 12;
        const HEIGHT = 5;
        function data_in();
        function data_out();
    }
    
    class MP3Player implements Player,USBset{
        function play(){}
        function next(){}
        function prev(){}
        function stop(){}
        function data_in(){}
        function data_out(){}
    }
    
### **3.关于接口的其他细节**

1.一个类在实现接口的同时也能继承父类
2.接口之间也可以相互继承
3.接口中的常量和抽象方法只能是 Public 无需写 abstruct 


## **七、MVC模式**

### **1.项目设计的基本流程**

1.需求分析
人员：项目经理，技术总监
目标：写出项目需求说明书

2.项目概要和详细设计
人员：项目经理，技术总监
目标：概要设计说明书和详细设计说明书

3.界面设计
人员： 设计师（美工）
目标：界面设计效果图

4.前端页面制作
人员：前端工程师
目标：html css js

5.前后台功能实现
人员：软件工程师
目标：php 代码

6.功能测试
人员：测试工程师
目标：测试功能，反馈问题并最终得到可用产品

7.发布上线，运行维护
人员：运维人员的工作
目标：保证系统的正常运行


### **2.显示与逻辑相分离的思想**

**需求：**

显示当前时间，要求有三种显示形式：
1.显示年月日
2.显示时分秒
3.两者都 显示


#### **1.显示与逻辑混合**

如果我们想实现这个功能写成一个页面应该是非常简单的，代码如下：

**示例代码：**

    <html>
    <body>
        <p align = right>
            <a href="?type=1">形式一</a>
            <a href="?type=2">形式一</a>
            <a href="?type=3">形式一</a>
            <hr>
        </p>
    
    <?php
    
    if(!empty($_GET['type']) && $_GET['type'] == "1"){
        $t1 = date("Y年m月d日");
    }elseif(!empty($_GET['type']) && $_GET['type'] == "2"){
        $t1 = date("H:i:s");
    }else{
        $t1 = date("Y年m月d日 H:i:s");
    }
    
    echo "<h1>$t1</h1>";
    ?>
    </body>
    </html>


#### **2.显示与逻辑相分离**

我们可以将显示与逻辑放到两个文件中去

**PHP 文件**
    
    show-time.php
    
    <?php
    
    if(!empty($_GET['type']) && $_GET['type'] == "1"){
        $t1 = date("Y年m月d日");
    }elseif(!empty($_GET['type']) && $_GET['type'] == "2"){
        $t1 = date("H:i:s");
    }else{
        $t1 = date("Y年m月d日 H:i:s");
    }
    include "./show-time.html";
    ?>

**HTML文件**

    show-time.html
    
    <html>
    <body>
    <p align = right>
        <a href="?type=1">形式一</a>
        <a href="?type=2">形式一</a>
        <a href="?type=3">形式一</a>
    <hr>
    </p>
    
    <?php
    echo "<h1>$t1</h1>";
    ?>
    </body>
    </html>


这样依然可以运行



#### **3.模板技术**

在很多的网站应用中，都要实现这样一种需求：整体上功能不变，但是界面经常要更换为不同的风格样式

在显示与逻辑相分离的技术基础上，做成多分不同的 HTML ，再通过用户的选择更换HTML来显示，那这些HTML 就构成了模板

### **3.MVC框架原理**

#### **1.概念：**


M: Model 模型
V: View  视图
C：Controller 控制器


#### **2.MVC 的简单演示**

我们假设求时间是一个非常复杂的工作，就像真实项目中的取数据要调用数据库模块一样，于是将求时间的部分抽象成 model 模块，通过 controller 模块调用 Model 来实现求时间的功能，然后再将结果放到 view 模块中

**示例代码：**

**show-time-controller.php**
    
    <?php
    
    require_once "show-time-model.php";
    
    if(!empty($_GET['type']) && $_GET['type'] == "1"){
        $obj = new GetTimeModel();
        $t1 = $obj->GetDate();
    
    }elseif(!empty($_GET['type']) && $_GET['type'] == "2"){
        $obj = new GetTimeModel();
        $t1 = $obj->GetTime();
    
    }else{
        $obj = new GetTimeModel();
        $t1 = $obj->GetDateTime();
    
    }
    
    include "./show-time-view.html";
    ?>


后面我们会将其改成一个类，根据传入的不同参数调用这个类的不同方法


**show-time-model.php**
    
    <?php
    
    class GetTimeModel{
        function GetDate(){
            return Date("Y年m月d日");
        }
    
        function GetTime(){
            return  Date("H:i:s");
        }
    
        function GetDateTime(){
            return Date("Y年m月d日 H:i:s");
        }
    
    }

**show-time-view.html**

    <html>
    <body>
    <p align = right>
        <a href="?type=1">形式一</a>
        <a href="?type=2">形式一</a>
        <a href="?type=3">形式一</a>
    <hr>
    </p>
    
    <?php
    echo "<h1>$t1</h1>";
    ?>
    </body>
    </html>


#### **4.MVC思想框架**

**如图所示：**


**解释：**

1.浏览器直接请求的是控制器文件，也只有控制器文件知道用户给了什么（请求数据）以及用户真正要的是什么（目标）

2.控制器根据用户的请求做两件事
（1）调用哪个模型
（2）获取什么数据
（3）显示数据到哪个视图中（其实就是载入视图文件）

3.模型文件：只根据控制器的调用生产数据并返回给控制器

4.视图文件：只负责显示数据（数据显示在页面的哪里以及显示的效果），并且是由控制器决定显示哪些数据

5.模型文件和视图文件没有直接的关系



**如下图所示：**

![此处输入图片的描述][4]

**再直观一点我们可以看一下类比图**

![此处输入图片的描述][5]

![此处输入图片的描述][6]


### **4.Model 层的典型实现**

#### **1.模型层的功能与典型的代码模式**

    class ModelDemo{
        function f1(){
            $result = 获取数据库中的数据1；
            return $result;
        }
        
         function f2(){
            $result = 获取数据库中的数据2；
            return $result;
        }
        
        ...
    } 
    

#### **2.控制器层的功能与典型的代码模式**

1.载入模型类文件
2.对模型类实例化以获得相应的对象
3.调用模型类对象的方法以获得相应的数据（不同的方法能获得不同的数据）

    require_once "模型文件"
    
    class xxxController{
        function xxxAction(){
            $obj = new 模型类();
            $res1 = $obj->方法1();
            
        }
        
        function yyyAction(){
            $obj = new 模型类();
            $res2 = $obj->方法2();
        }
        
        ...
    }
    
    $controller = new xxxController();
    
    $action = !empty($_GET['action']) ? $_GET['action'] : "Index";
    
    $action = $action."Action";
    
    $controller->$action();
    
我们对模型文件和模型类命名有如下习惯
    
类定义：

    class xxxModel{
        ...
    }
    
文件名：  
    
    xxxModel.class.php 



#### **3.模型类的单例工厂**

我们在之前的操作中都是每一个控制器的每一个方法都会实例化一个模型类，这在我们看来是没有必要的，一个模型类就能实现所有的功能了，于是我们利用之前学过的技术，创建一个模型类的单例工厂，实现我们只要传入我们想要实例化的模型类就能得到该模型类的实例化对象，并且，这个对象只会创建一次。

**示例代码：**

ModelFactory.class.php

    class ModelFactory{
        static $arry = array();
        static function M($class_name){
            if(empty(self::$array[$class_name])){
                self::$array[$class_name] = new $class_name();
            }
            return self::$array[$class_name];
        }
    }
    

我们只要将其包含到 xxxController.class.php 文件里面就可以了,从而可以代替上面代码中的 $obj = new 模型类(); 这一句话

    

#### **4.整个模型层的类库结构图**

在应用中通常每个数据表（table）都要使用一个模型类文件xxxModel ，来对这个表所需的数据进行相应的操作，比如用户表：注册、删除、登录、修改密码、修改常规信息、显示所有用户、查看单个用户信息

即，模型层和数据库中的表有如下大致关系

**如图所示：**

![此处输入图片的描述][7]

### **5.Controller 层的典型实现**

#### **1.控制器类的细节**

**1.功能**

用于获取用户的请求数据，根据用户的数据来选择控制器类对应的方法，这个方法会实例化模型类，然后调用对应的方法，获取数据，并再是图中显示

**2.划分：**

控制器通常按照应用的模块（功能组）进行划分，一个控制对应一个模块（页面）的不同的操作，比如用户界面的增删改查，实际上可以看成是一个模块，使用一个控制器（里面针对不同的功能实现不同的方法），商品列表的各种操作可以看成是一个模块，使用另一个控制器。

**3.与模型的区别：**

通常模型是严格按照表进行划分的，一个表对应一个模型类，对一个表进行各种的操作

**4.动作**

**动作是什么呢？ 动作就是用户在网页上所做的能跟服务器打交道的行为（比如点击链接或者提交表单）
在代码级别，页面上的任何一个动作都对应着控制器中的一个方法**

我们可以通过传参的方式进行动作的选择,这里有一个点可以优化，就是如果我们将传参的值和控制器的类的方法名称对应起来我们就能实现一行代码解决这个选择问题，并且扩展也非常的方便。

#### **2.基础控制器类**

为什么需要基础控制器类，因为我们想统一设置所有控制器中的一些东西，比如编码和某些都需要功能，于是我们想要创建一个所有控制器的上级，这个上级类就是基础控制器类，或者说是控制器基类。


### **6.View 层的典型实现**

**1.视图层的功能：**

展示页面的静态内容以及相关的变量数据

**2.数据的分类：**

(1)普通标量数据

    <?php echo "xx"?>、
    
(2)数组数据

一维数组： 

    <?php echo $arr['xxx']; ?>
 
二维数组：

    <?php foreach ($arr_list as $key=>$arr){ ?>
    <?php echo $arr['字段1']; ?>
    <?php echo $arr['字段2']; ?>
    <?php echo $arr['字段3']; ?>
    <?php echo $arr['字段4']; ?>
    <?php } ?>

**(3)对象数据：**

    <?php echo $obj->属性?>


## **八、关于 MVC 项目的其他常见做法**

### **1.请求分发器（前端控制器的实现）**

我们发现我们很多的控制器命名规则以及他的实例化的方法都是一样的，比如 UserController 和 ProductController ,于是我们就想能不能将其抽象出来，我们想寻找一个变量表示 User 和 Product ,并且这个变量也就能表示这个模块（因为我们当时约定的命名规则就是模块名加 Controller）,将这个模块的选择权交给用户

我们从每个控制器中抽象出来一部分，放在一个Index.php 中

![此处输入图片的描述][8]



**index.php**

    require_once "./ModelFactory.class.php";
    require_once "./BaseController.class.php";
    $ctrl = !empty($_GET['ctrl']) ? $_GET['ctrl'] : "User";
    
    require_once "./{$ctrl}Model.class.php";
    
    require+once "./{$ctrl}Controller.class.php";
    
    $ctrl_name = $ctrl."Controller";
    $controller = new $ctrl_name();
    $action = !empty($_GET['action']) ? $_GET['action'] : "Index";
    
    $action = $action."Action";
    
    $controller->$action();


这个文件写好以后，在每个控制器中的下面这部分代码就不需要了，就会变得非常的干净

    require_once "./ModelFactory.class.php";
    require_once "./BaseController.class.php";
    $ctrl = !empty($_GET['ctrl']) ? $_GET['ctrl'] : "User";
    
    require_once "./{$ctrl}Model.class.php";
    
    require+once "./{$ctrl}Controller.class.php";
    


    $controller = new $ctrl_name();
    $action = !empty($_GET['action']) ? $_GET['action'] : "Index";
    
    $action = $action."Action";
    
    $controller->$action();


### **2.目录结构的设定**

我们一个项目不可能只有一个文件夹，那样的话文件太多太杂没法看，非常不利于后期的维护，于是我们需要对文件进行按照类别的划分

站点根目录/
        
        /Models
        /Controllers
        /Views
        /Bases
        index.php
        
    
## **九、PDO 数据对象**

### **1.概念**：

PDO 是已经写好的数据库工具类，通过他能对数据库进行各种操作，非常的方便快捷

### **2.特点：**

(1)他能操纵各种的数据库 ,mysql oracle sybase...
(2)他能实现除了增删改查以外的更多功能，比如事务
(3)他对不同的数据库有着统一的应用方式，在使用的时候无需考虑不同数据库SQL语法的细节差异
(4)PDO 实际上是一个类，我们使用的时候就需要new 一个对象出来

### **3.PHP 代码中常见的操作数据库的情况**

(1)连接数据库得到数据库的连接资源
(2)执行各种sql 语句得到布尔值或者结果集
(3)对结果集的数据进行取用，遍历 一般就是 fetch


### **4.PDO 系统的逻辑结构**

**如图所示：**

![此处输入图片的描述][9]



要想操作不同的数据库，就要在 php.ini 中打开对应的PDO模块

### **5.POD 使用**

#### **1.连接数据库**

    $DSN = "mysql: host=服务器地址；port=端口号；dbname=数据库名"；
    $Options = array(PDO::MYSQL_ATTR_INIT_COMMADN => 'set names utf8'); 
    $pdo = new pdo($DNS,用户名,密码,$Options);

DSN : Data Source Name

#### **2.执行SQL语句**
    
    $result1 = $pdo->exec(增删改语句)；
    $result2 = $pdo->query(各种sql语句)；

exec 习惯上执行没有返回结果集的语句，返回值为 受影响的行数，当然也可能返回false

query 执行select 这种有返回结果集的语句，执行失败返回false,成功的话返回的结果集还需要进一步的处理。

#### **3.断开连接**

    $pdo = null;


### **6.POD 的错误处理**

PDO 的错误处理有两种模式：

**(1)静默模式:**

发生错误以后并不会产生任何提示，需要人为的通过代码获取

**示例代码：**

    $pdo->exec($sql);
    $code = $pdo->errorCode();//获取上一行代码执行的错误，如果没有错误则返回0、
    if($code == 0){
        echo "执行成功";
    }
    else{
        $info = $pdo->errorInfo();
        echo "失败,错误代号：".$info[2];
    }


**(2)异常模式:**、

**示例代码：**


    try{
        $pdo->exec($sql);
    }
    catch(Exception $e){
        echo "发生错误，请参考错误提示：".$e->GetMessage();
    }
    
    
### **7.POD 的结果集对象**

#### **1.得到结果集对象：**

    $res = $pdo->query("select ...");

#### **2.常见处理方法**

**得到行数**

    $res->rowCount();

**得到列数**

    $res->columnCount();

**返回一行数据（结果是一维数组）**

    $res->fetch(返回类型)

返回类型：

PDO::FETCH_ASSOC 返回关联数组
PDO::FETCH_NUM 返回索引数组
PDO::FETCH_BOTH 返回前两者皆有的数据（默认值）、
PDO::FETCH_OGJ 返回对象

**返回所有数据（结果二维数组）**

    $res->fetchAll(返回类型);
    
**返回指定的列**

    $resds->fetchColumn([$i]);

返回第i列


### **8.POD 结果集对象的预处理语法**、

#### **1.为什么需要预处理**

一般的sql 语句一条一条的执行是没有任何的问题的，但是如果我们希望大量的 sql语句批量的执行的话就会显得效率不足，于是我们就可以使用预处理语句来提升我们的执行效率，

**但是前提是：**
这些语句有一定的规律性，其中只有部分参数变化

#### **2.形式：**

**形式一：占位符形式**

参数的顺序一次是 1,2,3,4,5

    $sql = "select * from user_list where age = ?";
    
**示例代码：**

    $arr = array(11,28,32);
    $sql = "select * from user_list where age = ?";
    $result = $pdo->prepare($sql);
    foreach($arr as $value){
        $result->bindVaule(1,$value);//如果有多个问号就可以有多个语句
        $result->execute();
        $result = $rersult->fetch(PDO::FETCH_ASSOC);
        print_r($result);
    }


**形式二：命名参数形式**

    $sql = "select * from user_list where age= :v1";

**示例代码：**

    $sql = "select * from user_list where age = :v1 and edu = :v2";
        $result = $pdo->prepare($sql);
        $result->bindVaule(":v1",11);
        $result->bindVaule(":v2","高中");
        $result->execute();
        $result = $rersult->fetch(PDO::FETCH_ASSOC);
        print_r($result);


  [1]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%AF%B9%E8%B1%A1%E7%9A%84%E8%B5%8B%E5%80%BC1.png
  [2]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%AF%B9%E8%B1%A1%E7%9A%84%E8%B5%8B%E5%80%BC2.png
  [3]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%AF%B9%E8%B1%A1%E7%9A%84%E8%B5%8B%E5%80%BC3.png
  [4]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/MVC%E7%BB%93%E6%9E%84%E5%9B%BE.png
  [5]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/MVC%20%E7%B1%BB%E6%AF%94%E5%9B%BE1.png
  [6]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/MVC%E7%B1%BB%E6%AF%94%E5%9B%BE2.png
  [7]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E6%A8%A1%E5%9E%8B%E7%B1%BB%E5%92%8C%E6%A0%87%E7%9A%84%E5%85%B3%E7%B3%BB3.png
  [8]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/%E5%89%8D%E7%AB%AF%E5%88%86%E5%8F%91%E5%99%A8.png
  [9]:  https://picture-1253331270.cos.ap-beijing.myqcloud.com/PDO%E7%B3%BB%E7%BB%9F%E7%9A%84%E9%80%BB%E8%BE%91%E7%BB%93%E6%9E%84.png