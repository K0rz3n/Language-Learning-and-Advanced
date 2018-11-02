---
title: Python 实用编程技巧（序列篇）
date: 2018-9-2 15:48:18
tags: 编程 Python 进阶 备忘
categories: 编程
---

## 前言

Python 作为我们平时使用频率最高的脚本语言拥有着灵活、简洁、高效的特点，但是实际上大多数情况下我们都没有能够发挥其最大的能力，于是分几个部分总结一下编程的技巧，同时也期望对Python 有一个更深的认识。

## **1.如何在列表，字典，集合中筛选出指定条件的项**

最简单的方式就是循环
<!-- more -->

    dict=[1,2,-2,3,-5,13,5.2]
    res=[]
    for i in dict:
        if i>0:
            res.append(i)
    print res
    
但是循环代码非常的臃肿并且执行的效率很低，于是我们考虑使用函数式编程的方法
**（1）列表：**

filter函数：

    from random import randint
    data = [randint(-10,10) for i in range(10)]
    data = filter(lambda x:x>=0,data)
    print data



列表解析：

    from random import randint
    data = [randint(-10,10) for i in range(10)]
    data = [x for x in data if x>=0]
    print data

**（2）字典**

字典解析：

    grade = {x:randint(60,100) for x in range(1,20)}
    grade = {k:v for k,v in grade.iteritems() if v >90}
    print grade

**（3）集合**

集合解析：

    s = set([randint(-10,10) for i in range(10)])
    s = {x for x in s if x%3==0}
    print s

## **2.如何为元组中的每个元素命名来提高程序可读性**

因为元组结构简单，访问速度快，所以常常被我们用来存放结构一致的数据，但是他的访问还是存在一些问题，比如我们想访问元组中的元素的时候必须使用的是下标的方式，这样就大大降低了程序的可读性，于是我们需要找一个策略解决这个问题。

### **方法一：采用宏定义或者枚举类型类似的方式**
    
    
    NAME,AGE,SEX,EMAIL = xrange(4)
    student =("tom",16,"male","tom@163.com")
    
    print student[NAME]
    print student[AGE]
    print student[SEX]
    print student[EMAIL]

**注意：解释一下 xange() 在这里面的应用**

xrange() 区别于 range() ,它会生成一个生成器，然后这里面又利用了列表的拆包原理进行了循环的赋值

### **方法二：使用namedtuple这个模块创建一个命名元组**

    from collections import namedtuple
    
    Student = namedtuple("Student",['name','age','sex','email'])
    s = Student("tom",16,"male","tom@163.com")
    print s 
    print s.age

上面我们使用的是位置传参的方式，我们还能使用关键字进行传参，这样就不用位置一一对应


## **3.如何根据字典的键值对字典的元素进行排序**



### **方法一：使用 sorted() 方法的key 参数**


    from random import randint
    data = [randint(0,20) for i in range(30)]
    
    s = dict.fromkeys(data,0)
    
    for i in data:
        s[i]+=1
    
    print sorted(s.items(),key=lambda x:x[1])

**参数解释：**

（1）items()可将字典中的每个键值对转换成一个一个的元组，并将这些元组组合成一个list。
（2）key值的传入，我们这里使用匿名函数lambda，利用这个函数，我们可以对字典进行按键、按值排序，即x[0]则为按键排序，x[1]则为按值排序。

### **方法二：使用zip()将字典键值对互换排列**

因为 sorted() 是按照先比较第一个值在比较第二个值的方式进行比较的，于是我们在转化成元组列表的时候需要对键值对互换

因为我们能通过字典的 keys 和 values 方法分别获得键值对的列表，而 zip 正好能将多个列表重组成为元组列表。

    from random import randint
    data = [randint(0,20) for i in range(30)]
    
    s = dict.fromkeys(data,0)
    
    for i in data:
        s[i]+=1
    
    print sorted(zip(s.values(),s.keys()))
    

## **4.如何统计序列中出现的元素的频度**


### **方法一：先转化成字典**

