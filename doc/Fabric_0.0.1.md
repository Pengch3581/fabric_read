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

言归正传，Jeff 一开始的想法是通过 fab 去调用一个 fabfile 文件，根据我的理解，
一个 fabfile 文件就代表了一台或者一组服务器和各种远程指令，这样就把一连串的 SSH 便签就转换成为了一个 fabfile。

```python
# fab

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
以上是 fab 中的 main 函数，可以看出通过 execfile 函数去读取 fabfile 文件，python 首先会去找这个文件，然后加载到内存，
接着去执行，当然在 fabfile 中都是些函数，是不会执行的。

接着通过 locals 就可以获取这些命令了。

好奇心重的小伙伴就会问了，仅仅是获取另一个文件里面的函数，为什么不直接用 import 呢？或者干脆写到一起？有必要搞的这么麻烦？
一点也不 pythonic ......

可以看出最开始 fabric 的设计思路是：
```
fabric
------

-  fab
      + main 函数以及各种基础函数（set、get、put、run、sudo...等）
-  fabfile
      + env 远程机器信息、远程操作 command（deploy、setup...等）一些业务命令
```

## Step two

Date：Fri Jan 4 20:35:54 2008 

由于灵感来的太突然，所以 Jeff 最开始着重于架构和设计方面，将业务命令单独提取出来，并封装了基本的命令，
将远程服务器以及操作抽象成若干 fabfile ... balabala

好吧，这也确实不是第一版代码写的像路人甲的原因，强行洗白QAQ

于是乎，午休的时候，Jeff 做了一个梦，只见电闪雷鸣，风卷云涌，落叶在地上拼凑出一段话：

"Closer to a useable product, plus LICENSE file as required by nongne.org"

这么富有武侠气息的情节，当然不可能发生在富靠科技穷靠变异的超级英雄国度，
但是产品化、专业化确实是能将软件提升了不止一个 level.

Talk is cheap, Show me the code...

#### 添加 TODO 和 LICENSE 

```
TODO（一开始叫 KNOWN_ISSUES) 记录了整个开发过程中碰到的 bug 以及正在开发的功能和未来将要开发的功能

LICENSE：GNU GENERAL PUBLIC LICENSE 这玩意欧也没弄过
```

#### Internal plumbing

来看一下 profession 变化后的 main()

```python
# fab

def main(args):
    print(__license__ % ENV)
    _load_std_commands()
    fabfile = _pick_fabfile()
    load(fabfile)
    # validation
    for cmd in args:
        if not cmd in COMMANDS:
            print("No such command: %s" % cmd)
            _list()
            exit(1)
    # execution:
    for cmd in args:
        ENV['fab_cur_command'] = cmd
        print('Running %s...' % cmd)
        COMMANDS[cmd]()
    print('Done.')
```

产品化的过程中，fab 做了下面几个修改
- 对 fab 中的函数分类：标准命令和内部管道
- 标准命令：get、sudo、put、local、run 等针对远程服务器操作的命令，fabfile 中具体业务操作就是这些标准命令组合起来的
- 内部管道：相对标准命令更加底层的函数（_connect、_list、_pick_fabfile、_on_host_do 等）

当然这些只是对前一版做的优化，后续还有更多的地方可以看出开发者对于产品化所做的思考。

## Step three

Date：Thu Jan 10 01:02:08 2008

在经过一周的辛勤劳动，fab.py 中已经添加了很多功能，按照惯例是需要编一段关于 Jeff 的内心活动的，还是跳过直接撸代码吧.

在这个版本，对 fab 进行了分离：
- fab 正式改名 fabric.py
- fabfile 则变成 fab 文件

此时的 fabric.py 中已经包含了许多功能，下面将时光穿梭机时间调整到 33c9dcc

此时的 fab 文件变成命令的形式，通过 setup.py 部署，就跟平时在 pypi 上安装的库一样

```python
# fab

import sys

import fabric

if __name__ == '__main__':
    try:
        fabric.main(sys.argv[1:])
    except SystemExit:
        # a number of internal functions might raise this one
        raise 
    except:
        sys.excepthook(*sys.exc_info())
        # we might leave stale threads if wo don't explicitly exit()
        exit(1)
    exit(0)
```

通过 fab 命令加上参数的形式调用 fabric.py 完成操作

在使用 fabric 时有一个我常用的命令 run，fabric.py 139 行

```python
# fabric.py

