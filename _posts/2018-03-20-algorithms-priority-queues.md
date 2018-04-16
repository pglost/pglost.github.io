---
layout: post
title:  "优先级队列"
date:   2018-03-20 16:31:54
tag:
- 普林斯顿算法公开课
- Cousera
---

* content
{:toc}

## 1. API和初级实现 ##

### 1.1 API ###

~~~ java
public class MaxPQ<Key extends Comparable<Key>
----------------------------------------------
	MaxPQ()
	void			insert(Key v)
	Key				delMax()
	boolean		isEmpty()
	Key				max()
	int 			size()
~~~

### 1.2 应用举例 ###

输入N个项，找到最大的M个项。

限制条件：没有足够空间，去存储N个项。

应用场景：

- 交易欺诈监测：大额交易
- 文件系统维护：找到最大的文件或者文件夹

~~~ java
MinPQ<Transaction> pq = new MinPQ<Transaction>();

while(StdIn.hasNextLine()) {
	String line = StdIn.readLine();
	Transaction item = new Transaction(line);
	pq.insert(line);
	if (pq.size() > M)
		pq.delMin();
}
~~~

各种算法在解决该问题的复杂度比较：

|  实现				| 时间		|  空间 	| 
| :----: 			| :----: 	| :----: 	|
| 排序  			|N log N	|N			| 
| 初级优先级队列  	|N M		|M			| 
| 二叉堆  			|N log M	|M			| 
| 理论最优  		|N			|M			| 

### 1.3 初级实现：无序数组###

~~~ java
public class UnorderedMaxPQ<Key extends Comparable<Key>> {
	private Key[] pq;
	private int N;
	public UnorderedMaxPQ(int capacity) {
		pq = (Key[]) new Comparable[capacity];
	}
	public boolean isEmpty() {
		return N == 0;
	}
	public void insert(Key x) {
		pq[N++] = x;
	}
	public Key delMax() {
		int max = 0;
		for(int i = 1; i < N; i++) 
			if(less(pq[max], pq[i])) max = i;
		exch(max, N-1);
		return pq[--N];
	}
}
 
~~~

### 1.4 初级实现的性能分析###

目标：所有的操作都是高效的，但是初级实现无法达到此目标

|  实现				| 插入		|  删除最大 	| 最大     |
| :----: 			| :----: 	| 	:----: 	| :----:  |
| 无序数组			|1			|	N			| N       |
| 有序数组			|N			|1				| 1       |
| 目标 				|log N		|log N			| log N   |

## 2. 二叉堆 ###

### 2.1 完全二叉树 ###

二叉树：每个节点最多有两个子树的树结构。

完全树：除了最底层，完全平衡。

性质：具有N个节点完全树的高度为[lgN].

证明：只有当N是2的幂时，高度才会增加。

### 2.2 二叉堆表示方法 ###

定义：按照堆序排列的完全二叉树

有序堆：二叉树的每个节点都大于等于它的子节点

数组表示方法：索引从1开始，按照层序存储。

最大值为二叉树的根，a[1]。

可以使用索引去访问。

- 第k个元素的父亲为k/2
- 第k个元素的孩子为2k，2k+1

### 2.3 上浮 ###
子节点的值比父节点大。

- 交换子节点和父节点的值
- 重复直到堆有序

~~~ java
	private void swim(int k) {
		while(k>1 && less(2/k, k)) {
			exch(k, k/2);
			k = k/2;
		}
	}
	
~~~

### 2.4 插入 ###

- 插入节点到堆的末尾，然后上浮该节点
- 最多log N + 1次比较

~~~ java
	public void insert(Key x) {
		pq[++N] = x;
		swim(N); 
	}
	
~~~

### 2.5 下沉 ###

父节点比子节点小。

- 交换父节点与较大子节点的值
- 重复这一操作，直到堆恢复有序

~~~ java
private void sink(int k){
	while(2*k <= N) {
		int j = 2*k;
		if(j < N && less(j, j+1)) j++;
		if(!less(k, j)) break;
		exch(k, j);
		k = j;
	}
}
~~~

### 2.6 删除最大 ###

交换根节点与最后一个节点，然后下沉。

花费：最多2log N次比较

~~~ java
public Key delMax(){
	Key max = pq[1];
	exch(1,N);
	sink(1);
	pq[N+1] = null;
	return max;
}

~~~

### 2.7 Java实现###
~~~ java
public class MaxPQ<Key extends Comparable<Key>> {
	private Key[] pq;
	private int N;
	
	//for simplicity fix capacity
	public MaxPQ(int capacity)	{
		pq = (Key[]) new Comparable<Key>[capacity+1];
	}
	
	
	//PQ ops
	public boolean isEmpty() {
		return N == 0;
	}	
	public void insert(Key key) 
	public Key delMax()
	
	//heap helper function
	private void swim(int k)	
	private void sink(int k) 
	
