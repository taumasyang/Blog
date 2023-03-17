---
title: Caccetta-Häggkvist 猜想笔记
date: 2022-10-29 12:30:03
categories: PRP
tags: 图论
---

## Caccetta-Häggkvist 猜想

### C-H 猜想提出的原因[^1]

> The Caccetta-Häggkvist Conjecture is a generalization of an earlier conjecture of Behzad, Chartrand, and Wall, who conjectured it only for diregular digraphs.

Caccetta-Häggkvist 猜想是对 Behzad、Chartrand 和 Wall 早期猜想的概括，他们只对 diregular 有向图进行了猜想．

> Behzad–Chartrand–Wall Conjecture (1970)[^2]
>
> First I need to introduce three new definitions. A $d$-regular oriented graph is an oriented graph in which each vertex has both in-degree $d$ and out-degree $d$. The girth of a graph is the length (i.e. the number of edges) of a shortest cycle in the graph. A directed cage is defined as the smallest $d$-regular oriented graph of girth $g$.
>
> The Behzad–Chartrand–Wall Conjecture states that the number of vertices $n$ of a directed cage is given by $n=(g-1)d+1$. SSNC implies the case $d=\lceil n/3\rceil$ of this conjecture.

一个有向笼是指围长为 $g$ 的最小的 $d$-正则有向图．

Behzad-Chartrand-Wall 猜想指出，一个有向笼的顶点的数量 $n=(g-1)d+1$．SSNC 暗示了这个猜想在 $d=\lceil n/3\rceil$ 的情况．

### C-H 猜想对于无向图是否成立？

> **猜想 2.1.** *(L. Caccetta, R. Häggkvist)* 每个最小出度为 $r$ 的简单 $n$ 顶点有向图都有一个长度最多为 $\lceil\frac{n}{r}\rceil$ 的环．
>
> 这可以被表述成：令 $A$ 是一个 $n\times n$ 的 $0$-$1$ 矩阵，其中对于所有的 $i\ne j$，$a_{ij}=1$ 就意味着 $a_{ij}\ne1$．对于所有的 $i$，令 $a_{ii}=1$．如果 $A$ 中每一行的总和不小于 $r+1$，则 $A^{\lceil n/r\rceil}$的迹大于 $n$．

**猜想** *(C-H 猜想的无向图版本)* 每个最小度为 $r$ 的简单 $n$ 顶点无向图都有一个长度最多为 $\lceil\frac{2n}{r}\rceil$ 的环．

当 $r=1$ 时不成立，因为这样的图中可以没有环．

当 $r\geqslant2$ 时，无向图中必定含有环．

{% note danger %}
先前的证明有误，新的证明有待完善．
{% endnote %}

### C-H 猜想对于 $r=2$ 的情况的证明[^3]

{% note danger %}
找不到文献 L. Caccetta and R. Häggkvist. On minimal digraphs with given girth. *Congressus Numerantium*, XXI, 1978.
{% endnote %}

在 Mehdi Behzad 的文章《Minimal 2-regular digraphs with given girth》中，给出了下列命题的证明：

> 围长为 $n$ 的 2-正则有向图的最小顶点数是 $2n-1$．

从这个命题可以推出 Caccetta-Häggkvist 猜想在 $r=2$ 的情况．有关此文献的内容，参阅{% post_link Minimal-2-Regular-Digraphs %}．

### C-H 猜想对于 $r=3$ 的情况的证明[^4]

参阅{% post_link Minimal-Directed-Graphs %}．

## Seymour 第二邻域猜想

> **猜想 3.1.** *(Seymour)* 任何没有自环或平行边的简单有向图都有一个顶点 $v$，其第二邻域至少与第一邻域一样大，即 $|N_2^+(v)|\ge|N^+(v)|$．

### Seymour 猜想提出的原因

{% note danger %}
没有找到资料．
{% endnote %}

### Seymour 猜想推出 C-H 猜想对于 $r=n/3$ 的情况

假设 Seymour 猜想成立，需要证明 C-H 猜想对于 $r=n/3$ 的情况：

> 每个最小出度和入度为 $\lceil\frac{n}{3}\rceil$ 的简单 $n$ 顶点有向图都有一个长度最多为 3 的环．

