---
title: 与 Caccetta-Häggkvist 猜想有关的结果和问题总结
date: 2022-09-25 01:20:00
categories: 文献翻译
tags: 图论
---

{% note primary %}
原文：A Summary of Results and Problems Related to the Caccetta-Häggkvist Conjecture
原作者：Blair D. Sullivan
原文链接：https://arxiv.org/abs/math/0605646
{% endnote %}

## 1 引入

本文试图调查我们对 Caccetta-Häggkvist 猜想及相关问题的认识现状．2006 年 1 月，美国数学研究所在 Palo Alto 举办了一个关于 Caccetta-Häggkvist 猜想的研讨会，本文部分源于该研讨会，是对会上提出的未决问题和部分结果的总结．本文部分内容源于该研讨会，是对研讨会上提出的未决问题和部分结果的总结．我们感谢许多参与者对本文的帮助．

### 记号和定义

- *图（graph）* $G=(V(G),E(G))$ 是顶点 $V(G)$ 和边 $E(G)$ 的集合，其中每条边是不同顶点 $u,v$ 的无序对．
- *有向图（digraph）* $D=(V(D),E(D))$ 是顶点 $V(G)$ 和边 $E(G)$ 的集合，以及两个关联关系（incidence relation）$h,t:V(D)\times E(D)\to0,1$．我们令 $t(u,e)=1$ 当且仅当 $v$ 是这条边的尾（tail）（即，这条边是从 $u$ 指向另一个顶点 $v$），以及相似地 $h(v,e)=1$ 当且仅当 $v$ 是这条边的头（head）．注意这允许从 $u$ 到 $v$ 存在多条边．除非另有明确说明，本文将假设 $|V(D)|$ 和 $|E(D)|$ 是有限的．
- *简单有向图（simple digraph）* 是一个对于所有的 $u,v\in V(G)$，$E(G)$ 中最多只有一条从 $u$ 到 $v$ 的边的有向图（即没有平行有向边）．
- 在有向图 $G$ 中，对于 $u,v\in V(G)$，从 $u$ 到 $v$ 的距离 $d(u,v)$ 是 $u$ 到 $v$ 的最短有向路径的长度．我们说 $v$ 在 $u$ 的*出距离（out-distance）*$d(u,v)$，而 $u$ 在 $v$ 的*入距离（in-distance）*$d(u,v)$．
- 对于整数 $j>0$，$N_j^+(v)$ 是离 $v$ 正好出距离 $j$ 的顶点集合，$N_j^-(v)$ 是离 $v$ 正好入距离 $j$ 的顶点集合．我们可以将 $N_1^+(v)$ 和 $N_1^-(v)$ 分别缩写为 $N^+(v)$ 和 $N^-(v)$．
- 在一个有向图 $G$ 中，$\delta_G^+$ 和 $\delta_G^-$ 分别表示 $G$ 的最小出度和入度．对于一个给定的顶点 $v$，$\delta_G^+(v),\delta_G^-(v)$ 表示顶点 $v$ 的出度和入度．在被指代的图很清楚的情况下，我们可以写成 $\delta^+$ 和 $\delta^-$．

{% note info %}
- *环（cycle）*：在图论中，环是一条只有第一个和最后一个顶点重复的非空路径．一个没有环的图被称作无环图，一个没有有向环的有向图被称做有向无环图．一个无环的连通图被称作树．
- *自环（loop）*：一个自环是两个端点为同一顶点的边．如果有多于一条边连接同一对顶点，则它们均被称为重边．一个图的重数是重复次数最多的边的重复次数．如果一个图不含自环或重边，则称为简单图．多数情况下，如无特殊说明，可以假定“图”总是指简单图．
- *围长（girth）*：在图论中，一个图的围长定义为这个图所包含的最短环长。若这个图是无环图，它的围长则定义做无穷大。举例来说，4-环（正方形）的围长是 4。

参考：[图论术语 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/图论术语)
{% endnote %}

## 2 Caccetta-Häggkvist 猜想

**猜想 2.1.** *(L. Caccetta, R. Häggkvist [^5])* 每个最小出度为 $r$ 的简单 $n$ 顶点有向图都有一个长度最多为 $\lceil\frac{n}{r}\rceil$ 的环．

这可以被表述成：令 $A$ 是一个 $n\times n$ 的 $0$-$1$ 矩阵，其中对于所有的 $i\ne j$，$a_{ij}=1$ 就意味着 $a_{ij}\ne1$．对于所有的 $i$，令 $a_{ii}=1$．如果 $A$ 中每一行的总和不小于 $r+1$，则 $A^{\lceil n/r\rceil}$的迹大于 $n$．

### 2.1 部分结果

C-H 猜想在以下情况下被证明：

