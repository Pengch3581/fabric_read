# 版本 0.0.3

## TODO

先来看看 TODO 有哪些变化
```text
1   Ctrl-C dosn't quite work... especially not with the tail example
2   run, put, sudo et al. doesn't respect the 'fail' kvarg
5   AUTHORS file?
6   fabfiles can't use global import statements
    - some work has been done on this... needs testing
7   need something like roles in Capistrano
    - I'm thinking the ability to work with variable environments as first class
      objects
9   make fanout mode work
10  when an operation fails, fabric doesn't break and exit
11  add built-in help for built-in variables
12  allow run & sudo to save the output of stdout and stderr in variables
13  reduce print & exit() statements to as few functions as possible
14  also comment internal/private functions, if only a little
15  figure out a way to do plugins
    - SCM support should be done entirely in plugins
16  get a better shell
17  a web site for the project with online documentation
18  make local_per_host() adhere to the provided fab_mode
    - preferably by using _on_hosts_do, or something
21  automated test suite
22  find alternative to the deprecated 'execfile()'
23  *
```
在 0.0.2 版本的时候，作者就把 run、put、local 等命令提取出来，而不是仅仅当做内部命令使用了，
这些命令在后续的 fabric 中也成为了使用最多的。

在 0.0.3 版本中，引入了一个 python 特有的用法 [decorator 装饰器](./decorator.md)

```python
#
# Helper decorators:
#
def new_registering_decorator(registry):
    def registering_decorator(first_arg=None):
        if callable(first_arg):
            registry[first_arg.__name__] = first_arg
            return first_arg
        else:
            def sub_decorator(f):
                registry[first_arg] = f
                return f
            return sub_decorator
    return registering_decorator
command = new_registering_decorator(COMMANDS)
operation = new_registering_decorator(OPERATIONS)
strategy = new_registering_decorator(STRATEGIES)
```
0.0.3 版本中封装了一个装饰器函数 new_registering_decorator(registry)，用于处理用户传入的参数是 fabric
定义的函数的情况，例如 run(run)


从 git log 中可以看到
```bash
commit da319754e908c3729eea4052434af34e0c40325f
Author: Christian Vest Hansen <karmazilla@gmail.com>
Date:   Mon Jan 28 19:57:40 2008 +0100

    Use marker decorators for operations and commands.

    - and remove the now obsolete _load_std_commands and _load_operators_helper_map
```