---
title: Python 实用编程技巧（多线程篇）
date: 2018-10-1 23:524:18
tags: 编程 Python 进阶 备忘
categories: 编程
---

## **一、GIL(global_interpreter_lock)**

### **1.概念：**

Python 一开始为了简单，在多线程编程的时候会在我们的解释器上加一个非常大的锁，也就是允许我们一次只有一个线程运行在一个CPU上，gil 就能实现在同一时刻只有一个线程在CPU上执行字节码（目的当然是保证线程安全），当然他的性能也是非常让人诟病，因为他也无法将多个线程映射到多个CPU上（体现不出多核CPU的优势）

<!-- more -->

> **解释：** python 在执行程序之前会将其先编译成字节码，我们可以使用 dis.dis() 这个函数对某个函数进行反编译

**示例代码：**

    import dis
    def a():
        a = 1
        a +=1
        return a
    
    print dis.dis(a)

**结果：**
    
      4           0 LOAD_CONST               1 (1)
                  3 STORE_FAST               0 (a)
    
      5           6 LOAD_FAST                0 (a)
                  9 LOAD_CONST               1 (1)
                 12 INPLACE_ADD         
                 13 STORE_FAST               0 (a)
    
      6          16 LOAD_FAST                0 (a)
                 19 RETURN_VALUE        
    None
    

### **2.GPL 真的那么安全吗：**

实际上在程运行过程中GPL 是会在一定时候释放的，并不是一个执行完了才会执行另一个程序

**示例代码：**

    import threading
    
    total = 0
    def add():
        global total
        for i in xrange(100000):
             total += 1
    
    def desc():
        global total
        for i in xrange(100000):
            total -= 1
    
    thread1 = threading.Thread(target = add)
    thread2 = threading.Thread(target = desc)
    
    thread1.start()
    thread2.start()
    thread1.join()
    thread2.join()
    
    print total
    
上面这个程序你每运行一次得到而结果都不一样，说明并不是执行完一个再执行另一个，也就是说GIL会在中间释放，交给另一个线程。

**那么一般在什么情况下释放?**

一般是在执行一定行数的字节码或者执行一定时间，或者遇到I/O操作（因为I/O操作的时间非常长，程序不会等待）


## **二、多线程编程**

### **方法一：通过Thread类实例化**

我们可以以一个爬虫的例子来理解，我们可以用一个线程来爬去某网站的列表页，另一个线程根据这个列表来爬去详情页，两者同时进行效率很高（为什么能同时进行呢？因为我们的socket编程也是属于网络I/O编程的一种）

**示例代码：**

    import time
    import threading
    
    def show_html_detail(url):
        print "get html detail start\n"
        time.sleep(2)
        print "get html detail end\n"
    
    def show_url_detail(url):
        print "get url detail start\n"
        time.sleep(2)
        print "get url detail end\n"
    
    if __name__ == '__main__':
    
        thread1 = threading.Thread(target=show_url_detail,args=("",))
        thread2 = threading.Thread(target=show_html_detail,args=("",))
        start_time = time.time()
        thread1.start()
        thread2.start()
        print "last time {}\n".format(time.time()-start_time)

**结果：**

    get url detail start
    
    get html detail start
    last time 0.0
    
    
    get html detail end
    get url detail end

**解释：**

为什么是0s 呢？如果是并行的应该是两秒，其实是因为我们这里有三个线程，其中有一个是主线程，这里是主线程和子线程同时运行，，主线程运行结束以后就打印了这句话，子线程继续运行，输出了后面的 end

#### **需求一：**

那么我们其实希望主线程运行完（和退出不一样）后子线程也一起结束，我们可以设置子线程为守护线程，即thread.steDaemon(True)，守护线程会在主线程结束后跟随主线程结束（主线程结束：主线程所在的进程中的所有非守护线程都结束）

