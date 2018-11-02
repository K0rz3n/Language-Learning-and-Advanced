---
title: Python 实用编程技巧（文件篇）
date: 2018-10-1 14:29:18
tags: 编程 Python 进阶 备忘
categories: 编程
---

## **一、如何读写文本文件**

这个问题看似简单，但是由于py2 和 py3 的巨大差异导致我们依然要深入研究一下。

 那么 python2 和 python3 的什么区别导致了这种差异的发生呢？
 
<!-- more -->
>  **答：python2 和 python3 之间字符串的语意发生了变化**

**python2 有两种字符串的类型：**

**（1）str** 

表面上看是字符串，但是实际上是抽象的一串连续的字节（字符串中每个字符都是一个字节）

**（2）unicode**

这个类型是在更多不同的语言加入以后不得不进行调整的，不再用一个字节表示一个字符，但是，我们进行文件操作的时候是不支持直接将多个字节一下子存储进去的，我们必须将其转化成某种单个字节的编码再进行存储操作

**示例代码：**

将文字编码：

    In [1]: a = u'你好世界'
    In [2]: a.encode('utf8')
    Out[2]: '\xe4\xbd\xa0\xe5\xa5\xbd\xe4\xb8\x96\xe7\x95\x8c'
    In [3]: a.encode('gbk')
    Out[3]: '\xc4\xe3\xba\xc3\xca\xc0\xbd\xe7'

可以看到 utf-8 使用的字节比Gbk多

将文字解码:

注意：这里编码和解码一定要对应，否则会出现乱码。

**代码示例：**

    In [5]: '\xe4\xbd\xa0\xe5\xa5\xbd\xe4\xb8\x96\xe7\x95\x8c'.decode('gbk')
    Out[5]: u'\u6d63\u72b2\u30bd\u6d93\u682b\u666b'
    
    In [6]: print u'\u6d63\u72b2\u30bd\u6d93\u682b\u666b'
    浣犲ソ涓栫晫


**代码示例：**

    In [8]: '\xe4\xbd\xa0\xe5\xa5\xbd\xe4\xb8\x96\xe7\x95\x8c'.decode('utf8')
    Out[8]: u'\u4f60\u597d\u4e16\u754c'
    
    In [9]: print u'\u4f60\u597d\u4e16\u754c'
    你好世界
    

### **1.python2 的文本读写**

文本就是Unicode 字符串，我们写入文件前先要对其选择一个方式进行编码，我们读取文件的时候也要对其进行先解码，将其换原成 unicode 。

**文本的写入：**

    f = open('test.txt','w')
    s = u'你好世界'
    f.write(s.encode('utf8'))

**文本的读出：**
    
    In [8]: f = open('test.txt','r')
    
    In [9]: t = f.read()
    
    In [10]: t
    Out[10]: '\xe4\xbd\xa0\xe5\xa5\xbd\xe4\xb8\x96\xe7\x95\x8c'

可以看到这样读出来的都是字节码

我们必须要进行转化：

    In [11]: t.decode('utf8')
    Out[11]: u'\u4f60\u597d\u4e16\u754c'
    
    In [12]: print u'\u4f60\u597d\u4e16\u754c'
    你好世界
    

### **2.python3 的文本读写**

python3 对字符串的支持更加清晰合理，unicode 就是 python3 中的 str 而 str 就是python3 中的 byte

就比如说，python2 中字符串前面加一个u 才代表 unicode 字符串但是 python3 什么都不用加直接就是，反倒是如果想成为一个 byte 字符串要加 b 

python3 的读写可以指定一个参数 encoding = '' 自动的进行编解码

**文本的写入：**
    
    f = open('test.txt','w',encoding = 'utf8')
    f.write("国庆快乐")

**文本的读出：**
    
    f = open('test.txt','r',encoding='utf8')
    t = f.read()
    print (t)
    



## **二、如何处理二进制文件**

wav 是音频问价，前44字节是文件的头信息，我们可以尝试将其读出来

    In [6]: f = open('test.wav','rb')
    
    In [7]: info = f.read(44)
    
    In [8]: info
    Out[8]: 'RIFFt\x9b\x0b\x00WAVEfmt \x10\x00\x00\x00\x01\x00\x02\x00\x11+\x00\x00D\xac\x00\x00\x04\x00\x10\x00dataP\x9b\x0b\x00'
    
那么我们如何将其解析出来呢？
我们可以使用 struct 这个库中的unpack 方法

**举个例子：**

