---
title: hiho一下：Beautiful String
---

> 记不清这是 hiho一下第几周的题目了，题目不难，不过对于练习编程，训练思维很有帮助。况且当时笔者处于学习算法的早期，
所以也希望刚接触算法的同学能多去练习一下。

### 题目介绍

>时间限制:10000ms<br>
>单点时限:1000ms<br>
>内存限制:256MB<br>
>描述<br>
>We say a string is beautiful if it has the equal amount of 3 or more continuous letters (in increasing order.)<br>
>Here are some example of valid beautiful strings: "abc", "cde", "aabbcc", "aaabbbccc".<br>
>Here are some example of invalid beautiful strings: "abd", "cba", "aabbc", "zab".<br>
>Given a string of alphabets containing only lowercase alphabets (a-z), output "YES" if the string contains a 
beautiful sub-string, otherwise output "NO".<br>
> 输入<br>
>The first line contains an integer number between 1 and 10, indicating how many test cases are followed.<br>
>For each test case: First line is the number of letters in the string; Second line is the string. String length 
is less than 10MB.<br>
> 输出<br>
>For each test case, output a single line "YES"/"NO" to tell if the string contains a beautiful sub-string.

其实网站上面的题目分析写得很好了（还有翻译），要去看的，点击[这里](http://hihocoder.com/discuss/question/2083)。

### AC 源码
     1	#include <stdio.h>
     2	
     3	struct chnum
     4	{
     5		char c;
     6		int	 n;
     7	};
     8	
     9	struct chnum *col (int l, char s[])
    10	{
    11		int i, j = 0;
    12		int len = l;
    13		struct chnum cn[len];
    14	
    15		cn[0].c = s[0];
    16		cn[0].n = 1;
    17	
    18		for (i=1; i<len; i++)
    19		{
    20			char t = s[i];
    21	
    22			if (t != s[i-1])
    23			{
    24				j++;
    25				cn[j].c = s[i];
    26				cn[j].n = 1;
    27			}
    28			else
    29				cn[j].n++;
    30		}
    31	
    32		if (j < len - 1)
    33		{
    34			cn[++j].c = ' ';
    35		}
    36	
    37		return cn;
    38	}
    39	
    40	int check (struct chnum *cn, int len)
    41	{
    42		for (int i=1; i<=len-2; i++)
    43		{
    44			if ((cn->c + 1 == (cn+1)->c
    45				&& (cn+1)->c + 1 == (cn+2)->c)
    46				&& (cn->n >= (cn+1)->n
    47				&& (cn+1)->n <= (cn+2)->n)
    48			   )
    49				return 0;
    50			else if (cn->c == ' ')
    51				break;
    52			else 
    53				cn++;
    54		}
    55	
    56		return 1;
    57	}
    58	
    59	int main ()
    60	{
    61		int num;
    62		
    63		scanf ("%d", &num);
    64	
    65		int ret[num];
    66		for (int i=0; i<num; i++)
    67		{
    68			int len;
    69			scanf ("%d", &len);
    70	
    71			char str[len + 1];
    72			scanf ("%s", str);
    73	
    74			struct chnum *cn = col (len, str);
    75	
    76			if (check (cn, len) == 0)
    77				ret[i] = 1;
    78			else 
    79				ret[i] = 0;
    80		}
    81	
    82		for (int i=0; i< num; i++)
    83		{
    84			if (ret[i])
    85				printf ("YES\n");
    86			else 
    87				printf ("NO\n");
    88		}
    89	
    90		return 0;
    91	}

这种小代码我就不怎么搞注释了，我相信 main 函数的流程还是能被一眼看出来的，而且模块化的编程风格应该更好理解。下面讲下我
第一次提交遇到的 WA 错误。

### Handle 一切你能 Handle 的地方

开源项目有一条哲学:只要眼球多，bug 都好捉。对于个人写程序来说，撑死了也就俩眼球，所以你必须要尽可能地 handle 一切，
比如变量该初始化就初始化，数组长度一定要考虑等等。

第一次提交，上面代码中没有

     32		if (j < len - 1)
     33		{
     34			cn[++j].c = ' ';
     35		}
     
和

     50			else if (cn->c == ' ')
     51				break;
     
这两处的。看了代码就知道 cn 数组只有在字符串中每两个相邻字符都不相同的情况下才会全部被赋值。而其他的情况下，长度根本
没有到 len。那么问题来了，没有被赋值到的 cn 数组元素的值会是什么呢，鬼知道！然后就有可能本来输出该是 NO 的输出了 YES。
所以我决定手动给出结束标志，一个空格，这当然是一种权宜之策。

而且字符串数组声明的时候，长度一定得是串长度加一。我做实验来看，就算 strlen 访问 str[5]="hello"，越界了也还是正常输出
。但注意一下总是好的。

> In Wuhan 337 Prison
