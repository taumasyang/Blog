---
title: Caccetta-Häggkvist 猜想拓展
date: 2022-10-29 12:30:03
tags: 图论
---

<!-- （10.19）这是今天的作业以及短期的计划：
1. [x] CH猜想对于无向图是否成立？
2. [x] CH猜想对于r=2的情况的证明
3. [x] CH猜想提出的原因（阅读文献5）
4. [ ] Seymour猜想提出的原因
5. [ ] Seymour猜想如何推出CH猜想对于r=n/3的情况
6. [x] 学习Caylay图的概念
7. [x] 学习连通度，边连通度，Menger定理等
8. [ ] 下次着重讨论第五章 -->

## Caccetta-Häggkvist 猜想

### C-H 猜想对于无向图是否成立？

> **猜想 2.1.** *(L. Caccetta, R. Häggkvist)* 每个最小出度为 $r$ 的简单 $n$ 顶点有向图都有一个长度最多为 $\lceil\frac{n}{r}\rceil$ 的环．
>
> 这可以被表述成：令 $A$ 是一个 $n\times n$ 的 $0$-$1$ 矩阵，其中对于所有的 $i\ne j$，$a_{ij}=1$ 就意味着 $a_{ij}\ne1$．对于所有的 $i$，令 $a_{ii}=1$．如果 $A$ 中每一行的总和不小于 $r+1$，则 $A^{\lceil n/r\rceil}$的迹大于 $n$．

**猜想** *(C-H 猜想的无向图版本)* 每个最小度为 $r$ 的简单 $n$ 顶点无向图都有一个长度最多为 $\lceil\frac{n}{r}\rceil$ 的环．

当 $r=1$ 时不成立，因为这样的图中可以没有环．

当 $r\geqslant2$ 时，无向图中必定含有环．当 $r=n-1$ 时，我们构造一个 $n$ 顶点的完全图，其中每个顶点的度均为 $n-1$，并且这个图中最短的环长度为 3，无法达到 $\lceil\frac nr\rceil=2$，依然不成立．

### C-H 猜想对于 r = 2 的情况的证明

找不到文献 L. Caccetta and R. Häggkvist. On minimal digraphs with given girth. *Congressus Numerantium*, XXI, 1978.

### C-H 猜想提出的原因[^1]

> The Caccetta-Häggkvist Conjecture is a generalization of an earlier conjecture of Behzad, Chartrand, and Wall, who conjectured it only for diregular digraphs.

Caccetta-Häggkvist 猜想是对 Behzad、Chartrand 和 Wall 早期猜想的概括，他们只对 diregular 有向图进行了猜想．

> Behzad–Chartrand–Wall Conjecture (1970)[^2]
>
> First I need to introduce three new definitions. A $d$-regular oriented graph is an oriented graph in which each vertex has both in-degree $d$ and out-degree $d$. The girth of a graph is the length (i.e. the number of edges) of a shortest cycle in the graph. A directed cage is defined as the smallest $d$-regular oriented graph of girth $g$.
>
> The Behzad–Chartrand–Wall Conjecture states that the number of vertices $n$ of a directed cage is given by $n=(g-1)d+1$. SSNC implies the case $d=\lceil n/3\rceil$ of this conjecture.

## Seymour 第二邻域猜想

### Seymour 猜想提出的原因

> **猜想 3.1.** *(Seymour)* 任何没有自环或平行边的简单有向图都有一个顶点 $v$，其第二邻域至少与第一邻域一样大，即 $|N_2^+(v)|\ge|N^+(v)|$．

### Seymour 猜想如何推出 C-H 猜想对于 r = n / 3 的情况

假设 Seymour 猜想成立，需要证明 C-H 猜想对于 $r=n/3$ 的情况：

> 每个最小出度为 $\lceil\frac{n}{3}\rceil$ 的简单 $n$ 顶点有向图都有一个长度最多为 3 的环．

**证明：** 设 $v$ 是一个最小出度为 $\lceil\frac{n}{3}\rceil$ 的简单 $n$ 顶点有向图 $G$ 中满足 Seymour 第二邻域猜想的一个顶点．由定义可知，$N^+(v)$ 与 $N^-(v)$ 无交，且 $|N^+(v)|\ge\frac{n}{3}$．由 Seymour 第二邻域猜想，有 $|N_2^+(v)|\ge|N^+(v)|\ge\frac{n}{3}$，并且 $N_2^+(v)$ 与 $N^+(v)$ 无交．
- 若 $N^-(v)\cap N_2^+(v)\ne\varnothing$，则存在一条长度为 3 的环
  $$v\to u\in N^+(v)\to w\in N_2^+(v)\cap N^-(v)\to v$$
