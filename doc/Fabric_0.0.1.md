# 开始拆轮子

第一次拆轮子，内心还是蛮紧张的

### README
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

