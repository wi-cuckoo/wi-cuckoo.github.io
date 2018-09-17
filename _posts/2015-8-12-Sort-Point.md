---
title: 插排二维坐标点
---

这是练习编程早期做的一个小 quiz 了，觉得对学习插排挺有帮助的，所以还是厚着脸皮写了这篇小文。

### 经典插排

	#define len 5
	int a[len] = {1, 45, 2, 0, 23};

	int main (void)
	{
		for (int j=0; j= 0 && a[i] > key)
			{
				a[i+1] = a[i];
				i--;
			}
			a[i+1] = key;
		}

		return 0;
	}

插排是最基础的排序算法了，思路也好理解。作为一个刚接触算法的小白来说，得把基础的搞好，于是我扩展了一下，将一堆二维坐标
的点按照横轴（x）的方向排序，遇到横坐标相同的，以纵坐标方向排。似乎也没什么难的，参照插排的代码就好了？把插排的代码嵌
套一下就好了，但是我设计下来的代码居然超过了四次缩进，显然不够简洁，这里暂且贴出来，望指教。

### 坐标点排序代码

	struct point
	{
		int x;
		int y;
	};

	int main (void)
	{
		struct	point	input[8] = \{\{2,3\}, \{3,4\}, \{2,0\}, \{3,2\},
					     \{4,9\}, \{1,0\}, \{4,2\}, \{0,0\}\};
		int		j = 0;

		for (j=1; j<8; j++)
		{
			struct	point key = input[j];
			int		temp = j-1;

			while (temp >= 0 && input[temp].x >= key.x)
			{
				if (input[temp].x == key.x)
				{
					while (temp >= 0 && input[temp].y >= key.y)
					{
						if (input[temp].y == key.y) break;

						input[temp+1] = input[temp];
						temp--;
					}
					input[temp+1] = key;
					break;
				}
				input[temp+1] = input[temp];
				temp--;
			}
			input[temp+1] = key;
		}
		return 0;
	}
		
（请忽略那几个反斜杠，GitHub生成Page的需要）三个循环的外层两个完成按照 X 轴方向排序，而最里层的一个 while 完成对 X 值相同时，对 Y 轴的排序。整个一轮的过程中，
temp 的值始终是 key 元素将插入位置的前一个数组元素的索引。最后的排序结果没问题，唯一就是感觉应该能简化一下设计就
好了。

> In Wuhan 337 Prison