- 若 $N^-(v)\cap N_2^+(v)=\varnothing$：
  - 若 $N_3^+(v)=\varnothing$，则考虑子图 $G'=N^+(v)\cup N_2^+(v)$，满足 $|G'|=n'=n-1$，最小出度为 $\lceil\frac{n}{3}\rceil\ge\lceil\frac{n'}{3}\rceil$．问题转化为最小出度为 $\lceil\frac{n'}{3}\rceil$ 的简单 $n'$ 顶点有向图 $G'$ 是否有一个长度最多为 3 的环．当 $n'=3$ 时显然存在这样一个环．
  - 若 $N_3^+(v)\ne\varnothing$，我们依次按以下要求寻找环 $x\to y\to z\to x$：
    - $x\in N^+(v),y\in N_2^+(v),z\in N_3^+(v)$
    - $x\in N_2^+(v),y\in N_3^+(v),z\in N_3^+(v)$
    - $x\in N_2^+(v),y\in N_2^+(v),z\in N_3^+(v)$
    - $x\in N^+(v),y\in N_2^+(v),z\in N^+(v)$
  - 如果能找到上述路径中的任意一条，命题成立．若均无法找到，则剩余的图的限制条件为：
    - 不存在一条边 $(u,w)$ 满足任意下列条件：
      - $u\in N^+(v)\cup N_2^+(v),w=v$
      - $u\in N_3^+(v),w\in N_3^+(v)$
      - $u\in N_2^+(v),w\in N_2^+(v)$
      - $u\in N^+(v),w\in N^+(v)$
    - 图中的边只可能是：
      - $u=v,w\in N^+(v)$
      - $u\in N^+(v),w\in N_2^+(v)$
      - $u\in N_2^+(v),w\in N^+(v)$
      - $u\in N_2^+(v),w\in N_3^+(v)$
      - $u\in N_3^+(v),w\in N_2^+(v)$
      - $u\in N_3^+(v),w=v$
    - 我们发现，从 $N^+(v)$ 中的点出发的边只能指向 $N_2^+(v)$ 中的点，而从 $N_3^+(v)$ 中的点出发的边除了 $v$ 以外也只能指向 $N_2^+(v)$ 中的点．设 $\frac{n}{3}\le|N_2^+(v)|=m\le\frac{2n}{3}-1$，$N^+(v)$ 和 $N_3^+(v)$ 中的点至少有 $\frac{n}{3}\times\left(\frac{n}{3}+\frac{2n}{3}-m-1\right)=\frac{n}{9}(n-m-1)$ 个出度，因此 $N_2^+(v)$ 中的点接收了 $\left(\frac{n}{3}-1\right)(n-m)+1$ 个入度，剩余 $-\frac{n^2}{3}+\frac{4mn}{3}-m^2+n-2m-1$ 个度，而 $N_2^+(v)$ 中的点至少需要 $m\times\frac{n}{3}$个出度，由此我们列出不等式
    $$-\frac{n^2}{3}+\frac{4mn}{3}-m^2+n-2m-1\ge m\times\frac{n}{3}$$
    其判别式
    $$\Delta=-\frac13n^2<0$$
    $m$ 无解，这种情况不能成立．

综上所述，我们总能在图 $G$ 中找到长度为 3 的环，命题成立．

## Caylay 图[^3]

> 凯莱图（英语：Cayley graph），也叫做凯莱着色图，是将离散群的抽象结构画出的一种图．它的定义是凯莱定理（以阿瑟·凯莱命名）所暗含的．画凯莱图时，要选定群的一个生成元集合（通常有限），不同选法可能得到不同的凯莱图．凯莱图是组合群论与几何群论的中心工具．
> <!-- ![在两个生成元a和b上的自由群的凯莱图](https://upload.wikimedia.org/wikipedia/commons/e/e8/F2_Cayley_Graph.png) -->
> 假设 $G$ 是群，而 $S$ 是 $G$ 的生成集．凯莱图 $\Gamma=\Gamma(G,S)$，是如下构造的着色的有向图：
> - $G$ 的每个元素 $g$ 对应一个顶点．换言之，图 $\Gamma$ 的顶点集合 $V(\Gamma)$ 视为与 $G$ 等同；
> - $S$ 中每个生成元 $s$，对应一种颜色 $c_s$；
> - 对于任何 $g\in G,s\in S$，画一条由元素 $g$ 至 $gs$ 的有向边，染成 $c_s$ 色．换言之，边集合 $E(\Gamma)$ 由形如 $(g,gs)$ 的有序对构成，边的颜色由 $s\in S$ 确定．
>
> 在集合群论中，集合 $S$ 通常取为有限、对称（即满足 $S=S^{-1}$），并且不包含这个群的单位元 $e$．在这种情况下，凯莱图是简单无向图：它的边没有方向（由对称性），并且不包含自环（因为 $e\notin S$）．

## 连通度，边连通度，Menger 定理

### 连通度，边连通度[^4]

> **连通度**是指为了让图分解成孤立的子图所要删除的顶点数的最小值．
>
> 连通图 $G$ 的**割点**是指一个由顶点组成的集合，在 $G$ 删除了这些点之后，会变得不连通．点连通度 $\kappa(G)$ 是割点集阶数的最小值．如果图 $G$ 不是完全图，且 $\kappa(G)=k$，则图 $G$ 是 $k$-点连通的．更确切地来说，如果图 $G$（不论是否完全）可以在删除了 $k+1$ 个点之后变得不连通，却不能在删除 $k-1$ 个点之后变得不连通，则图 $G$ 是 $k$-点连通的，特别地，阶数为 $n$ 的完全图是 $n-1$-点连通的．
>
> 一对端点 $u,v$ 的**割点**是是指一个由顶点组成的集合，在 $G$ 删除了这些点之后，$u,v$ 会变得不连通．局部连通度 $\kappa(u,v)$ 是 $u,v$ 的最小割点集的阶数．在无向图上，局部连通度是对称的，也就是说，$\kappa(u,v)=\kappa(v,u)$，另外，除了完全图之外，$\kappa(G)$为所有不相邻的点对 $u,v$ 的局部联通度中的最小值．
>
> 类似的概念可以用来定义**边连通度**．如果在 $G$ 上删除一条边可以导致不连通性，则这条边被称作桥．更一般地，割边是指一个由边组成的集合，在 $G$ 删除了这些边之后，会变得不连通．边连通度在 $lambda(G)$ 是最小的割边集的大小，局部边连通度 $\lambda(u,v)$ 是
>
> 如果图 $G$ 的边连通度大于等于 $k$，则它被称作 $k$-边连通的．

### Menger 定理[^5]

> 在图论中，门格尔定理（英：Menger's Theorem）指在有限图中，最小割集的大小等于任意在所有顶点对之间可以找到的不相交路径的最大数量．这一定理的证明由卡尔·门格尔于1927年发表．这被认为是图论中最重要且经典的定理之一．该定理刻画了连通性的性质，增加了边的权重可推广成最大流量小割定理，而最大流量小割定理是线性规划的强对偶性定理的直接推论．
>
> #### 边连通度
>
> 门格尔定理的边连通度版本叙述为：
>
>> 设 $G$ 是个有限简单图，$x$ 和 $y$ 是其中两个不相邻的顶点．则 $x$ 和 $y$ 之间的最小边割集元素个数等于从 $x$ 到 $y$ 两两边独立的路径的最多个数．其中一个 $x$ 和 $y$ 之间的边割集是一些边的集合，使得 $G$ 扣除这些边会使 $x$ 和 $y$ 不连通． 延伸至所有点对：$G$ 是 $k$-边连通当且仅当 $G$ 中任两点之间都可以找到 $k$ 条两两边独立的路径．
>
> #### 点连通度
>
> 门格尔定理的点连通度版本叙述为：
>
>> 设 $G$ 是个有限简单图，$x$ 和 $y$ 是其中两个不同的顶点．则 $x$ 和 $y$ 之间的最小点割集元素个数等于从 $x$ 到 $y$ 两两端点外点独立的路径的最多个数．其中一个 $x$ 和 $y$ 之间的点割集是搜集一些点，使得 $G$ 扣除这些点会使 $x$ 和 $y$ 不连通． 延伸至所有点对：$G$ 是 $k$-连通当且仅当 $G$ 中任两点之间都可以找到 $k$ 条两两端点外点独立的路径．
>
> #### 有限有向图
>
> 上述两版本对于 G 是有向有向图的情况仍然成立，唯独路径将修改成有向路径．
>
> #### 定义
> - **$x,y$-点割集**：给定一个图 $G$ 和 $x,y\in V(G)$，一个点集 $S\subseteq V(G)$，如果 $G-S$ 中无 $x$ 到 $y$ 的路径，则称 $S$ 是 $x,y$-点割集．
> - **$x,y$-边割集**：给定一个图 $G$ 和 $x,y\in V(G)$，一个边集 $S\subseteq E(G)$，如果 $G-S$ 中无 $x$ 到 $y$ 的路径，则称 $S$ 是 $x,y$-边割集．
> - **$X,Y$-路径**：给定一个图 $G$ 和两个点集 $X,Y\subseteq V(G)$，$X,Y$-路径是指一条起点在 $X$ 中，终点在 $Y$ 中，中间点均不在 $X\cup Y$ 中的路径．
> - **内部不相交路径**是指除端点外其他点互不相交的路径．

---

[^1]: [Caccetta-Häggkvist Conjecture | Open Problem Garden](http://openproblemgarden.org/op/caccetta_haggkvist_conjecture)
[^2]: <https://www.math.ru.nl/OpenGraphProblems/TimV/relatedproblems.html>
[^3]: [凯莱图 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E5%87%B1%E8%90%8A%E5%9C%96)
[^4]: [连通图 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E8%BF%9E%E9%80%9A%E5%9B%BE)
[^5]: [门格尔定理 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E9%97%A8%E6%A0%BC%E5%B0%94%E5%AE%9A%E7%90%86)