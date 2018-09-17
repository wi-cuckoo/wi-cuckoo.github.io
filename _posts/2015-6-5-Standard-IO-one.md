---
title: Standard I/O Learning (1)
---

> Under  normal circumstances every UNIX program has three streams opened for it when it starts up, one for input, one for output,  and one for printing diagnostic or error messages.

### Important Functions

下面的函数是从 **stdio.h** 中选取的，在我看来都是很基础，也很常用的函数。
  
  * `int      fclose(FILE *);`
  * `int		  fgetc(FILE *);`
  * `int      fputc(int, FILE *);`
  * `char		  *fgets(char *, int, FILE *);`
  * `int      fputs(const char *, FILE *);`
  * `int      fprintf(FILE *, const char *, ...);`
  * `int      fscanf(FILE *, const char *, ...);`
  * `char     *gets(char *);`
  * `int      getc(FILE *)`
  * `int      putc(int, FILE *);`
  * `int      putchar(int);`
  * `int      puts(const char *);`
  * `int      scanf(const char *, ...);`
  * `int      printf(const char *, ...);`
  * `int		  fflush(FILE *);`
  * `void     rewind(FILE *);`
  * `void     setbuf(FILE *, char *);`
  
当然，我们如果仅仅讨论黑框框界面的输入输出，是没必要提那些 `f` 开头的函数的。但是在现代操作系统中，我们已经将一切设备
当文件来对待了，在 **stdio.h** 中有如下定义：

	extern FILE *stdin;
	extern FILE *stdout;
	extern FILE *stderr;

	  	
所以，这里我们完全可以用 stream  的操作函数来实现终端的读写操作，这与读写文件是一个意思，主要还是为了形成一个认识。不过那些简单的终端输入输出函数
用起来还是很顺手的，因此我一并加入了列表，重点还是放在带 FILE 结构体参数的函数理解上。

#### stdin 

首先，还是来看一段代码:
	
		char c1, c2;
		c1 = fgetc(stdin);
		// getchar();
		c2 = fgetc(stdin);
		printf(stdout, "%d  %d\n", c1, c2);
	
这是很单纯的一段代码，但我们会从中获取一定信息。 

来看一份样例输出：

    a<enter>
    97  10
    
当我输入 a 后，回车结束，这时候程序直接输出，根据 c2 的 ASCII 码我们知道，c2 取到了回车符 '\n'。 所以在我们回车结束后，
stdin 的缓冲区存储了字符 a 和 '\n'，然后被两个 fgetc 分别读取了。那么这时候我们可以插入一条语句（被注释那个），然后就
能输入两次了，但记住，程序结束后缓冲区还是存有一个回车字符 '\n'。这里我们可以得到一个结论，fgetc 是以回车作为输入结束标志的，并且可以处理回车符。

再看另一段代码：

	char s[4];
	scanf ("%s", s); 
	fprintf (stdout, "%s\n", s); 
	
	fgetc (stdin);
	
	char buf[5];
	fgets (buf, 5, stdin);
	fprintf (stdout, "%snew line", buf);
	
这段代码能说的东西很多，大部分暂且放到下一篇。这里先说 scanf 读取和 fgets 读取的差别，首先一点，scanf 读取会留下回车符
在 stdin 缓冲区内，而 fgets 会把 '\n' 一并读入 buf 中。同样来看以上代码执行的一个样例输出：

	scanf
	scanf
	fgets
	fgetnew line

这里我们用了 `fgetc(stdin)` 来吸收 '\n'，这样可以使执行 fgets 的时候再次输入，不至于读到 scanf 剩下的 '\n' 就直接输出了。但这个输出中有很多疑点，读者可以先想想，下一篇里我会来分析。

> In Wuhan 337 Prison
