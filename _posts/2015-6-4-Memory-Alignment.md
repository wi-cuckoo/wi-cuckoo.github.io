---
title: Issues of Alignment
---

> All machine architectures have *data alignment* requirements, or called *alignment of memory*. As we all know, the data stored in memory is in byte-sized. But our processors, do not read and write from memory in byte-sized chunks. Instead, 
processors access memory with a specific granularity, such as 2, 4, 8, or 16 bytes. SO, the binary data written with one 
application may not be readable by a different application, or even by the same application on a different machine.

### Why we should concern about alignment

Normally, the compiler naturally aligns all data. So for most of programmers, they needn't concern about it. while for 
system programmers, dealing with structures, performing memory managment by hand, saving binary data to disk, and comm
unicating over a network may bring alignment issues to the forefront. Therefore, we should be well versed in issues of 
alignment.

### Have a experiment

System Environment: CentOS 7, Kernel 3.10.0-229.11.1.el7.x86_64

The code:
	
	#include <stdio.h>
	//#pragma pack(4)      //change the factor of alignment, may be a num of power-of-2
	
	int main(void)
	{
		struct me{
			int a;
			char b[5];
			double c;
			int d;
			char *p;
		};
	
		struct me t;
	
		/* make sure the size of used type of data. */
		printf ("int:%d\nchar:%d\ndouble:%d\npoint:%d\n",
			   	sizeof (int), sizeof (char),
				sizeof (double), sizeof (char *));
	
		printf ("the head address: %p\n", &t);
		printf ("a:%p\nb:%p\nc:%p\np:%p\n",  &(t.a), &(t.b), &(t.c), &(t.p));
	
		return 0;
	}

First, we commented `#pragma pack(4)` statement. And the output may be the following (the address will be reallocated by system in the next).

	int:4
	char:1
	double:8
	point:8
	the head address: 0x7ffe00291870
	a:0x7ffe00291870
	b:0x7ffe00291874
	c:0x7ffe00291880
	p:0x7ffe00291890
	
OK, let's do some analysis. Before we start, we should know memory that is aligned along an 8-byte boundary on 32-bit
systems and along a 16-byte boundary on 64-bit systems. So, here, it's 16-byte. In the output, we can see the address of member `a` is same as that of `struct t`. That is all the members within `struct t` are stored continuously from the head address of `t`. Then, the `a` takes 4 bytes, `b` takes 5 byte. So when stored `b`, the next address 0f memory should be **0x7ffe00291879**, if you are not sure, can display `&b[4]+1`. Now, the compiler won't place `c` from this address, because it will  exceed the 16-byte boundary. Therefore, the output shows that the address of `c` start at 
**0x7ffe00291880**, which differs the **a:0x7ffe00291870** 16 bytes. Likewise, the distance between address of point `p` and that of `c` is also 16 byte. If you search picures for "data alignment", you'll find lots of visual description.

Like:

![](/img/alignment.jpg)

### #pragma pack(n)

As we mentioned above, this time we add a statement `#pragma pack(4)`. This is to change the default boundary to 4-byte boundary. C is a pretty flexible language, so that it supports we can control the boundary.

So, here the question is left to be thought by our reader. If we make the change, how to explain its output ? The following is an example output of an execution.

	the head address: 0x7ffe25528ec0
	a:0x7ffe25528ec0
	b:0x7ffe25528ec4
	c:0x7ffe25528ecc
	p:0x7ffe25528ed8

### Conclusion

Rules pertaining to alignment derive from hardware and thus differ from system to system. Some machine architectures have very stringent requirements on the alignment of data. Others are more lenient. When write portable code, programmers must be careful to avoid violating alignment requirements.

> In Wuhan 337 Prison