**示例代码：**

    import time
    import threading
    
    def show_html_detail(url):
        print "get html detail start\n"
        time.sleep(2)
        print "get html detail end\n"
    
    def show_url_detail(url):
        print "get url detail start\n"
        time.sleep(2)
        print "get url detail end\n"
    
    if __name__ == '__main__':
    
        thread1 = threading.Thread(target=show_url_detail,args=("",))
        thread2 = threading.Thread(target=show_html_detail,args=("",))
        start_time = time.time()
        thread1.setDaemon(True)
        thread2.setDaemon(True)
        thread1.start()
        thread2.start()
        print "last time {}\n".format(time.time()-start_time)

**结果：**

    get url detail start
    
    get html detail start
    
    last time 0.0

可见，end 并没有输出来，说明子线程在主线程结束时被强制结束了

我们尝试关闭一个守护线程并延长另一个守护线程的时间再次验证一下

**示例代码：**

    import time
    import threading
    
    def show_html_detail(url):
        print "get html detail start\n"
        time.sleep(2)
        print "get html detail end\n"
    
    def show_url_detail(url):
        print "get url detail start\n"
        time.sleep(4)                       #注意这里对守护线程的时间进行了修改
        print "get url detail end\n"
    
    if __name__ == '__main__':
    
        thread1 = threading.Thread(target=show_url_detail,args=("",))
        thread2 = threading.Thread(target=show_html_detail,args=("",))
        start_time = time.time()
        thread1.setDaemon(True)
        thread1.start()
        thread2.start()
        print "last time {}\n".format(time.time()-start_time)
    

**结果：**

    get url detail start
    
    get html detail start
    last time 0.00100016593933
    
    get html detail end
    
可以看到守护线程并没有输出，我们的分析是正确的

#### **需求二：**

我们其实希望我们子线程和主线程不要并发运行，主线程等等子线程运行结束再运行，于是Python也给我们提供的对应的方法，join() 主线程阻塞方法,使用以后主线程就会被阻塞等待子线程运行

**示例代码：**

    import time
    import threading
    
    def show_html_detail(url):
        print "get html detail start\n"
        time.sleep(2)
        print "get html detail end\n"
    
    def show_url_detail(url):
        print "get url detail start\n"
        time.sleep(2)
        print "get url detail end\n"
    
    if __name__ == '__main__':
    
        thread1 = threading.Thread(target=show_url_detail,args=("",))
        thread2 = threading.Thread(target=show_html_detail,args=("",))
        start_time = time.time()
        #thread1.setDaemon(True)
        #thread2.setDaemon(True)
        thread1.start()
        thread2.start()
        thread1.join()
        thread2.join()
        print "last time {}\n".format(time.time()-start_time)

**结果：**

    get url detail start
    
    get html detail start
    
    get url detail end
    
    get html detail end
    
    last time 2.00100016594

**解释：**

可以看到我们的确在2s 左右运行结束了两个线程，实现了并发，（如果我们将其中一个线程的时间设置为4那么我们最后的时间将会是4s左右，也就是多个线程的最常时间）


### **方法二：通过继承Thread类实现多线程**


    import time
    import threading
    
    class Get_html_detail(threading.Thread):
        def __init__(self,name):
            super(Get_html_detail,self).__init__(name = name)
    
        def run(self):
            print "get html detail start\n"
            time.sleep(2)
            print "get html detail end\n"
    
    class Get_url_dtail(threading.Thread):
        def __init__(self, name):
            super(Get_url_dtail,self).__init__(name = name)
    
        def run(self):
            print "get url detail start\n"
            time.sleep(2)
            print "get url detail end\n"
    
    
    if __name__ == '__main__':
    
        thread1 = Get_url_dtail("show_url_detail")
        thread2 = Get_html_detail("show_html_detail")
        start_time = time.time()
        thread1.start()
        thread2.start()
        thread1.join()
        thread2.join()
        print "last time {}\n".format(time.time()-start_time)

## **三、线程间通信**

### **为什么需要线程间的通信呢？**

我们上面说过，在爬虫的爬取URL线程爬取成功以后，我们需要将爬取到的线程丢给另一个爬取文章详情页的线程，这时候就需要使用到 Python的线程间通信

