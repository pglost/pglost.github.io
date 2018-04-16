---
layout: post
title:  "二叉搜索树"
date:   2018-04-06 23:59:54
tag:
- 普林斯顿算法公开课
- Cousera
---

* content
{:toc}

## 1. 二叉搜索树 ##

### 1.1 概述 ###

定义：对称顺序（symetric order）的二叉树(binary tree)

二叉树：

- 要么是空的
- 要么左右是两个不相交的二叉树 

堆成顺序：每一个节点都有一个键，并且这个键有以下属性

- 比左子树的所有键都大

- 比右子树的所有键都小

### 1.2 在Java中的定义 ###

Java定义：BTS是对根节点（root Node）的引用

一个节点由四个成员变量组成

~~~ java
public class BST<Key extends Comparable<Key>, Value> {
	private Node root;
	
	private class Node {	   private Key key;	   private Value val;	   private Node left, right;	   public Node(Key key, Value val) {	      this.key = key;	      this.val = val;   		}	}
	
	public void put(Key key, Value val) {
		
	}
	
	public Value get(Key key) {
		
	}
	
	public void delete(Key key) {
		
	}
	
	public Iterable<Key> iterator() {
		
	}
}

~~~
### 1.3 BST实现 ###

~~~ java
public class BST<Key extends Comparable<Key>, Value> {	private Node root;
	
	pirvate class Node {
		/* see previous slide */
	}
	public void put(Key key, Value value) {
		/* see next slide */
	}
	public Value get(Key key) {
		/* see next slide */
	}
	public void delete(Key key) {
		/* see next slide */		
	}
	public Iterable<Key> iterator() {
		/* see next slide */
	}
}
~~~
### 1.4 BTS搜索 ###

~~~ java
public Value get(Key key) {
	Node x = root;
	while(x != null) {
		int cmp = key.compareTo(x.key);
		if (cmp == 0) return x.val;
		else if (cmp > 0) x = x.right;
		else x = x.left;
	}
	return null;		
}
~~~

性能：需要花费树的高度+1次比较
### 1.5 BTS插入 ###
~~~ java
	public void put(Key key, Value val) {
		root = put(root, key, val);
	}
	
	private Node put(Node x, Key key, Value val) {
		if(x == null) return new Node(key, val);
		int cmp = key.compareTo(x.key);
		if(cmp == 0) x.val = val;
		else if(cmp > 0) x.right = put(x.right, key, val);
		else x.left = put(x.left, key, val);
		return x;
	}
	
~~~

性能：需要花费树的高度+1次比较

### 1.6 树的形状 ###

- 树的形状取决于插入节点的顺序。

- 树的形状决定树的高度，数的高度决定搜索和插入的性能。

- 为了让树更平衡，应该使用随机顺序插入。

### 1.7 性能 ###


- 如果没有重复的键，BTS的与快排的分割十分相似。
- 如果N个不同的键以随机的顺序插入二叉搜索树中，对于搜索/插入所需花费的比较次数~2 ln N
- 如果N个不同的键以随机的顺序插入二叉搜索树中，树的高度 ~ 4.311 ln N.

但是，最坏情况下，树的高度为N。

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
   <tr	align="center">
      <td>二叉搜索树</td>
      <td>N</td>
      <td>N</td>
      <td>1.39log N</td>
      <td>1.39log N</td>
      <td>next</td>
      <td>compareTo()</td>
   </tr>
</table>


## 2. 有序的操作 ###

### 2.1 Minimum 和 maximum ###

### 2.2 Floor 和 ceiling ###

- Floor：二叉搜索树小于等于某一键的最大键。

- Ceiling：二叉搜索树大于等于某一键值的最小键。

计算Floor：

- k等于根节点。k的floor为root。
- k小于根节点。k的floor在左子树。
- k大于根节点。k的floor在右子树（如果存在key<=k）;否则，k的floor就为root。

~~~ java
public Key floor(Key key) {
	Node x = floor(root, key);
	if(x == null) return null;
	return x.key;	
}

private Node floor(Node x, Key key) {
	if(x == null) return null;
	int cmp = key.compareTo(x.key);
	
	if(cmp == 0) return x;
	
	if(cmp < 0) return floor(x.left, key);
	
	Node t = floor(x.right, key);
	
	if(t != null) return t;
	else return x;
}
~~~
### 2.3 子树节点计数（subtree count） ###

~~~ java
private class Node {
	private Key key;
	private Key val;
	private Node left;
	private Node right;
	
	//subtree counts
	private int count;
}

public int size() {
	return size(root);
}

private int size(Node x) {
	if(x == null) return 0；
	return x.count;
}

private Node put(Node x, Key key, Value val) {
	if(x == null) return new Node(key, val);
	int cmp = key.compareTo(x.key);
	if(cmp == 0) x.val = val;
	if(cmp > 0) x.right = put(x.right, key, val);
	else return x.left = put(x.left, key, val);
	
	x.count = size(x.left) + size(x.right) + 1;
	
	return x;
}
~~~

### 2.4 排名（Rank） ###

~~~ java
public int rank(Key key) {
	return rank(key, root);
}

