---
title: Python 实用编程技巧（装饰器篇）
date: 2018-10-4 01:14:18
tags: 编程 Python 进阶 备忘
categories: 编程
---

## **一、如何使用函数装饰器？**

### **1.需求：**

比如说，我们想为很多不同的函数添加相同的功能，比如说计时统计、记录日志、缓存运算结果等，但是我们又不想在每个函数中添加相同的代码

<!-- more -->

### **2.举个例子：**

我们还是以斐波那契数的计算为例

**示例代码：**
    
    def fib(n):
        if n <= 1:
            return 1
        return fib(n-1)+fib(n-2)
    
    if __name__ == '__main__':
        print fib(50)
    

这一段代码想要跑出来非常的慢，因为我们在这个运算的过程中经历了非常多的重复运算，比如我们想计算50就要计算49,48 我们要计算49 就要计算 48，47 ,看到了吧，48 就出现了重复运算，那么这里面有着太多太多的重复运算，导致我们的计算非常的慢，并且非常的消耗 CPU

**那么怎么办呢？**

我们可以创造一个缓存，每次算到一个新的结果我们都放在这个缓存中，这样我们每次都判断缓存有没有我们想要的值就可以了，有的话直接拿过来用，没有再加入缓存，这样就能大大提高我们的运行效率，并且减轻了我们的CPU 的负担

**示例代码：**

    def fib(n,cache = None):
        if cache is None:
            cache = {}
        if n in cache:
            return cache[n]
    
        if n <= 1:
            return 1
    
        cache[n] = fib(n-1,cache)+fib(n-2,cache)
        return cache[n]
    
    if __name__ == '__main__':
        print fib(50)

**结果：**

    20365011074

我们发现效率出现了质的飞跃，很快就算出了结果，但是换做别的函数我么又要添加这个缓存的代码了，这样就非常的烦，

**那么怎么解决呢？**

我们考虑创建一个包裹函数 wrap ，在这个函数内部我们实现我们的缓存代码，并且调用原函数，我们的函数装饰器就是为了生成这样的包裹函数的

**示例代码：**

    def memo(func):
        cache = {}
        def wrap(*args):
            if args not in cache:
                cache[args] = func(*args)
            return cache[args]
        return wrap
    
    
    def fib(n):
        if n <= 1:
            return 1
        return fib(n - 1) + fib(n - 2)
    
    
    if __name__ == '__main__':
        fib = memo(fib)
        print fib(50)
    
**结果：**

    20365011074

当然这样在函数中写是非常啰嗦的，我们python 给我们提供了一个语法糖，

**示例代码：**

    def memo(func):
        cache = {}
        def wrap(*args):
            if args not in cache:
                cache[args] = func(*args)
            return cache[args]
        return wrap
    
    @memo
    def fib(n):
        if n <= 1:
            return 1
        return fib(n - 1) + fib(n - 2)
    
    
    if __name__ == '__main__':
        print fib(50)

**结果：**

    20365011074


## **二、如何为被装饰的函数保存元数据**

### **1.概念：**

在函数对象中保存着一些函数的元数据,例如：

`f.__name__`   函数的名字
`f.__doc__ `   函数的文档字符串
`f.__model__`  函数所属的模块名
`f.__dict__`   属性字典
`f.__defaults__`  默认参数元组

我们在使用装饰器以后，再使用上面这些属性访问的时候，看到的是包裹函数的元数据，而原始函数的元数据不见了，我们该如何解决

### **2.举个例子：**

下面是原始函数的返回结果

**示例代码：**

    def example():
        '''example function'''
        print "In example"
    
    if __name__ == '__main__':
        print example.__name__
        print example.__doc__
        
**结果：**
    
    example
    example function
    
这下面是经过装饰器装饰以后的返回结果

**示例代码：**

    
    def mydecorator(func):
        def wrap(*args,**kargs):
            '''wrap function'''
            print "In wrapper"
            func(*args,**kargs)
        return wrap
    
    
    @mydecorator
    def example():
        '''example function'''
        print "In example"
    
    if __name__ == '__main__':
        print example.__name__
        print example.__doc__

**结果：**
    
    wrap
    wrap function


**解决：**

我们使用 functools 中的 wraps 装饰内部的包裹函数，可以定义将原函数的某些属性更新到包裹函数上面

