1、注册GitHub账号并登陆

2、创建远程库

3、给远程库另取别名

```git
git remote add git-demo https://github.com/niutongg/git-demo.git
```

4、可以简单查看别名

```git
git remote -v
```

5、推送

此时我们的远程库还是没有代码的，我们将本地库推送到远程库

```git
#git push 别名/地址 分支名
git push git-demo master
```

6、拉取

就是假如远程库被其他人修改了，然而和你现在的本地库不一样了，你要确保和远程库同步，这时需要拉取更新

```git
#git pull 别名 需要拉取的分支
git pull git-demo master
```

然后提示如下：

![B99E00C4-D752-48D3-A9FF-F7BEED83841E.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gqp1ap3mvkj30kn0cpq68.jpg)

然后你可以查看本地库以及文件的内容，都是同步了。

7、克隆

（1）假如在团队中我是B克隆了A的代码，进行修改了

```git
git clone https地址
```

（2）修改结束记住要提交暂存区和提交本地库

```git
git add hello.txt
git commit -m "B commit" hello.txt
```

（3）然后将代码`push`到远程库

```git
#git push https地址 master分支（将该分支push到远程库）
git push https地址 master
```

8、其他人员再次拉取

```git
git pull git-demo master
```

> 完整步骤：
>
> ```git
> git clone https://...
> git init
> git status
> git add 文件名
> git commit -m "second commit" 文件名
> git push 别名或地址 推送的分支
> ```
>
> 