h 是一个 short 的意思

    import struct
    
    print struct.unpack('h','\x01\x02')//小端序
    print '\n'
    print struct.unpack('>h','\x01\x02')//大端序


    (513,)//--------->2x256+1
    
    (258,)//--------->1x256+2
    

比如我们的音频文件的 22-24 字节是声道数，于是我们就能使用这个函数将其解析出来

**代码示例：**

    import struct
    f = open('test.wav','rb')
    info = f.read(44)
    print struct.unpack('h',info[22:24])
  
**结果：**

    (2,)

说明这个音频文件是双声道


再比如我们想读音频文件的采样频率，这个东西是24-28 4个字节

**代码示例:**
    
    import struct
    
    f = open('test.wav','rb')
    info = f.read(44)
    print struct.unpack('i',info[24:28])//因为这里是4个字节所以我们就是用的i 

**结果：**

    (11025,)

**综合实验：**

我们现在想读入音频文件的数据部分，并且不能以字符串的形式，因为字符串的形式没法进行输入操作，我们可以将其以每两个字节为一组读入一个buf 

我们首先看一下我们的音频的数据有多大：
    
    import array
    f = open('test.wav', 'rb')
    f.seek(0, 2)  # 移动指针的位置
    t = f.tell()  # 返回指针的位
    a = (t-44)/2  # 每两个一组进行分组
    print a

a  就是最终的组数

然后我们就创建指定类型大小的Buf，我们把buf初始化，并将44字节以后的输入写入buf
    
    buf = array.array('h',(0 for _ in xrange(a)))
    f.seek(44)
    f.readinto(buf)

接下来我们对 buf 进行操作，将操作完的结果重新写成另一个文件
    info = f.read(44)
    for i in xrange(a):
        buf[i]/=8
    
    f2 = open('demo.wav','wb')
    f2.write(info)
    buf.tofile(f2)
    f2.close()
  
 以上步骤，我们其实已经将我们的音频声音变成了原来的1/8
 
 
 **代码示例：**
     
    # coding=utf-8
    import struct
    import array
    f = open('test.wav', 'rb')
    info = f.read(44)
    f.seek(0, 2)  # 移动指针的位置
    t = f.tell()  # 返回指针的位置
    a = (t-44)/2
    buf = array.array('h',(0 for _ in xrange(a)))
    f.seek(44)
    f.readinto(buf)
    
    for i in xrange(a):
        buf[i]/=8
    
    f2 = open('demo.wav','wb')
    f2.write(info)
    buf.tofile(f2)
    f2.close()
    

## **三、如何设置文件的缓冲** 
 
将文件写入硬件设备的时候是按照块进行写入的，每个块有固定的大小，也就是说，进行一次写入操作的时间是固定的，不管你有没有把块填满，因此如果没有缓冲，就会在块不满的情况下进行写入，导致次数过多时间过长，于是我们需要设置文件缓冲。

缓冲又分为全缓冲和行缓冲，一般文件的缓冲就是全缓冲，意思就是填满一个就释放一个，而行缓冲就是遇到换行符就释放。

当然并不是所有的设备都需要缓冲的，串口设备我们就希望能即时发送，于是我们不设置缓冲区

### **1.检验缓冲区大小实验：**

**ipython ：**

In [13]: f = open('demo.txt','w')

In [14]: f.write('abc')

In [15]: f.write('+'*4093)

In [16]: f.write('-')

**另一个终端：**

    $ tail -f demo.txt

14、15 命令执行以后 tail 都没反应，直到16命令执行，在tail 后面输出了 abc和 4093个*，可见缓冲区的大小是4096字节

### **2.修改默认缓冲区的大小**

#### **(1)全缓冲：**

open 函数 有一个 buffering 的选项可以设置缓冲区大小，我们设置为大于一的整数

    f = open('demo.txt','w',buffering = 2048)

#### **(2)行缓冲：**

将 buffering 设置为1 

    f = open('demo.txt','w',buffering = 1)

#### **(3)无缓冲：**

将 buffering 设置为0

    f = open('demo.txt','w',buffering = 0)



## **四、如何将文件映射到内存** 

**需求：**

1.在访问二进制文件的时候，希望把文件映射到内存空间，实现随机访问
2.嵌入式设备中，寄存器被编址到内存地址空间，我们可以映射 /dev/mem 的某些范围，来操纵这些寄存器
3.如果多个进程映射同一个文件，还能实现进程通信

使用 mmap.mmap() 

通过下面这条命令创建一个1M的全0文件：

    dd if=/dev/zero of=demo.bin bs=1024 count=1024

使用 od -x 查看，发现的确是全0

     od -x demo.bin 