**证明：** 设 $v$ 是一个最小出度和入度为 $\lceil\frac{n}{3}\rceil$ 的简单 $n$ 顶点有向图 $G$ 中满足 Seymour 第二邻域猜想的一个顶点．由定义可知，$N^+(v)$ 与 $N^-(v)$ 无交，且 $|N^+(v)|\ge\frac{n}{3},|N^-(v)|\ge\frac{n}{3}$．由 Seymour 第二邻域猜想，有 $|N_2^+(v)|\ge|N^+(v)|\ge\frac{n}{3}$，并且 $N_2^+(v)$ 与 $N^+(v)$ 无交．

由于 $|N^+(v)|+|N_2^+(v)|+|N^-(v)|+|\{v\}|>n$，那么必然有 $N_2^+(v)\cap N^-(v)\ne\varnothing$，即存在 $x\in N^+(v),y\in N_2^+(v)\cap N^-(v)$ 满足 $v\to x\to y\to v$ 是一条长度为 3 的环，命题成立．

## Caylay 图[^5]

> 凯莱图（英语：Cayley graph），也叫做凯莱着色图，是将离散群的抽象结构画出的一种图．它的定义是凯莱定理（以阿瑟·凯莱命名）所暗含的．画凯莱图时，要选定群的一个生成元集合（通常有限），不同选法可能得到不同的凯莱图．凯莱图是组合群论与几何群论的中心工具．
>
> 假设 $G$ 是群，而 $S$ 是 $G$ 的生成集．凯莱图 $\Gamma=\Gamma(G,S)$，是如下构造的着色的有向图：
> - $G$ 的每个元素 $g$ 对应一个顶点．换言之，图 $\Gamma$ 的顶点集合 $V(\Gamma)$ 视为与 $G$ 等同；
> - $S$ 中每个生成元 $s$，对应一种颜色 $c_s$；
> - 对于任何 $g\in G,s\in S$，画一条由元素 $g$ 至 $gs$ 的有向边，染成 $c_s$ 色．换言之，边集合 $E(\Gamma)$ 由形如 $(g,gs)$ 的有序对构成，边的颜色由 $s\in S$ 确定．
>
> 在集合群论中，集合 $S$ 通常取为有限、对称（即满足 $S=S^{-1}$），并且不包含这个群的单位元 $e$．在这种情况下，凯莱图是简单无向图：它的边没有方向（由对称性），并且不包含自环（因为 $e\notin S$）．

### $D_4$ 二面体群的 Caylay 图是一个立方体

二面体群 $D_n\;(n\geqslant3)$，即正 $n$ 边形的对称群的阶为 $2n$，它由一个旋转 $\sigma$ 和 一个翻转 $\tau$ 生成，其中 $\sigma$ 和 $\tau$ 满足条件 $\sigma^n=1,\tau^2=1,(\sigma\tau)^2=1$．

我们画出 $D_4$ 二面体群的 Caylay 图，用蓝色表示 $\sigma$，用红色表示 $\tau$：
<img src=/img/D4-Caylay.pdf alt="D4-Caylay" width=50%>

## Menger 定理

### 连通度，边连通度[^6]

> **连通度**是指为了让图分解成孤立的子图所要删除的顶点数的最小值．
>
> 连通图 $G$ 的**割点**是指一个由顶点组成的集合，在 $G$ 删除了这些点之后，会变得不连通．点连通度 $\kappa(G)$ 是割点集阶数的最小值．如果图 $G$ 不是完全图，且 $\kappa(G)=k$，则图 $G$ 是 $k$-点连通的．更确切地来说，如果图 $G$（不论是否完全）可以在删除了 $k+1$ 个点之后变得不连通，却不能在删除 $k-1$ 个点之后变得不连通，则图 $G$ 是 $k$-点连通的，特别地，阶数为 $n$ 的完全图是 $n-1$-点连通的．
>
> 一对端点 $u,v$ 的**割点**是是指一个由顶点组成的集合，在 $G$ 删除了这些点之后，$u,v$ 会变得不连通．局部连通度 $\kappa(u,v)$ 是 $u,v$ 的最小割点集的阶数．在无向图上，局部连通度是对称的，也就是说，$\kappa(u,v)=\kappa(v,u)$，另外，除了完全图之外，$\kappa(G)$为所有不相邻的点对 $u,v$ 的局部连通度中的最小值．
>
> 类似的概念可以用来定义**边连通度**．如果在 $G$ 上删除一条边可以导致不连通性，则这条边被称作桥．更一般地，割边是指一个由边组成的集合，在 $G$ 删除了这些边之后，会变得不连通．边连通度在 $\lambda(G)$ 是最小的割边集的大小，局部边连通度 $\lambda(u,v)$ 是 $u,v$ 的最小割边集的阶数．同样，局部边连通度是对称的．如果图 $G$ 的边连通度大于等于 $k$，则它被称作 $k$-边连通的．

