# 开始拆轮子

第一次拆轮子，内心还是蛮紧张的

## README
anyway，轮子还是要拆的。

"惯例" 还是从 README 开始（其实没看过什么源代码）
```text
FABRIC README.

Fabric is a simple pythonic remote deployment tool. 

It is designed to upload files to, and run shell commands on, a number of
servers in parallel or serially. These commands are grouped in tasks (regular
python functions) and specified in a 'fabfile'. 

It is a bit like a dumbed down Capistrano, except it's in Python, dosn't expect
you to be deploying Rails applications, and the 'put' command works. 

Unlike Capistrano, Fabric want's to stay small, light, easy to change and not
bound to any specific framework.

Once installed, you can run 'fab help' to learn more about how to use Fabric.
```

恩，以我的英语水平，这一小段 .......... 还是用 google 吧

大体上是一个简单化的 [Capistrano](https://github.com/capistrano/capistrano) （一个用 ruby 语言写的远程多服务器自动化工具）

但是怎么能跟 Capistrano 一样捏，当然还是要有区别的，同时也是最重要的，保持了小、轻、容易改变的特点，而不是绑定到别的框架。

可以看出 Jeff Forcier （开发者）是很有想法的，大神在碰到多服务管理的时候就是异于常人，异于欧的。

## Step one

Date：Fri Jan 4 13:24:26 2008 

Jeff 打开一封邮件，这封邮件是来自兄弟部门的需求，运营小妹需要对服务器上的一个文件做修改，
面对运营小妹的请求，Jeff 打开了终端，敲起了 SSH 命令，可是仅有的热情很快就被茫茫多的 SSH 标签页冲淡了，
于是一个 Idea 产生了（以上纯属胡扯，如有雷同，可能你该改变了 -_-|）

言归正传，Jeff 一开始的想法是通过 fab.py 去调用一个 fabfile 文件，根据我的理解，
一个 fabfile 文件就代表了一台或者一组服务器和各种远程指令，这样就把一连串的 SSH 便签就转换成为了一个 fabfile。

```python
# fab.py

def main(args):
    # loading:
    execfile('fabfile')
    cmds = dict([n for n in filter(lambda n: (n[0][0] != '_') and callable(n[1]), locals().items())])
    
   # validation:
   max_name_len = reduce(lambda a, b: max(a, len(b)), cmds.keys(), 0)
   for cmd in args:
       if not cmd in cmds:
           print "No such command: %s" % cmd
           print "Available commands are:"
           for name, fn in cmds.items():
               print ' ', name.ljust(max_name_len), ':', fn.__doc__.splitline()[0]
              exit(1)
   
   # execution
   for cmd in args:
       cmds[cmd]()
```
以上是 fab.py 中的 main 函数，可以看出通过 execfile 函数去读取 fabfile 文件，python 首先会去找这个文件，然后加载到内存，
接着去执行，当然在 fabfile 中都是些函数，是不会执行的。

接着通过 locals 就可以获取这些命令了。

好奇心重的小伙伴就会问了，仅仅是获取另一个文件里面的函数，为什么不直接用 import 呢？或者干脆写到一起？有必要搞的这么麻烦？
一点也不 pythonic ......

