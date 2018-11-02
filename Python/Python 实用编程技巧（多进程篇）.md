---
title: Python 实用编程技巧（多进程篇）
date: 2018-10-2 12:50:18
tags: 编程 Python 进阶 备忘
categories: 编程
---

## **一、为什么选择多进程编程**

我们在多线程篇说过 Python 有一个 GIL 锁，这导致我们无法发挥多核CPU 的性能，于是对于一些耗CPU 的操作（比如：计算、图像处理），我们使用多线程编程显得就不那么好，于是我们采用多进程编程，这样就能充分利用CPU 并发来提高运行的效率（多I/O操作的尽量使用多线程编程，这样不会影响性能）
<!-- more -->
> **注：**本文使用 Python3 实现，但是除了和 Python2 在 print 上的差别外其他都是一样的

## **二、多线程与多进程的直观比较**

### **1.我们以计算斐波那契数列为例来比较两种方式的执行速度**

#### **1.多线程方式**

**示例代码：**

    import time
    from concurrent.futures import ThreadPoolExecutor,as_completed
    
    def fib(n):
        if n<= 2:
            return 1
        return fib(n-1)+fib(n-2)
    
    
    with ThreadPoolExecutor(3) as executor:
        tasks = [executor.submit(fib, (num)) for num in range(25,35)]
        start_time = time.time()
        for future in as_completed(tasks):
            data = future.result()
            print("exe result {num}".format(num = data))
    
        print("last time is {time}".format(time = time.time()-start_time))

**结果：**

    exe result 75025
    exe result 121393
    exe result 196418
    exe result 317811
    exe result 514229
    exe result 832040
    exe result 1346269
    exe result 2178309
    exe result 3524578
    exe result 5702887
    last time is 3.65224289894104

#### **2.多进程方式：**

**示例代码：**
    
    import time
    from concurrent.futures import ThreadPoolExecutor,as_completed
    from concurrent.futures import ProcessPoolExecutor
    
    def fib(n):
        if n<= 2:
            return 1
        return fib(n-1)+fib(n-2)
    
    if __name__ == '__main__':
        with ProcessPoolExecutor(3) as executor:
            tasks = [executor.submit(fib, (num)) for num in range(25,35)]
            start_time = time.time()
            for future in as_completed(tasks):
                data = future.result()
                print("exe result {num}".format(num = data))
    
            print("last time is {time}".format(time = time.time()-start_time))

**结果：**

    exe result 75025
    exe result 121393
    exe result 196418
    exe result 317811
    exe result 514229
    exe result 832040
    exe result 1346269
    exe result 2178309
    exe result 3524578
    exe result 5702887
    last time is 2.270967960357666


**结论：**

可以很清楚地看到使用多线程的方式计算来的更快


### **2.我们以频繁I/O操作的任务为例来比较两种方式的执行速度**

#### **1.多线程方式**

**示例代码：**

    import time
    from concurrent.futures import ThreadPoolExecutor,as_completed
    from concurrent.futures import ProcessPoolExecutor
    
    
    def random_sleep(n):
        time.sleep(n)
        return n
    
    if __name__ == '__main__':
        with ThreadPoolExecutor(3) as executor:
            tasks = [executor.submit(random_sleep, (num)) for num in [2]*30]
            start_time = time.time()
            for future in as_completed(tasks):
                data = future.result()
                print("exe result {num}".format(num = data))
    
            print("last time is {time}".format(time = time.time()-start_time))
    
**结果：**

    last time is 20.006227493286133

#### **2.多进程方式：**

**示例代码：**
    
    import time
    from concurrent.futures import ThreadPoolExecutor,as_completed
    from concurrent.futures import ProcessPoolExecutor
    
    def random_sleep(n):
        time.sleep(n)
        return n
    
    if __name__ == '__main__':
        with ProcessPoolExecutor(3) as executor:
            tasks = [executor.submit(random_sleep, (num)) for num in [2]*30]
            start_time = time.time()
            for future in as_completed(tasks):
                data = future.result()
                print("exe result {num}".format(num = data))
    
            print("last time is {time}".format(time = time.time()-start_time))
    
**结果:**

    last time is 20.205044746398926

**结论：**

对于I/O 操作来讲，多线程方式要优于多进程方式


## **三、multiprocessing 多进程编程**

进程的数据是完全隔离的，不能像线程一样通过全局变量进行通信。
多进程编程首选还是使用上面我们测试用的那个包，因为他和多线程编程的接口一致，设计精良，而这个multiprocessig 更加底层一些