### Menger 定理[^7]

> 在图论中，门格尔定理（英：Menger's Theorem）指在有限图中，最小割集的大小等于任意在所有顶点对之间可以找到的不相交路径的最大数量．这一定理的证明由卡尔·门格尔于1927年发表．这被认为是图论中最重要且经典的定理之一．该定理刻画了连通性的性质，增加了边的权重可推广成最大流量小割定理，而最大流量小割定理是线性规划的强对偶性定理的直接推论．
>
> #### 边连通度
>
> 门格尔定理的边连通度版本叙述为：
>
>> 设 $G$ 是个有限简单图，$x$ 和 $y$ 是其中两个不相邻的顶点．则 $x$ 和 $y$ 之间的最小边割集元素个数等于从 $x$ 到 $y$ 两两边独立的路径的最多个数．其中一个 $x$ 和 $y$ 之间的边割集是一些边的集合，使得 $G$ 扣除这些边会使 $x$ 和 $y$ 不连通．延伸至所有点对：$G$ 是 $k$-边连通当且仅当 $G$ 中任两点之间都可以找到 $k$ 条两两边独立的路径．
>
> #### 点连通度
>
> 门格尔定理的点连通度版本叙述为：
>
>> 设 $G$ 是个有限简单图，$x$ 和 $y$ 是其中两个不同的顶点．则 $x$ 和 $y$ 之间的最小点割集元素个数等于从 $x$ 到 $y$ 两两端点外点独立的路径的最多个数．其中一个 $x$ 和 $y$ 之间的点割集是搜集一些点，使得 $G$ 扣除这些点会使 $x$ 和 $y$ 不连通．延伸至所有点对：$G$ 是 $k$-连通当且仅当 $G$ 中任两点之间都可以找到 $k$ 条两两端点外点独立的路径．
>
> #### 有限有向图
>
> 上述两版本对于 $G$ 是有向有向图的情况仍然成立，唯独路径将修改成有向路径．
>
> #### 定义
> - **$x,y$-点割集**：给定一个图 $G$ 和 $x,y\in V(G)$，一个点集 $S\subseteq V(G)$，如果 $G-S$ 中无 $x$ 到 $y$ 的路径，则称 $S$ 是 $x,y$-点割集．
> - **$x,y$-边割集**：给定一个图 $G$ 和 $x,y\in V(G)$，一个边集 $S\subseteq E(G)$，如果 $G-S$ 中无 $x$ 到 $y$ 的路径，则称 $S$ 是 $x,y$-边割集．
> - **$X,Y$-路径**：给定一个图 $G$ 和两个点集 $X,Y\subseteq V(G)$，$X,Y$-路径是指一条起点在 $X$ 中，终点在 $Y$ 中，中间点均不在 $X\cup Y$ 中的路径．
> - **内部不相交路径**是指除端点外其他点互不相交的路径．

### Menger 定理的证明

**门格尔定理**：如果 $x,y$ 是图 $G$ 的两个顶点，且 $xy\notin E(G)$，那么最小 $x,y$-点割集的大小等于内部不相交的 $x,y$-路径的条数．

**证明：** 记最小 $x,y$-点割集的大小为 $\kappa(x,y)$，内部不相交的 $x,y$-路径的条数为 $\lambda(x,y)$．

因为 $x,y$-点割集必须包含任意一条 $x,y$-路径上的一点，而共有 $\lambda(x,y)$ 条内部不相交的 $x,y$-路径，所以 $\lambda(x,y)\geq\kappa(x,y)$．下面我们证明二者相等．

我们对图的阶数进行归纳．当 $n(G)=2$，因为 $xy\notin E(G)$，所以 $\kappa(x,y)=\lambda(x,y)=0$，成立．

令 $k=\kappa(x,y)$，我们下面证明可以找到 $k$ 条内部不相交的 $x,y$-路径．

