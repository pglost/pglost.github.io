---
layout: post
title:  "Union-Find"
date:   2018-02-07 17:31:54
tag:
- 普林斯顿算法公开课
---

* content
{:toc}

## 1. 产出可用算法的步骤

- 问题建模
- 找到一个能解决问题的算法
- 足够快？存储够？
- 如果不是，搞清楚为什么
- 找到解决算法速度、内存问题的方法
- 迭代直到满足需求

## 2. 动态连通性问题


### 2.1 概述

在N个对象的集合中

- Union操作：连接两个对象
- Find操作（connect query）：两个对象之间是否联通

### 2.2 对象建模

在实际应用中，动态连通性问题中的对象可能是多种多样的

- 数字照片中的像素
- 网络中的计算机
- 社交网络中的朋友
- 电脑芯片中的晶体管
- 数学集合中的元素

在程序设计中，所有对象对抽象成数字0到N-1

### 2.3 连通性建模

连接具有以下性质：

- 自反性：p与p相连接
- 对称性： 如果p与q相连接，那么q与p也相连接
- 传递性：如果p与q相连接，q与r相连接，那么p与r也相连接

连通集：相互连接对象的最大集合

### 2.4 实现
- 查询（Find）操作：检查两个对象是否在同一连通集内

- 合并（Union）操作：将两个对象所在的连通集合并

### 2.5 Union-Find数据结构
~~~ java
 public class UF 
 UF(int N)   
 void union(int p, int q)
 boolean connected(int p, int q)
 int find(int p)
 int count()
~~~
## 3. 快速查找算法（Quick Find）
### 3.1 数据结构

用长度为N的数组id[]来表示N个对象的集合。**数组索引代表对象，数组值代表连通子集的标识**。

对象p与对象q相连当且仅当它们有相同的id，即id[p]=id[q]

- Find：检查p与q的id是否相同
- Union：合并包含p、q的连通集，即把数组中所有等于id[p]的项的值重置为id[q]

### 3.2 Java实现

~~~ java
public class QuickFindUF {

    private int[] id;    // id[i] = component identifier of i
    private int count;   // number of components
    public QuickFindUF(int n) {
        count = n;
        id = new int[n];
        for (int i = 0; i < n; i++)
            id[i] = i;
    }

    public boolean connected(int p, int q) {
        validate(p);
        validate(q);
        return id[p] == id[q];
    }
  
    public void union(int p, int q) {
        validate(p);
        validate(q);
        int pID = id[p];   // needed for correctness
        int qID = id[q];   // to reduce the number of array accesses

        // p and q are already in the same component
        if (pID == qID) return;

        for (int i = 0; i < id.length; i++)
            if (id[i] == pID) id[i] = qID;
        count--;
    }
    
    public int find(int p) {
        validate(p);
        return id[p];
    }

    private void validate(int p) {
        int n = id.length;
        if (p < 0 || p >= n) {
            throw new IllegalArgumentException("index " + p + " is not between 0 and " + (n-1));
        }
    }
}
~~~

### 3.3 算法分析
数组访问次数

| 算法 | 初始化 | Find | Union |
| :----:  | :----: | :----: | :----: |
| quick-find | N | 1 | N|

对于有N个对象的并查集，执行N次union操作需要$$N^2$$次数组访问操作，效率太低。

## 4. 快速合并（QuickUnion）算法
### 4.1 数据结构
用长度为N的数组id[]来表示N个对象的集合。**数组索引代表对象，数组元素代表其父对象。**

i的父亲是id[i]。i的根是id[id[...id[i]...]]。

- Find操作：检查p、q是否有相同的根
- Union操作：合并p、q的连通子集，将p的根设置为q的根
 
### 4.2 Java实现
~~~ java
public class QuickUnionUF {
    private int[] parent;  // parent[i] = parent of i
    private int count;     // number of components

    public QuickUnionUF(int n) {
        parent = new int[n];
        count = n;
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    public void union(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        if (rootP == rootQ) return;
        parent[rootP] = rootQ; 
        count--;
    }
    
    public int count() {
        return count;
    }
  
    public int find(int p) {
        validate(p);
        while (p != parent[p])
            p = parent[p];
        return p;
    }

    // validate that p is a valid index
    private void validate(int p) {
        int n = parent.length;
        if (p < 0 || p >= n) {
            throw new IllegalArgumentException("index " + p + " is not between 0 and " + (n-1));
        }
    }
~~~

### 4.3 算法分析

| 算法 | 初始化 | Find | Union|  
| :----:  | :----:   | :----:   | :----:  | 
| quick-find |   N | 1 | N|
| quick-union（最坏情况） |   N | N | N|

quick-find的缺陷

- union操作开销太大（N次数组访问）


quick-union的缺陷

- 连通子集组成的数结构的高度可能过大，这样就导致寻找root的数组访问开销最高可能为N
- find和union的操作都需要寻找root，开销最大可能为N

## 5. 算法改进
### 5.1 加权快速合并（Weighted Quick Union）
#### 5.1.1 基本思路

- 改进quick-union算法，从而避免连通子集的树结构过高
- 追踪每个树的节点数量
- 做union操作时，将节点少的树连接到节点多的树上

#### 5.1.2 数据结构

数据结构与Quick Union算法基本一致，增加了一个数组sz[]去追踪以每个对象i为root的树的节点个数。

#### 5.1.3 Java实现
find操作：

- 与Quick Union完全一样

~~~ java
return root(q) == root(q)
~~~
union操作：

- 将节点数少的树合并到节点数多的树
- 更新sz[]

~~~ java
	int i = root(p);
	int j = root(q);
	if(i == j) return;
	if(sz[i] < sz[j]) {
		id[i] = j;
		sz[j] += sz[i]; 
	} else {
		id[j] = i;
		sz[i] += sz[j];
	}
~~~
#### 5.1.4 算法分析
时间复杂度：

- Find：与p、q所在树的深度成正比
- Union：找到root之后，花费常数时间

> **命题**：
> 
> 在weightedUF算法中，任意一个节点x的深度最多是lgN
> 
> **证明**：
> 
> - 当包含x的树$$T_{1}$$合并到另一个树$$T_{2}$$时，x的深度才会加1；
> - 由于$$\vert T_{1} \vert$$ < $$\vert T_{2}\vert$$，包含x的新树节点数量相比于以前至少会翻倍；
> - 最坏的情况，节点x的深度一直在增加，最多增加lgN次

| 算法 | 初始化 | Find | Union|  
| :----:  | :----:   | :----:   | :----:   | 
| quick-find |   N | 1 | N|
| quick-union（最坏情况） |   N | N | N|
| weighted QU |  N | lgN | lgN|

### 5.2 路径压缩（略）

进一步让树更平，找到节点p的根节点root之后，将寻找过程中访问过的节点的parent设置为root。

### 5.3 总结
从一个空的数据结构开始，对N个对象的集合做M次union-find的系列操作。

| 算法 | 最坏时间复杂度|  
| :----:  | :----:   | 
| quick-find |   MN | 
| quick-union |   MN | 
| weighted QU |  N + MlogN| 
| QU+path compression|N + MlogN |
| weighted QU + path compression|  N + Mlg*N |


