---
title: Standard I/O Learning (2)
---

> Under  normal circumstances every UNIX program has three streams opened for it when it starts up, one for input, one for output,  and one for printing diagnostic or error messages.

### scanf 访问越界

先来看看上一篇中那个程序代码和执行输出:

**Code**:

	char s[4];
	scanf ("%s", s); 
	fprintf (stdout, "%s\n", s); 
	
	fgetc (stdin);
	
	char buf[5];
	fgets (buf, 5, stdin);
	fprintf (stdout, "%snew line", buf);

**Output**:

	scanf
	scanf
	fgets
	fgetnew line

代码中，我们定义了一个 4 个字节长度的 char 数组 **s[4]**，然后执行过程中输入了 5 个字符， “scanf“。按通常情况，我们最多允许输入 3 个字符，最后一个字符作为 '\0'。但从输出来看，这里 s 的打印输出完全正确。 虽然正确，但这里已经出现访问越界了，由于 `scanf` 并不检查访问越界，所以这种情况下超出数组长度的字符会去占用其他的内存地址，我们来修改一下代码，看看 gdb 的一些调试结果。

修改后的代码：

	struct string
	{	
		char s[5];
		char s2[4];
	};

	struct string str;
	scanf ("%s", str.s);
	fprintf (stdout, "%s\n", str.s);

gdb 调试：

	Temporary breakpoint 1, main () at a.c:26
	26		scanf ("%s", str.s);
	(gdb) display &str.s[4]
	1: &str.s[4] = 0x7fffffffdf44 "\377\177"
	(gdb) display &str.s2
	2: &str.s2 = (char (*)[4]) 0x7fffffffdf45
	(gdb) display &str.s[5]
	3: &str.s[5] = 0x7fffffffdf45 "\177"
	(gdb) n
	scanfffo
	27		fprintf (stdout, "%s\n", str.s);
	3: &str.s[5] = 0x7fffffffdf45 "ffo"
	2: &str.s2 = (char (*)[4]) 0x7fffffffdf45
	1: &str.s[4] = 0x7fffffffdf44 "fffo"
	(gdb) display str.s2
	4: str.s2 = "ffo"

代码修改定义了一个结构体，用来存储两个字符数组。 由前面的文章 Issues of Alignment 知道，这样两个数组在内存空间上的地址
就连续了，这个通过 gdb 的调试结果也能看出。 **s[4]** 的下一个地址就是 **s2** 的首地址。但这里也有个地方值得注意，就是 **s[5]** 的地址和 **s2** 的首地址一样，但明显 **s[5]** 是未定义的，这里访问已经越界了，因此看来 C 在一般情况是不检查数组越界的（可以
通过函数来完成）。

继续调试， 我们输入了超出 s 数组长度的一串字符， **“scanfffo“**。然后 s 取到了 **“scanf“**，注意数组没有存储 '\0'，严格来说 s 不能称为一个字符串。然后 **s2** 中获得了 **“ffo“**，当然 **s2[3]** 就是 '\0'，这个不用怀疑。但从中我们看到了最大的问题，就是当 `scanf` 访问越界的时候，会发生占用其他内存的情况，这是相当危险的，许多底层的攻击就源于此。而且用 `fprintf` 输出的时候，由于
 s 不包含 '\0'，所以会一直顺着内存访问下去，直到遇到 '\0' 结束。

### fgets 防止访问越界

既然上面说到 `scanf` 会发生访问越界而不检查，那么 `fgets` 可以解决这个问题。 从前面的输出可以看到，**buf** 只存储了 5 个字符， "fget\0"。这很直观看出 `fgets` 做了越界检查，仅取了 **stdin** 缓冲区的 "fget"， 然后最后补齐了一个 '\0'。

### fgets 与 scanf 的读取差别

如果抛开越界访问问题，`fgets` 和 `scanf` 在读取上还存在其他差别。还是回到最初那段代码（文章开头），再来看一个样例
输入输出：

	sca add
	sca
	 addnew line
	 
这里我们输入了两个单词（不检查单词正确与否），"sca" and "add"。发现 `scanf` 只取了第一个空格前的单词，这是因为其读取是以空格为分割符的。此时，缓冲区就剩下了 " add"（注意空格），这被 `fgets` 取走，并且刚好填满数组。因此，`fgets` 是可以处理空格符的，如果你想读取一个句子，那么必须用 `fgets` 来读取，而且记得如果没有越界，会连同换行符 '\n' 一并取
走。

### 其他读取函数

**`fscanf`**，这个函数也是不检查越界访问的，慎用。当然从一般文件 stream 中读取就问题不大，大不了读到 EOF 接结束嘛。 如果
程序调用 `fscanf` 读取 **wint_t** 或 **wchar_t** 对象，需要包含 <wchar.h>。

**`gets`**，在 stdio.h 的 man 手册里对其介绍有这么一句话：

	Reading a line that overflows the array pointed to by s causes undefined results. 
	The use of fgets() is recommended.
	
嗯，所以最好还是用 `fgets`，不过对于 **C Programmer** 来说，无论用哪个，都要清楚自己写的代码会发生什么情况。	
	
**`getc`**，这个和 `fgetc` 一样，除了在被用在宏定义的情况下可能会访问 stream 多次，所以以 `getc` 作为参数的时候永远
不能是一个 **[Side Effect](http://twobitsboy.top/side-effect)** 的表达式。

在下一篇中，会简单地介绍那些输出函数，相对于输入，输出就不是很需要注意了。

> In Wuhan 337 Prison