**示例代码:**

    from functools import wraps
    def mydecorator(func):
        @wraps(func)
        def wrap(*args,**kargs):
            '''wrap function'''
            print "In wrapper"
            func(*args,**kargs)
        return wrap
    
    
    @mydecorator
    def example():
        '''example function'''
        print "In example"
    
    if __name__ == '__main__':
        print example.__name__
        print example.__doc__

**结果：**
    
    example
    example function


## **三、如何自定义带参数的装饰器**

比如说我们想实现一个装饰器来检查被装饰函数的参数类型，装饰器能定义函数的参数类型，如果函数调用的时参数类型不对就抛出异常

带参数的装饰器就是根据参数定制化一个装饰器，可以看成是生产装饰器的工厂，每次调用这个装饰器都能返回一个特定的装饰器，然后再用其修饰其它函数

**示例代码：**

    from inspect import signature
    
    def typeassert(*ty_args,**ty_kargs):
        def decorator(func):
            sig = signature(func)
            btypes = sig.bind_partial(*ty_args,**ty_kargs).arguments
            def wrapper(*args,**kargs):
                for name,obj in sig.bind(*args,**kargs).arguments.items():
                    if name in btypes:
                        if not isinstance(obj,btypes[name]):
                            raise TypeError("%s must be %s" % (name,btypes[name]))
                return func(*args,**kargs)
            return wrapper
        return decorator
    
    @typeassert(int,str,list)
    def f(a,b,c):
        print (a,b,c)
    
    if __name__ == '__main__':
        f(1,"abc",[1,2,3])
        f(1,2,[1,2,3])
    

**结果：**

    1 abc [1, 2, 3]
    TypeError: b must be <class 'str'>


## **四、如何实现属性可修改的装饰器**

### **背景：**

为了分析程序内哪些程序开销较大，我们可以定义一个带有timeout 参数的函数装饰器，他实现以下功能：

1.统计被装饰的函数的单次调用的时间
2.时间大于timeout 的将此次函数的调用记录记录在 log 日志文件中
3.运行时可以修改 timeout 的值

**示例代码：**

    from functools import wraps
    import time
    import logging
    from random import randint
    
    
    def warn(timeout): 
        def decorator(func):
            @wraps(func)
            def wrapper(*args,**kargs):
                start = time.time()
                res = func(*args,**kargs)
                used = time.time() - start
                if used > timeout:
                    msg = "%s : %s > %s" % (func.__name__,used,timeout)
                    logging.warn(msg)
                return res
            return wrapper
        return decorator
    
    @warn(1.5)
    def test():
        print("In test")
        while randint(0,1):
            time.sleep(0.5)
    
    
    for i in range(30):
        test()
    

我们可以在包裹中添加一个函数，然后用这个函数来修改闭包中的自由变量

**Python3** 

**示例代码：**

    from functools import wraps
    import time
    import logging
    from random import randint
    
    
    def warn(timeout):
        def decorator(func):
            @wraps(func)
            def wrapper(*args,**kargs):
                start = time.time()
                res = func(*args,**kargs)
                used = time.time() - start
                if used > timeout:
                    msg = "%s : %s > %s" % (func.__name__,used,timeout)
                    logging.warn(msg)
                return res
    
            def setTimeout(k):
                nonlocal timeout
                timeout = k
            wrapper.setTimeout = setTimeout
            
            return wrapper
        return decorator
    
    @warn(1)
    def test():
        print("In test")
        while randint(0,1):
            time.sleep(0.5)
    
    
    for i in range(30):
        test()
    
    test.setTimeout(1)
    for i in range(30):
        test()

但是由于 python2 并不支持 nonlocal ，于是我们还要修改，使用列表将其修改成一个可变变量

**示例代码：**

    from functools import wraps
    import time
    import logging
    from random import randint
    
    
    def warn(timeout):
        timeout = [timeout]
        def decorator(func):
            @wraps(func)
            def wrapper(*args,**kargs):
                start = time.time()
                res = func(*args,**kargs)
                used = time.time() - start
                if used > timeout[0]:
                    msg = "%s : %s > %s" % (func.__name__,used,timeout[0])
                    logging.warn(msg)
                return res
    
            def setTimeout(k):
                #nonlocal timeout
                timeout[0] = k
            wrapper.setTimeout = setTimeout
    
            return wrapper
        return decorator
    
    @warn(1)
    def test():
        print("In test")
        while randint(0,1):
            time.sleep(0.5)
    
    
    for i in range(30):
        test()
    
    test.setTimeout(1)
    for i in range(30):
        test()
    
