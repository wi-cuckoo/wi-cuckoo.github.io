---
title: Standard I/O Learning (3)
---

> Under normal circumstances every UNIX program has three streams opened for it when it starts up, one for input, one for output, and one for printing diagnostic or error messages.

### stdout

我突发奇想地想从 **stdout stream** 中读取数据，能不能办到呢？ 先来一片代码：

	char str[10];
	fprintf (stdout, "%s\n", "stdout");
	rewind (stdout);           //Maybe i should seek to the head of stream
	fgets (stdout, "%s", str);
	fprintf (stdout, "%s\n", str);
	
哈哈，脑洞来了。但是很遗憾，**str** 输出是一串乱码，说明没有读取成功。 不过我并不感到失望，下面说说我的理解。	

屏幕是标准的输出设备，所以这里可以把屏幕看作一个文件。这一系列文章开头的摘要中反复提到，程序启动时，系统会为其打开三个 stream，**stdout** 就是其中一个，那么 **stdout** 这个 FILE 变量指针就指向屏幕这个文件。这里我想强调一下 FILE  类型仅仅是对文件描述符的一种包装，我个人认为这和 C++ 里流的概念有所不同，虽然使用上并无差别。我们向 **stdout** 写数据，就等于把信息显示在屏幕上。

**stdout** 默认是 **line-buffered**。既然是有缓冲，那么我们还是有机会从 **stdout** 读取数据的，在打印在屏幕上之前数据都还是放在缓冲区的。但是我一时没想到如何去阻塞 **stdout** 写入文件。如果看到这里的朋友有什么建议，可以留言。

### puts 和 fputs

简单来说，`puts` 会自动换行，而后者不会。其余没什么特别。

也说一下 `putc` 与 `fputc` 的差别，对于 **stdout** 来说基本一样，除了 `putc` 用于宏定义的时候有可能出现问题。具体什么问题我也没有去研究，其实这些不完美的函数都是历史遗留问题，按常规使用，就不会有大问题，但往往被骇客利用。

### fflush and fclose

**`fflush`** 用文件的概念来讲就比较好理解，在文件写入的时候，该函数会将用户缓冲区的数据写回内核缓冲区。必须了解的是，**Standard I/O** 都是在用户空间，不是内核空间，这也是为什么使用 **stream** 读写比系统调用， 如：`read()`, `write()` 来得效率高。

**`fclose`** 在使用上就非常简单了。在程序开头写上两句
	
	fclose (stdin);
	fclose (stdout);
	
这样，你的整个程序就别想能够输入输出了，哈哈哈。

### 总结

这个系列就写完了，当然并没有把所有函数都介绍完，有些我自己也很少用，就算 `printf`, `scanf` 这种超级常用但带有复杂的格式化输入输出的，我都记不住，也不想去了解太多，遇到不常用的就直接查手册呗，又不是考试。

> In Wuhan 337 Prison