**示例代码：**

    import multiprocessing
    import time
    
    def get_html(n):
        time.sleep(n)
        print("sub progress success")
        return n
    
    if __name__ == '__main__':
        progress = multiprocessing.Process(target=get_html,args=(2,))
        progress.start()
        print(progress.pid)
        progress.join()
        print("main progress end")

**结果：**
    
    29244
    sub progress success
    main progress end


## **四、使用进程池**

**示例代码：**

    import multiprocessing
    import time
    
    def get_html(n):
        time.sleep(n)
        print("sub process success")
        return n
    
    if __name__ == '__main__':
        pool = multiprocessing.Pool(multiprocessing.cpu_count())# 创建线程池
        result = pool.apply_async(get_html,args=(3,)) #添加进线程池
        pool.close() #禁止其他进程再次加入
        pool.join()
        print(result.get())
        
**结果：**

    sub process success
    3


当然我们可能会想用迭代的进程池，没问题，Python 给我们提供了这样的方法 pool.imap()，

**示例代码**：

    import multiprocessing
    import time
    
    def get_html(n):
        time.sleep(n)
        print("sub process success")
        return n
    
    if __name__ == '__main__':
        pool = multiprocessing.Pool(multiprocessing.cpu_count())# 创建线程池
        for result in pool.imap(get_html,[1,3,5,7,9]):
            print("sleep {time} success".format(time = result))
        

**结果：**

    sub process success
    sleep 1 success
    sub process success
    sleep 3 success
    sub process success
    sleep 5 success
    sub process success
    sleep 7 success
    sub process success
    sleep 9 success


## **五、进程间通信**

### **方法一：使用进程队列**


**示例代码：**


    from multiprocessing import Process,Queue
    import time
    
    def Producer(queue):
        queue.put("a")
        time.sleep(2)
    
    def Consumer(queue):
        time.sleep(2)
        res = queue.get()
        print(res)
    
    if __name__ == '__main__':
        queue = Queue(10)
        task1 = Process(target=Producer,args=(queue,))
        task2 = Process(target=Consumer,args=(queue,))
        task1.start()
        task2.start()
        task1.join()
        task2.join()
    
**结果：**

    a


> **注意：**
> 
> 1.共享全局变量进行通信的方法在多进程编程中是不能实现的，因为进程在 fork 的时候会将所有的变量赋值一分到自己的空间，进程之间是隔离的
> 
> 2.Queue() 是不能用于进程池的进程之间的通信的


### **方法二：使用Manager 中的 Queue 实现进程池中的通信**

**示例代码：**

    from multiprocessing import Process,Queue,Pool,Manager
    import time
    
    def Producer(queue):
        queue.put("a")
        time.sleep(2)
    
    def Consumer(queue):
        time.sleep(2)
        res = queue.get()
        print(res)
    
    if __name__ == '__main__':
        queue = Manager().Queue(10)
        pool = Pool(2)
        pool.apply_async(Producer,args=(queue,))
        pool.apply_async(Consumer,args=(queue,))
        pool.close()
        pool.join()
    
**结果：**

    a


### **方法三：使用Pipe 实现两个进程之间的通信**

pipe 只能用于两个进程之间的通信，效率比queue 高

**示例代码：**

from multiprocessing import Process,Pipe

def Producer(pipe):
    pipe.send("a")

def Consumer(pipe):
    print(pipe.recv())

if __name__ == '__main__':
    receive_pipe,send_pipe = Pipe()
    task1 = Process(target=Producer,args=(send_pipe,))
    task2 = Process(target=Consumer,args=(receive_pipe,))
    task1.start()
    task2.start()
    task1.join()
    task2.join()

**结果:**

    a

## **六、进程间内存共享**

**示例代码：**
    
    from multiprocessing import Process
    import multiprocessing
    
    def add_data(p_dict,key,value):
        p_dict[key] = value
    
    if __name__ == '__main__':
        mgr = multiprocessing.Manager()
        progress_dict = mgr.dict()
        first_progress = Process(target=add_data,args=(progress_dict,"Bob",22))
        second_progress =  Process(target=add_data,args=(progress_dict,"Alic",20))
        first_progress.start()
        second_progress.start()
        first_progress.join()
        second_progress.join()
        print progress_dict

**结果：**

    {'Bob': 22, 'Alic': 20}
    

**解释：**

可以看到，虽然是不同的进程，但是他们共同操纵了一个变量



