---
layout: post
title:  "Stacks and Queues"
date:   2018-02-09 16:31:54
tag:
- 普林斯顿算法公开课
- Cousera
---

* content
{:toc}

## 1. 概述 ##

基本数据类型

- 值：对象的集合
- 操作：插入、删除、遍历、检查是否为空

**栈（Stack）** 后入先出（LIFO）

**队列（Queue）** 先入先出（FIFO）

模块式编程，将接口与实现完全分离

## 2. 栈（Stacks）##

### 2.1 API ###

~~~ java
public class StackOfStrings	StackOfStrings()	void push(String item)	String pop()	boolean isEmpty()	int size()
~~~

### 2.2 链表实现 ###
维护一个指针，指向链表第一个节点。从链表头插入、删除

~~~ java
public class linkedStackOfStrings {
	private Node first = null;
	private class Node {
		String item;
		Node next;
	}
	public boolean is Empty() {
		return first == null;
	}
	public void push(String item) {
		Node oldFirst = first
		first = new Node;
		first.item = item;
		first.next = oldFirst;
	}
	public String pop() {
		String item = first.item;
		first = item.next;
		return item;
	}	
}
~~~

性能：

- 时间：在最坏情况下，每个操作常数时间
- 空间：N个元素~40N byte

| Node | 空间花费 |
| :----:  | :----: |
| object overhead | 16 byte| 
| extra overhead | 8 byte（inner class）| 
| item | 8 byte（reference to String）| 
| next | 8 byte（reference to Node）| 
### 2.3 数组实现 ###
使用数组去存储栈中的元素。

- push(): 在数组的尾部增加一个元素（S[N]）
- pop(): 从数组的尾部删除一个元素(S[N-1])

~~~ java
	public class FixedCapacityStackOfStrings {
		private String[] s;
		private int N = 0;
		
		public FixedCapacityStackOfStrings(int capacity) {
			s = new String[capacity];
		}
		public boolean isEmpty(){
			return N == 0;
		}
		public void push(String item) {
			s[N++] = item;
		}
		public String pop() {
			return s[--N];
		}
	}
~~~

该实现有以下问题

- 上溢（Overflow）、下溢（Underflow）。
- 允许插入空指针
- 指针游离

纸质游离可以通过以下代码解决

~~~ java
	public String pop() {
		String item = s[--N];
		s[N] = null;
		return item;
	}
~~~

### 2.3 可变长度（resizeing）数组实现 ###

改变数组长度的开销很大，因为需要重新开辟一片内存，并将原数组的内容拷贝过去。

如果每次入栈都改变数组的长度的话，N个元素入栈需要花费的时间~1+2+...+N=$$N^2$$$;

遵循的原则：resizing操作尽可能少

### 2.3.1 入栈 ###

重复倍增（repeated doubling）：如果数组满容量，创建一个2倍容量的新数组，并将原数组拷贝过去

入栈N个元素的开销：N+(2+4+8+...+N) ~3N

### 2.3.2 出栈 ###

栈内元素个数达到数组长度的1/4时，将数组长度减半

在可变长度的数组中，元素的数量会一直占据组长度的25%-100%

~~~ java
	public ResizingArrayStackOfString{
		rivate String[] s;
		private int N = 0;
		
		public ResizingArrayStackOfString(){
			s = new String[1];
		}
		
		public void push(String item) {
			if (N == s.length) resize(2*s.length);
			s[N++] = item;
		}
		
		public String pop() {
			String item = s[--N];
			s[N] = null;
			if (N > 0 && N == s.length/4) resize(s.length/2);
			return item;
		}
		
		private void resize(int capacity) {
			String[] copy = new String[capacity];
			for (int i = 0; i < N; i++)
				copy[i] = s[i];
			s = copy;
		}
		
	}
~~~

### 2.3.3 性能分析 ###

均摊分析：在最坏的情况下，每个操作的平均时间复杂度

结论：从一个空栈开始，任意M次的出入栈操作的时间复杂度正比于M

| 操作 | 最好 | 最差 | 均摊|
| :----:  | :----:   | :----:   | :----:   |
| constructor |   1 | 1 | 1|
| push |   1 | N | 1|
| pop |  1 | N | 1|
| size |  1 | 1 | 1|