0000000 0000 0000 0000 0000 0000 0000 0000 0000
*
4000000

我们获取文件描述符

**示例代码：**


    f = open('demo.bin','r+b')
    print f.fileno()
    
**结果：**

    3
    
我们将整个文件映射到变量m 

    m = mmap.mmap(num,0,mmap.ACCESS_WRITE) # 这里的0表示的是全部文件

我们看一下内容：

    In [10]: m[100]
    Out[10]: '\x00'

修改：

    In [11]: m[100] = '\x08'

查看修改后的内容

    In [12]: m[99:120]
    Out[12]: '\x00\x08\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'
    
实际上在这种情况下文件已经被修改。

我们还可以跳过整数倍的页的大小对文件进行修改（页的概念来源于操作系统，如果不了解可以去查一下）

     m = mmap.mmap(num,mmap.PAGESIZE*8,mmap.ACCESS_WRITE,offset = mmap.PAGESIZE*4)
 

## **五、如何访问文件的状态**

在某些项目中我们需要获取文件的状态，
比如：
(1)文件类型
(2)文件权限
(3)文件最后修改时间
(4)文件的大小

### **方法一：系统调用**

**1.os.stat()**

**2.os.lstat()**

它与os.stat 的区别在于它不跟随符号链接文件，意思就是lstat 拿到的是快捷方式的属性，而stat 拿到的是原始文件的属性

**3.os.fstat()**

这个函数和上面两个函数的区别是这个函数传入的参数是文件描述符，而不是上面两个参数传入的路径。

**示例代码：**

    In [3]: import os
    
    In [4]: os.stat('demo.bin')
    Out[4]: posix.stat_result(st_mode=33188, st_ino=264254, st_dev=2049, st_nlink=1, st_uid=0, st_gid=0, st_size=1048576, st_atime=1538365252, st_mtime=1538364797, st_ctime=1538364797)


**查看文件的类型：**

文件的类型就存储在 st_mode  里面，我们需要将其解析

    In [15]: import os
    
    In [16]: s = os.stat('demo.bin')
    
    In [17]: import stat
    
    In [18]: stat.S_ISDIR(s.st_mode)
    Out[18]: False
    

**查看文件的权限**

    In [19]: s.st_mode & stat.S_IRUSR
    Out[19]: 256

只要这个返回值大于0就说明有用户读的权限

    In [20]: s.st_mode & stat.S_IXUSR
    Out[20]: 0

可见用户的执行权限是0


**查看文件的最后访问时间：**

    In [25]: import time
    
    In [26]: time.localtime(s.st_atime)
    Out[26]: time.struct_time(tm_year=2018, tm_mon=10, tm_mday=1, tm_hour=11, tm_min=40, tm_sec=52, tm_wday=0, tm_yday=274, tm_isdst=0)
    

### **方法二：快捷函数**

快捷函数在底层实现上是利用的是系统函数，但是会让你操作起来更加方便

**判断文件类型：**

    In [27]: os.path.isdir('demo.bin')
    Out[27]: False
    
    In [28]: os.path.isfile('demo.bin')
    Out[28]: True
    

**判断文件最后访问时间：**
    
    In [32]: time.localtime(os.path.getatime('demo.bin'))
    Out[32]: time.struct_time(tm_year=2018, tm_mon=10, tm_mday=1, tm_hour=11, tm_min=40, tm_sec=52, tm_wday=0, tm_yday=274, tm_isdst=0)



## **六、如何使用临时文件：**

比如我们采集数据进行分析，但是我们只需要保存结果，我们采集的数据如果常驻内存就会让电脑崩溃，于是我们将这个数据放在临时文件中（外部存储），在文件关闭后将被删除

**示例代码：**

    In [15]: from tempfile import TemporaryFile,NamedTemporaryFile
    
    In [16]: f = TemporaryFile()
    
    In [17]: f.write('abc'*10000)
    
    In [18]: f.seek(0)
    
    In [19]: f.read(100)
    Out[19]: 'abcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabca'
    

这个临时文件在系统中是找不到的，如果我们想创建一个能在文件系统中看到的临时文件，我们就用NamedTemporaryFile

**示例代码：**

我们能看到文件路径

    In [20]: nte = NamedTemporaryFile()
    In [21]: nte.name
    Out[21]: 'c:\\users\\k0rz3n\\appdata\\local\\temp\\tmpob51yb'

如果我们重新创建一个文件的话，原来的文件就会被关闭，关闭以后临时文件就会被自动删除，如果我们不想让他删除，我们在创建的时候就要设置一个参数

    nte = NamedTemporaryFile(delete = False)