def run(cmd, **kwargs):
    "Run a shell command on the current hosts."
    if not CONNECTIONS: _connect()
    _on_host_do(_run, cmd)
```

run 先会检查 CONNECTIONS 列表，CONNECTIONS 列表中存放了使用 paramiko 模块连接成功返回的 (host, client)，
如果没有则调用内部管道函数 _connect() 连接，fabric.py 310 行

```python
# fabric.py

def _connect():
    "Populate CONNECTIONS with (hostname, client) tuples as per fab_hosts"
    _check_fab_hosts()
    signal.signal(signal.SIGINT, _trap_sigint)
    ENV['fab_password'] = getpass.getpass()
    port = int(ENV['fab_port'])
    username = ENV['fab_user']
    password = ENV['fab_password']
    pkey = ENV['fab_pkey']
    key_filename = ENV['fab_key_filename']
    for host in ENV['fab_hosts']:
        client = ssh.SSHClient()
        client.load_system_host_keys()
        if 'fab_new_host_key' in ENV and ENV['fab_new_host_key'] == 'accept':
            client.set_missing_host_key_policy(ssh.AutoAddPolicy())
        client.connect(
            host, port, username, password, pkey, key_filename
        )
        CONNECTIONS.append((host, client))
    if not CONNECTIONS:
        print("The fab_hosts list was empty.")
        print("Please specify some hosts to connect to.")
        exit(1)
```

_connect() 函数会读取 ENV['fab_hosts'] 中的主机名，通过 [paramiko（一个很好用的 SSH 库）](https://github.com/paramiko/paramiko)
创建 SSH 连接，并添加到 CONNECTIONS 列表中。

接下来便是将 cmd 传入到远程主机上执行，_on_host_do(_run, cmd)，fabric.py 388 行

```python
# fabric.py

def _on_host_do(fn, *args):
    '''Invoke the given function with hostname and client parameters in
    accord with the current fac_mode strategy.
    
    fn should be of type:
        (str:hostname, paramiko. SSHClient:client) -> bool:success
        
    '''
    
    strategy = ENV['fab_mode']
    if strategy == 'fanout':
        threads = []
        for host, client in CONNECTIONS:
            env = dict(ENV)
            env['fab_host'] = host 
            thread = threading.Thread(None, lambda: fn(host, client, env, *args))
            thread.setDaemon(True)
            thread.start()
            threads.append(thread)
        for thread in threads:
            thread.join()
    elif strategy == 'rolling':
        for host, client in CONNECTIONS:
            env = dict(ENV)
            env['fab_host'] = host
            fn(host, client, env, *args)
    else:
        print("Unsupported fab_mode: %s" % strategy)
        print("Supported modes are: fanout, rolling")
        exit(1)   
```

这里 fabric 使用了两种模式去执行 cmd，fanout 和 rolling 即使用 threading 模块多进程并行执行，或者串行一个一个地去执行。

_on_host_fo(fn, *args) 的第一个参数传入的是函数，这样写出来的代码看起来就很舒服，不像我平时写的那些 "东北乱炖" 大杂烩。

通过前面这些函数，已经完成了获取主机名、用户密码、需要执行的命令，创建 SSH 连接，接下来就是具体执行命令了，_run() 427 行

```python
# fabric.py

def _run(host, client, env, cmd):
    cmd = _lazy_format(cmd, env)
    real_cmd = env['fab_shell'] % cmd.replace('"', '\\"')
    print("[%s] run: %s" % (host, (env['fab_debug'] and real_cmd or cmd)))
    stdin, stdout, stderr = client.exec_command(real_cmd)
    out_th = _start_outputter("[%s] out" % host, stdout)
    err_th = _start_outputter("[%s] out" % host, stderr)
    out_th.join()
    err_th.join()
    
def _start_outputter(prefix, channel):
    def outputter():
        line = channel.readline()
        while line:
            print("%s: %s" % (prefix, line))
            line = channel.readline()
    thread = threading.Thread(None, outputter, prefix)
    thread.setDaemon(True)
    thread.start()
    return thread
```

事实上真正去远程服务器上执行命令的是这一段：

```python
stdin, stdout, stderr = client.exec_command(real_cmd)
```

为了更好的理解，还是介绍一下 python 的多任务

[Python 多任务模块](./doc/Python_Multitasking.md)

了解了 Python 多线程机制，基本也就弄明白 fabric 为什么这么快了，当然以后你也可以这么快。

## Step four