private int rank(Key key, Node x) {
	if(x == null) return 0;
	int cmp = key.compareTo(x.key);
	
	if(cmp < 0) return rank(key, x.left);
	else if (cmp > 0) return 1+size(x.left)+rank(key, x.right);
	else return size(x.left);
}
~~~

### 2.5 中序遍历（Inorder traversal） ###

~~~ java
public Iterable<Key> keys(){
	Queue<Key> q = new Queue<Key>();
	inorder(root, q);
	return q;
}

private void inorder(Node x, Queue<Key> q) {
	if(x == null) return;
	inorder(x.left, q);
	q.enqueue(x.key);
	inorder(x.right, q);
}
~~~

使用中序遍历可以使得输出的key为升序。

性能：二叉搜索树可以使得操作的复杂度为树的高度；如果键值以随机顺序插入到BTS中，数的高度正比于log N

<table>
   <tr align="center">
      <td></td>
      <td>顺序查找</td>
      <td>二分查找</td>
      <td>二叉搜索树</td>
   </tr>
   <tr	align="center">
      <td>search</td>
      <td>N</td>
      <td>log N</td>
      <td>h</td>
   </tr>
   <tr	align="center">
      <td>insert/delete</td>
      <td>N</td>
      <td>N</td>      
      <td>h</td>
   </tr>
   <tr	align="center">
      <td>min/max</td>
      <td>N</td>
      <td>1</td>      
      <td>h</td>     
   </tr>
      <tr	align="center">
      <td>floor/ceiling</td>
      <td>N</td>
      <td>log N</td>      
      <td>h</td>     
   </tr>   
   <tr	align="center">
      <td>rank</td>
      <td>N</td>
      <td>log N</td>      
      <td>h</td>
   </tr>   
   <tr	align="center">
      <td>select</td>
      <td>N</td>
      <td>1</td>      
      <td>h</td>     
   </tr>
   <tr	align="center">
      <td>ordered iteration</td>
      <td>NlogN</td>
      <td>N</td>      
      <td>h</td>     
   </tr>
</table>

## 3. 删除 ###

### 3.1 懒惰方法（lazy approach）####

- 找到对应的key，设置value为null
- 花费：如果键满足随机分布，~logN
- 不足：内存会过载

### 3.2 删除最小（Deleting the minimun）####

- 遍历左子树，直到找到有一个左子节点为null的节点
- 将此节点与其右子节点交换
- 更新子树计数

~~~ java
public void deleteMin(){
	root = deleteMin(root);
}

private Node deleteMin(Node x) {
	if(x.left == null) return x.right;
	x.left = deleteMin(x.left);
	x.count = 1 + size(x.left) + size(x.right);
	return x;
}
~~~
### 3.3 希尔增量删除（Hibbard deletion） ####

删除包含key的节点：搜索拥有key的节点t

- 节点t没有孩子
	-  通过将其父亲指向null删除t
- 节点t有1个孩子
	- 交换其父的指向
- 节点t有2个孩子
	- 找到节点t的继承者x，x没有左子树
	- 在t的右子树中删除最小，但是不要垃圾回收x
	- 将x放置到t点，此时仍然保持BTS的特性

~~~ java
public void delete(Key key) {
	root = delete(root, key);		
}

private Node delete(Node x, Key key) {
	if(x == null) return null;
	int cmp = key.compareTo(x.key);
	if(cmp < 0) x.left = delete(x.left, key);
	else if(cmp > 0) x.right = delete(x.right, key);
	else {
		if(x.right == null) return x.left;
		if(x.left == null) return x.right;
		
		Node t = x;
		
		x = min(t.right);
		
		x.right = deleteMin(t.right);
		x.left = t.left;
	}
	x.count = 1 + size(x.left) + size(x.right);
	return x;
}
~~~

存在的问题：非对称。使得树失去随机的特征，将会使得树的高度~$$\sqrt{N}$$。

复杂度：

<table>
   <tr  align="center">
      <td rowspan="2">ST实现</td>
      <td colspan="3">最坏</td>
      <td colspan="3">平均</td>
      <td>按序遍历</td>
      <td>键需要实现的接口</td>
   </tr>
   <tr align="center">
      <td>查找</td>
      <td>插入</td>
      <td>删除</td>
      <td>查找</td>
      <td>插入</td>
      <td>删除</td>
      <td></td>
      <td></td>
   </tr>
   <tr	align="center">
      <td>顺序查找</td>
      <td>N</td>
      <td>N</td>      
      <td>N</td>
      <td>N/2</td>
      <td>N</td>      
      <td>N/2</td>
      <td>no</td>
      <td>equals()</td>
   </tr>
   <tr	align="center">
      <td>二分查找</td>
      <td>log N</td>
      <td>N</td>     
      <td>N</td>
      <td>log N</td>
      <td>N/2</td>     
      <td>N/2</td>
      <td>yes</td>
      <td>compareTo()</td>
   </tr>
   <tr	align="center">
      <td>二叉搜索树</td>
      <td>N</td>
      <td>N</td>      
      <td>N</td>
      <td>1.39log N(若考虑删除，可能变为$$\sqrt{N}$$)</td>
      <td>1.39log N(若考虑删除，可能变为$$\sqrt{N}$$)</td>   
      <td>$$\sqrt{N}$$</td>
      <td>next</td>
      <td>compareTo()</td>
   </tr>
</table>

