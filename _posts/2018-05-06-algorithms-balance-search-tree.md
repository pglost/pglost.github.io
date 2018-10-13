---
layout: post
title:  "平衡搜索树"
date:   2018-05-06 22:39:54
tag:
- 普林斯顿算法公开课
---

* content
{:toc}



符号表回顾

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
      <td>1.39log N</td>
      <td>1.39log N</td>   
      <td>$$\sqrt{N}$$</td>
      <td>next</td>
      <td>compareTo()</td>
   </tr>
    <tr align="center">
      <td>目标</td>
      <td style="color:red">log N</td>
      <td style="color:red">log N</td>
      <td style="color:red">log N</td>
      <td>log N</td>
      <td>log N</td>   
      <td>log N</td>   
      <td>yes</td>
      <td>compareTo()</td>
   </tr>  
</table>

挑战：保证搜索的性能

- 2-3搜索树
- 左倾红黑树LLRB
- B树



## 1. 2-3搜索树
### 1.1 基本概念

- 每个节点允许1-2个键
	- 2-node：1个键，两个孩子
	- 3-node：2个键，三个孩子

- 对称顺序：中序遍历，键升序排列

- 完全平衡：从根节点到null的每条路径都有同样的长度


### 1.2 查找

- 与当前节点的键作比较
- 找到对应的区间
- 沿着对应的链接递归搜索

### 1.3 2-3树的全局属性

**<span style="color:blue">不变性</span>**。保持对称顺序和完全平衡。

证明：每个变换保持对称顺序和完全平衡。

**<span style="color:blue">完全平衡</span>**。从根节点到null的每条路径都有同样的长度

树高：

最坏情况：lgN（全是2-node）

最好情况：0.631lgN（全是3-node）

<span style="color:red">保证了查找和插入的对数复杂度</span>

### 1.4 搜索树性能总结

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
      <td>1.39log N</td>
      <td>1.39log N</td>   
      <td>$$\sqrt{N}$$</td>
      <td>next</td>
      <td>compareTo()</td>
   </tr>
    <tr align="center">
      <td>2-3搜索树</td>
      <td style="color:red">c log N</td>
      <td style="color:red">c log N</td>
      <td style="color:red">c log N</td>
      <td>c log N</td>
      <td>c log N</td>   
      <td>c log N</td>   
      <td>yes</td>
      <td>compareTo()</td>
   </tr>  
</table>

<span style="color:red">常数c取决于具体实现</span>

### 1.5 实现

直接实现2-3搜索树很复杂：

- 维护不同的节点类型很复杂
- 向下搜索，需要多次比较
- 分裂4-node，需要向上回溯搜索树
- 分裂节点需要考虑很多的情况

总结：可以实现，但是有更好的方法


## 2. 红黑树
### 2.1 定义

左倾红黑红（LLRB）

- 用BST表示2-3搜索树
- 用内部的左倾链接将3-node中的两个键粘在一起


等价的定义：左倾红黑是具有以下性质的BST

- 没有节点有两个红链接
- 从根节点到null的每一个路径都有相同数量的黑色链接
- 红链接左倾

LLRB与2-3搜索树具有一一对应关系

### 2.2 红黑树的实现

大部分的操作：查找、Floor、Iteration、Selection，不用考虑红链接，与BST完全相同


~~~ java
private static final boolean RED = true;
private static final boolean BLACK = false;

private class Node {
	Key key;
	Value val;
	Node left, right;
	boolean color; // 与父节点链接的颜色
}

private boolean isRed(Node x) {
	if(x == null) rerurn false;
	return x.color == RED;
}

~~~

### 2.3 红黑树的基本操作

- 左旋转（Left Rotation）

~~~ java
private Node rotatLeft(Node h) {
	assert isRed(h.right);
	Node x = h.right;
	h.right = x.left;
	x.left = h;
	x.color = h.color;
	h.color = RED;
	return x;
}
~~~

<span style="color:blue">不变性。</span>左旋转保持了对称顺序和完全平衡。

- 右旋转（Right Rotation）

~~~ java
private Node rotateRight(Node h) {
	assert isRed(h.left);
	Node x = h.left;
	h.left = x.right;
	x.right = h;
	x.color = h.color;
	h.color = RED;
	return x;
}
~~~
<span style="color:blue">不变性。</span>右旋转保持了对称顺序和完全平衡。

