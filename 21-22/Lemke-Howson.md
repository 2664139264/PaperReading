<h1 align="center"> Lemke Howson 算法 </h1>

### 背景知识

​	**双矩阵博弈**：有两个参与者$\{1,2\}$的博弈，收益矩阵分别为$A_{m\times n},B_{m\times n}$. 

当参与者$1,2$分别采取纯策略$i,j$时，效用分别为$A_{i,j},\ B_{i,j}$.

​	**混合策略**：纯策略的概率分布，为一向量。纯策略$i$可以看成混合策略$e_i=[0_1,...0_{i-1},1,...,0]^\top$.

设参与人$1,2$的混合策略分别$x,y$，其收益分别为$x^\top Ay, \ x^\top By$.

​	**混合策略的支撑集**：混合策略$x$含有的系数非$0$的纯策略分量集合，即$Supp(x):=\{i:x_i>0\}$.

​	**最优对策**： 设参与者$1$的策略为$x$，参与者$2$的策略为$y$. 

若$x^\top By\ge x^\top By' \ (\forall y')$，则$y$是$x$的最优对策。

若$x^\top Ay\ge x'^\top Ay \ (\forall x')$，则$x$是$y$的最优对策。

​	**Nash均衡**：若$x,y$互为最优对策，则$(x,y)$是博弈的Nash均衡。

​	**无差异条件**：若$(x,y)$是Nash均衡，则$x$支撑集中纯策略都是$y$的最优对策，反之亦然。

​	**非退化的双矩阵博弈**：对参与者$1$的任意策略$x$，若$y$是其最优对策，则$\abs{Supp(y)}\le \abs{Supp(x)}$. 反之亦然。

### 算法思想

1. 将博弈双方的策略空间表达为两个超多面体。
2. 考虑两个超多面体顶点构成的图，确定Nash均衡点在其中的分布方式。
3. 按照Nash均衡点在其中的分布方式，在超多面体顶点图上沿边追踪求解。

### 策略空间的超多面体表示

​	设非退化的双矩阵博弈为$(A_{m\times n},B_{m\times n})$. $(x,y)$是Nash均衡，$(v,u)$为此均衡的效用。

​	由非退化条件：$\abs{Supp(x)}=\abs{Supp(y)}$.

​	由无差异条件，$\forall i\in Supp(x), (Ay)_i=\max_k(Ay)_k$，$\forall j\in Supp(y),(x^\top B)_j=max_k(x^\top B)_k$.

​	因此，可以分别构造$m+1,n+1$维超多面体$P,Q$，使得所有的Nash均衡及其效用都可表示为这两个多面体的顶点对。
$$
P=\{(u,x):x_i\ge 0,\sum_i x_i=1,x^\top B\le u\mathbf{1^\top}\}\\
Q=\{(v,y):y_j\ge 0,\sum_j y_j=1,Ay\le v\mathbf{1}\}
$$
​	映射：$(u,x)\mapsto \frac{1}{u}x, \ (v,y)\mapsto\frac{1}{v}y$ 分别将$P,Q$变换为$\bar P,\bar Q$，即：
$$
\color{red}{P=\{x:\color{green}{x_i\ge 0},\color{blue}x^\top B\le \mathbf{1}\color{red}\}; \ Q=\{y:\color{green}Ay\le \mathbf{1},\color{blue}y_j\ge 0\color{red}\}}
$$
​	对$P$，为约束条件$x_i\ge 0$赋予标号$i$，为约束条件$(x^\top B)_j\le 1$赋予标号$m+j$.

​	对$Q$，为约束条件$(Ay)_i\le 1$赋予标号$i$，为约束条件$y_j\le 0$赋予标号$m+j$.

​	（每个标号都代表其对应的超平面约束）

​	若$x$具有标号$k$，则$x$使得标号为$k$的约束等号成立。记$x$具有的标号集为$L(x)$. 
$$
\color{green}M:=\{1,2,...,m\},\color{blue}N:=\{m+1,m+1,...,m+n\}.
$$

### Nash均衡具有全部标号

**定理**：若$(x,y)\neq (0,0)$，则$(x,y)$是Nash均衡当且仅当$L(x)\cup L(y)=M\cup N$.

**证明**：由非退化条件$\abs{L(x)}=m,\abs{L(y)}=n$. 

$\Rightarrow$：若$i\in L(x)\cap M$，即$x_i=0$，$i$不在$x$支撑集中，则不是$y$的最优对策，即$(Ay)_i<1$，$i\notin L(y)$.

若$i\in L(x)\cap N$，即$(x^\top B)_{i-m}=1$，$i-m$是$x$的最优对策，故而在$y$支撑集中，$y_{i-m}> 0,\ i\notin L(y)$.

对$i\in L(y)$同理.

$\Leftarrow$：满足$L(x)\cup L(y)=M\cup N$时，使用无差异条件可得$(x,y)$为Nash均衡。$\square$



### Nash均衡成对出现

设$G_1,G_2$分别是$\bar{P},\bar{Q}$的顶点和边构成的图，$G:=G_1\times G_2$.

定义$U_k:=\{v\in V(G):(M\cup N)-\{k\}\subseteq L(v)\}$,   $k\in M\cup N$.

**定理**：$U_k$包括所有Nash均衡点和$(0,0)$，并且在$U_k$导出子图中

1. $(0,0)$和Nash均衡点的度数为1
2. 非$(0,0)$或Nash均衡点的度数为2

**证明**：1. Nash均衡$(x,y)$具有的标号满足$L(x)\cup L(y)=M\cup N$，且$x,y$标号不重复。则$k\in L(x),k\in L(y)$有且仅有一种情况成立。

不妨设$k\in L(x)$，则在$\bar{P}$中，仅有1条边在放弃$k$对应的超平面约束的同时保留其余约束。（非退化条件、简单超多面体）

2. 非Nash均衡$(x,y)$因在$U_k$中，则二者标号不含$k$，且必有一重复$l\in L(x)\cap L(y)$. 则在$U_k$导出子图中，有在$\bar{P},\bar{Q}$中两种方式放弃$l$对应的超平面约束。$\square$



**奇数定理**：非退化双矩阵博弈的$Nash$均衡有奇数个。

![image-20220104030128376](C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220104030128376.png)

### 算法描述

```pascal
procedure Lemke_Howson(A,B,k):
	x, y := 0, 0;
	initial_k := k;
	Construct Polytope P,Q with A,B;
	while
   		In P, drop label k, Pick up label k1;
   		update x;
   		if k1 = initial_k:
   			break;
   		In Q, drop label k1, Pick up label k2;
   		update y;
   		if k2 = initial_k:
   			break;
		k := k2;
	end while
	Print x/sum(x), y/sum(y);   		
```

### 计算示例

类似单纯形法，使用Minimum test保证在超多面体上。

实质上是交替决策，逐步达到均衡。

![image-20220104040007388](C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220104040007388.png)



![image-20220104040027700](C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220104040027700.png)

![image-20220104040100845](C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220104040100845.png)

### 开源工具

```shell
> pip install nashpy
```

```python
import nashpy
game = nashpy.Game([[1,2],[2,1]], [[0,3],[4,5]])
game.lemke_howson(initial_dropped_label=0)
```