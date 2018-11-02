---
title: Python 编程进阶（重点在于面向对象和魔法方法）
date: 2018-10-4 01:16:18
tags: 编程 Python 进阶 备忘
categories: 编程
---

## **一、Python 一切皆对象**

Python 的一切皆对象是非常彻底的，不管是函数还是类，都是对象，对象就有一些独特的特性

1.赋值给一个变量
2.可以添加到集合对象中
3.都能作为函数的参数进行传递
4.都能当做函数的返回值
<!-- more -->

### **1.赋值给一个变量**

**(1)函数赋值给一个变量**

**示例代码:**

    def add(name = "K0rz3n"):
        print name
    
    object_test = add
    object_test()


**(2)类赋值给一个变量**
    
    
    class Person():
        def __init__(self):
            print "K0rz3n"
    
    class_test = Person
    class_test()
    

### **2.添加到集合对象中**

    def add(name = "K0rz3n"):
        print name
    
    class Person():
        def __init__(self):
            print "K0rz3n"
    
    obj_list = []
    obj_list.append(add)
    obj_list.append(Person)
    
    for item in obj_list:
        print item()

**结果：**

    K0rz3n
    None
    K0rz3n
    <__main__.Person instance at 0x0000000003277E08>
    
### **3.可以作为函数的返回值**

函数作为返回值其实是 python 装饰器的精髓

**示例代码：**

    def add(name = "K0rz3n"):
        print name
    
    class Person():
        def __init__(self):
            print "K0rz3n"
    
    def decorator():
        print "dec success"
        return add
    
    test = decorator()
    test()

**结果：**

    dec success
    K0rz3n
    

## **二、type 和 object 的关系**

### **1.type**

**type 这个类实例化了一切，包括 object 和 他自己**

**示例代码：**

    >>> type(1)
    <class 'int'>
    
    >>> type(int)
    <class 'type'>
    
    >>> type(type)
    <class 'type'>
    
    >>> type(object)
    <class 'type'>
    
    
可以看到，1 是由 int 这个类生成的对象，int 这个类是由type 这个类生成的对象

**结论：**

    type->class->obj
    
### **2.object**

object 是最顶层的基类，**所有的类都继承了Object，包括type** 

**示例代码:**
    
    >>> class Student():
    ...     pass
    ...
    >>> Student.__bases__
    (<class 'object'>,)
    
    >>> type.__bases__
    (<class 'object'>,)
    
    >>> object.__bases__
    ()
    
但是 object 是由 type 实例化的

**示例代码：**
    
    >>> type(object)
    <class 'type'>

**总结：**

type 实例化了一切，包括 object 和他自己,object 是所有类的基类（最顶层，包括type）。于是乎 Type 和 object 就形成了一个回路，**这其实也就是python 中类也是对象的原因（不仅继承了object 还是type 这个类的实例）**


## **三、Python 中的内置类型**

### **1.对象的三个特征**

#### **（1）身份**

我们把身份理解成对象在内存中的地址

我们可以通过id()这个函数查看一个对象在内存中的地址

**示例代码:**

    >>> a= 1
    >>> id(a)
    1465347104
    >>> a = []
    >>> id(a)
    2406326173512

#### **（2）类型**

比如有：

**None （全局唯一）**

**示例代码：**

    >>> a =None
    >>> b = None
    >>> id(a)
    1464902800
    >>> id(b)
    1464902800

可以看到两个变量都是指向了一个地址，说明其是全局唯一的

**数值**

Int、float、bool、complex...

**迭代类型**

可以遍历的

**序列类型**

list 、tuple 、str 、array 、range 、bytes... 

**映射（dict）**

相当于字典，有 Key 和 value 

**集合**

set 和 forzenset

**上下文管理器**

with 语句

**其他**

模块类型、class和实例、函数类型、方法类型、代码类型 、object 对象 、type 类型、ellipsis 类型


Python 有些类型是隐式的，**主要是通过对象内置的魔法方法来判断对象是什么类型**，这其实就是Python魔法方法的精髓，这在我们后面会详细的讲到


#### **（3）值**

这个我就不再细说了，相信大家都懂


## **四、Python 中的魔法函数**

### **1.什么是魔法函数**

1.Python中为我们提供的以双下划线开头和结尾的函数
2.魔法函数属于全局，在我们自定义的任意一个类中我们能添加任意一个魔法函数，来让这个类有着不同的性质（来增强这个类的特性，**简单的说就相当于给这个类安装了一个插件**）

在我们没有定义魔法函数之前，我们想遍历这个类中的员工，我们需要像这样写

**示例代码：**

    class Company(object):
        def __init__(self,employ_list):
            self.employ = employ_list
    
    company = Company(["Tom","Alice","Bob"])
    employ = company.employ
    for item in employ:
        print item

**结果：**

    Tom
    Alice
    Bob

但是我们在定义了一个魔法方法以后事情就变得非常的简单

**示例代码：**

    class Company(object):
        def __init__(self,employ_list):
            self.employ = employ_list
    
        def __getitem__(self, item):
            return self.employ[item]
    
    company = Company(["Tom","Alice","Bob"])
    for item in company:
        print item
    
**结果：**

    Tom
    Alice
    Bob


**解释：**

实际上我们定义了 `__getitem__` 这个魔法方法以后，这个类实例化出来的对象就是一个可迭代的对象