### **方法一：共享变量**

设置一个全局变量实现多个线程之间的通信

    import time
    import threading
    
    detail_url_list = []
    
    def show_html_detail():
        global detail_url_list
        url = detail_url_list.pop() # 注意这里没有在函数中写循环，而是选择在主函数中国实现循环多线程提高效率
        print "get html detail start\n"
        time.sleep(2)
        print "get html detail end\n"
    
    def show_url_detail():
        global detail_url_list
        print "get url detail start\n"
        time.sleep(2)
        for i in xrange(20):
            detail_url_list.append("http://www.threadtest.com/{id}".format(id = i))
        print "get url detail end\n"
    
    if __name__ == '__main__':
        thread1 = threading.Thread(target=show_url_detail)
        thread1.start()
        thread1.join()
        for i in xrange(20):
            thread2 = threading.Thread(target=show_html_detail)
            thread2.start()
            thread2.join()


**实际上可以直接使用传参的方式：**


    import time
    import threading
    
    detail_url_list = []
    
    def show_html_detail(detail_url_list):
        url = detail_url_list.pop()
        print "get html detail start\n"
        time.sleep(2)
        print "get html detail end\n"
    
    def show_url_detail(detail_url_list):
        print "get url detail start\n"
        time.sleep(2)
        for i in xrange(20):
            detail_url_list.append("http://www.threadtest.com/{id}".format(id = i))
        print "get url detail end\n"
    
    if __name__ == '__main__':
    
        thread1 = threading.Thread(target=show_url_detail,args=(detail_url_list,))
        start_time = time.time()
        thread1.start()
        thread1.join()
        for i in xrange(20):
            thread2 = threading.Thread(target=show_html_detail,args=(detail_url_list,))
            thread2.start()
            thread2.join()
        print "last time {}\n".format(time.time() - start_time)


但是使用Pop 方法其实并不是线程安全的，我们还是需要在上面加一把锁

### **方法二：线程队列**

**示例代码：**

    import Queue
    import time
    import threading
    
    
    def show_html_detail(queue):
        url = queue.get()#这是一个阻塞的操作，当队列为空的时候会阻塞,所以我们不用 join
        print "get html detail start\n"
        time.sleep(2)
        print "get html detail end\n"
    
    
    def show_url_detail(queue):
        print "get url detail start\n"
        time.sleep(2)
        for i in xrange(20):
            queue.put("http://www.threadtest.com/{id}".format(id=i))
        print "get url detail end\n"
    
    
    if __name__ == '__main__':
        detail_url_queue = Queue(maxsize=1000)
        thread1 = threading.Thread(target=show_url_detail, args=(detail_url_queue,))
        start_time = time.time()
        thread1.start()
        for i in xrange(20):
            thread2 = threading.Thread(target=show_html_detail, args=(detail_url_queue,))
            thread2.start()
        print "last time {}\n".format(time.time() - start_time)


## **四、线程同步**

### **1.为什么要线程同步？**

我们用之前的一个例子给大家分析一下

**示例代码：**

    import dis
    
    def add(a):
        a += 1
    
    def desc(a):
        a -= 1
    
    print dis.dis(add)
    print dis.dis(desc)

**结果：**


      5           0 LOAD_FAST                0 (a)
                  3 LOAD_CONST               1 (1)
                  6 INPLACE_ADD         
                  7 STORE_FAST               0 (a)
                 10 LOAD_CONST               0 (None)
                 13 RETURN_VALUE        
    None
      8           0 LOAD_FAST                0 (a)
                  3 LOAD_CONST               1 (1)
                  6 INPLACE_SUBTRACT    
                  7 STORE_FAST               0 (a)
                 10 LOAD_CONST               0 (None)
                 13 RETURN_VALUE        
    None

**解释：**

可以发现要执行这两个函数实际上在底层运行了四个步骤

    1.load a 
    2.load 1
    3.+/-操作
    4.赋值给a 