- 颜色翻转（Color Flip）从新着色，分裂一个4-node

~~~ java
private Node flipColors(Node h) {
	assert !isRed(h);
	assert isRed(h.left);
	assert isRed(h.right);
	h.color = RED;
	h.left.color = BLACK;
	h,right.color = BLACK;
	
}
~~~

### 2.4 红黑树的插入

<span style="color:blue">基本策略。</span>通过应用基本的红黑树操作，维持与2-3搜索树的一一对应关系。

- case1：插入到底部的2-node
	- 做标准的BST插入，将新的链接标红。
	- 如果新链接右倾，做左旋转操作。

	
- case2：插入到底部的3-node
	- 做标准的BST插入，将新的链接标红。
	- 如果需要，旋转从而平衡4-node
	- 翻转颜色，使得红的颜色上升一层
	- 如果需要，旋转从而使得树左倾
	- 如果需要，逐层重复。

- Java实现
	- 右孩子红，左孩子黑：左旋转
	- 左孩子红，左孩子的左孩子红：右旋转
	- 左右孩子都为红：颜色翻转

~~~ java
private Node put(Node h, Key key, Value val) {
	//insert at the bottom and color it red
	if(h == null) reutrn new Node(key, val, RED);
	int cmp = key.compareTo(h.key);
	if (cmp < 0) h.left = put(h.left, key, val);
	else if(cmp > 0) h.right = put(h.right, key, val);
	else h.val = val;
	
	//lean left
	if(isRed(h.right) && !isRed(h.left)) h = rotateLeft(h);
	//balance 4-node
	if(isRed(h.left) && isRed(h.left.left)) h = rotateRight(h);
	//split 4-node
	if(isRed(h.left) && isRed(h.right)) flipColors(h);
	
	return h; 
}
~~~	 

### 2.5 LLRB树的平衡性

<span style="color:blue">结论：</span>最坏情况下，树的高度$$\leq$$2lgN

<span style="color:blue">证明</span>

- 从根节点到null的每天路径有相同数量的黑色节点
- 每个节点不会有两个

 
在实际的应用中，数的高度 ~ 1.00 lg N。

### 2.6 总结


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
      <td>1.39log N</td>
      <td>1.39log N</td>   
      <td>$$\sqrt{N}$$</td>
      <td>next</td>
      <td>compareTo()</td>
   </tr>
    <tr align="center">
      <td>2-3搜索树</td>
      <td>c log N</td>
      <td>c log N</td>
      <td>c log N</td>
      <td>c log N</td>
      <td>c log N</td>   
      <td>c log N</td>   
      <td>yes</td>
      <td>compareTo()</td>
   </tr>  
    <tr align="center" style="color:red">
      <td>红黑搜索树</td>
      <td >2 log N</td>
      <td>2 log N</td>
      <td>2 log N</td>
      <td>1.00 log N *</td>
      <td>1.00 log N *</td>   
      <td>1.00 log N *</td>   
      <td>yes</td>
      <td>compareTo()</td>
   </tr>    
</table>

<span style="color:red">*1.00不是精确的系数，但是很接近1.00</span>

## 3. B树
页（Page）：连续的数据块
探针（Probe）：访问页的指针

性质：探针所花费的时间要远比访问页中的数据所需的时间要大得多。

成本模型：探针的数量

目标：使用最少的探针访问数据

### 3.1 基本概念

B-树：广义的2-3搜索树，每个节点最多允许有M-1个键

- 根节点最少有2个key
- 其他节点最少有M/2个key
- 外部节点包含业务键
- 内部节点存储业务键的拷贝，用来优化查找


### 3.2 查找
- 从根节点开始
- 找到对应的区间，选择相应的链接
- 在底部节点结束搜索

### 3.3 插入
- 查找一个新的键
- 在底部插入
- 自底向上，分裂达到M个键数量的节点

### 3.4 B树的平衡性

结论：N个键，容量为M的B树，查找或者插入的操作需要介于$$log_{M-1}N$$和$$log_{M/2}N$$个探针。

证明：所有的内部节点有M/2到M-1个分叉

在实践中，探针的数量最多为4。M=1024 N=62000000

优化：root页总在内存中存储

### 3.5 应用

B树被广泛的用于文件系统和数据库系统中。