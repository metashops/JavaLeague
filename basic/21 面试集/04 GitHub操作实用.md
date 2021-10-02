#### 1、GitHub常用词：

面试：你用过GitHub，那你如何在GitHub找到优秀代码，学习其他高手的代码呢？然后如何共享自己代码？

* watch：会持续收到该项目的动态
* fork：复制某个项目到自己的GitHub仓库中
* start：点赞数
* clone：将项目克隆至本地
* follow：关注你感兴趣的作者，会收到他们的动态
* in：关键词限制搜索范围
* start或fork数量关键词去查找
* awesome加强搜索

#### 2、GitHub使用限制搜索：

In :如老板给你做个秒杀系统，简单版的？你咋做？我们也没有做个，那我们可以去GitHub参考别人的代码。

```
xxx关键词 in:name  //如：seckill in:name 或 seckill in:name,readme,description
```

Star 或 fork数量关键词去查找：

* 如查看star数量大于5000的SpringBoot项目：

  * 公式：关键词 stars 通配符(:> or :>=)
  * 实战：Springboot stars:>=5000

* 查找fork数大于500的SpringBoot项目

  * Springboot forks:>=500

* 查找fork数在 [1000-5000] 和 stars在 [3000-500]之间的SpringBoot项目

  * Springboot forks:1000..5000 stars:3000..5000

* awesome：想要学习什么使用awesome

  * awesome 关键词

  * awesome redis

#### 3、高亮度显示一行代码

用于给别人指出关键代码的行号，加入让你同事看第20行

格式：`地址 + #L31`,表示第31行显示高亮度

格式：`地址 + #L31-L38`,表示第31到38行显示高亮度

```
https://github.com/codingXiaxw/seckill/blob/master/src/main/java/cn/codingxiaxw/web/SeckillController.java#L31
```

![Snip20211001_35.png](http://ww1.sinaimg.cn/large/006FuVcvgy1gv03mh17g2j62gu0j215h02.jpg)

#### 4、项目内搜索

在你键盘按住：`t` 即可

#### 5、搜索某个地区内的大佬

* 公式
  * location:地区
  * location:language
* 如北京的Java方法用户
  * location:beijing language:java

## Typora快捷键

| Markdown | 图标 | 快捷键           |
| -------- | ---- | ---------------- |
| 撤销     |      | Ctrl /⌘+Z        |
| 重做     |      | Ctrl /⌘+Y        |
| 加粗     |      | Ctrl /⌘+B        |
| 斜体     |      | Ctrl /⌘+I        |
| 标题     |      | Ctrl /⌘+Shift +H |
| 有序列表 |      | Ctrl /⌘+Shift +O |
| 无序列表 |      | Ctrl /⌘+Shift +U |
| 待办列表 |      | Ctrl /⌘+Shift +C |
| 插入代码 |      | Ctrl /⌘+Shift +K |
| 插入链接 |      | Ctrl /⌘+Shift +L |
| 插入图片 |      | Ctrl /⌘+Shift +G |
| 查找     |      | Ctrl /⌘+F        |
| 替换     |      | Ctrl /⌘+G        |