那么在这个过程中GIL随时都能进行切换，就会造成最后赋值的混乱，这种场景在web 开发，特别是电商网站的开发中出现的概率非常大，因为很多人会同时减库存

### **2.怎样实现线程同步**

#### **(1)加锁**

python 为我们提供了一些锁的机制，最常见的就是 from threading import Lock

**示例代码：**
    
    # coding=utf-8
    import threading
    from threading import Lock
    
    total = 0
    lock = Lock()  # 声明一把锁
    
    def add():
        global total
        global lock
    
        for i in xrange(100000):
            lock.acquire()
            total += 1
            lock.release() #一定要去释放这个锁，要不然程序就无法继续运行了
    
    
    def desc():
        global total
        global lock
        for i in xrange(100000):
            lock.acquire()
            total -= 1
            lock.release()
    
    
    thread1 = threading.Thread(target=add)
    thread2 = threading.Thread(target=desc)
    
    thread1.start()
    thread2.start()
    thread1.join()
    thread2.join()
    
    print total


**结果：**

    0       

**解释：**

这样无论怎么运行结果都是0了，有了锁以后我们就能实现代码段的交替运行，但是锁会影响性能

#### **(2)锁会引起死锁**

如果我们在 acquire() 里面再次 acquire() 就会引起互相的等待造成死锁，因此我们使用锁的时候要格外的小心


#### **(3)可重入的锁 RLock**

有了这个锁我们就能在一个线程里面调用多次 acquire() （注意：调用 acquire()的此时一定要和 release()保持一致）

**示例代码：**

    # coding=utf-8
    import threading
    from threading import RLock
    
    total = 0
    
    lock = RLock()  # 声明一把锁
    
    def add():
        global total
        global lock
    
        for i in xrange(100000):
            lock.acquire()
            lock.acquire()
            total += 1
            lock.release() #一定要去释放这个锁，要不然程序就无法继续运行了
            lock.release()
    
    def desc():
        global total
        global lock
        for i in xrange(100000):
            lock.acquire()
            total -= 1
            lock.release()
    
    
    thread1 = threading.Thread(target=add)
    thread2 = threading.Thread(target=desc)
    
    thread1.start()
    thread2.start()
    thread1.join()
    thread2.join()
    
    print total


## **五、多线程中的condition**

condition---> 条件变量，用于复杂的线程间同步

### **1.Lock 能否实现协同读诗？**

一般来讲这个东西的使用是在完成类似对话的操作的时候，比如天猫精灵和小爱的对话，但是这个你一句我一句的形式感觉 lock 也能完成，我们先实验一下.

**示例代码：**

    # coding=utf-8
    import threading
    from threading import Lock
    
    class XiaoAi(threading.Thread):
        def __init__(self,lock):
            super(XiaoAi,self).__init__(name = "小爱")
            self.lock = lock
        def run(self):
            self.lock.acquire()
            print "{name}:哎".format(name = "小爱")
            self.lock.release()
    
            self.lock.acquire()
            print "{name}:好啊".format(name="小爱")
            self.lock.release()
    
    
    class TianMao(threading.Thread):
        def __init__(self,lock):
            super(TianMao,self).__init__(name = "天猫精灵")
            self.lock = lock
        def run(self):
            self.lock.acquire()
            print "{name}:小爱同学".format(name = "天猫精灵")
            self.lock.release()
    
            self.lock.acquire()
            print "{name}:我们来对古诗吧".format(name = "天猫精灵")
            self.lock.release()
    
    if __name__ == '__main__':
        lock = Lock()
        xiaoai = XiaoAi(lock)
        tianmao = TianMao(lock)
        tianmao.start()
        xiaoai.start()


**结果：**

    天猫精灵:小爱同学
    天猫精灵:我们来对古诗吧
    小爱:哎
    小爱:好啊

我们发现，天猫精灵把话都说了然后才给了小爱，这并不是我们想要的结果


### **2.condition 实现协同操作**

condition 的锁的机制实际上还是使用的是 RLock 中的 acquire() 和 release() ,而condition中的 wait 和 notify 才是他的精髓所在

