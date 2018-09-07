---
layout: post
title:  "归并排序"
date:   2018-03-05 17:31:54
tag:
- 普林斯顿算法公开课
---

* content
{:toc}

## 1. 归并排序

### 1.1 基本思想

- 把数组均分为两部分
- 递归排序两部分
- 合并两部分


### 1.2 合并Java实现

~~~ java
private static void merge (Comparable[] a, Comparable[] aux, int lo, int mid, int hi) {
	assert isSorted(a, lo, mid);
	assert isSorted(a, mid+1, hi);
	
	for(int k = lo; k <= hi; k++) {
		aux[k] = a[k];
	}
	
	int i = lo, j = mid + 1;
	for(int k = lo; k <= hi; k++) {
		if (i > mid) a[k] = aux[j++];
		else if (j > hi) a[k] = aux[i++];
		else if(less(aux[j], aux[i]) a[k] = aux[j++];
		else a[k] = aux[i++];
	}
	
	assert isSorted(a, lo, hi);
}
~~~

`if(less(aux[j], aux[i]) a[k] = aux[j++]`保证了排序算法的稳定性。

**断言**：检验程序假设的语法声明。

- 可以帮助发现逻辑上的bug
- 可以使代码更具可读性

**Java断言**

- 当断言的判断条件为false时，会抛出异常；
- 可以在运行时选择是否生效断言。

`java -ea MyProgram`

`java -da MyProgram`


### 1.3 归并排序Java实现

~~~ java
	public class Merge {
	
		private static void merge (Comparable[] a, Comparable[] aux, int lo, int mid, int hi){
			/* as before */	
		}
		
		private static void sort (Comparable[] a, Comparable[] aux, int lo, int hi) {
			if(hi <= lo) return;
			int mid = lo + (hi - lo) / 2;
			sort(a, aux, lo, mid);
			sort(a, aux, mid+1, hi);
			merge(a, aux, lo, mid, hi);
		}
		
		public static void sort (Comparable[] a) {
			aux = new Comparable[a.length];
			sort(a, aux, 0, a.lenth - 1);
		}
		
	}
~~~

- aux作为辅助数组，只new一次


### 1.4 算法性能分析

#### 1.4.1 时间复杂度

命题：对于长度为N的数组，归并排序使用最多NlgN次比较和6NlgN次数组访问，可以完成排序。

证明思路：

当N>1时

C(N) $$\leq$$ C(N/2) + C(N/2) + N

A(N) $$\leq$$ A(N/2) + A(N/2) + 6N

当N=1时
C(1) = 0, A(1) = 0

具体证明略去。

#### 1.4.1 空间复杂度

命题：对于长度为N的数组，归并排序使用了正比于N的额外内存

证明: 对于最后一次合并，aux[]的长度必须是N

**原位（in-place）排序算法**：使用小于等于c log N的额外内存

### 1.5 改进

对于长度较小的子数组，使用插入排序。

- 归并排序对于小的子数组来说开销过大
- 对于长度为7的子数组，切换为插入排序

如果已经有序，停止算法

避免额外的拷贝到辅助数组的开销，省时但不省空间。

## 2. 自下而上（bottom-up）的归并算法
### 2.1 基本思想
- 遍历整个数组，合并长度为1的子数组
- 对长度为2，4，8，16...的子数组重复合并操作

### 2.2 Java实现
~~~ java
public class MergeBU {
	private static void merge(...) {
		/* as before */ 
	}
	public static void sort(Comparable a[]) {
		int N = a.length;
		Comparable[] aux = new Comparable[N];
		for (int sz = 1; sz < N; sz = sz + sz) {
			for (int lo = 0; lo < N - sz; lo = lo + sz + sz) {
				merge(a, aux, lo, lo+sz-1, Math.min(lo+sz+sz-1, N-1));
			}
		}
	}
}
~~~

## 3. 排序的复杂度分析

### 3.1 基本概念

- 计算复杂度：研究算法解决问题效率的框架
- 计算模型：允许的操作
- 成本模型：操作次数的总和
- 上边界：已知算法的计算成本
- 下边界：已被证明的解决某一问题所需花费计算成本的下限
- 最优算法：尽可能花费最小计算成本的算法，上边界~下边界

**排序**

- 计算模型：决策树（只有通过比较获取信息）
- 成本模型：比较的次数
- 上边界：~NlgN，归并排序
- 下边界：？
- 最优算法：？

### 3.2 基于比较的排序算法下边界
命题：在最坏的情况下，任何基于比较的排序算法最少需要花费lg(N!) ~ NlgN次的比较。

证明：

- 假设数组由N个不同的项组成
- 最坏情况下，决策树的高度等于比较的次数
- 高度为h的二叉树最多有$$2^h$$个叶子节点
- 数组有N!个排序，代表着决策树最少有N!个叶子节点

$$N!\leq $$叶子节点数量$$\leq 2^h$$

$$h \geq lg(N!) $$ ~ NlgN
 
 **排序**

- 计算模型：决策树（只有通过比较获取信息）
- 成本模型：比较的次数
- 上边界：~NlgN，归并排序
- 下边界：~NlgN
- 最优算法：归并排序

### 3.3 复杂度的进一步说明
 
归并排序是时间上的最优算法，但是并非空间最优。

- 可以设计一个算法，比较次数~1/2 NlgN
- 可以设计一个算法，即是时间最优，也是空间最优。 
 
如果已知如下情况，排序算法的下边界会随之改变

- 数组的初始顺序：例如部分有序数组，根据数组的初始顺序，可能不需要NlgN次比较。对于有序数组，插排只需要N-1次比较。
- 数组值的分布：例如有重复值的数组，根据重复值的分布，可能不需要NlgnN次比较。3-way quicksort。
- 数组值的属性：例如数字数组和字符串数组，可以使用基数排序radix sort


## 4. 比较器（comparators）

- Comparable接口：定义的数据类型的自然顺序
- Comparator接口：定义了数据类型的其他可选的顺序

~~~ java
public interface Comparator<Key> {
	int compare(Key v, Key w)
}
~~~
需要保证数组是全序的。比较器解耦了数据类型的定义与数据类型比较关系之间的定义。

## 5. 稳定性

稳定的排序保持了数组中相同值的相对顺序。

- 插入排序是稳定的。只做相邻位置的交换，并且相等不做交换。
- 选择排序是不稳定的。长距离的交换。
- 希尔排序也是不稳定的。长距离交换。
- 归并排序是稳定的。因为归并操作是稳定的，当两个子数组的元素相等时，选择左边的数组进行归并。
