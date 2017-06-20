Python 多任务
============

# 简介

并发和并行一直以来是效率优化围绕的中心，硬件方面，多核 CPU 已经普及很久了，底层内核多任务调度算法也十分完善，
多任务通常会伴随着通信跟协调的问题，这会加大软件开发的复杂度，但是带来的收益真的非常客观。

通常多任务有三种模式：多进程、多线程和多进程加多线程

Python 中提供了许多库供开发者使用：

Libraries for concurrent and parallel execution.
- [eventlet](http://eventlet.net) - Asynchronous framework with WSGI support.
- [gevent](http://www.gevent.org) - A coroutine-based Python networking library that uses greenlet.
- [multiprocessing](https://docs.python.org/2/library/multiprocessing.html) - (Python standard library) Process-based "threading" interface.
- [threading](https://docs.python.org/2/library/threading.html) - (Python standard library) Higher-level threading interface.
- [Tomorrow](https://github.com/madisonmay/Tomorrow) - Magic decorator syntax for asynchronous code.
- [uvloop](https://github.com/MagicStack/uvloop) - Ultra fast implementation of asyncio event loop on top of libuv.

# 多进程

首先，让我们问自己一个问题：什么是进程？

其实这个问题困惑了我好久，同时它还有几个兄弟姐妹，什么是文件？什么是套接字通信？...

简单的解释就是，我们编写了一段代码并把它放到了磁盘中，那么这段可执行的代码文件就叫做程序，
这个程序的执行实例就叫做进程。

进程是存在在内存中的，通过一个进程 ID 来标识，也就是 PID。

在 Linux 中，内核提供了一个 fork() 的系统调用，供我们去创建进程，在 python 中被封装在 os 模块中。

关于 fork() 的声明：
```cython
#include <sys/types.h>
#include <unistd.h>

pid_t  fork(void);
fork 返回两次：子进程中为 0，父进程中为子进程 ID，出错为 -1

pid_t  vfork(void);
vfork 返回两次：子进程中为 0，父进程中为子进程 ID，出错为 -1
```

两者的区别是 fork 不能保证子父进程哪个先执行。

## multiprocessing

当然在 python 中，很少使用 os.fork() 去直接创建进程，multiprocessing 就是一个跨平台的多进程模块。

多说无益，还是直接上代码

```python
from multiprocessing import Process
import os

def process(name):
    print("I am child process: %s, my pid is: %s..." % (name, os.getpid()))
    
if __name__ == '__main__':
    print("I am parent process, my pid is: %s..." % os.getpid())
    p = Process(target=process, args=('child', ))
    print("child process start.")
    p.start()
    p.join()
    print("child process end.")
```
Process 在创建进程时，传入函数和函数需要的参数，返回一个 Process 实例，start() 方法启动进程，
join() 方法等待子进程结束后再继续运行，通常用于进程间同步。

multiprocessing 还提供了进程池，用于批量创建进程：
```python
from multiprocessing import Pool
import os

def task(name):
    print("run task %s, my pid is: %s" % (name, os.getpid()))
    
if __name__ == '__main__':
    print("I am parent process, my pid is: %s..." % os.getpid())
    p = Pool(4)
    for i in range(5):
        p.apply_async(task, args=(i, ))
    p.close()
    p.join()
    print("all subprocesses done...")
```

在使用 join() 之前需调用 close()，避免继续添加新的 process。

有关 multiprocessing 模块更多的功能，可参考官方文档。

#多线程

线程是操作系统中最小的执行单元，比进程更轻量级，一个进程至少由一个线程组成，线程之间的变量是共享的，因为使用的是同一块内存数据段。

所以在多线程模式下，需要特别注意不同线程修改同一变量的问题。

## threading

python 提供了 threading 模块供开发者创建使用线程

```python
import threading

def task_thread():
    print('thread %s is running...' % threading.current_thread().name)
    print("thread %s end..." % threading.current_thread().name)

if __name__ == '__main__':
    print("thread %s is running..." % threading.current_thread().name)
    t = threading.Thread(target=task_thread, name='LoopThread')
    t.start()
    t.join()
    print("thread %s end..." % threading.current_thread().name)
```
每个进程默认会启动一个线程，称为主线程，python 中返回为 MainThread，子线程在创建时可以指定名称，
 默认为 Thread-1、Thread-2
 
 当然 threading 模块还有很多很多功能，还是参考官方文档吧...