- $r=2$ 由 Caccetta 和 Häggkvist [^5]
- $r=3$ 由 Hamidoune [^17]
- $r=4$ 和 $r=5$ 由 Hoáng 和 Reed [^19]
- $r\le\sqrt{n/2}$ 由 Shen [^30]．关于他的结果的确切陈述，见[定理 5.1](#Theorem_5.1)．这表明，对于任何给定的 $r$，猜想的反例数（如果有的话）是有限的．
- Cayley 图（这意味着所有使用陪集（coset）表示的顶点传递图）由 Hamidoune [^15]．这个证明使用了 Kemperman [^21] 的一个定理（[引理 5.9](#Lemma_5.9)）．

{% note info %}
凯莱图（英语：Cayley graph），也叫做凯莱着色图，是将离散群的抽象结构画出的一种图。它的定义是凯莱定理（以阿瑟·凯莱命名）所暗含的。画凯莱图时，要选定群的一个生成元集合（通常有限），不同选法可能得到不同的凯莱图。凯莱图是组合群论与几何群论的中心工具。
<!-- ![在两个生成元a和b上的自由群的凯莱图](https://upload.wikimedia.org/wikipedia/commons/e/e8/F2_Cayley_Graph.png) -->
假设 $G$ 是群，而 $S$ 是 $G$ 的生成集。凯莱图 $\Gamma=\Gamma(G,S)$，是如下构造的着色的有向图：
- $G$ 的每个元素 $g$ 对应一个顶点。换言之，图 $\Gamma$ 的顶点集合 $V(\Gamma)$ 视为与 $G$ 等同；
- $S$ 中每个生成元 $s$，对应一种颜色 $c_s$；
- 对于任何 $g\in G,s\in S$，画一条由元素 $g$ 至 $gs$ 的有向边，染成 $c_s$ 色。换言之，边集合 $E(\Gamma)$ 由形如 $(g,gs)$ 的有序对构成，边的颜色由 $s\in S$ 确定。

在集合群论中，集合 $S$ 通常取为有限、对称（即满足 $S=S^{-1}$），并且不包含这个群的单位元 $e$。在这种情况下，凯莱图是简单无向图：它的边没有方向（由对称性），并且不包含自环（因为 $e\notin S$）。

参考：[凯莱图 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E5%87%B1%E8%90%8A%E5%9C%96)
{% endnote %}

另外，Shen [^32] 证明，如果对于所有 $(u,v)\in E(G)$，$\deg^+(u)+\deg^+(v)\ge4$，则 $g\le\lceil n/2\rceil$，其中 $g$ 表示 $G$ 的围长．这是对 Caccetta-Häggkvist 猜想的 $r=2$ 情况的平均局部出度版本（average local outdegree version）．

### 2.2 近似结果 I：加上常数（Additive Constant）

另一种方法是证明，对某些小的 $c$，如果 $\delta_G^+\ge r$，则存在一个长度最长为 $\frac{n}{r}+c$ 的环．对于一些特定的 $c$，这已被证明，如下所示：

- $c=2500$ 由 Chvátal 和 Szemerédi [^9]
- $c=304$ 由 Nishimura [^27]
- $c=73$ 由 Shen [^31]

### 2.3 近似结果 II：特殊情况 n/3

$r=n/2$ 的情况是显然的，但 $r=n/3$ 的情况受到了很多关注．研究寻找最小常数 $c$，使 $\delta_G^+\ge cn$ 在一个 $n$ 顶点的简单有向图 $G$ 中迫使（forces）一个长度最多为 3 的有向环．猜想 $c=1/3$，目前的结果是：

- $c\le(3-\sqrt5)/2=0.382$ 由 Caccetta 和 Häggkvist [^5]
- $c\le(2\sqrt6-3)/5=0.3797$ 由 Bondy 在一个整齐的子图计数论证中给出 [^4]
- $c\le3-\sqrt7=0.3542$ 由 [^29]

类似地，Seymour、Graaf 和 Schrijver [^12] 提出了 $\beta$ 的最小值，以便当 $G$ 的最小入度和出度至少为 $\beta n$ 时，$G$ 有一个长度最多为 3 的有向环．他们证明了 $\beta\le0.3487$，并给出了一个与 $\beta$ 和 $c$ 有关的公式．Shen 将这个公式应用于他 1998 年的结果，得到了 $\beta\le0.3477$ 的轻微改进 [^29]．

## 3 Seymour 第二邻域猜想

这个猜想表示当入度和出度都至少为 $n/3$ 时，Caccetta-Häggkvist 的特殊情况，它本身也受到了很多关注．

**猜想 3.1.** *(Seymour)* 任何没有自环（loops）或平行边（digons）的简单有向图都有一个顶点 $v$，其第二邻域至少与第一邻域一样大，即 $|N_2^+(v)|\ge|N^+(v)|$．

以下是 Seymour 第二邻域猜想的已知情况．

- 当 $G$ 是一个锦标赛图（tournament）时，这是 Dean 的猜想，并由 Fisher 用概率方法证明 [^13]．Havet 和 Thomassé 也有一个组合证明 [^18]．
{% note info %}
**锦标赛图**是一个有向图，通过为无向完整图中的每条边分配一个方向而得到。也就是说，它是完整图的一个方向，或者说是一个有向图，其中每一对不同的顶点都由一条有向边（通常称为**弧**）连接，有两个可能的方向中的任何一个。

锦标赛图的许多重要属性是由 H. G. Landau 在 Landau (1953) 中首次研究的，用于模拟鸡群中的支配关系。目前锦标赛图的应用包括投票理论和社会选择理论的研究等。

*锦标赛图*的名称源于这样一个图，它被解释为一个循环赛的结果，在这个循环赛中，每个玩家正好遇到其他玩家一次，并且没有平局发生。在锦标赛有向图中，顶点对应于球员。每对选手之间的边是由赢家指向输家的。如果选手 $a$ 击败了选手 $b$，那么就说 $a$ *支配了* $b$。如果每个选手都击败了相同数量的其他选手（入度 = 出度），那么这个锦标赛图就被称为*正规的*。

![A tournament on 4 vertices](https://upload.wikimedia.org/wikipedia/commons/8/89/4-tournament.svg)

参考：[Tournament (graph theory) - Wikipedia](https://en.wikipedia.org/wiki/Tournament_(graph_theory))
{% endnote %}
- 它被 Kaneko 和 Locke [^20] 证明适用于最小出度 $\le6$ 的有向图．
- 有一个顶点 $v$，其中 $|N_2^+(v)|\ge\gamma|N^+(v)|$，并且 $\gamma=0.657298\dots$ 是 $2x^3+x^2-1=0$ 的唯一实根．（注意猜想是 $\gamma=1$）由 Chen、Shen 和 Yuster [^7]提出．他们还声称对 $\gamma=0.67815$ 略有改进（证明未发表）．
- Godbole、Cole 和 Wright [^14] 表明，该猜想对几乎所有的有向图都成立．

## 4 r-正则有向图（r-Regular Digraphs）

如果一个有向图的每个顶点 $v$ 都有 $\delta_G^+(v)=\delta_G^-(v)=r$，那么它是 *$r$-正则*的．

**猜想 4.1.** *(Behzad, Chartrand, Wall [^2])* 一个围长为 $g$ 的 $r$-正则有向图的最小顶点数是 $r(g-1)+1$．

通过在一个圈上放置 $r(g-1)+1$ 个顶点，每个顶点都有通往接下来 $r$ 个顶点的顺时针顺序的边，Behzad、Chartrand 和 Wall给出了一个实现这一目标的例子．Caccetta-Häggkvist 猜想是对这个早期猜想的概括．

Behzad-Chartrand-Wall 猜想在以下特殊情况下被证明：

- $r=2$ 由 Behzad [^1]
- $r=3$ 由 Bermond [^3]
- 顶点传递图由 Hamidoune [^16]
{% note info %}
在图论的数学领域，**顶点传递图**是一个图 $G$，在这个图中，给定 $G$ 的任何两个顶点 $v_1$ 和 $v_2$，有一些自同构
$$f:G\to G$$
使得
$$f(v_1)=v_2$$
换句话说，如果一个图的自变形群对其顶点的作用是传递的，那么这个图就是顶点传递的。一个图是传递的当且仅当其补图是顶点传递的，因为群作用是相同的。

每个没有孤立顶点的对称图都是顶点传递的，每个顶点传递的图都是正规的。然而，并不是所有的顶点传递图都是对称的（例如，截断四面体的边），也不是所有的正规图都是顶点传递的（例如，Frucht 图和 Tietze 的图）。

参考：[Vertex-transitive graph - Wikipedia](https://en.wikipedia.org/wiki/Vertex-transitive_graph)
{% endnote %}
- 若 $\delta_G^+\ge r$，则 $g\le3\left\lceil\ln\left(\frac{2+\sqrt7}{3}\right)\right\rceil\approx\frac{1.312n}{r}$ 由 Shen [^31]

> 未完待续……

## 参考文献

[^1]: M. Behzad. Minimal 2-regular digraphs with given girth. *Journal of the Mathematical Society of Japan*, 25:1–6, 1973.
[^2]: M. Behzad, G. Chartrand, and C. Wall. On minimal regular digraphs with given girth. *Fundamenta Mathematicae*, 69:227–231, 1970.
[^3]: J.-C. Bermond. 1-graphes réguliers minimaux de girth donné. *Cahiers du Centre d’Études de Recherche Opérationnelle*, 17(2-4):125–135, 1975.
[^4]: J. Bondy. Counting subgraphs: a new approach to the Caccetta-Häggkvist conjecture. *Discrete Math*, 165/166:71–80, 1997.
[^5]: L. Caccetta and R. Häggkvist. On minimal digraphs with given girth. *Congressus Numerantium*, XXI, 1978.
[^6]: A. L. Cauchy. Recherches sur le nombres. *J. Ecole Polytechnique*, 9:99–123, 1813.
[^7]: G. Chen, J. Shen, and R. Yuster. Second neighborhood via first neighborhood in digraphs. *Annals of Combinatorics*, 7(1):15–20, 2003.
[^8]: I. Chowla. A theorem on the addition of residue classes: Application to the number $\gamma(k)$ in Waring’s problem. *Proceedings of the Indian Academy of Sciences*, 2:242–243, 1935.
[^9]: V. Chvatal and E. Szemeredi. Short cycles in directed graphs. *Journal of Combinatorial Theory, Series B*, 35(3):323–327, 1983.
[^10]: H. Davenport. On the addition of residue classes. *Journal of the London Math Society*, 10:30–32, 1935.
[^11]: H. Davenport. A historical note. *Journal of the London Math Society*, 22:100–101, 1947.
[^12]: M. de Graaf, A. Schrijver, and P. Seymour. Directed triangles in directed graphs. *Discrete Math*, 110:279–282, 1992.
[^13]: D. C. Fisher. Squaring a tournament: a proof of Dean’s conjecture. *Journal of Graph Theory*, 23:43–48, 1996.
[^14]: Godbole, Cole, and Wright. Probabilistic versions of Seymour’s distance two conjecture. *to appear*.
[^15]: Y. O. Hamidoune. An application of connectivity theory in graphs to factorizations of elements in groups. *European Journal of Combinatorics*, 2(4):349–355, 1981.
[^16]: Y. O. Hamidoune. Quelques problèmes de connexité dans les graphes orientés. *Journal of Combinatorial Theory*, Series B, 30(1):1–10, 1981.
[^17]: Y. O. Hamidoune. A note on minimal directed graphs with given girth. *Journal of Combinatorial Theory, Series B*, 43(3):343–348, 1987.
[^18]: F. Havet and S. Thomassé. Median orders of tournaments: a tool for the second neighborhood problem and Sumner’s conjecture. *Journal of Graph Theory*, 35:244–256, 2000.
[^19]: C. Hoáng and B. Reed. A note on short cycles in digraphs. *Discrete Math*, 66(1-2):103–107, 1987.
[^20]: Y. Kaneko and S. Locke. The minimum degree approach for Paul Seymour’s distance 2 conjecture. *Congressus Numerantium*, 148:201–206, 2001.
[^21]: J. Kemperman. On complexes in a semigroup. *Indagationes Mathematicae*, 18:247–254, 1956.
[^22]: W. Mader. Existenz gewisser konfigurationen in n-gesaettigten graphen und in graphen genuegend grosser kantendicht. *Math Ann.*, 194:295–312, 1971.
[^23]: W. Mader. Kantendisjunkte wege in graphen. *Monatshefte fuer Mathematik*, 78:395–404, 1974.
[^24]: W. Mader. Degree and local connectivity in digraphs. *Combinatorica*, 5:161–165, 1985.
[^25]: W. Mader. Existence of vertices of local connectivity k in digraphs of large outdegree. *Combinatorica*, 15:533–539, 1995.
[^26]: W. Mader. On topological tournaments of order 4 in digraphs of outdegree 3. *Journal of Graph Theory*, 21:371–376, 1996.
[^27]: T. Nishimura. Short cycles in digraphs. *Discrete Math*, 72(1-3):295–298, 1988.
[^28]: H. Plünnecke. Eigenschaften un abschatzungen von wirkingsfunktionen. *Berichte der Gesellschaft fr Mathematik und Datenverarbeitung*, 22, 1969.
[^29]: J. Shen. Directed triangles in digraphs. *Journal of Combinatorial Theory, Series B*, 74(2):405–407, 1998.
[^30]: J. Shen. On the girth of digraphs. *Discrete Math*, 211(1-3):167–181, 2000.
[^31]: J. Shen. On the Caccetta-Häggkvist conjecture. *Graphs and Combinatorics*, 18(3):645–654, 2002.
[^32]: J. Shen. Short cycles in digraphs with local average outdegree at least two. *Electronic Journal of Combinatorics*, 10, 2003.
[^33]: J. D. D. Silva and Y. Hamidoune. Cyclic spaces for Grassman derivatives and additive theory. *Bulletin of the London Math Society*, 26:140–146, 1994.
[^34]: C. Thomassen. Even cycles in directed graphs. *Europ. Journal of Combinatorics*, 6:85–89, 1985.