**情况 1**：当 $G$ 有一个最小 $x,y$-点割集 $S$，$S$ 既不是 $N(x)$ 也不是 $N(y)$，其中 $N(x),N(y)$ 分别是 $x$ 和 $y$ 的邻点．

令 $V_1$ 为所有 $x,S$-路径上的点，$V_2$ 为所有 $S,y$-路径上的点．根据 $S$ 的最小性，任意 $v\in S$，都有一条 $x,y$-路径 $xPy$ 经过 $v$，且 $P\cap S=v$，因此 $v\in V_1\cup V_2$．反过来，任意 $v\in V_1\cup V_2$，必有 $v\in S$，否则 $x,y$ 在 $G-S$ 中通过 $v$ 连通．因此，$S=V_1\cup V_2$．

构造一个新的图 $H$，使得 $H_1$ 是 $G$ 的 $V_1$-导出子图再加上一个新的点 $y'$，使得 $y'$ 与 $S$ 中所有点相连．因为 $G$ 中每一条 $x,y$-路径都从 $x$ 开始经过 $S$，所以 $H$ 中的 $x,y'$-点割集也是 $G$ 中的 $x,y$-点割集．又因为 $S$ 是 $H$ 的 $x,y'$-点割集，所以 $\kappa_H(x,y)=k$．又因为$|N(y)-S|>0$，所以 $H$ 比 $G$ 的阶数小，根据归纳假设，$H$ 中有 $k$ 条内部不相交的 $x,y'$-路径，即 $G$ 中有 $k$ 条内部不相交的 $x,S$ -路径．同理，$G$ 中有 $k$ 条内部不相交的 $S,y$-路径，把它们合起来得到 $k$ 条内部互不相交的 $x,y$-路径．

**情况 2**：$G$ 的最小 $x,y$-点割集不是 $N(x)$ 就是 $N(y)$．

如果存在一点 $v\in G\backslash\{x\cup y\cup N(x)\cup N(y)\}$，那么 $v$ 不在 $G$ 的任意一个最小 $x,y$-点割集中，因此 $\kappa_{G-v}(x,y)=k$．根据归纳假设，可以在 $G-v$ 中找到 $k$ 条内部不相交的 $x,y$-路径，它们也是 $G$ 中 $k$ 条内部不相交的 $x,y$-路径．

如果存在一点 $u\in N(x)\cap N(y)$，那么 $\kappa_{G-u}(x,y)=k-1$．根据归纳假设，可以在 $G-u$ 中找到 $k-1$ 条内部不相交的 $x,y$-路径，再加上 $xuy$，得到 $G$ 中 $k$ 条内部不相交的 $x,y$-路径．

否则，$N(x)$ 和 $N(y)$ 是 $V(G)-\{x,y\}$ 的一个分划．令 $G'$ 是由 $N(x)$ 和 $N(y)$ 以及它们之间的边 $[N(x),N(y)]$ 构成的二部图．$x,y$-点割集实际上对应了一个 $G'$ 中的点覆盖，根据 [Kőnig定理](https://en.wikipedia.org/wiki/Kőnig%27s_theorem_(graph_theory))，$G'$ 的最小点覆盖等于最大匹配．因此 $G'$ 包含一个大小为 $k$ 的匹配，即找到了 $G$ 中 $k$ 条内部不相交的 $x,y$-路径．证毕．

---

[^1]: [Caccetta-Häggkvist Conjecture | Open Problem Garden](http://openproblemgarden.org/op/caccetta_haggkvist_conjecture)
[^2]: <https://www.math.ru.nl/OpenGraphProblems/TimV/relatedproblems.html>
[^3]: [Mehdi BEHZAD. Minimal 2-regular digraphs with given girth. Journal of the Mathematical Society of Japan, 25(1) 1-6 January, 1973.](https://doi.org/10.2969/jmsj/02510001)
[^4]: [Y. O. Hamidoune. A note on minimal directed graphs with given girth. Journal of Combinatorial Theory, Series B, 43(3):343–348, 1987.](https://doi.org/10.1016/0095-8956(87)90009-8)
[^5]: [凯莱图 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E5%87%B1%E8%90%8A%E5%9C%96)
[^6]: [连通图 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E8%BF%9E%E9%80%9A%E5%9B%BE)
[^7]: [门格尔定理 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E9%97%A8%E6%A0%BC%E5%B0%94%E5%AE%9A%E7%90%86)