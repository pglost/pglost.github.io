---
layout: post
title:  "基本排序方法"
date:   2018-03-02 16:31:54
tag:
- 普林斯顿算法公开课
- Cousera
---

* content
{:toc}

## 1. 概述 ##

### 1.1 回调 ###

- **目标**：可以排序任意数据类型
- **问题**：如何针对不同的数据类型，设计一个通用的sort()方法

回调：可执行代码的引用

### 1.2 全序关系 ###
元素在排序中能够按照特定顺序排列

- 非对称性：如果$$ v \leq w$$ 并且$$w \leq v$$，那么v=w
- 传递性：如果$$ v \leq w$$ 并且 $$w \leq x$$，那么 $$v \leq x$$
- 完全性：要么$$ v \leq w$$，要么 $$$w \leq v$，要么两者相等

### 1.3 Comparable API###
实现compareTo方法，`v.compareTo(w)`

接口规则：

- v < w, -1
- v == w, 0
- v > w, 1

### 1.4 排序的两个基本操作###
比较：

~~~ java
private static boolean less(Comparable v, Comparable w) 
{  
	return v.compareTo(w) < 0;  
}
~~~

交换：

~~~ java
private static void exch(Comparable[] a, int i, int j){   Comparable swap = a[i];   a[i] = a[j];   a[j] = swap;}
~~~

### 1.5 测试是否有序 ###

~~~ java
private static boolean isSorted(Comparable[] a){   for (int i = 1; i < a.length; i++)      if (less(a[i], a[i-1])) return false;   return true;}
~~~

Q：通过isSorted()测试，是否能确保数组一定有序？
A：如果排序算法能保证只通过less、exch两个排序的基本方案去操作数组，那么通过isSorted()测试的数组可以保证一定是有序的。

## 2. 选择排序 ##

### 2.1 基本思想 ###
- 在i此迭代中，找到无序项中最小项的索引min
- 交换a[i]和a[min]

### 2.2 算法正确性分析 ###
对于选择排序算法，有一个指针（数组索引i），从左到右对数组进行扫描

算法不变性：

- 指针左边的项已经是升序，不会再改变
- 指针右边的项都比左边的大

维持算法的不变性

- 将指针右移（此时，破坏了不变性）

~~~ java
i++
~~~

- 找到最小项的索引

~~~ java
int min = i;for (int j = i+1; j < N; j++)	if (less(a[j], a[min]))   		min = j;
~~~

- 交换

~~~ java
exch(a, i, min);
~~~

### 2.3 Java实现 ###

~~~ java
public class Selection{	public static void sort(Comparable[] a) 
	{
		int N = a.length;
		for(int i = 0; i < N; i++)
		{
			int min = i;
			for(int j = i+1; j < N; j++)
			{
				if(less(a[j], a[min]))
					min = j;
		     		exch(a, i, min);
			}
		}
	}   private static boolean less(Comparable v, Comparable w)   {  /* as before */  }   private static void exch(Comparable[] a, int i, int j)   {  /* as before */  }}
~~~

### 2.4 性能 ###
~$$N^2$$compare操作，N次exchange操作

算法运行时间对输入不敏感。即使是已排序数组，也需要$$N^2$$次的比较操作。

数据移动次数最少。线性次数的交换操作。

## 3. 插入排序 ##

### 3.1基本思想 ###

在第i次迭代中 将a[i]与其左侧比它更大的项进行交换，直到找到合适的位置

### 3.2 算法正确性分析 ###

对于插入排序算法，有一个指针（数组索引i），从左到右对数组进行扫描


- 指针左边的项已经是升序，不会再改变
- 指针右边的项都是未知的

保持算法的不变性

- 将指针右移（此时，算法不变性破坏）

~~~ java
i++;
~~~

- 从右到左移动a[i]，与比a[i]大的每个项交换位置

~~~ java
for (int j = i; j > 0; j--)	if (less(a[j], a[j-1]))   		exch(a, j, j-1);
   	else break;	
~~~

### 3.3 Java实现 ###

~~~ java
public class Insertion{   public static void sort(Comparable[] a)   {      int N = a.length;      for (int i = 0; i < N; i++)         for (int j = i; j > 0; j--)            if (less(a[j], a[j-1]))               exch(a, j, j-1);            else break;   }   private static boolean less(Comparable v, Comparable w)   {  /* as before */  }   private static void exch(Comparable[] a, int i, int j)   {  /* as before */  }}
~~~

### 3.3 性能分析 ###

- 最好情况：数组为升序，使用插入排序算法会有N-1次比较操作，0次交换操作。**优于选择排序**。

- 最坏情况：数组为降序，并且没有重复项，使用插入排序算法会有~$$\frac{N^2}{2}$$
$次比较操作，~$$\frac{N^2}{2}$$次交换操作。**不如选择排序**。

### 3.3 部分有序数组###

- 定义：逆序对
- 定义：部分有序数组。逆序对数$$\leq$$cN
- 结论：对于部分有序数组，插入排序算法为线性时间复杂度
- 证明：交换次数=逆序对数；比较次数=交换次数+N-1


## 4. 希尔排序 ##
### 4.1 基本思想 ###
基于插入排序的改进，插排每次比较之把项目向前移动1步。在对数组扫描中的每次迭代，通过h-sort算法，使得当前项向左移动大于1步。

### 4.2 h-sort ###
步长为h的插排

为什么选择插入排序？

- 大步长（h很大），可以使得h-sort处理的子问题规模更小（子数组的排序）
- 小步长（h小），当前数组已经基本有序，插入排序对处理部分有序数组性能很好

### 4.3 h序列的选择 ###

有多种选择，还未找到最好的。最常用的是`3x+1`

### 4.4 Java实现 ###
~~~ java
public class Shell {   public static void sort(Comparable[] a) {		int N = a.length;
		int h = 1;
		while(h < N/3) h = 3*h + 1;
		while (h >= 1) {
			for (int i = h; i < N; i++) {
				for(int j = i; j >=h; j -= h) {
					if(less(a[j], a[j-h]))
						exch(a, j, j-h);
					else break;
				}
			}
		}
	}    private static boolean less(Comparable v, Comparable w) {
    /* as before */
   }   private static void exch(Comparable[] a, int i, int j) {
    /* as before */ 
   }}~~~ 

### 4.5 性能分析 ###

还未得出准确的模型。相比较于插入排序，性能有大幅度提升。

### 4.6 希尔排序的启示 ###

对经典算法的简单改进可以导致性能的显著提升。一些好的算法还有待于我们去发现。

在工程中有很好的应用：

- 对于中小型数组运行速度很快
- 代码简单，可用于嵌入式系统
- 硬件中排序算法的原型

## 5. 洗牌 ##
### 5.1 问题概述 ###
重新排列一个数组，使其均匀随机分布。

### 5.2 洗牌排序（Shuffle sort）

- 对于数组中的每一项生成一个随机实数。
- 重新排序（根据随机实数）

### 5.3 Knuth shuffle

- 在第i次迭代中，在0-i间找到符合随机分布的整数r

- 交换a[i]和a[r]

java实现

~~~ java
public class StdRandom {   ...   public static void shuffle(Object[] a) {      int N = a.length;      for (int i = 0; i < N; i++) {         int r = StdRandom.uniform(i + 1);         exch(a, i, r);      }	} 
}	
~~~

## 6.凸包 ##