**示例代码：**
    
    # coding=utf-8
    import threading
    from threading import Condition
    
    class XiaoAi(threading.Thread):
        def __init__(self,cond):
            super(XiaoAi,self).__init__(name = "小爱")
            self.cond = cond
        def run(self):
            with self.cond:
                self.cond.wait()
                print "{name}:哎".format(name = "小爱")
                self.cond.notify()
    
                self.cond.wait()
                print "{name}:好啊".format(name="小爱")
                self.cond.notify()
    
    
    class TianMao(threading.Thread):
        def __init__(self,cond):
            super(TianMao,self).__init__(name = "天猫精灵")
            self.cond = cond
        def run(self):
            with self.cond:
                print "{name}:小爱同学".format(name = "天猫精灵")
                self.cond.notify()
                self.cond.wait()
    
                print "{name}:我们来对古诗吧".format(name = "天猫精灵")
                self.cond.notify()
                self.cond.wait()
    
    if __name__ == '__main__':
        cond = Condition()
        xiaoai = XiaoAi(cond)
        tianmao = TianMao(cond)
        xiaoai.start()            # 注意这里的顺序和之前的代码不同
        tianmao.start()

**结果：**

    天猫精灵:小爱同学
    小爱:哎
    天猫精灵:我们来对古诗吧
    小爱:好啊

> **解释：**
> 
> 1.使用 condition 的时候执行的先后顺序是一定不能弄错的，否则就会出现执行不下去的情况，比如我们先启动天猫精灵，那么我们的 Notify 就已经在小爱没有启动的时候就发出去了，小爱就永远收不到天猫的请求，于是对话就陷入的僵局
> 
> 2.使用了 with 语句，就相当于调用了两个魔法方法，就不用单独写 self.cond.acquire() 和 self.cond.release() 了。（必须在 调用了 with 以后才能调用 self.cond.wait() 和 self.cond.notify()）


## **六、可控数量的线程同步机制：Semaphore**

Semaphore（信号量） 是另一个线程同步机制，是一种用于控制进入数量的锁

### **什么时候会用到这个机制呢？**

1.比如我们做爬虫，我们想控制线程的最大数量，这样就能防止被反爬
2.比如我们想要控制对一个文件的读取线程的数量不能超过某一个值

### **没有使用前的例子**

**代码示例：**

    import threading
    import time
    
    class HtmlSpider(threading.Thread):
        def __init__(self,url):
            super(HtmlSpider,self).__init__()
            self.url = url
    
        def run(self):
            time.sleep(2)
            print "got html successfully\n"
    
    
    class UrlProducer(threading.Thread):
        def run(self):
            for i in xrange(20):
                html_thread = HtmlSpider("http://www.html_spider.com/{id}".format(id = i))
                html_thread.start()
    
    
    if __name__ == '__main__':
        url_producer = UrlProducer()
        url_producer.start()

**结果：**

    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully
    got html successfully

这里现在有20个线程，但是如果我们有这样的需求，我们希望每并发的数量不超过3个，那么我们就要使用到Semaphore

**代码示例：**

    # coding=utf-8
    import threading
    import time
    
    class HtmlSpider(threading.Thread):
        def __init__(self,url,sem):
            super(HtmlSpider,self).__init__()
            self.url = url
            self.sem = sem
    
        def run(self):
            time.sleep(2)
            print "got html successfully\n"
            self.sem.release() # 在这里释放---------------->注意一定要在执行完全结束以后释放
    
    class UrlProducer(threading.Thread):
        def __init__(self,sem):
            super(UrlProducer,self).__init__()
            self.sem = sem
    
        def run(self):
            for i in xrange(20):
                self.sem.acquire() # 在这里上锁
                html_thread = HtmlSpider("http://www.html_spider.com/{id}".format(id = i),self.sem)
                html_thread.start()
                ## self.sem.release()  # 在这里释放 ----------->这里释放还是20个并发，因为没在执行完毕的时候释放，start()执行后还要等函数执行完毕
    
    if __name__ == '__main__':
        sem = threading.Semaphore(3)
    
        url_producer = UrlProducer(sem)
        url_producer.start()