	//array helper function
	private boolean less(int i, int j)
	private void exch(int i, int j)
	
}
~~~

### 2.8 优先级队列各种实现的复杂度分析 ###

|  实现				| 插入		|  删除最大 	| 最大     |
| :----: 			| :----: 	| 	:----: 	| :----:  |
| 无序数组			|1			|	N			| N       |
| 有序数组			|N			|1				| 1       |
| 二叉堆 			|log N		|log N			| 1       |
| 多叉堆 			|$$log_{d}N$$	| $$dlog_{d}N$$| 1   |
| Fibonacci 		|1|log N	| log N (均摊)  |1|
| impossible 		|1	|1	| 1  |

### 2.9 二叉堆实现的一些细节 ###

- 节点的值具有不可变性。
- 下溢和上溢
	- 上溢：如果对空的优先级队列做删除操作，抛出异常
	- 下溢：放弃固定容量模式的构造函数，改用resizing模式
- 面向最小值的优先级队列
	- 使用greater()函数替代less();
	- 实现greater()函数
- 其他操作
	- 移除任意项？？？
	- 改变某一项的优先级值？？？

### 2.10 不可变性的Java实现 ###

数据类型：一系列值和操作的集合

不可变的数据类型：一旦创建，不能改变数据的值

~~~ java
public	final class Vector {
	private final int N;
	private final double[] data;
	
	public Vector(double[] data) {
		this.N = data.length;
		this.data = new double[N];
		for(int i = 0; i < N; i++)
			this.data[i] = data[i];
	}
}
~~~

- 不可变的数据类型：String，Integer，Double，Color，Vector，Transaction，Point2D
- 可变的数据类型：StringBuilder，Stack，Counter，Java array

不可变数据类型的好处：

- 简化调试
- 更安全
- 简化并发编程
- 对于优先级队列、符号表里面的值更安全

坏处：

- 对于每一个数据类型的值，必须创建一个新的对象


## 3. 堆排 ##

### 3.1 基本思想 ###

原位排序：

- 对于待排序的长度为N的数组，原位创建一个面向最大值的堆
- 循环移除最大值

### 3.2 Java实现 ###

- 原地构建
- 循环移除

~~~ java
 public class Heap{   public static void sort(Comparable[] a)   {   		int N = a.length;
   		//in-place build heap
		for(int k = N / 2; k >= 1; k--)
			sink(a, k, N);
		//remove max repeatly
		while(N > 1) {
			exch(a, 1, N--);
			sink(a, 1, N);
		}
   }
   
   private static void sink(Comparable[] a, int k, int N)
      
   private static boolean less(Comparable[] a, int i, int j)
   
   private static boolean exch(Comparable[] a, int i, int j)

~~~

### 3.3 复杂度分析 ###
- 复杂度
	- 堆的构造花费最多2N次比较和交换
	- 堆排使用最多2NlogN次比较和交换

- 原位的NlogN复杂度的排序算法	- 归并排序: 线性的额外空间. 
	- 快速排序: 最怀情况下平方复杂度 
	- 堆排序: 符合

- 总结：堆排序是空间和时间最优的。然而还有如下缺点
	- 内部循环比快排要长
	- 缓存利用率比较低
	- 不稳定


|           	| 原位（inplace）   	|  稳定（stable）？  | 最坏  |    平均    |     最好     |   备注    |
| :----:    	| :----: 				| :----:  | :----:| :----: |:----:  |:----:|:----: |
| 选择排序   	| <i class="fa fa-check"></i>  |        | $$N^2/2$$     | $$N^2/2$$ |  $$N^2/2$$      |N次交换|
| 插入排序   	| <i class="fa fa-check"></i> |   <i class="fa fa-check"></i>   | $$N^2/2$$     | $$N^2/4$$  |   N    |用于小数组，以及部分有序数组|
| 希尔排序   	| <i class="fa fa-check"></i>   |    | ?  | ? |   N  |简介的代码，在平方复杂度层级上优化了插入排序|
| 归并排序   	|      						|  <i class="fa fa-check"></i>    | NlogN |   NlogN     | NlogN| 可以保证NlgN的复杂度，并且是稳定的|
| 快排      	| <i class="fa fa-check"></i>    |   |$$N^2/2$$  | 2NlogN|   NlogN    |可以在概率上保证NlgN的复杂度，并且在实际中比归并排序更快|
| 3-way 快排  | <i class="fa fa-check"></i>   |   |$$N^2/2$$     | 2NlogN |   N     | 在重复项上对快排的进一步优化|
| 堆排  | <i class="fa fa-check"></i>   |   |2NlogN     | 2NlogN |   NLogN     | 可以保证NlogN的复杂度，原位，不使用额外空间|
| ？？？  		| <i class="fa fa-check"></i>    |  <i class="fa fa-check"></i>  |NlgN    | NlgN|   N     |理想型|

## 4.事件驱动仿真  ##
略