最开始我们的方法可以是将一个序列的值作为字典的键，然后键对应的值默认是0，我们在利用循环遍历整个序列，不断的为字典的值加一


    from random import randint
    data = [randint(0,20) for i in range(30)]
    
    s = dict.fromkeys(data,0)
    for i in data:
        s[i]+=1
    print s

我们现在有了字典以后就需要**根据字典的值对结果进行排序**

### **方法二：使用 collections 的  Counter 方法**

经过 Counter 方法包装过的字典自带了一个叫做most_common 的方法，能直接找到出现频率较高的的指定个数的元素

    from random import randint
    from collections import Counter
    data = [randint(0,20) for i in range(30)]
    
    s = dict.fromkeys(data,0)
    
    for i in data:
        s[i]+=1
    
    print Counter(s).most_common(3)
  
  
  **实例: 统计LICENCE 文件中出现频率最多的单词**
  
    import re
    from collections import Counter
    txt = open('LICENCE').read()
    print Counter(re.split('\W+',txt)).most_common(10)

结果：
[('the', 309), ('of', 210), ('to', 177), ('a', 171), ('or', 138), ('you', 106), ('work', 97), ('that', 91), ('and', 91), ('in', 76)]


## **4.如何快速找到字典中的公共键**

### **方法一：按照一个字典的键值进行遍历**

我们最简单的思路就是找按照一个字典的键进行遍历，然后判断是不是在另外的字典中

    from random import randint,sample
    
    s1 = {i:randint(1,4) for i in sample('abcdefg',randint(1,7))}
    
    s2 = {i:randint(1,4) for i in sample('abcdefg',randint(1,7))}
    
    s3 = {i:randint(1,4) for i in sample('abcdefg',randint(1,7))}
    
    res = []
    for x in s1:
        if x in s2 and x in s3:
            res.append(x)
    
    print res
    

### **方法二：使用集合的交集操作**

集合有一个内置的方法就是 viewkeys 能够以集合的形式显示出来所有的键值

    from random import randint,sample
    
    s1 = {i:randint(1,4) for i in sample('abcdefg',randint(1,7))}
    
    s2 = {i:randint(1,4) for i in sample('abcdefg',randint(1,7))}
    
    s3 = {i:randint(1,4) for i in sample('abcdefg',randint(1,7))}
    
    print s1.viewkeys() & s2.viewkeys() & s3.viewkeys()


### **方法三：使用函数式编程 map() 和 reduce() 配合**

reduce() 在两两进行操作的时候的作用非常的大

    from random import randint,sample
    
    s1 = {i:randint(1,4) for i in sample('abcdefg',randint(1,7))}
    
    s2 = {i:randint(1,4) for i in sample('abcdefg',randint(1,7))}
    
    s3 = {i:randint(1,4) for i in sample('abcdefg',randint(1,7))}
    
    res = reduce(lambda x,y:x&y,map(dict.viewkeys,[s1,s2,s3]))
    
    print res

## **5.如何让字典保持有序**

#### 使用 collections 下面的 OrdetredDict方法

    from collections import OrderedDict
    
    d = OrderedDict()
    
    d['Jim'] = (1,20)
    d['Bob'] = (2,30)
    d['Keen'] = (3,40)
    
    for i in d:
        print i


## **6. 如何实现显示用户的历史记录功能**

### **方法一：使用容量为n的队列**

使用collections 中的 deque 方法
    
    from collections import deque
    
    q = deque([],5)
    q.append(1)
    q.append(2)
    
    print q

但是这个方法有一个弊端，就是我们目前是把记录存储在内存中的，一旦程序退出就消失了，于是我们需要使用文件进行存储

### **方法二：使用 pickle 这个工具**

pickle 能将python 对象存入文件中，还能将该对象从文件中还原出来

    from collections import deque
    import pickle
    q = deque([],5)
    
    q.append(1)
    q.append(2)
    
    pickle.dump(q,open('history','w'))
    
    q2 = pickle.load(open('history'))
    
    print q2
    