> **解释：**
> 
> Semaphore 底层是调用condition 实现的，sem.acquire() 会记录数量，满三个就上锁，解锁一个数字就减一


## **七、线程池concurrent.futures(Python 3)**

### **1.为什么要用线程池？**

线程池能够自动的帮助我们进行线程的调度，而不用我们手动控制，我们有新的线程就丢到线程池中就可以了，减轻了我们的负担，非常方便

比如：
1.我们想在主线程中获取某一个线程或者任务的状态或者返回值
2.当一个线程完成的时候主线程能立刻的知道
3.futures 可以让多线程和多进程的编码接口一致

#### **小试牛刀**

**示例代码:**
        
    from concurrent.futures import ThreadPoolExecutor
    import time
    
    def get_html(times):
        time.sleep(times)
        print("get html {id} successfully".format(id = times))
        return times
    
    if __name__ == '__main__':
        executor = ThreadPoolExecutor(max_workers=2)# 创建一个线程池的执行器
        task1 = executor.submit(get_html,(3)) # 将程序丢入线程池
        task2 = executor.submit(get_html,(2))
        print(task2.done())
        time.sleep(3)
        print(task2.done())
        print(task2.result()) # 获取返回结果

    
**结果：**

    False
    get html 2 successfully
    get html 3 successfully
    True
    2

> **注意：**
> 
> 线程池对象还有个 cancel()
> 方法能够结束**未开始**执行的线程（注意是未开始执行），因为我们现在允许一下子执行两个线程，所以是没法cancel()
> 掉的，我们如果想cancel() 的话我们可以将这个最多同时执行两个改成同时执行一个吗，这样就能 cancel()了

### **2.我们想获取到执行成功的task 的返回**

#### **方法一：使用 as_completed**

**示例代码：**

    from concurrent.futures import ThreadPoolExecutor
    from concurrent.futures import as_completed
    import time
    
    def get_html(times):
        time.sleep(times)
        print("get html {id} successfully".format(id = times))
        return times
    
    if __name__ == '__main__':
        executor = ThreadPoolExecutor(max_workers=2)# 创建一个线程池的执行器
    
        urls = [2,3,4,5,6,7]
        tasks = [executor.submit(get_html,(url)) for url in urls]
        for future in as_completed(tasks):
            data = future.result()
            print("get {data} successfully\n".format(data = data))


#### **方法二：使用 executor 自己的 map 方法**

**示例代码：**

    from concurrent.futures import ThreadPoolExecutor
    from concurrent.futures import as_completed
    import time
    
    def get_html(times):
        time.sleep(times)
        print("get html {id} successfully".format(id = times))
        return times
    
    if __name__ == '__main__':
        executor = ThreadPoolExecutor(max_workers=2)# 创建一个线程池的执行器
        urls = [2,3,4,5,6,7]
        for data in executor.map(get_html,urls):
            print("get {data} successfully\n".format(data = data))
        
### **3.wait()方法申请等待**

wait() 什么线程就会等待这个线程执行完再执行其他的线程

**示例代码：**


    from concurrent.futures import ThreadPoolExecutor
    from concurrent.futures import as_completed
    from concurrent.futures import wait
    from concurrent.futures import FIRST_COMPLETED
    import time
    
    def get_html(times):
        time.sleep(times)
        print("get html {id} successfully".format(id = times))
        return times
    
    if __name__ == '__main__':
        executor = ThreadPoolExecutor(max_workers=2)# 创建一个线程池的执行器
        urls = [2,3,4,5,6,7]
        tasks = [executor.submit(get_html,(url)) for url in urls]
        wait(tasks,return_when=FIRST_COMPLETED)
        print('main')
    

**结果：**

    get html 2 successfully
    main
    get html 3 successfully
    get html 4 successfully
    get html 5 successfully
    get html 6 successfully
    get html 7 successfully


