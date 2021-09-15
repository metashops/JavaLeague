### 1、概述

Git是一个免费、开源的分布式版本控制系统。

### 2、Git常用命令

| 命令                                 | 作用           |
| :----------------------------------- | :------------- |
| Git config --global user.name 用户名 | 设置用户名     |
| Git config --global user.email 邮箱  | 设置用户名     |
| Git init                             | 初始化本地库   |
| Git status                           | 查看本地库状态 |
| Git add 文件名                       | 添加到暂存区   |
| Git commit -m "日志信息" 文件名      | 提交到本地库   |
| Git reflow                           | 查看历史记录   |
| Git reset --hard 版本号              | 版本穿梭       |

### 3、安装Git进行以下操作

（1）设置用户名

设置用户名：`Git config --global user.name niutongg`

邮箱：`Git config --global user.email mutongg@qq.com`

> *注意*：设置的用户签名和将来登陆GitHub的账号是没有任何关系的
>
> 把Git命令行中文提示改为英文
>
> * 普通命令行
>
> ```git
> echo "alias git='LANG=en_GB git'" >> ~/.bashrc
> ```
>
> * 安装ZSH命令行
>
> ```git
> echo "alias git='LANG=en_GB git'" >> ~/.zshrc
> ```
>
> * 重起Git

（2）初始化本地库

这里注意，首先你要明确你初始化那个工作目录让Git来管理（如你的工作目录是在D盘的Git-Project下的，那么你要切换到该目录下载进行初始化）这时候就可以开始初始化本地库了，命令如下：

```git
git init
```

初始化完成后就生成一个`.git`文件，一般是隐藏的（查看隐藏命令：`ls -al`，window可以通过文件隐藏查看）

（3）查看本地库状态

```git
git status
```

![image-20210519152426419.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqqewb9u18j30jh04tmxd.jpg)

（4）添加到暂存区（也就是Git追文件的过一个过程）

```git
git add hello.txt
```

（5）再次查看本地库会有什么变化呢

```git
git status
```

![image-20210519152717433.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqqewsf929j30g003n3yi.jpg)

这是Git就追踪到这么一个文件，这时候文件只是存在于「暂存区」里头的，如何你不想让它成为历史版本是可以删除掉的。删除命令：`git rm -cached hello.txt`，（这只是删除暂存区里面的，工作区还是有的）

（6）提交本地库

就是将暂存区的文件提交到本地库，这时候就形成自动的历史版本了，提交命令如下：

```git
git commit -m "first commit" hello.txt
```

![image-20210519154009097.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqqex5pc4uj30jk01pmxb.jpg)

（7）再次查看状态

```git
git status
```

![image-20210519154118210.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqqexh69f8j30b90133ye.jpg)

（8）查看历史记录（两个命令都可以）

```git
#第1个命令
Git reflow
显示：9bd3a4d (HEAD -> master) HEAD@{0}: commit (initial): first commit
#第2个命令查看更为详细
git log
显示如下：
commit 9bd3a4dd0dd673da13363c04ff322eb72f57fba0 (HEAD -> master)
Author: niutongg <2652020872@qq.com>
Date:   Wed May 19 15:37:44 2021 +0800
    first commit
```

### 4、修改

（1）修改后再次查看本地库状态命令

```git
git status
```

![006FuVcvgy1gqnsficaeoj30iy03q754.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqqexzqoirj30iy03qwf6.jpg)

modified被修改，文件还是红色，我们还没有追踪，则将进行追踪文件,也就是再次添加到暂存区

（2）添加到暂存区

```git
git add hello.txt
```

（3）再次查看，就已经添加到暂存区了

```git
git status
```

![006FuVcvgy1gqnslg0p3wj30go0260sx.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqqeyb9mbpj30go026t8r.jpg)

（4）提交本地库

```git
git commit -m "second commit" hello.txt
```

显示：

```git
1 file changed, 1 insertion(+), 1 deletion(-)
#一个文件被修改，1行新增，1行删除
```

（5）再次查看本地库

```git
gti status
```

（6）查看历史版本

```git
git reflog #查看版本信息
git log #查看版本详细信息
```



### 5、版本穿梭

