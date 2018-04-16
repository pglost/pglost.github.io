---
layout: post
title:  "符号表"
date:   2018-04-04 16:31:54
tag:
- 普林斯顿算法公开课
- Cousera
---

* content
{:toc}

## 1. API ##

### 1.1 符号表 ###

键值对的集合

- 插入键值对
- 给定键，快速找到相关值



### 1.2 基本的API ###

~~~ java
public class ST<Key, Value>
------------------------------------------------------
	ST()
	void put(Key key, Value value)
	Value get(Key key)
	void delete(Key key)
	boolean contains(Key key)
	boolean isEmpty()
	int size()
	Iterable<Key> keys()
~~~
### 1.3 默认的约定 ###

- 符号表中的值（Values）不为null
- 如果键不在符号表中，get()返回null
- put()方法对于某键有新值时会覆盖旧值

### 1.4 键与值 ###
值的类型：泛型

键的类型：
- 如果键实现了Comparable接口，使用compareTo()方法去比较大小
- 如果键是泛型，使用equals()去检验相等，使用hashCode()去映射key

equals()不仅包含对象引用相等的检查，而且包括业务上相等的检查


~~~ java
	public final class Date implements Comparable<Date> {
		private final int month;	   	private final int day;   		private final int year;
   		...
   		
   		public boolean equals(Object y) {
   			//check for null
   			if(y == null) retrun false;
   			
   			//check for true equals
   			if(y == this) return true;
   			
   			//objects must be in the same class
   			if(y.getClass() != this.getClass()) return false;
   			
   			//
   			Date that = (Date) y;
   			if (this.day   != that.day  ) return false;      		if (this.month != that.month) return false;      		if (this.year  != that.year ) return false;   			return true;
   		}
	}
~~~

通用方法：

- 检查引用是否相等
- 检查是否为null
- 检查两个对象是否为相同的数据类型，如果是，强转
- 根据业务上的需求，检查相应的成员变量
	- 如果成员变量为基本数据类型，使用 ==	- 如果成员变量为对象，使用quals()	- 如果成员变量为数组, 检查数组的每一项。此外，还可以使用Arrays.equals(a, b)或者Arrays.deepEquals(a, b)，绝对避免使用equals(a, b)。


## 2. 初级实现 ###

### 2.1 链表中的顺序查找###
- 数据结构：维护一个无序的链表去存储键值对
- 搜索：扫描链表
- 插入：扫描链表，如果有匹配的键，更新值；如果没有匹配的键，插入键值对到链表的头。

复杂度：

<table>
   <tr  align="center">
      <td rowspan="2">ST实现</td>
      <td colspan="2">最坏</td>
      <td colspan="2">平均</td>
      <td>按序遍历</td>
      <td>键需要实现的接口</td>
   </tr>
   <tr align="center">
      <td>查找</td>
      <td>插入</td>
      <td>查找</td>
      <td>插入</td>
      <td></td>
      <td></td>
   </tr>
   <tr	align="center">
      <td>顺序查找</td>
      <td>N</td>
      <td>N</td>
      <td>N/2</td>
      <td>N</td>
      <td>no</td>
      <td>equals()</td>
   </tr>
</table>



### 2.2 有序数组的二分查找 ###

- 数据结构：维护一个有序数组去存储键值对
- 排序方法rank(key)：返回keys<key的数量

java实现

~~~ java
	public Value get(Key key) {
		if(isEmpty()) return null;
		int i = rank(key);
		if(i < N && keys[i].compareTo(key) == 0) return vals[i];
		else return null;
	}
	//number of keys < key
	private int rank(Key key) {
		int lo = 0, hi = N - 1;
		while(lo <= hi) {
			int mid = lo + (hi - lo) / 2;
			int cmp = key.compareTo(keys[mid]);
			if(cmp < 0) hi = mid - 1;
			else if (cmp > 0) lo = mid + 1;
			else return mid;
		}
		return lo;
	}
~~~

复杂度：

<table>
   <tr  align="center">
      <td rowspan="2">ST实现</td>
      <td colspan="2">最坏</td>
      <td colspan="2">平均</td>
      <td>按序遍历</td>
      <td>键需要实现的接口</td>
   </tr>
   <tr align="center">
      <td>查找</td>
      <td>插入</td>
      <td>查找</td>
      <td>插入</td>
      <td></td>
      <td></td>
   </tr>
   <tr	align="center">
      <td>顺序查找</td>
      <td>N</td>
      <td>N</td>
      <td>N/2</td>
      <td>N</td>
      <td>no</td>
      <td>equals()</td>
   </tr>
   <tr	align="center">
      <td>二分查找</td>
      <td>log N</td>
      <td>N</td>
      <td>log N</td>
      <td>N/2</td>
      <td>yes</td>
      <td>compareTo()</td>
   </tr>
</table>

## 3. 有序的符号表实现 ##

### 3.1 有序符号表的API ###
~~~ java
public class ST<Key extends Comparable<Key>, Value>
-----------------------------------------------------
		ST()
				
		void put(Key key, Value val)
				
		Value get(Key key)
				
		void delete(Key key)
				
		boolean contains(Key key)
				
		boolean isEmpty()
				
		int size()
				
		Key min()
				
		Key max()
				
		Key floor(Key key)
				
		Key ceiling(Key key)
				
		int rank(Key key)
				
		Key select(int k)			
	
		void deleteMin()
				
		void deleteMax()
				
		int size(Key lo, Key hi)
				
		Iterable<Key>	 keys(Key lo, Key hi)
				
		Iterable<Key> keys()			
~~~

### 3.2 性能分析 ###

  <table>
   <tr align="center">
      <td></td>
      <td>顺序查找</td>
      <td>二分查找</td>
   </tr>
   <tr	align="center">
      <td>search</td>
      <td>N</td>
      <td>log N</td>
   </tr>
   <tr	align="center">
      <td>insert/delete</td>
      <td>N</td>
      <td>N</td>
   </tr>
   <tr	align="center">
      <td>min/max</td>
      <td>N</td>
      <td>1</td>
   </tr>
      <tr	align="center">
      <td>floor/ceiling</td>
      <td>N</td>
      <td>log N</td>
   </tr>   
   <tr	align="center">
      <td>rank</td>
      <td>N</td>
      <td>log N</td>
   </tr>   
   <tr	align="center">
      <td>select</td>
      <td>N</td>
      <td>1</td>
   </tr>
   <tr	align="center">
      <td>ordered iteration</td>
      <td>NlogN</td>
      <td>N</td>
   </tr>
</table>