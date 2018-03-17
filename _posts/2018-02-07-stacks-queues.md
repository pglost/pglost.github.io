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
public class StackOfStrings
	StackOfStrings()
	void push(String item)
	String pop()
	boolean isEmpty()
	int size()
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

如果每次入栈都改变数组的长度的话，N个元素入栈需要花费的时间~1+2+...+N=$$N^2$$;

遵循的原则：resizing操作尽可能少

### 2.3.1 入栈 ###

重复倍增（repeated doubling）：如果数组满容量，创建一个2倍容量的新数组，并将原数组拷贝过去

入栈N个元素的开销：N+(2+4+8+...+N) ~3N

### 2.3.2 出栈 ###

栈内元素个数达到数组长度的1/4时，将数组长度减半

在可变长度的数组中，元素的数量会一直占据组长度的25%-100%

~~~ java
	public ResizingArrayStackOfString{
		private String[] s;
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


## 2.3 队列（Queues）##
### 2.3.1 API ###
~~~ java
public class QueueOfStrings
	StackOfStrings()
	void enqueue(String item)
	String dequeue()
	boolean isEmpty()
	int size()
~~~

### 2.3.2 链表实现 ###

维护两个指针，分别指向链表头和链表尾，链表头出队，链表尾入队

~~~ java
	public class LinkedQueueOfStrings {
		private Node first, last;
		private class Node {
			String item;
			Node next;
		}
		public boolean isEmpty(){
			return first == null;
		}
		public void enqueue(String item) {
			Node oldLast = last;
			last = new Node();
			last.item = item;
			last.next = null;
			if (isEmpty()) first = last;
			else 			  oldLast.next = last;
		}
		public String dequeue() {
			String item = first.item;
			first = first.next;
			if(isEmpty) last = null;
			return item;
		}
	}
~~~
## 2.4 泛型（Generics）##

我们已经实现了字符串栈，对于其他的数据类型，我们如何扩展

### 2.4.1 尝试1 ###
对于每一种数据类型都实现一种栈，代码会越来越难以维护。

### 2.4.2 尝试2 ###
实现一个通用的对象栈，但是使用这种数据结构需要强制类型转换。
### 2.4.2 尝试3 泛型 ###

- 客户端不用强制转换
- 编译的时候就能发现类型不匹配的错误

链表实现

~~~ java
	public class Stack<Item> {
		private Node first = null;

		private Node {
			Item item;
			Node next;
		}

		public boolean isEmpty() {
			return first == null;
		}
		public void push(Item item) {
			Node oldFirst = first;
			first = new Node();
			first.item = item;
			first.next = oldFirst;
		}
		public Item pop() {
			Item item = first.item;
			first = first.next;
			return item;
		}
	}
~~~

数组实现

Java无法new泛型数组，因此要用到强制类型转换。



~~~ java
	public class FixedCapacityStack<Item> {
		private Item[] s;
		private int N = 0;

		public FixedCapacityStack(int capacity) {
			s = (Item[])new Object[capacity];
		}
		public boolean isEmpty() {
			return N == 0;
		}
		public void push (Item item) {
			s[N++] = item;
		}

		public Item pop() {
			return s[--N];
		}
	}
~~~

## 2.5 迭代器（Iterators）##

### 2.5.1 迭代 ###
不管栈的具体实现形式，提供栈内元素的遍历的通用方法。
Java提供了`java.lang.Iterable`接口。

### 2.5.2 迭代器 ###

- Iterable接口：有返回迭代器的方法
~~~ java
	public interface Iterable<Item> {
		Iterator<Item> iterator();
	}
~~~

- Iterator接口: 有hasNext()和next()方法
~~~ java
	public interface Iterator<Item> {
		boolean hasNext();
		Item next();
	}
~~~
- 为何让数据结构可迭代(Iterable):可以使用forEach语法，让代码更简洁

~~~ java
	for(String s:stack)
		StdOut.println(s);
~~~

### 2.5.3 栈迭代器 ###

#### 2.5.3 链表实现####
~~~ java
	import java.util.Iterator;

	public class Stack<Item> implements Iterable<Item> {
		...
		public Iterator<Item> iterator(){return new ListIterator();}

		private class ListIterator implements Iterator<Item> {
			private Node current = first;

			public boolean hasNext() { return current != null;}
			public Item next() {
				Item item = current.item;
				current = current.next;
				return item;
			};
		}
	}
~~~
#### 2.5.4 数组实现 ####
~~~ java
	import java.util.Iterator;
	public class Stack<Item> implements Iterable<Item> {
	...
	public Iterator<Item> iterator() {
		return new ReverseArrayIterator();
	}
	private class ReverseArrayIterator implements Iterator<Item> {
		private int i = N;

		public boolean hasNext(){return i > 0;}

		public Item next { return s[--i]; }
	}

}
~~~

## 2.6 背包（Bag）API##

添加元素到集合中，并且可以遍历所有的元素。

~~~ java
	public class Bag<Item> implements Iterable<Item>
	Bag()
	void add(Item x)
	int size()
	Interator<Item> iterator()
~~~

## 2.7 应用 ##
