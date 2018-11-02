---
title: Python 实用编程技巧（字符串篇）
date: 2018-9-5 21:56:18
tags: 编程 Python 进阶 备忘
categories: 编程
---

## **1.如何拆分含有多种分隔符的字符串**

### **(1)我们首先考虑单一分隔符的情况：**

 s = "10800       1   10800      10800  ?         197609 14:12:16 /usr/bin/mintty"
 
 我们使用字符串的 split()方法

<!-- more -->
    
    s = "10800       1   10800      10800  ?         197609 14:12:16 /usr/bin/mintty"
    print s.split()


### **(2)多重分隔符**

**方法一：既然split() 这个函数每次只能匹配一个，那我们可以弄一个循环**
    
    
    def mySplit(s,ds):
        res = [s]
        for d in ds:
            t = []
            map(lambda x:t.extend(x.split(d)),res)
            res = t
        return t
    
    s = 'asb,ksh|aw.mwdn/zxcm?xmcin<zxc'
    ds = ',|./?<'
    print mySplit(s,ds)

这种情况下如果出现连续的两个分隔符的话，结果就会多出一个空白，我们最好还加一个过滤

    def mySplit(s,ds):
        res = [s]
        for d in ds:
            t = []
            map(lambda x:t.extend(x.split(d)),res)
            res = t
        return [x for x in t if x]
    
    s = 'asb,ksh|aw.mwdn/zxcm??xmcin<zxc'
    ds = ',|./?<'
    print mySplit(s,ds)
    

**方法二：使用 re.split() 正则匹配函数，一次性完成**

    import re
    s = 'asb,ksh|aw.mwdn/zxcm??xmcin<zxc'
    print re.split(r'[,|./?<]+',s)

## **2.如何判断字符串a以字符串b开头或者结尾**

使用字符串的 startwith 和 endwith 

比如我们需要找出当前目录下的一以 java 、php 、log 结尾的文件，并去除一部分权限

    import os,stat
    
    s = [x for x in os.listdir('.') if x.endswith(('log','java','php'))]
    a = map(lambda i:oct(os.stat(i).st_mode),s)
    map(lambda i:os.chmod(i,stat.S_IXOTH),s)
    b = map(lambda i:oct(os.stat(i).st_mode),s)
    
    print a
    print b
    
**结果：**

    ['0100666', '0100666', '0100666']
    ['0100444', '0100444', '0100444']


## **3.如何调整字符串中文本的格式**

比如我们有这样的需求，将日志文件中的本来的年月日格式：2018-01-02 全部替换成 01/02/2018 这样的格式

我们使用正则表达式  re.sub()

    
    import re
    
    s = open("./test.log").read()
    
    print re.sub('(\d{4})-(\d{2})-(\d{2})',r'\2/\3/\1',s)
    
    
## **4.如何将一个小字符串拼接成一个大字符串**

### **方法一：使用 + 拼接两个字符串**
    
    str1 = "111"
    str2 = "222"
    
    print str1+str2
    
这里说一下，其实这个+是字符串自带的方法，是 `__add__` 的重载，相当于执行了 

    srr.__add__(str1,str2)

如果字符串在一个列表中的话：
    
    
    l = ["zxc","zzzz","weqe","1231","<><>","[][]"]
    
    print reduce(lambda x,y:x+y,l)
    
### **方法二：使用join 方法**

join 能传入一个可迭代对象
    
    
    l = ["zxc","zzzz","weqe","1231","<><>","[][]"]
    
    print ";".join(l)

**结果：**

    zxc;zzzz;weqe;1231;<><>;[][]


**注意：**

如果我们的列表里面不只有字符串还有数字类型怎么办？

**方法一：我们使用列表解析**
    
    pl = [123,"xxx",456,"dasdafa"]
    
    print "".join([str(x) for x in pl])

但是这种方法会产生一个新的列表，如果列表很长的胡话就会非常耗费资源，于是有了方法二

**方法二：使用生成器**

不加[]的列表解析语句会生成一个生成器

    pl = [123,"xxx",456,"dasdafa"]
    
    print "".join(str(x) for x in pl)
    


## **5.如何对字符串进行了左右居中的对齐**

### **方法一：使用字符串的 str.ljust(),str.rjust(),str.center()方法**

    s = "asd"
    
    print s.ljust(20)
    print s.ljust(20,"=")
    print s.center(20)
    print s.center(20,"=")
    print s.rjust(20)
    print s.rjust(20,"=")
    

**结果：**

    asd
    asd=================
            asd
    ========asd=========
                     asd
    =================asd


### **方法二：使用内置的字符串格式化方法----format**
    
    s = "asd"
    
    print format(s,"<20")
    print format(s,">20")
    print format(s,"^20")

**结果：**

    asd
                     asd
            asd
    

## **6.去掉字符串中不需要的字符**

举例几个可能的情况

1. 我们需要去掉字符串两边的空白符
2. 我们需要将Windows 下的换行符\r\n，转换成 Linux 下的换行符 \n
3. 我们需要去掉拼音中的音调

### **方法一：strip() 、lstrip() 、 rstrip()**
    
    s = "  dasda  asda  "
    print s.strip()

lstrip() 、 rstrip() 同理，我就不再举例

现在如果是 "-----asda  adadad+++++"这种字符串的话，我们依然能用 strip()
    
    
    s = "-----adada  asdada++++"
    print s.strip("-+")

### **方法二：使用切片加 +(拼接)删除固定位置的字符**
    
    s = "asdb:213"
    print s[:4]+s[5:]

### **方法三：字符串的replace()或者 re.sub()**

    s = "\tasd\t123\t789"
    
    print s.replace("\t","")
    
如果有多个需要替换的话建议使用正则表达式 re.sub()

    import re
    s = "asd,asqw\tzxc|"
    print re.sub('[,\t|]',"",s)
    

### **方法四：字符串的translate 方法来替换多个字符**

先来介绍一下 translate

translate 是映射功能，可以根据映射表来将字符替换

首先创建一个映射表
    
    import string
    string.maketrans('abcxyz','xyzabc')

意思是将 abcxyz 映射为 xyzabc

然后就是根据映射表来操纵字符串

    import string
    string.maketrans('abcxyz','xyzabc')
    s = "abc9790907xyz"
    
    print s.translate(string.maketrans('abcxyz','xyzabc'))
    

translate 除了能根据映射替换字符以外，当你第一个参数传递为 None 的时候，第二个参数可以传递逆向删除的字符的自负串
    
    s = "asd/afji,1231\tzzz\r\n"
    
    print s.translate(None,',/\t\r\n')

**补充：如何去除标点**
        
标点实际上是 unicode 的一个组合字符


s = u'ni\u0301 ha\u030co,chi\u0304 fa\u0300n'

我们主要是利用 unicode 的 translate 方法,它传入一个字典


    s = u'ni\u0301 ha\u030co,chi\u0304 fa\u0300n'
    print s.translate(dict.fromkeys([0x0301,0x030c,0x0304,0x0300]))
    
