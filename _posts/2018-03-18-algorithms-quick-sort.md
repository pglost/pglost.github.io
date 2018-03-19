---
layout: post
title:  "快速排序"
date:   2018-03-18 19:31:54
tag:
- 普林斯顿算法公开课
- Cousera
---

* content
{:toc}

## 1. 快速排序 ##

### 1.1 基本思想 ###

- 对数组做随机排列
- 分割数组，找到某一分割点j，左边的不比a[j]大，右边的不比a[j]小
- 对于分割的两部分，分别递归排序

### 1.2 分割算法 ###

重复一下操作，直到i，j指针交汇

- 只要a[i]<a[lo]，指针i从左到右一直扫描
- 只要a[j]>a[lo], 指针j从右到左一直扫码
- 交换a[i],a[j]

当指针交汇，

-交换a[lo],a[j]


### 1.3 分割算法的Java实现 ###
~~~ java
	private static int partition(Comparable[] a, int lo, int hi) {
		int i = lo, j = hi + 1;
		while(true) {
			while(less(a[++i], a[lo])
				if(i == hi) break;
			while(less(a[lo],a[--j]))
				if(j == lo) break;
			if(i>=j) break;
			exch(a, i, j)	
		}
		exch(a, lo, j);
		return j;
	}
~~~

### 1.4 快排算法的Java实现 ###

~~~ java
	public class Quick {
	
		private static int partition (Comparable[] a, int lo, int hi){
			/* as before */	
		}
		
		public static void sort(Comparable[] a) {
			StdRandom.shuffle(a);
			sort(a, 0, a.length - 1);
		}
		
		private static void sort (Comparable[] a, int lo, int hi) {
			if(hi <= lo) return;
			int j = partition(a, lo, hi);
			sort(a, lo, j-1);
			sort(a, j+1, hi);
		}
		
	}
~~~

### 1.5 算法性能分析 ###

最好情况：比较次数 ~ NlgN

**最坏情况**： 比较次数 ~ 1/2 $$N^2$$

- N + (N-1) + ... +1 ~ 1/2 $$N^2$$

**平均情况**：

命题：快排N个值不重复数组所需要花费的比较次数$$C_N$$~2NlnN，交换的次数~1/3NlnN

证明：略

-  比较的次数 ~ 1.39 N lg N，比归并排序多39%
-  但是由于更少的数据移动，实际上比归并排序更快

**随机排序**：
- 避免出现最坏情况在概率上有了保证

**需要注意的情况**：
许多教科书上的算法实现，在一些特殊情况，是平方级的复杂度

- 是有序的或者是反向有序的
- 有很多重复项，即使已经做随机化处理


### 1.6 快排的性质 ###

- 原地排序算法：分割只使用了常数的额外空间；递归的深度使用了对数级的额外空间
- 快排是不稳定的

### 1.7 算法工程上的改进 ###

- 对于小规模的子数组使用插入排序。一般数组的边际长度为10左右。
- 对于首元素（pivot item）的选择，不使用lo，而是使用中位数取样（median sample），比如medianOf3算法。

## 2. 选择算法 ##

问题：在长度为N的数组中，找到第k小的项。

上边界：先排序，~NlgN

下边界：~N，至少需要扫描一遍数组


### 2.1 基本思想 ###

基于quick-sort算法的思想，得出quick-select算法
**分割**
- 对数组做随机排列
- 分割数组，找到某一分割点j，使得左边的不比a[j]大，右边的不比a[j]小
**缩小问题的规模**
基于分割点j的值，选出某一子数组；当j=k时，返回a[k];

### 2.2 Java实现###
~~~ java
public static Comparable select (Comparable[] a, int k) {
	StdRandom.shuffle(a);
	int lo = 0, hi = a.length - 1;
	while(hi > lo) {
		int j = partition(a, lo, hi);
		if(j < k) lo = j + 1;
		else if(j > k) hi = j -1;
		else return a[k];
	}
	return a[k];   
}
~~~

### 2.3 复杂度分析###
最坏情况：~1/2 $$N^2$$次比较
平均情况：线性时间，数组随机打乱可以提供概率上的保证

## 3. 重复项的处理 ##

### 3.1 问题概述 ###
特征：大规模的数据量，有限的取值

- 根据年龄排序人口信息
- 从邮件箱里面去重
- 根据学校排序工作申请

快排算法在处理有重复项的输入时，复杂度变为$$N^2$$，除非分割算法停止在相同项上

### 3.2 三路分割（3-way partitioning） ###

基本思想：把数组分为三部分

- 在lt和gt之间的项与分割项v相等
- lt左边没有比v大的项
- gt右边没有比v小的项

具体实现：v为分割项a[lo]

从左到右扫描数组

- 如果a[i] < v，交换a[i]和a[lt]，i++,lt++
- 如果a[i]>v, 交换a[i]和a[gt]，gt--
- 如果a[i]== v，i++

### 3.3 Java实现 ###

**Java实现**

~~~ java
	private static void sort(Comparable[] a, int lo, int hi) {
		if(hi <= lo) return;
		int lt = lo, gt = hi;
		Comparable v = a[lo];
		while (i < gt) {
			int cmp = a[i].compare(v);
			if(cmp < 0) exch(a, lt++, i++);
			else if(cmp > 0) exch(a, gt--, i);
			else i++;
		}
		sort(a, lo, lt - 1);
		sort(a, gt + 1, hi);		
	}
~~~

### 3.4 性能分析 ###

排序算法的下边界（考虑到重复元素）：假设有n个不同的值，第i个值在数组出现$$x_i$$次，任何基于比较的排序算法在最坏情况下，至少会使用比较的次数为：

$$lg \left (  \frac{N!}{x_1 !+...+x_n !} \right ) \sim -\sum_{1}^{n}x_i lg \frac{x_i}{N}$$

注：当N个元素完全不相同时，下边界为NlgN；当只有常数个不同的元素时，下边界为线性复杂度

总结：三路分割的快速排序算法，在一些应用场景下，可以把NlgN的复杂度降低为线性复杂度。

## 4. 工程系统中的排序 ##

### 4.1 应用 ###

排序算法的应用非常广泛。对于某些问题，可以直接用排序算法去解决；对于另一些问题，先使用排序算法，可以使问题简化；

### 4.2 Java中的排序函数 ###

`Array.sort()`:

- 对于不同的原始数据类型，有不同的算法
- 对于实现了Comparable接口的数据类型，有一个处理方法
- 对于使用了Comparator参数的情况，有一个处理方法
- 对于原始数据类型，使用快排；对于对象，使用归并排序

### 4.3 快排工程上的实现方法 ###

- 对于小的子数组，切换为插入排序. 
- 分割方案: Bentley-McIlroy 3-way partitioning（三路分割）.- 分割的项的选择.
	- 小数组: 中间项
	- 中等数组: median of 3
	-  大数组: Tukey's ninther（9分法）

### 4.4 排序总结 ###

|           	| 原位（inplace）   	|  稳定（stable）？  | 最坏  | 平均 | 最好 | 备注 |
| :----:    	| :----: 				| :----:  | :----:| :----: |:----:  |:----:|:----: |
| 选择排序   	| <i class="fa fa-check"></i>  |        | $$N^2/2$$     | $$N^2/2$$ |  $$N^2/2$$      |N次交换|
| 插入排序   	| <i class="fa fa-check"></i> |   <i class="fa fa-check"></i>   | $$N^2/2$$     | $$N^2/4$$  |   N    |用于小数组，以及部分有序数组|
| 希尔排序   	| <i class="fa fa-check"></i>   |    | ?  | ? |   N  |简介的代码，在平方复杂度层级上优化了插入排序|
| 归并排序   	|      						|  <i class="fa fa-check"></i>    | NlgN |   NlgN     | NlgN| 可以保证NlgN的复杂度，并且是稳定的|
| 快排      	| <i class="fa fa-check"></i>    |   |$$N^2/2$$  | 2NlgN|   NlgN    |可以在概率上保证NlgN的复杂度，并且在实际中比归并排序更快|
| 3-way 快排  | <i class="fa fa-check"></i>   |   |$$N^2/2$$     | 2NlgN |   N     | 在重复项上对快排的进一步优化|
| ？？？  		| <i class="fa fa-check"></i>    |  <i class="fa fa-check"></i>  |NlgN    | NlgN|   N     |理想型|