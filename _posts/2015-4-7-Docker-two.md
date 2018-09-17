---
title: Docker 实战系列 — 运行第一个容器
---

### 当你对一些概念理解不深的时候，实践是最好的解决办法 ###

那么废话不多说，就是干。

首先安装docker，这里没必要说明，直接去参考[官方文档](https://docs.docker.com/installation/#installation)，如果英文不好就百度。跟着文档做，把docker daemon打开，有需求的可以指定某个用户有执行docker的权限，这就看爱好了。

打开daemon的时候也许会遇到无法建立起docker0网桥的问题，这个通常出现在内网主机上，解决办法是将IP地址池中172那个内网网段留给docker。具体做法我不细说，不会的参照这篇[博文](https://docs.docker.com/installation/#installation)。我喜欢简洁的表述，focus on the point。

接下来，运行一下docker这个命令

`# docker`


你会看到docker所有的命令介绍，当然是简介，不过可以留作以后翻阅。我们选择images这个选项，即运行

`# docker images`

这会输出你本地所拥有的镜像及其相关信息，当然目前我们还没有任何镜像。那么我们去pull一个镜像下来（是不是有种github的即视感）。

`# docker pull ubuntu`

也就200M不到的东西，默认是从docker的官方库下载的。然后你再运行images那个命令，就会看到有镜像列出了。这里可能会纳闷，怎么这么多。这是因为ubuntu的镜像在docker官方hub里不止一个，如果你指定了tag，就会唯一标识一个image，比如**ubuntu：14.04.1**。

image的信息最重要的当属ID了，其次就是tag和name。这里的ID很长一串，包括后面每运行一个容器也会有一个ID，但我们涉及到需要ID的参数时，只需要其**前四个字符**就可以了。这里先说，因为很实用。

然后我们假设一个镜像的ID为cde5bc7c4c8b，那么如何运行一个容器呢，follow me

`# docker run -it cde5`

这里我也不想多解释，因为linux用户都知道如何获取命令帮助，上面也说了运行docker可以获得命令的简介。俗话说，授人鱼，不如授人以渔（时时刻刻学会装逼）。

上面运行后会进入一个bash交互的环境下，对于linux用户，没有比这个更亲切的了吧。你可以在里面为所欲为了，你会发现那就是一个mini的linux系统。既然这样，我们简单的安装一个软件吧，比如安装ssh，以ubuntu的镜像为例。

`# apt-get install -y ssh`

安装好后，记得启动服务。你可以试着连接你的主机，虽然很无聊，但是装逼好啊。这里我相信有人会问如果装一个GUI的程序能不能运行啊？答案是YES。怎么做呢，我给个连接自己去玩吧

[如何在Docker容器中运行GUI程序](https://linux.cn/article-5304-1.html)

因为是mini的系统，所以不要指望很多命令都能用，当然常用的肯定有。在虚拟的系统里待腻了，我们需要退出，那就试试exit吧，卧槽！真退出了。虽然是退出了，但运行的容器进程却没有kill掉。用这个命令可以查看

`# docker ps -a`

这里我们关注容器的ID和state。我们要做的就是kill掉它，因为被我们玩腻了。

`# docker rm -f ID`

好了，再查看没有docker的容器在运行啦。整个过程就这么简单，当然以后再回来看，你会由衷的发出一声>_<卧槽。

#####See You Later!
