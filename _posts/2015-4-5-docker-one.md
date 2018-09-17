---
title: Docker 实战系列 — 前言
---

>  This serial of Docker learning records four posts about my own way to know Docker, and using Docker.

### Docker是个什么鬼 ###

![docker](https://github.com/wi-cuckoo/BlogData/blob/master/images/docker.jpg?raw=true)

###### 这是Docker的Logo，一个萌萌哒的小鲸鱼 ######

对于用过Docker的人来说，对这幅图估计都有着自己的体会。这里有[官方的介绍](https://docs.docker.com/)，但我接下来写的肯定不能把原文翻译过来（我不会告诉你我正在干着翻译技术文章的勾当）。对于这幅图，我的理解是，，，这条**小鲸鱼**确实很萌。开玩笑啦。

不扯淡了。docker就好像那条小鲸鱼，提供了一个平台。而镜像就等同上面放置的各种各样的集装箱啦，这里的镜像和后面提到的容器等概念你都不必深究，知道有这么个东西就行，后续章节会介绍，如果知道则更好。然后，我们大家都知道箱子放的肯定是货物了。那么，这里的货物肯定不care到底是大海是什么玩意，船是什么玩意，只要有个箱子装着就行了。现在回到docker应用上来，我们的应用程序就像货物一样，可以被装载在镜像（箱子）里。镜像为我们提供了一个程序可以运行的环境。当我们需要运行程序的时候，就运行一个容器，简单来说如同打开一个箱子检查里面的东西一样，这么说有点生硬，但我还没想到更好的表达 (>:。

如果你理解能力好的话，直接去我的[百度云盘](http://pan.baidu.com/s/1qWmgPWw)（提取码：47ng）里直接下载几份讲docker的pdf。我个人来说是十分不喜欢看视频学习的，感觉就和老师的现场直播一样，昏昏欲睡。而看文档者不一样，可以快速浏览，而且能不能接受都由自己主导。

还是扯点蛋吧，docker相比VMware的优点，众所周知是轻量级。VMware的虚拟化在硬件层面，而docker则是在系统层面，然而，说这些并没有什么~~luan
~~用。Who \*\*** cares？还能不能好好一起装逼了。大部分的人，如我，只关心docker这玩意到底好不好用，谁在乎docker是用**Golang**语言编写的，那些屌到没朋友的**名字空间**。

不bb了，给点链接吧，我翻译的两篇有关docker基础的文章

[如何交互式地创建一个Docker容器](http://linux.cn/article-5484-1.html)

[如何在Fedora / CentOS上面升级Docker 1.6](http://linux.cn/article-5488-1.html)

当然，我翻译不多，如果想阅读更多可以去[Linux中国](http://linux.cn/)上混混，很好的社区。
