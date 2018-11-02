---
title: Python 实用编程技巧（迭代篇）
date: 2018-9-5 14:11:18
tags: 编程 Python 进阶 备忘
categories: 编程
---

## **1.如何实现可迭代对象和迭代器对象**

如果想从网络上抓取数据存入字典，然后再对字典进行迭代显示，由于网络I/O操作的时间相对较长，这样就会造成用户的长时间等待，我们希望能一次抓取就显示一次，于是迭代器对象出现了。
<!-- more -->
在 for 循环的时候 in 后面跟的是一个可迭代对象，在循环的过程中自动调用 iter(） 将可迭代对象传入其中，返回一个迭代器对象

**比如我们常见的列表和字符串都是可迭代对象，为什么呢？**

这涉及到了 Python 的魔法方法的问题，python一切皆对象，而魔法方法就是好像是python对象的一个插件，有什么样子的魔法方法，python 对象就会在关键时刻显示某种特性（仿佛科幻小说中主人公体内某种隐藏的力量被激活）。迭代对象有一个魔法方法 `__iter__`,如果没有这个方法，那么python 还会退而求其次，去寻找`__getitem__` 这个代表他是一个序列的方法，也是可迭代的。

迭代器对象只有一个方法就是 next()，每调用一次就会迭代一次，知道全部迭代完毕抛出异常，这其实也是for 循环的工作机制（这同时也说明了一个问题：迭代器内部持有一个状态，该状态用于记录当前迭代所在的位置，以方便下次迭代的时候获取正确的元素）。



    l = [1,2,3,4,5,6]
    
    t = iter(l)
    print t.next()
    print t.next()
    print t.next()

**结果：**

    1
    2
    3


**实例：**

1. 实现一个迭代器对象，有next 方法每次返回一个值
2. 实现一个可迭代对象  `__iter__` 方法返回上面的那个迭代器对象

实际上就是创建一个可迭代对象的类，实例化以后成为一个可迭代对象，然后一旦在循环中调用这个可迭代对象就能自动调用`__init__`,然后实例化迭代器对象的类，这个类的实例会在迭代中不断调用next方法。

代码如下：
    
    import requests
    
    from collections import Iterable,Iterator
    
    class WeatherIterator(Iterator):
        def __init__(self,cities):
            self.cities = cities
            self.index = 0
    
        def getWeather(self,city):
            r = requests.get(u"http://wthrcdn.etouch.cn/weather_mini?city=" + city)
            data = r.json()['data']['forecast'][0]
            return '%s: %s , %s' % (city, data['low'], data['high'])
    
        def next(self):
            if self.index == len(self.cities):
                raise StopIteration
            city = self.cities[self.index]
            self.index += 1
            return self.getWeather(city)
    
    
    class WeatherIterable(Iterable):
        def __init__(self,cities):
            self.cities = cities
    
        def __iter__(self):
            return WeatherIterator(self.cities)
    
    
    for x in WeatherIterable([u"北京",u"上海",u"广州",u"长春"]):
        print x


## **2.如何使用生成器函数实现可迭代对象**

那么什么是生成器？

生成器对象其实是一种特殊的可迭代对象，他自己调用`__iter__`方法返回的是他自身，因此他既是一个可迭代对象，也是一个迭代器对象，而且它不需要再像上面的类一样写`__iter__()`和`__next__()`方法了，只需要一个yiled关键字（当然你可以重写`__iter__`来实现自己的功能）。 (说人话就是这个生成器的对象在每一次迭代的时候都会被yiled卡住并返回，下一次再迭代就会接着上次执行，是不是很优雅？)

举一个简单的生成器的例子：

    def f():
        print 'first'
        yield 1
    
        print 'second'
        yield 2
    
        print 'third'
        yield 3
    
    g = f()
    for x in g:
        print x
 
 **结果：**   
 
    first
    1
    second
    2
    third
    3



**实例：**

找出指定范围内的所有素数


    class PrimeNumbers:
        def __init__(self,start,end):
            self.start = start
            self.end = end
    
        def isPrimeNum(self,k):
            if k<2:
                return False
            for x in xrange(2,k):
                if k % x == 0:
                    return False
            return True
    
        def __iter__(self):
            for k in xrange(self.start,self.end+1):
                if self.isPrimeNum(k):
                    yield k
    
    for x in PrimeNumbers(1,100):
        print x

## **3.如何进行反向迭代以及如何实现反向迭代**

### **列表的反向迭代**

（1）使用列表的反转操作

    l = [1,2,3,4,5]
    x = l.reverse()

但这种情况会改变原列表

（2）使用切片且步进为-1
    
    l = [1,2,3,4,5]
    x = l[::-1]

但这样会生成一个新的列表

（3）列表反向迭代器

    l = [1,2,3,4,5]
    for x in reversed(l):
        print x

这种情况和iter()刚好是相反的，在迭代的时候会自动调用 `__reversed__`对象。

**实例：**

写一个浮点数生成器，既可以正向迭代又可以反向迭代

    class FloatRange:
        def __init__(self,start,end,step):
            self.start = start
            self.end  = end
            self.step = step
    
        def __iter__(self):
            t = self.start
            while t <= self.end:
                yield t
                t +=self.step
        def __reversed__(self):
            t = self.end
            while t >= self.start:
                yield t
                t -= self.step
    
    for x in FloatRange(1.0,3.0,0.5):
        print x
    
    print "===============cut-off rule====================="
    
    for x in reversed(FloatRange(1.0,3.0,0.5)):
        print x
        

## **4.如何对迭代器做切片操作**

我们知道文本文件本身也是一个可迭代对象，每次迭代返回的是文本文件的一行，那么我们思考一个问题，我们能不能像对列表切片一样对文本文件切片得到一个迭代器（生成器），这样比如我们想迭代的是100行带300行之间的内容就能直接迭代了。

### **简单回顾文件迭代**

由于文件对象没有`__getitem__`这个方法，于是没有和列表一样的迭代操作，那我们就可以先把文件的内容放到一个列表里面，然后再进行切片，如下：

    f = open('./LICENCE')
    lines = f.readlines()
    for x in lines[100:300]:
        print x
    
但是这样有一个问题，readlines 会把文件的所有内容都先加载到内存里面，但是如果文件非常大，比如有几个G大小，那么就会遇到内存不足的问题，于是我们只能选择使用

    for line in f:
        print line,

**注意：**如果文件指针此时已经在文件的末尾，你是循环不出内容的，我们还需要将使用 f.seek(0)，将文件指针还原回去

因此我们迫切的需要将文件变成一个迭代器。
    
    from itertools import islice
    f = open('./LICENCE')
    for i in islice(f,100,300):
        print i


如果是想得到前100行的迭代器

    from itertools import islice
    f = open('./LICENCE')
    for i in islice(f,100):
        print i

如果想得到从100行开始到最后的迭代器

    from itertools import islice
    f = open('./LICENCE')
    for i in islice(f,100,None):
        print i

注意： islice() 虽然看上去是从100开始的，但是前99行实际上也迭代了，因此下一次使用的时候注意还原。

## **5.如何在一个for 语句中迭代多个可迭代对象**

### **1.并行迭代**

比如 语数外三科成绩分别存储在3个列表中，我们现在需要同时迭代三个列表取出三个成绩，并计算总成绩

最简单的我们可以使用索引的方式
    
    from random import randint
    chinese = [randint(60,100) for i in xrange(40)]
    math = [randint(60,100) for i in xrange(40)]
    english = [randint(60,100) for i in xrange(40)]
    
    for x in xrange(len(math)):
        print chinese[x]+math[x]+english[x]
        
但是这个方法有局限性，因为并不是所有的可迭代对象都支持索引的方法访问其中的元素

**高阶推荐：zip()**

zip() 中能传入多个**可迭代对象**并将其逐项合并成一个元组列表，然后我们就能使用元组拆包的方式进行迭代
    
    from random import randint
    chinese = [randint(60,100) for i in xrange(40)]
    math = [randint(60,100) for i in xrange(40)]
    english = [randint(60,100) for i in xrange(40)]
    
    grade = []
    for c,m,e in zip(chinese,math,english):
        grade.append(c+m+e)
    print grade

### **2.串行**

比如每个班的英语成绩放在一个列表中，现在想迭代全年级的英语成绩，找出分数高于90分的人数

使用 itertools 的 chain 可以多个可迭代对象进行串行连接

    from random import randint
    from itertools import chain
    e1 = [randint(60,100) for i in xrange(40)]
    e2 = [randint(60,100) for i in xrange(40)]
    e3 = [randint(60,100) for i in xrange(40)]
    e4 = [randint(60,100) for i in xrange(40)]
    
    count = 0
    
    for x in chain(e1,e2,e3,e4):
        if x > 90:
            count += 1