### **2.`__str__` 和 `__repr__`**


#### **（1） `__str__`**

Python 在使用 print 的时候会隐式的调用 str() 这个函数，而这个函数会隐式的调用  `__str__` 这个魔法方法,比如我们想改变一个对象的默认输出格式，我们就需要在实例化这个对象的类中重写 `__str__`方法

**示例代码：**
    
    class Company(object):
        def __init__(self,employ_list):
            self.employ = employ_list
        
        def __str__(self):
            return ','.join(self.employ)
        
    company = Company(["Tom","Alice","Bob"])
    print company
    
**结果：**

    Tom,Alice,Bob
`
#### **(2)`__repr__`**

`__repr__` 是开发者模式下（交互式Python解释器中在直接写某个对象的时候会调用的函数），我们平时直接把对象写在交互式解析器中的时候会输出一些尖括号包裹的类或者对象就是隐式调用这个函数的结果，如果我们重写了这个魔法方法就能改变输出，这个和 上面的例子是一样的，我就不再重新写代码


## **五、Python 深入类和对象**

### **1.鸭子类型和多态**

一个对象如果他 走起来 叫起来 看起来 都像一个鸭子那我们就把他看成是一个鸭子，其实意思就是说我们关注的是这个对象内部实现了什么方法，实现的方法决定了我们将其看成是什么

1.比如extend(),它里面并没有要求传入某一个具体的类型，而是要求传入一个可迭代对象，所以很多的类型都是可以的

2.再比如，我们实现多态的时候并不像JAVA一样要求全部继承同一个类，python 只要内部实现同一个方法就可以了，到时候直接赋值调用就ok

**示例代码:**

    class Cat(object):
        def say(self):
            print "i am a cat\n"
    
    class Dog(object):
        def say(self):
            print "i am a dog\n"
    
    animal = [Cat,Dog]
    for i in animal:
        i().say()

**结果：**

    i am a cat
    
    i am a dog


### **2.Python 中的抽象基类**

#### **概念：**

可以把 Python 的抽象基类看成是 java 中的接口，java 不能实现多继承单数能实现多个接口，接口在 java 中是不能实例化的，同样抽象基类在 Python 中也是不能实例化的（我们可以继承抽象基类，然后去实例化抽象基类的所有方法）

但是我们不是说过，python 是一种动态语言，**动态语言是没有变量的类型的**，Python中变量只是一个符号而已，他可以指向任何类型的对象。

**那么我们为什么还需要有抽象基类这个概念呢？**

1.因为我们在有些时候想判断某一个对象的类型，而不是通过一个一个的魔法函数的检验去查看对象的属性如何，这样的体验非常不好，我们提供抽象基类实际上就是提供一种类型（属性）的打包

2.我们在某些时候需要强制某些子类必须实现某些方法

**示例代码：**

    class CacheBase():
        def get(self,key):
            raise NotImplemented
        def set(self,key,value):
            raise NotImplemented
    
    class cache(CacheBase):
       pass
    
    my_cache = cache()
    my_cache.get("key")

**结果：**

    TypeError: exceptions must be old-style classes or derived from BaseException, not NotImplementedType

当然我们如果想在实例化的时候就抛出异常的话就需要用到abc 模块以及装饰器的操作，这里不再多介绍

### **3.isinstance 和 type 的区别**

这两个函数都是查看对象的归属的，但是 isinstacne() 会根据继承关系不断的向上递推，直到最顶层为止，但是 Type() 只能向上递推一个


**另外 == 和 is 不要乱用**

== 代表的是返回值是不是相等
is 查看的是 id() 的结果是不是相等

### **4.类变量和对象变量**

**示例代码：**

    class A:
        aa = 1
        def __init__(self,x,y):
            self.x = x
            self.y = y
    
    a = A(2,3)
    print a.x,a.y,a.aa
    print A.aa
   
 **结果：**
 
    2 3 1
    1

这里 aa 就是类变量，那么为什么我们能打印出来呢？因为我们在寻找实例的属性的时候如果找不到就会自动的向上搜索

**还有一个比较神奇的地方**

**示例代码：**
    
    
    class A:
        aa = 1
        def __init__(self,x,y):
            self.x = x
            self.y = y
    
    a = A(2,3)
    A.aa = 10
    a.aa = 100
    print A.aa
    print a.aa
    
**结果：**
    
    10
    100
    
可以看到我们在实例中修改了 aa 的值，但是我们在类中访问却依然没改变，这是为什么呢？

> **解释：**
> 
> 我们使用 a.aa
> 访问或者赋值的时候实际上会在我们的实例中新建一个属性，我们访问或者修改的都是这个新的属性，这个属性初始值和类的一样，但是他们有着不用的地址空间，因此修改后互不影响


### **4.类和实例的属性查找顺序`__mro__`**

在历史上 python 设置了很多的查找算法，比如 DFS BFS 等，但是依然不能用单纯的一个方法解决所有的问题，于是后来出现了 C3 算法，这个算法比较复杂，能针对不同的模式调整查找顺序

#### **（1）菱形继承方式**

**实例代码：**


    class D(object):
        pass
    
    
    class C(D):
        pass
    
    
    class B(D):
        pass
    
    
    class A(B, C):
        pass
    
    
    print A.__mro__
    
**结果：**

    (<class '__main__.A'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.D'>, <type 'object'>)
    
**解释：**

可见这种继承方式使用的是 BFS 


    
#### **（2）锥形继承方式**

**示例代码：**

    class E(object):
        pass
    
    class D(object):
        pass
    
    
    class C(E):
        pass
    
    
    class B(D):
        pass
    
    
    class A(B, C):
        pass
    
    
    print A.__mro__


**结果：**

    (<class '__main__.A'>, <class '__main__.B'>, <class '__main__.D'>, <class '__main__.C'>, <class '__main__.E'>, <type 'object'>)

**解释：**

可见这种结构的继承方式使用的是 DFS 

### **5.类方法、静态方法和实例方法**

#### **（1）实例方法：**

**示例代码：**


    class Date():
        def __init__(self,year,month,day):
            self.year = year
            self.month = month
            self.day = day
    
        def __str__(self):
            return "{year}/{month}/{day}".format(year = self.year,month = self.month,day = self.day)
    
    new_day = Date(2018,10,2)
    print new_day
    
    
**结果：**

    2018/10/2
    

但是如果我们有这样的需求，我们输入 2018-10-2 也能直接解析怎么办，这个时候我们就需要使用静态方法

#### **（2）静态方法：**

**示例代码：**

    
    class Date():
        def __init__(self,year,month,day):
            self.year = year
            self.month = month
            self.day = day
    
        @staticmethod
        def parse_fro_string(data_str):
            year,month,day = tuple(data_str.split('-'))
            return Date(int(year),int(month),int(day))
    
    
        def __str__(self):
            return "{year}/{month}/{day}".format(year = self.year,month = self.month,day = self.day)
    
    
    new_day = Date.parse_fro_string("2018-10-2")
    print new_day
    
**结果：**

    2018/10/2


但是，这个还有一个问题，就是静态方法是硬编码的，如果我们的类的名字改了，静态方法也要改，这很不好，于是我们就引出了类方法

#### **（3）类方法：**

    class Date():
        def __init__(self,year,month,day):
            self.year = year
            self.month = month
            self.day = day
    
        @staticmethod
        def parse_fro_string(data_str):
            year,month,day = tuple(data_str.split('-'))
            return Date(int(year),int(month),int(day))
    
        @classmethod
        def fro_string(cls,data_str):
            year, month, day = tuple(data_str.split('-'))
            return cls(int(year), int(month), int(day))
    
    
        def __str__(self):
            return "{year}/{month}/{day}".format(year = self.year,month = self.month,day = self.day)
    
    new_day = Date.fro_string("2018-10-2")
    print new_day
    
**结果：**

    2018/10/2

### **6.数据封装和私有属性**

python 不像 java 有 Private 或者 protetced 这种，那么 Python 是怎么实现私有属性的

**示例代码：**
    
     class Date():
        def __init__(self,year,month,day):
            self.year = year
            self.month = month
            self.day = day
    
        @staticmethod
        def parse_fro_string(data_str):
            year,month,day = tuple(data_str.split('-'))
            return Date(int(year),int(month),int(day))
    
        @classmethod
        def fro_string(cls,data_str):
            year, month, day = tuple(data_str.split('-'))
            return cls(int(year), int(month), int(day))
    
    
        def __str__(self):
            return "{year}/{month}/{day}".format(year = self.year,month = self.month,day = self.day)
    
    class User:
        def __init__(self,birthday):
            self.birthday = birthday
    
        def get_age(self):
            return 2018-self.birthday.year
    
    if __name__ == '__main__':
        user = User(Date(1990,2,1))
        print user.get_age()
        print user.birthday

**结果:**

     28
     1990/2/1

 
在这种情况下，我们可以直接访问到用户的生日，如果我们不想直接访问到怎么办？我们可以在属性前面加上双下划线来解决

**示例代码：**
    
    class User:
        def __init__(self,birthday):
            self.__birthday = birthday
    
        def get_age(self):
            return 2018-self.__birthday.year
    
    if __name__ == '__main__':
        user = User(Date(1990,2,1))
        print user.get_age()
        print user.__birthday

可以测试一下 get_age 这个函数还是可以使用的但是 直接访问会出错

但是实际上这个是可以绕过的,python 只是将其做了一个变形而已,变成了  _User__birthday

**示例代码：**

    class User:
        def __init__(self,birthday):
            self.__birthday = birthday
    
        def get_age(self):
            return 2018-self.__birthday.year
    
    if __name__ == '__main__':
        user = User(Date(1990,2,1))
        print user.get_age()
        print user._User__birthday
    
这样就又能正常访问了


### **7.Python 对象的自省机制**

#### **1. 什么叫做自省？**

自省就是通过一定的机制来查看对象的内部结构（看看自己是什么东西...自我认识一下）

**示例代码：**
    
    class Person():
        name = "user"
    
    class Student(Person):
        def __init__(self,school_name):
            self.school_name = school_name
    
    if __name__ == '__main__':
        user = Student("K0rz3n")
        print user.__dict__
        print Person.__dict__
    

**结果：**

    {'school_name': 'K0rz3n'}
    {'__module__': '__main__', 'name': 'user', '__doc__': None}

除了使用 `__dict__` 以外我们还能使用 dir() 这个函数，这个函数功能更加强大，能列出对象的所有属性

**示例代码：**

    class Person():
        name = "user"
    
    class Student(Person):
        def __init__(self,school_name):
            self.school_name = school_name
    
    if __name__ == '__main__':
        user = Student("K0rz3n")
        print user.__dict__
        print Person.__dict__
        print dir(user)
        print dir(Person)

**结果:**

    {'school_name': 'K0rz3n'}
    {'__module__': '__main__', 'name': 'user', '__doc__': None}
    ['__doc__', '__init__', '__module__', 'name', 'school_name']
    ['__doc__', '__module__', 'name']


### **8.super 真的是调用父类吗**

我们可以在 B中用super()函数调用了父类 A 中的构造方法

**示例代码：**

    class A(object):
        def __init__(self):
            print "a"
    
    class B(A):
        def __init__(self):
            super(B,self).__init__()
            print "b"
    
    if __name__ ==  '__main__':
    
        b = B()
    
**结果：**

    a
    b

#### **1.为什么要这样用**

有时候我们可以通过这种方式来重用父类的方法，而不是重写

#### **2.真的是调用父类吗？**

我们来做一个实验

**示例代码：**


    class A(object):
        def __init__(self):
            print "a"
    
    class B(A):
        def __init__(self):
            print "b"
    
            super(B,self).__init__()
    
    class C(A):
        def __init__(self):
            print "c"
            super(C,self).__init__()
    
    
    class D(B,C):
        def __init__(self):
            print "d"
            super(D,self).__init__()
    
    
    if __name__ ==  '__main__':
    
        b = D()
    
**结果：**

    d
    b
    c
    a
    
**解释:**

我们看到当我们调用到 B 的时候，super 并没有去找B 的父类A 而是转向了C ，这说明我们单纯的将 super 理解为寻找父类是不正确的，其实这个顺序是我们之前讲的 mro

### **9.Python 中的 with 语句**

with 语句是用来使用上下文管理器的，那么什么叫做上下文管理器呢？

#### **1.上下文管理器**

介绍这个概念就涉及到我们之前说的魔法函数，只要拥有`__enter__` 和  `__exit__` 这两个属性的类实例化的对象就可以作为上下文管理器

我们可以举例看一下

**示例代码：**


    class Sample(object):
        def __enter__(self):
            print "enter"
            return self # 注意这句话绝对不能少，要不然sample 根本得不到这个对象的实例
        def __exit__(self, exc_type, exc_val, exc_tb):
            print "exit"
        def do_something(self):
            print "do_something"
    
    with Sample() as sample:
        sample.do_something()

**结果:**

    enter
    do_something
    exit

**解释：**

可以看到，`__enter__` 和 `__exit__` 魔法函数会在调用和结束的时候自动运行，同时，拥有这两个魔法函数的类可以用 with 语句进行实例化，成为一个上下文管理器，我们可以在 `__enter__` 中创建资源，在 `__exit__` 中释放



### **10.使用 contextlib 简化上下文管理器**

**实例代码：**

    import contextlib
    
    @contextlib.contextmanager
    def file_open(file_name):
        print "file_open"  # 这里写的是 __enter__ 中的代码
        yield{}
        print "file_close" # 这里写的是 __exit__ 中的代码
    
    
    with file_open("test.txt") as f:
        print "test"
    
**结果：**
    
    file_open
    test
    file_close


## **五、自定义序列类**

### **1.序列的分类：**

#### **(1)容器序列: list 、tuple 、deque**

容器序列中可以放置任何的数据类型

#### **(2)扁平序列：str 、bytes 、bytearray 、array.array**

该序列中只有一钟类型

#### **(3) 可变序列: list 、deque 、bytearray 、array**

#### **(4) 不可变序列： str 、tuple 、bytes**


### **2.序列中的魔法函数**

`__len__` 有这个方法就能实现计算长度 

`__iter__` 有这个方法就能实现迭代

`__container__` 有这个方法就能实现使用in判断元素是否存在

等等

我们如果要自定义序列类型就要去实现这些函数

### **3.序列中的 + += 和 extend 的区别**

#### **1. + 的使用**
    
**示例代码：**

    a = [1,2]
    c = a +[3,4]
    print c

**结果：**

#### **2.+= 的使用**

**示例代码：**

    a = [1,2]
    a += [3,4]
    print a 
    
**结果：**

    [1, 2, 3, 4]

#### **3.但是这两个实际上是有一些区别的**

**（1）区别一： += 是直接在 a 上进行操作**
这个就不用多解释了

**(2)区别二：+= 可以扩展不同类型的可迭代对象**

**示例代码：**


    a = [1,2]
    a += (3,4)
    print a

**结果：**

    [1, 2, 3, 4]

我们发现+=的神奇特性，实际上，+=底层调用的是 extend()方法，这个方法实际上在调用for 循环，因此支持for 循环的都能添加去

**还有一点就是人们经常把 append() 和 extend() 混淆**

append(） 会将参数直接放进列表中

**示例代码：**
    
    a = [1,2]
    a.append([3,4])
    print a
    
**结果：**
    
    [1, 2, [3, 4]]



### **4.实现可切片对象**

#### **1.先来回顾一下切片操作**

模式： [start:end:step]

start 意思是起始位置，默认是0
end 意思是结束的位置，默认是列表的长度
step 默认是1

> **注意：**
>1.当step 是负数的时候代表反向切片，这个时候 start 要比 end 来的大
>2.切片返回的是一个新的元素而不会改变原来列表的值
>3.步长为2表示包括自己在内数两个，也就是隔一个取一个

**实例:**

在末尾插入一个元素

    a[len(a):] = [1] 

在开头插入一个元素

    a[:0] = [1]

在特定位置插入一个元素
    
    a[3:3] = [1]

隔一个修改一个

    a[::2] = [0]*3

隔一个删除一个

    del a[::2]

#### **2.自己构造一个可切片对象**

**示例代码：**


    import numbers
    class Group():
        def __init__(self,group_name,company_name,staffs):
            self.group_name = group_name
            self.company_name = company_name
            self.staffs = staffs
    
    
        def __getitem__(self, item):
            cls = type(self)
            if isinstance(item,slice):
                return cls(group_name=self.group_name,company_name=self.company_name,staffs=self.staffs[item])
            elif isinstance(item,numbers.Integral):
                return cls(group_name=self.group_name,company_name=self.company_name,staffs=[self.staffs[item]])
    
    
    staffs = ["Alice","Bob","Tom","Kali"]
    group = Group(company_name="K0rz3n",group_name="user",staffs=staffs)
    sub_group = group[1:2]
    sub_group = group[0]


这个代码我本地也没有实现，会报错，还有待解决

### **5.bisect 维持已排序的序列**

bisect 能维持一个已经排序好的序列（以升序排序）

**示例代码：**

    import bisect
    
    inter_list = []
    bisect.insort(inter_list,2)
    bisect.insort(inter_list,5)
    bisect.insort(inter_list,1)
    bisect.insort(inter_list,6)
    bisect.insort(inter_list,3)
    bisect.insort(inter_list,4)
    
    print inter_list
   
**结果：**

    [1, 2, 3, 4, 5, 6]
    
我们还能查看某个元素应该插入什么位置

**示例代码：**

    print bisect.bisect(inter_list,3)
    
**结果：**

    3

### **6.什么时候我们不应该使用列表**

array 相当于 C语言的数组，在数据处理中的效率是非常高的，但是我们要注意 array 中存放的只能是一种数据类型，我们在声明的时候就要指定

**示例代码：**

    import array
    
    my_array = array.array("i")
    my_array.append(1)
    my_array.append(2)
    print my_array

**结果：**

    array('i', [1, 2])


## **六、Python 中的 set 和 dict**

### **1.dict 中的常用方法**

#### **(1)clear 方法：**

**示例代码：**

    a = {"K0rz3n":{"hello":"world"},
         "KKKK":{"hello":"K0rz3n"},
         "xxxxx":{"xxx":"sssss"}}
    
    a.clear()
    print a 
    
**结果：**

    {}

#### **(2)copy() 方法**

> **注意：**
> 
> 这个拷贝是浅拷贝，也就是说不能循环嵌套拷贝

**示例代码：**

    a = {"K0rz3n":{"hello":"world"},
         "KKKK":{"hello":"K0rz3n"},
         "xxxxx":{"xxx":"sssss"}}
    
    new_dict = a.copy()
    new_dict["K0rz3n"]["hello"] = "K0rz3n"
    print a

**结果：**

    {'K0rz3n': {'hello': 'K0rz3n'}, 'KKKK': {'hello': 'K0rz3n'}, 'xxxxx': {'xxx': 'sssss'}}
    
我们发现我们修改了拷贝值以后原始值也发生了变化，这其实就是浅拷贝导致的问题，浅拷贝遇到循环嵌套的数据时只能拷贝一层，另外的层都是一个指向，也就是指向原始的位置，因此修改了拷贝以后原始值也发生了变化

**解决：**

使用python 的一个 copy 的库

**示例代码**：

    import copy
    a = {"K0rz3n":{"hello":"world"},
         "KKKK":{"hello":"K0rz3n"},
         "xxxxx":{"xxx":"sssss"}}
    
    new_dict = copy.deepcopy(a)
    new_dict["K0rz3n"]["hello"] = "K0rz3n"
    print a

**结果：**

    {'K0rz3n': {'hello': 'world'}, 'KKKK': {'hello': 'K0rz3n'}, 'xxxxx': {'xxx': 'sssss'}}
    
#### **(3)fromkeys 方法**

将可迭代的对象转化成为一个dict

**示例代码：**
    
    new_list = ["K0rz3n","hello","world"]
    new_dict = dict.fromkeys(new_list,{"hhh"})
    print new_dict

**结果：**

    {'K0rz3n': set(['hhh']), 'world': set(['hhh']), 'hello': set(['hhh'])}

#### **(4)get 方法**

为了防止出现 keyerror 的错误

**示例代码：**


    new_list = ["K0rz3n","hello","world"]
    new_dict = dict.fromkeys(new_list,{"hhh"})
    
    value = new_dict.get("hh",{})
    print value

**结果：**

    {}


#### **(5)items 方法**

Items 方法可以实现元祖的拆包

**示例代码：**

    
    new_list = ["K0rz3n","hello","world"]
    new_dict = dict.fromkeys(new_list,{"hhh"})
    
    for key,value in new_dict.items():
        print key,value
    
**结果：**

    K0rz3n set(['hhh'])
    world set(['hhh'])
    hello set(['hhh'])
    
#### **(6)setdefault 方法**

除了试下 get 一样的操作以外，还会将这个值设置进映射

**示例代码：**

    new_list = ["K0rz3n","hello","world"]
    new_dict = dict.fromkeys(new_list,{"hhh"})
    
    new_dict.setdefault("hh","xxx")
    print new_dict

**结果：**

    {'K0rz3n': set(['hhh']), 'world': set(['hhh']), 'hh': 'xxx', 'hello': set(['hhh'])}


#### **(7)update 方法**

更新dict 

**示例代码：**

    
    new_list = ["K0rz3n","hello","world"]
    new_dict = dict.fromkeys(new_list,{"hhh"})
    
    new_dict.update(update="hahah")
    new_dict.update({"upupup":"xixixi"})
    print new_dict

**结果：**

    {'K0rz3n': set(['hhh']), 'world': set(['hhh']), 'upupup': 'xixixi', 'hello': set(['hhh']), 'update': 'hahah'}


### **2.set 和 frozeset**

set 是集合，是无序的，不重复的，frozenset 是不可变集合

set 中放置的是一个可迭代对象

#### **(1)无序**

**示例代码：**
    
    a = set('abcdefs')
    print a

**结果：**

    set(['a', 'c', 'b', 'e', 'd', 'f', 's'])

可见显示的顺序和我们传入的顺序是不一样的，也就是说是无序的

#### **（2）不重复**

**示例代码：**
    
    a = set('abcdefssss')
    print a

**结果：**

    set(['a', 'c', 'b', 'e', 'd', 'f', 's'])
    
#### **（3）frozenset 是不可变的**

**示例代码：**

    a = set('abcdefssss')
    a.add("x")
    print a
    
    b = frozenset('abcdefssss')
    b.add("x")
    
    print b

**结果：**
    
    set(['a', 'c', 'b', 'e', 'd', 'f', 's', 'x'])
    AttributeError: 'frozenset' object has no attribute 'add'


**（4）set 能使用update 更新**

**示例代码：**

    a = set('abcdefssss')
    a.add("x")
    b = set("xxx")
    a.update(b)
    print a

**结果：**

    set(['a', 'c', 'b', 'e', 'd', 'f', 's', 'x'])

**（5）set 使用difference 求差集**

**示例代码：**

    a = set('abcdefssss')
    a.add("x")
    b = set("abcde")
    new_set = a.difference(b)
    print new_set

**结果：**

set(['x', 's', 'f'])

还有一种方式就是直接使用 - 号，其实底层都是使用魔法函数实现的，当然还有 | & 

**示例代码：**

    a = set('abcdefssss')
    a.add("x")
    b = set("abcde")
    new_set = a.difference(b)
    another_set = a-b
    print new_set
    print another_set

**结果：**

    set(['x', 's', 'f'])
    set(['x', 's', 'f'])


### **2.dict 背后的实现原理**

dict 背后都是通过hash 实现的，背后是一个数组，连续的内存空间，通过键值的hash到数组中寻找，因此速度非常的块。

**因此：**

1. dict 的 key 和 set 的值都必须是可hash 的
2. dict 的内存花销大，但是查询速度快，很多自定义的对象或者是python 内部的对象都是用dict 包装的
3. dict 的存储顺序和存入的顺序有关
4. 添加新的元素可能会改变已有数据的顺序（因为，在数组空间不足以后可能会新开辟一个新的数组，在元素进行转移的时候可能会改变存储顺序）


## **六、对象引用、可变性和垃圾回收**

### **1.Python 中的变量是什么？**

Python 的变量和 Java 中的变量是不一样的，Java 中的变量是有大小的（因为有类型），声明一个变量就会在内存中开辟一定大小的空间，存放不同类型的数据，但是Python 不一样，Python 中的变量是一个指针，大小都是一样的

**示例代码：**
    
    a = [1,2,3,4]
    b = a
    b.append(5)
    print a 

**结果：**

    [1, 2, 3, 4, 5]
    
我们看到，我们修改b 实际上把a 也修改了，本质上是指向了同一个内存空间

### **2.Python 中的 == 和 is**

#### **（1）is 比较的是两个的 id** 

**示例代码：**
    
    a = [1,2,3,4]
    b = a
    print id(a),id(b)
    print a is b

**结果：**

    50435592 50435592
    True

但是有一个奇怪的情况

**示例代码：**

    
    a = [1,2,3]
    b = [1,2,3]
    
    print a is b
    
    a = 1
    b = 1
    
    print a is b

**结果：**

    False
    True

**解释：**

其实python 在对于小整数、小字符串的情况下不会另外新建内存空间

#### **（2）== 比较的是值**

**示例代码：**

    a = [1,2,3]
    b = [1,2,3]
    
    print a == b
    
    a = 1
    b = 1
    
    print a == b

**结果：**

    True
    True


### **3.del 和垃圾回收**

Python 中的垃圾回收机制使用的是引用计数的方式

del 并不能直接释放内存，只有在引用计数为0的时候才会释放

**示例代码：**


        a = object()
        b = a
        
        del  a
        print b
        print a

**结果：**

    <object object at 0x00000000030F90C0>
    NameError: name 'a' is not defined


## **七、元类编程**

### **1.property 动态属性**

使用 @property 装饰器将取函数的模式改变成取属性的模式

先看一下原始的使用调用函数的方法：

**示例代码：**

    from  datetime import date,datetime
    class User():
        def __init__(self,name,birthday):
            self.name = name
            self.birthday = birthday
    
        def get_age(self):
            return datetime.now().year - self.birthday.year
    
    if __name__ == '__main__':
        user = User("K0rz3n",date(year = 1999,month = 11,day = 12))
        print user.get_age()
    
**结果：**

    19

**我们如果想用调用类的属性的方式访问呢？**

**示例代码：**


    from  datetime import date,datetime
    class User():
        def __init__(self,name,birthday):
            self.name = name
            self.birthday = birthday
    
        @property
        def get_age(self):
            return datetime.now().year - self.birthday.year
    
    if __name__ == '__main__':
        user = User("K0rz3n",date(year = 1999,month = 11,day = 12))
        print user.get_age
    
**结果：**

    19
    
### **2.__getattr__ 和 __getattribute__**

#### **(1)`__getattr__`**

这个魔法方法会在找不到属性的时候调用

**示例代码：**


    from  datetime import date,datetime
    class User():
        def __init__(self,name,birthday):
            self.name = name
            self.birthday = birthday
    
        def __getattr__(self, item):
            return "can not find the attr"
    
    if __name__ == '__main__':
        user = User("K0rz3n",date(year = 1999,month = 11,day = 12))
        print user.get_age
    
**结果：**

    can not find the attr

#### **(2)`__getattribute__`**

这个魔法函数在属性调用的时候会第一个调用，不管属性存不存在，可以把他看成是属性调用的入口，因为地位比较重要因此不建议重写

**示例代码:**

    from  datetime import date,datetime
    class User(object):
        def __init__(self,name,birthday):
            self.name = name
            self.birthday = birthday
    
        def __getattr__(self, item):
            return "can not find the attr"
    
        def __getattribute__(self, item):
            return "hello"
    
    if __name__ == '__main__':
        user = User("K0rz3n",date(year = 1999,month = 11,day = 12))
        print user.get_age

**结果：**

    hello

### **3.属性描述符和属性的查找过程**


#### **（1）属性描述符**：

开发过程中可能会遇到一个问题：们想对用户传进来的数据类型进行控制，但是由于我们需要控制的数据过多，我们不能每一个都写同样的代码，于是这就涉及到了属性描述符，实际上属性描述符也只是实现了几个魔法方法而已（`__get__` 、`__set__` 、`__delete__`）

下面我们就自己实现一个属性描述符

**示例代码:**

    import numbers
    class IntField(object):
        def __get__(self, instance, owner):
            return self.value
        def __set__(self, instance, value):
            if not isinstance(value,numbers.Integral):
                raise ValueError("not int")
            self.value = value
        def __delete__(self, instance):
            pass
    
    class User(object):
        age = IntField()
    
    if __name__ == '__main__':
        user = User()
        user.age = "xxx"
        print user.age


**结果：**

    ValueError: not int


当然我们能多加几个条件：

**示例代码：**

    import numbers
    class IntField(object):
        def __get__(self, instance, owner):
            return self.value
        def __set__(self, instance, value):
            if not isinstance(value,numbers.Integral):
                raise ValueError("not int")
            if value < 0:
                raise ValueError("not > 0")
            self.value = value
        def __delete__(self, instance):
            pass
    
    class User(object):
        age = IntField()
    
    if __name__ == '__main__':
        user = User()
        user.age = -9
        print user.age


**结果：**

    ValueError: not > 0

### **4.`__new__` 和 `__init__` 的区别**

`__new__` 可以自定义类的生成过程,而`__init__`控制的是类生成的对象，因此 new 的调用是在 init 的之前

**示例代码:**
    
    class User(object):
        def __new__(cls, *args, **kwargs):
            print "new"
        def __init__(self,name):
            self.name = name
            print "init"
    if __name__ == '__main__':
        user = User("K0rz3n")


**结果：**

    new


这里其实还发现一个问题，就是如果 new 方法不返回对象，Init 就永远得不到调用

**示例代码：**

    class User(object):
        def __new__(cls, *args, **kwargs):
            print "new"
            return super(User,cls).__new__(cls)
        def __init__(self,name):
            self.name = name
            print "init"
    
    if __name__ == '__main__':
        user = User("K0rz3n")


**结果：**

    new
    init
    


### **5.自定义元类**

**概念：**

元类是什么呢？元类就是创建类的类，比如我们之前讲的 type() 就是一个元类，再比如我们在Py3 中创建类的时候在括号中写 metaclass =  这个就是在指明这个类的元类，MetaClass 也是一个类，他是继承于type的，我们自定义 MetaClass 来控制类对象生成的过程


通过 type() 动态的创建一个类,**语法是 type(类名，继承关系，属性或者方法)**

**示例代码：**

    User = type("User",(),{})
    
    user = User()
    
    print type(user)

**结果：**

    <class '__main__.User'>

**我们现在来创建一个带有属性的类**

**示例代码：**

    User = type("User",(),{"name":"K0rz3n"})
    user = User()
    print user.name

**结果:**

    K0rz3n


**我们现在来创建一个带有方法的类**

**示例代码：**

    def get_age(self):
        print "get_age"
    
    if __name__ == '__main__':
        User = type("User",(),{"name":"K0rz3n","get_age":get_age})
        user = User()
        print user.name
        print user.get_age()

**结果：**
    
    K0rz3n
    get_age
    None


**我们可以创建一个继承基类的类**

**示例代码：**

    class BaseClass(object):
        def answer(self):
            return "i am baseclass"
    
    
    if __name__ == '__main__':
        User = type("User",(BaseClass,),{"name":"K0rz3n","get_age":get_age})
        user = User()
        print user.answer()

**结果:**

    i am baseclass
    


## **八、Python 的迭代器和生成器**

### **1.Python 中的迭代协议：**

**什么是迭代器？**

迭代器是访问集合类元素的一种方式，我们能实现for循环其实背后就是迭代器,迭代器在Python 中叫做 Iterator ,背后实现的是 `__next__` 和 `__iter__` 方法

**示例代码：**

    from collections import Iterable,Iterator
    a = [1,2,3,4,5]
    
    print isinstance(a,Iterable)
    print isinstance(a,Iterator)

**结果：**
    
    True
    False


**解释：**

可以看到 a 是一个列表，他是可迭代的，但是他并不是一个迭代器


### **2.Python 中迭代器和可迭代对象的区别：**

#### **1.回顾一下之前的代码：**

    class Company(object):
        def __init__(self,employ_list):
            self.employ = employ_list
        def __getitem__(self, item):
            return self.employ[item]
    
    if __name__ == '__main__':
        company = Company(["tom","bob","angel"])
        for item in company:
            print item
    
**为什么这个能执行循环呢？**

实际上，我们在调用循环的时候会自动调用，iter() 这个方法，这个方法回去寻找实例化这个对象的类中有没有`__iter__` 这个方法,如果没有他会再退一步寻找`__getitem__` 这个魔法方法，然后完成调用.


#### **2.我们来自己实现一个迭代器：**

**示例代码：**

    # coding=utf-8
    from collections import Iterator
    
    class MyIterator(Iterator):
        def __init__(self, employ_list):
            self.iter = employ_list
            self.index = 0
    
        def next(self):  # 真正返回迭代之的逻辑
            try:
                word = self.iter[self.index]
            except IndexError:
                raise StopIteration
            self.index += 1
            return word
    
    
    class Company(object):
        def __init__(self, employ_list):
            self.employ = employ_list
    
        def __getitem__(self, item):
            return self.employ[item]
    
        def __iter__(self):
            return MyIterator(self.employ)
    
    
    if __name__ == '__main__':
        company = Company(["tom", "bob", "angel"])
        for i in company:
            print i
        
**结果：**

    tom
    bob
    angel


### **3.Python 中的生成器函数：**

#### **1.概念：**

> **什么是生成器函数？**
> 
> 只要函数中有 yield 关键字，这个函数就是生成器函数

**示例代码：**
    
    def gen_test():
        yield 1
    
    def test():
        return 2
    
    if __name__ == '__main__':
        gen = gen_test()
        res = test()
    
        print gen
        print res

**结果：**
    
    <generator object gen_test at 0x00000000032457E0>
    2

**解释：**

我们看到，看似同样是定义一个函数，但是第一个实际上是一个生成器，生成器返回的是一个生成器对象，第二个返回的只是一个干巴巴的数据。

**那么我们怎么利用这个生成器的返回值呢？**

实际上他底层实现也是一个生成器，既然是生成器我们就能通过循环的方式调用

**示例代码：**

    def gen_test():
        yield 1
    
    def test():
        return 2
    
    if __name__ == '__main__':
        gen = gen_test()
        res = test()
    
        for i in gen:
            print i
        
**结果：**

    1

#### **2.看一下生成器的优势：**

原始的生成 斐波那契数列的方法：

**示例代码：**

    def fib(index):
        fib_list = []
        n,a,b = 0,0,1
        while n < index:
            fib_list.append(b)
            a,b = b,a+b
            n += 1
        return fib_list
    
    fib_list = fib(10)
    print fib_list
    
**结果：**

    [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]


但是这种方法在数字非常大的时候就非常的占用内存空间，他会先把结果放在数组中再返回给我们，于是我们尝试使用生成器

**示例代码：**

    def fib(index):
        n,a,b = 0,0,1
        while n < index:
            yield b
            a,b = b,a+b
            n += 1
    
    if __name__ == '__main__':
        fib_list = []
        for i in fib(10):
            fib_list.append(i)
        print fib_list
    
**结果：**

    [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]

生成器方式就能实现在循环中不断生成，避免了一下子生成，（当然我这个代码为了方便大家看最后还是放在了列表中）


### **4.Python 是怎么实现生成器的：**

#### **1.先来讲讲Python 是怎么实现函数调用的**

**实例函数：**


    def foo():
        pass
    def bar():
        pass

首先 python 有一个C语言写的Python的解释器，Python 的所有的代码都是运行在这个解释器之上的，然后我们开始调用foo 函数，一调用函数解释器就会在堆上分配一个栈帧，然后我们在这个栈帧上执行这个函数，然后发现他调用了一个子函数，又会在堆上分配一个新的栈帧，所有的栈帧都分配在堆上，这也就是说，栈帧能脱离于调用者存在

而生成器实际上就是对我们的栈帧进行的一次封装。保存了当前运行的状态，也就是说，我们能够使用其实现迭代

#### 接下来介绍函数装饰器的使用

这一部分我打算单独拿出来做一篇文章，请关注我的文章列表的 **Python实用编程技巧(装饰器篇)**



