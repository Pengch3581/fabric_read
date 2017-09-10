Dismantling the wheel  ---- fabric
==============

# 传送门

[Fabric_0.0.1](./doc/Fabric_0.0.1.md)

[Fabric 0.0.3](./doc/Fabric_0.0.3.md)

# 职业生涯

是的，我的职业生涯碰到麻烦了。

在低级联赛摸爬滚打了三年了，每天徘徊在非职业跟职业边缘，
至今未能在标签栏中 tag -a programmer -m 'i am a programmer'。

今年勉强升了一个联赛级别，可除了照着文档博客安装应用，写一些 low 到爆的脚本（
low 可能还不能完全形容），处理一些日常报错，没有别的技能了。

距离甲级联赛还差了 \d+ 个级别，这让人感到很不舒服，特别是对一个落选秀来说更为致命。


# 源码

下雨天，拆轮子跟源码更配哦。

[Fabric 源码](https://github.com/fabric/fabric) 配合 git checkout diff 使用
 
# 小技巧 & 工具

当然，对于一个刚解锁 " 恭喜完成 helloworld " 成就的我来说，在运用 git 方面的技巧很 low，
还是厚着脸皮记录下来，主要是怕自己忘了 0-0

工具选用 [GitHub Desktop](https://desktop.github.com) 和 [TortoiseGit](https://tortoisegit.org)

还有一款 [SourceTree](https://www.sourcetreeapp.com) 也很不错，当然 [git](https://github.com/git/git) 命令还是必不可少的

小时候特别幻想能拥有一台时光穿梭机，git have one

```bash
# git log 命令可以查看所有的历史记录
➜  fabric git:(master) ✗ git log 

commit 63b622bdcf3408c24a0434b15a5ac7c98d741314
Author: Jeff Forcier <jeff@bitprophet.org>
Date:   Mon Apr 24 16:28:00 2017 -0700

    Cut 1.13.2

commit 1deacf60db6e145329cdbf2c0437f28e376259e3
Merge: 0a3d11f5 8cbae806
Author: Jeff Forcier <jeff@bitprophet.org>
Date:   Mon Apr 24 16:27:30 2017 -0700

    Merge branch '1.12' into 1.13

commit 8cbae8069f7eb9011dd7c7838e44532267e4332c
Author: Jeff Forcier <jeff@bitprophet.org>
Date:   Mon Apr 24 16:26:58 2017 -0700

    Cut 1.12.2
    
# HEAD 指向的版本就是当前版本，使用 git reset --hard commit_id 即可以穿越了
➜  fabric git:(master) ✗ git reset --hard 1deacf60
# 这样 HEAD 指针就指向 1deacf60，穿越速度非常快
# reflog 可以查看整个穿越的记录
➜  fabric git:(master) ✗ git reflog 

# git 中标签是版本库的快照，就是指向某个 commit 的指针，拆轮子之 fabric 就是遵循 tag 的脚步前进的

```

利用时空穿梭机就可以去体验开发者纠结又激情的项目旅程了~




