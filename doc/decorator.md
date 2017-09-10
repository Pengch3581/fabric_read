# 唏嘘

从学习《廖雪峰 Python 教程》开始慢慢接触 Python 这一门语言，由于最开始从事的是集成运维，平时工作上也就是发布而已，
简单来说就是从开发那边拿到代码，编译生成 jar 包放到服务器上的 tomcat 加载起来。

好吧，我承认第一份工作很简单，会一点控制台命令就可以完成了，当然薪水也就呵呵了。

扯远了，在大环境的推动下，运维学习 Python 开始变成一种政治正确了，胶水语言的优势完美的和零开发基础的运维结合起来，
周期短、强大的公共库、语法简单、学习成本低，这些都使的 Python 成为了运维洗白之路的不二之选。

### 装饰器

python 的高级特性之一，一直以来都没能完全弄懂，在阅读 fabric 源码中拖节奏了，是时候搞懂它了。

装饰器在日常开发中有很多实用场景，插入日志、性能测试、事务处理等，装饰器可以提取出大量可以复用的代码了。

### 给代码计时

在 shell 脚本时代，分析过滤服务器日志是一件很耗时的事情，想要知道一个脚本或命令执行的时间，
通常的做法是在执行前获取下系统时间，结束后再获取一次，通过两个的差就可以知道脚本的耗时。

```bash
!#/bin/bash

# 计算脚本运行时间

read -p "请输入需要运行的脚本:" shname

begin_time=$(date +%s)
sh $shname

end_time=$(date +%s)
use_time=$(($end_time - begin_time))
echo "运行 $shname 脚本花费了 $use_time 秒"
```
通过这个脚本去执行需要运行的脚本就能知道运行的时间了，类似这样把计算运行时间的逻辑放在一个单独的模块里面，
通过这个模块去调用其他脚本的方式跟装饰器很像了。

在 Python 中一切都是对象，函数也是，装饰器通过改变函数对象，从而给函数增加更多的功能，还是对函数运行时间的功能

```python
import time 

def deco(func):
    def wrapper():
        startTime = time.time()
        func()
        endTime = time.time()
        msecs = (endTime - startTime) * 1000
        print("use time: %f ms" % msecs)
    return wrapper

def myfunc():
    print("start func")
    time.sleep(1)
    print("end func")

print("func is:",myfunc.__name__)          
myfunc = deco(myfunc)
print("func is:", myfunc.__name__)
myfunc()
```
deco 就是一个完整的装饰器，装饰器没有影响原来的函数 myfunc，也没有影响函数的调用方法

### 装饰器语法糖

Python 提供了 @ 语法糖来精简装饰器的代码
```python
import time

def deco(func):
    def wrapper():
        startTime = time.time()
        func()
        endTime = time.time()
        msecs = (endTime - startTime) * 1000
        print("use time: %f ms" % msecs)
    return wrapper

@deco
def myfunc():
    print("start func")
    time.sleep(1)
    print("end func")

print("func is:", myfunc.__name__)
myfunc()
```
使用语法糖 @ 之后就不需要给 myfunc 重新定义了，其实语法糖 @ 的本质就是 myfunc = deco(myfunc)

Python 中，函数可以支持 (*args, **kwargs) 可变参数，装饰器可以通过可变参数的形式来实现内嵌函数的签名，
装饰器本身也是可以支持参数的

当一个函数有多个装饰器的时候，装饰器是有调用顺序的，对于Python中的”@”语法糖，装饰器的调用顺序与使用 @ 语法糖声明的顺序相反

### 内置装饰器

在Python中有三个内置的装饰器，都是跟class相关的：staticmethod、classmethod 和property

```text
staticmethod 是类静态方法，其跟成员方法的区别是没有 self 参数，并且可以在类不进行实例化的情况下调用
classmethod 与成员方法的区别在于所接收的第一个参数不是 self （类实例的指针），而是cls（当前类的具体类型）
property 是属性的意思，表示可以通过通过类实例直接访问的信息
```