现在你的项目已经是第9版本了，但是老板又觉得第8版本好，想穿越回去怎么办？

（1）查看历史版本

```git
git reflog
```

![29C92989-F53A-4780-8D10-BB243D025BC2.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqnt5lfq40j30gv01874i.jpg)

以上就是HEAD指针指向就是第二版本，然后我们要回去第一个版本，先将要穿越回去的版本号复制下来。

（2）穿梭命令如下

```git
git reset --hard 9bd3a4d
显示：HEAD is now at 9bd3a4d first commit
```

（3）再次查看历史版本信息

```git
git reflog
```

![29D00C54-57B5-49DB-820A-9441AD9AA9C8.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqnt9gjv97j30i001q3yw.jpg)

### 6、分支

（1）介绍

在控制版本过程中，同时推进多个任务，为每个任务我们就可以创建每个任务的单独分支。使用分支意味着程序员可以把自己的工作开发主线分离开来，开发 自己的 分支的时候不会影响主线分支的运行。（分支底层也是指针的引用）

（2）分支好处

* 同时并行推进多个功能开发，提高开发效率
* 各个分支在开发过程中，如一个分支开发失败不会对其他分支有任何影响。

（3）分支操作

| 命令名称     | 作用                       |
| :----------- | :------------------------- |
| Git branch   | 创建分支                   |
| Git branch   | 查看分支                   |
| Git checkout | 切换分支                   |
| Git merge    | 把指定分支合并到当前分支上 |

（4）查看当前分支

```git
git branch -v
```

（5）创建分支

```git
git branch hot-fix
```

> Hot-fix是分支名，自己命名

（6）再次查看分支

```git
git branch -v
```

这时候会有两个分支了一个是mater分支和刚刚自己所创建的分支，结构如下：

```git
  hot-fix 9bd3a4d first commit
* master  9bd3a4d first commit
```

（7）切换到热修分支上进行自己的操作

```git
git checkout hot-fix
```

> Git checkout 你的要切换的分支名

（8）再次查看分支名结果如下（这时*号指针hot-fix）

```git
* hot-fix 9bd3a4d first commit
  master  9bd3a4d first commit
```

（9）在热修分支进行修改完后进行查看本地库

```git
git status
```

（10）再次提交到暂存区

```git
git add hello.txt
```

（11）提交本地库

```git
git commit -m "hot-fix first commit" hello.txt
```

（12）切换回master分支

```git
git checkch master
```

（13）分支合并

```git
git merge hot-fix
```

> 如果你想把你刚才创建的热分支hot-fix合并到master分支上，这时你要先回到 master分支才可以。

结果如下：

```git
Updating 9bd3a4d..b16fc4c
Fast-forward
hello.txt | 4 ++--
1 file changed, 2 insertions(+), 2 deletions(-)
```

### 7、合并冲突

> 产生冲突的原因：
>
> 合并分支时，两个分支同时对一个文件进行修改操作，这时就有两套不用的修改，进行合并时Git无法替我们决定使用哪一个，则合并冲突，需要手动合并。



（1）假设master也修改了，然后hot-fix也被修改了，然后切换master进行合并hot-fix

```git
git merge hot-fix
```

这时合并冲突了结果如下：

```git
Auto-merging hello.txt
CONFLICT (content): Merge conflict in hello.txt
Automatic merge failed; fix conflicts and then commit the result.
```

（2）查看本地库

```git
git status
```

可以查看本地库会显示合并不成功结果如下：

```git
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

（3）这时要手动合并 

这时可以先查看文件情况如下`vat hello.txt`，结果如下：

![21F83CCA-2D04-4016-9CD5-39ED41F5D23C.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqozpaokb2j30du05bmxp.jpg)

手动删除一下不要的内容即可，然后保存，手动合并后这样的如下：

![3D16619B-79D2-4404-A443-C41AE4FF664D.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqozvbbpxhj30s20b80uf.jpg)

（4）手动删除不需要的内容后，我们再次把修改后的提交到暂存区

```git
git add hello.txt
```

（5）提交本地库

```git
git commit -m "merge test" hello.txt
```

> 注意：这时候提交本地库有些版本不能带文件名了，但是我的新版本还是可以带文件的

（6）这时可以通过查看内容，就合并成功了

