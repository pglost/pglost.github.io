---
layout: post
title:  "几何搜索"
date:   2018-05-08 22:39:54
tag:
- 普林斯顿算法公开课
---

* content
{:toc}




## 1. 一维区间搜索
### 1.1 问题概述

有序符号表的扩展：

- 插入键值对
- 搜索键
- 删除键
- 区间搜索
- 区间计数


应用：数据库的查询

几何含义：

- 键是在一条直线上的点
- 给定区间，对键进行查找/计数



### 1.2 基本实现

- 无序列表。快插入，慢区间搜索。
- 有序列表。慢插入，使用二分查找进行区间搜索

区间搜索的时间复杂度：

<table>
   <tr	align="center">
      <td>数据结构</td>
      <td>插入</td>
      <td>区间查找</td>
      <td>区间计数</td>
   </tr>
   <tr	align="center">
      <td>无序列表</td>
      <td>1</td>
      <td>N</td>      
      <td>N</td>
   </tr>
   <tr	align="center">
      <td>有序列表</td>
      <td>N</td>
      <td>logN</td>     
      <td>R+logN</td>
   </tr>
   <tr	align="center" style="color:red">
      <td>目标</td>
      <td>logN</td>
      <td>logN</td>      
      <td>R+logN</td>
   </tr>
</table>

N：键的数量

R：匹配到键的数量



### 1.3 一维区间计数：BST实现
介于lo、hi键的数量

~~~ java
	public int size(Key lo, Key hi) {
		if(contains(hi)) return rank(hi) - rank(lo) + 1;
		else return rank(hi) - rank(lo);
	}
~~~

时间复杂度~logN


### 1.4 一维区间搜索：BST实现
一维区间搜索：找到所有介于lo、hi之间的键

- 在所有key的左子树中递归查找
- 检查当前节点的键
- 在所有key的右子树中递归查找

时间复杂度~R + logN

总结：可以实现，但是有更好的方法


## 2. 线段求交
### 2.1 问题概述
给出N个水平、垂直的线段，找到所有相交

平方复杂度算法：暴力查找


### 2.2 扫描线算法

用垂直线从左到右扫描

- x坐标定义事件
- 水平线左端点：插入其y坐标到BST中
- 水平线右端点：从BST中移除y坐标
- 垂直线：在其y坐标的范围内做BST的区间搜索


对于N个相互正交的线段，有R个相交点，扫描线时间复杂度正比于~NlogN + R。

证明：

- 将x轴坐标插入到PQ中 -> NlogN
- 将y轴坐标插入到BST中 -> NlogN
- 将y轴坐标从BST中删除 -> NlogN
- 在BST中做区间搜索 -> NlogN + R

总结：扫描线算法2维正交线段求交问题转换为1维区间搜索


## 3. kd树

### 3.1 二维正交区间搜索

有序符号表对于2维键的扩展

- 插入2维键
- 删除2维键
- 查找2维键
- <span style="color:red">区间搜索：</span>在指定的二维区间中找到所有的键
- <span style="color:red">区间计数：</span>返回指定二维区间内键的个数

应用：网络、电路设计、数据库

几何含义：

- 键是平面上的点
- 给定一个矩形范围，去做相应点的搜索和计数

### 3.2 网格实现
- 将空间划分成M*M个分割
- 在每一个分割中插入点
- 使用2维数组去标记分割框的序号
- 插入：将(x,y)插入到对应的分割框中
- 区间搜索：在与区间相交的分割框中，去做查找

### 3.3 网格实现的性能
- 空间和时间的平衡
	- 空间：$$M^2+N$$ 
	- 时间：平均在每个分割框中花费1+N/$$M^2$$
- 网格大小的选择
	- 太小：浪费空间
	- 太大：每个网格中点过多
	- 一般情况：根号N*根号N
- 运行时间（点在平面上平均分布）
	- 初始化数据结构：N（M=根号N）
	- 插入：1
	- 区间搜索：1*R (R为区间中的点数)

网格算法对于<span style="color:red">平均分布</span>的点，非常快而且易于实现。
	 
然而在实际的几何问题中，点的<span style="color:red">聚类</span>问题很常见。这时，网格分割就不试用了。

### 3.4 空间分割树

使用树去递归的分割2维平面空间。

- 网格分割。将空间平均的分割为正方形。- 2d树。递归地将空间分割成2个平面
- Quad树。递归地将空间分割成4个平面
- BSP树。递归地将空间分割成2个区域.

2d树

- 区间搜索  平均：R+logN  最坏：R+根号N
- 最近的邻居搜索 平均：logN 最坏：N


## 4. 一维区间搜索树

维护（重叠）区间集合的搜索树

### 4.1 API

~~~ java
	public class IntervalST<Key extends Comparable<Key>, Value>							IntervalST()
		void 				put(Key lo, Key hi, Value val)		Value				get(Key lo, Key hi)		void				delete(Key lo, Key hi)		Iterable<Value>	intersects(Key lo, Key hi)
~~~

### 4.2 创建
创建一个BST，每个节点存储区间(lo, hi)

- 使用左端点作为BST的键
- 存储以当前节点为根的子树的最大端点

### 4.3 插入
插入(lo, hi)

- 使用lo作为键，插入BST中
- 更新搜索路径中的每个节点的max值

### 4.4 重叠搜索
在查询区间(lo, hi)中，查找任意重叠的区间

- 如果节点中的区间与查询区间相交，则返回
- 否认，如果左节点为null，向右子树查找
- 否则，如果左子树的最大端点小于lo，向右子树查找
- 否则向左子树查找

case1: 如果向右子树做查找，那么在左子树中就不存在相交的区间
case2：如果向左子树查找，要么在左子树找到结果、要么就是没有结果（包括右子树）

~~~ java
	Node x = root;
	while(x != null) {
		if(x.interval.intersects(lo, hi)) return x.interval;
		else if(x.left == null) x=x.right;
		else if(x.left.max < lo) x=x.right;
		else	x=x.left;
	}
~~~


## 5. 矩形求交


### 5.1 扫描线算法

从左到右用一个垂直线扫描

- 使用左右端点的x坐标定义事件
- 在区间搜索树中维护与扫描线相交的矩形的集合（使用矩形的y坐标）
- 左端点：根据当前矩形的y坐标区间范围去搜索区间树；插入y区间。
- 右端点：移除y区间。

### 5.2 复杂度分析

在N个矩形的集合中，有R对个相交，扫描线算法的时间复杂度~NlogN+RlogN

证明
- 将x坐标放入PQ中。 N log N
- 将y坐标区间插入搜索树中。 N log N 
- 从搜索树种删除y坐标区间。 N log N
- 区间搜索。 NlogN + RlogN总结：
扫描线算法将正交矩形相交问题转化为1维区间搜索问题。

## 6. 几何问题总结


<table>
   <tr	align="center">
      <td>问题</td>
      <td>解决方案</td>
   </tr>
   <tr	align="center">
      <td>1维范围搜索</td>
      <td>BST</td>
   </tr>
   <tr	align="center">
      <td>2维正交线段求交</td>
      <td>使用扫描线算法降维成1维范围搜索</td>
   </tr>
   <tr	align="center">
      <td>k维范围搜索</td>
      <td>kd树</td>
   </tr>
   <tr	align="center">
      <td>1维区间搜索</td>
      <td>区间搜索树</td>
   </tr>  
   <tr	align="center">
      <td>2维正交矩形求交</td>
      <td>使用扫描线算法降维成1维区间搜索</td>
   </tr>    
</table>


