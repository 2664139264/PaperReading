<h1 align=center>On the Convergence of Model Free Learning in Mean Field Games</h1>

<center>Romuald Elie, Julien Perolat, Mathieu Lauriere, Matthieu Giest, Oliver Pietquin</center>
<center>Universite Paris-Est, Deepmind, ORFE, Princeton Unversity, Google Research, Brain Team</center>
<center>AAAI-20</center>
<center>报告人：汪永毅</center>

## 1. MFG (Mean Field Games, 平均场博弈) 介绍

### MFG模型提出者

​	2006年由法国数学家Jean-Michel Lasry和Pierre-Louis Lions (1994年菲尔兹奖得主）在法文论文Jeux à champ moyen. I – Le cas stationnaire中提出MFG模型。

### MFG的研究对象

​	MFG（平均场博弈），顾名思义就是利用统计力学和量子力学上的平均场手段处理**大量同质**对象之间的博弈问题，它在金融学和管理学上有非常自然的应用。

​	**例**：股市中大量依据其他用户行为交易股票的股民、无人机蜂群调度……

​	**平均场**：某个对象进行决策所依赖的信息来自于场景中所有对象状态的概率分布，而非具体每个对象的状态（因为大量对象被认为是同质不可区分的）。

​	按照平均场的思想考虑菜市场的买菜行为：我们决定是否买菜，只需要依据菜的市场价。菜的市场价可以看做一种平均场，其已经反映了市场中所有买家卖家的决策行为，因此在决定是否买菜时，没有必要去了解同一个市场中其他买家卖家具体的决策。

### MFG模型的导出

​	平均场博弈论建模$N$个对象的博弈模型在$N\rightarrow\infty$时的情况。这$N$个对象之间不可区分，因为我们只关心这些对象状态的概率分布。

​	建模可从最小化成本的角度出发，也可从最大化收益的角度出发，本节使用前者。

#### 	$N$个对象的平均场博弈：

​		设$X_t^i$表示时刻$t$第$i$个对象所处的状态，其由下列ODE（常微分方程）决定：
$$
\left\{
	\begin{array}{**lr**}
	dX_t^i=\alpha_t^i(X_t^i)dt\\
	X_0^i=x_0^i
	\end{array}
\right.\tag{1.1}
$$
​		$\alpha_t^i$是状态$X_i^t$对时间$t$的变化率，称为对象$i$在$t$时刻的**控制变量**，即其决策。

​		$x_0^i$为对象$i$的初始状态。

​		**例**：状态$X_t^i$表示第$i$辆车在$t$时刻的位置，则$\alpha_t^i(X_t^i)$是其在$t$时刻的速度。车可以通过改变自身速度来调整位置，故称为控制。

​		对象$i$在状态$X_t^i$下，经过$dt$的时间，产生成本/消耗为$\mathcal{L}(X_t^i,\alpha_t^i(X_t^i),m_t^i)dt$，其中
$$
m_t^i:=\frac{\sum_{j\neq i}^N\delta_{X_t^j}}{N-1}\tag{1.2}
$$
​		$m_t^i$为除了对象$j$之外所有对象在$t$时刻状态的（经验）概率分布函数，$\delta$为dirac分布。

​		**例**：建模城市车流，$\mathcal{L}(X_t^i,\alpha_t^i,m_t^i):=(1+m_t^i)^\gamma\frac{\abs{\alpha_t^i(X_t^i)}^2}{2}$，表示在车流密度越大的地方速度越快运行耗费越大，即越危险。

​		当包含$i$的系统在时刻$T$停止，假设对象$i$有一个结束成本/消耗$G(X_T^i,m_T^i)$，这个消耗取决于最终状态$X_T^i$，以及$T$时刻其他对象状	态的概率分布$m_T^i$.

​		**例**：建模城市车流，刹车时汽车刹车片发生磨损，可计入结束成本$G$中。

​		综上所述，在整个时间区间$[0,T]$中，对象$i$产生的总成本为：
$$
J(x_0,\alpha_t^i;\alpha_t^{-i}):=G(X_T^i,m_T^i)+\int_0^T\mathcal{L}(X_t^i,\alpha_t^i(X_t^i),m_t^i)\mathrm{d}t\tag{1.3}
$$
​		在整个过程中，对象$i$的目标就是在观察到其他对象的决策$\alpha_t^{-i}$的情况下，选择一个最优策略$\hat{\alpha}_t^i$	使得自己的总成本最少。
$$
\hat{\alpha}_t^{i}\in\arg\min_{\alpha_t^i}J(x_0,\alpha_t^i;\alpha_t^{-i})\tag{1.4}
$$
​		当所有对象$i$在任意时刻$t$的决策$\alpha_t^i$都是对手策略$\alpha_t^{-i}$的最优对策（不能通过改变策略$\alpha_t^i$使得自己成本$J(x_0,\alpha_t^i;\alpha_t^{-i})$降低），则形	成纳什均衡。

#### 		从$N$个对象到无限对象

​		当参与博弈的同质对象非常多时，按照有限对象平均场博弈建模会导致模型过于复杂。

​		当$N\rightarrow\infty$时，按照有限对象平均场博弈（经验）概率分布的定义$({1.2})$：
$$
m_t:=\lim_{N\rightarrow\infty}\frac{\sum_{j\neq i}^N\delta_{X_t^j}}{N-1}=\lim_{N\rightarrow\infty}\frac{\sum_{j=1}^N\delta_{X_t^j}}{N}\tag{1.5}
$$
​		此时任一对象$i$在$t$时刻所面对的环境状态（其余对象的状态）的分布均为同一分布$m_t$，模型反而得到简化。

​		故而只需任意选取一个对象作为代表，记其在$t$时刻的状态为$X_t$，则其满足下列ODE：
$$
\left\{
	\begin{array}{**lr**}
	dX_t=\alpha_t(X_t)dt\\
	X_0=x_0
	\end{array}
\right.\tag{1.6}
$$
​		类似地，在$[0,T]$时段内，代表对象的总成本为：
$$
J(x_0,\alpha_t):=G(X_T,m_T)+\int_0^T\mathcal{L}(X_t,\alpha_t,m_t)\mathrm{d}t\tag{1.7}
$$
​		其中$m_t(t>0)$为对象总体在$t$时刻的分布，依赖$m_0,\alpha$.

​		在此系统中，所有的对象均遵照$\alpha_t$，根据时刻$t$与其所处状态$X_t$产生决策$\alpha_t(X_t)$.

​		按照对象的初始状态分布$m_0$，平均总成本为：
$$
\mathbb{J}(m_0, \alpha_t):=\mathbb{E}_{x_0\sim m_0}J(x_0,\alpha_t)\tag{1.8}
$$
​		当初始分布为$m_0$时，纳什均衡策略$\hat{\alpha_t}$满足
$$
\hat{\alpha_t}\in\arg\min_{\alpha_t}\mathbb{J}(m_0,\alpha_t)\tag{1.9}
$$
## 2. 求解MFG的最优控制/纳什均衡	

​	已知环境状态转移模型的情况下，MFG可以建模为连续最优控制问题，使用连续动态规划（HJB方程）求解最优控制。求解最优控制得到的是给定对象初始状态、初始环境分布、环境分布的转移方式下某个对象的最优决策函数。

​	未知环境状态转移模型的情况下，可以使用Fictitious Play的方法求解纳什均衡。求解纳什均衡得到的是给定初始状态和分布的最优决策函数。所有的对象均按照此决策函数在各个时刻选择自己的动作，并且任何对象无法通过单方面改变策略来降低自身总成本。

### 连续动态规划（HJB方程）求解最优控制

​	要将MFG建模为最优控制问题，需要明确**状态变量、控制函数、性能指标**的表示。

​	$t$时刻，处于状态$x_t$的对象所处环境分布为$m_t$，采用策略为$\alpha_t(x_t,m_t)$.

​	则**状态变量**可以表示为$s_t:=(x_t,m_t)$，**控制函数**可以表示为$u_t(s_t):=\alpha_t(x_t,m_t)$.

​	$[t,t_f]$时间段内的**性能指标**记为
$$
J(u(t);s(t),t):=h(s({t_f}),t_f)+\int_t^{t_f}g(s(\tau),u(\tau),\tau)\mathrm{d}\tau\tag{2.1}\label{2.1}
$$
​	最优控制下的性能指标为
$$
V(s(t),t):=\min_{u\in\mathcal{U}}J(u(t),s(t),t)\tag{2.2}\label{DefV}
$$
​	即该**最优控制问题的值函数**。

#### HJB方程

​	状态变量$s(t):[t_0,t_f]\rightarrow\mathbb{R}^n$和控制变量$u(t):[t_0,t_f]\rightarrow\mathbb{R}^m$连续可微，被控对象的状态方程为
$$
\dot{s}(t)=f(s(t),u(t),t), \ t\in[t_0,t_f]\tag{2.3}\label{2.3}
$$
​	终端状态自由，以$(2.1)$为性能指标。若值函数$V(s,t)$二阶连续可微，则如下HJB方程是最优控制的充要条件。（$t\in[t_0,t_f]$）
$$
-\frac{\partial V}{\partial t}(s(t), t) = \min_{u(t)\in\mathbb{R}^m}\mathcal{H}(s(t),u(t),\frac{\partial V}{\partial s}(s(t),t),t)\tag{2.4}\label{2.4}
$$
​	边界条件为
$$
V(s(t_f),t_f)=h(s(t_f),t_f)\tag{2.5}\label{2.5}
$$
​	其中Hamiltonian函数为
$$
\mathcal{H}(s,u,p,t):=p\cdot f(s,u,t)+g(s,u,t)\tag{2.6}
$$
**必要性**：

​	边界条件$\ref{2.5}$令$t=t_f$显然可得。
$$
V(s(t),t):=\min_{u\in\mathcal{U}}J(u(t),s(t),t)\\
=\min_{u:[t,t_f]\rightarrow\mathbb{R}^m}\{h(s(t_f),t_f)+\int_{t}^{t_f}g(s(\tau),u(\tau),\tau)\mathrm{d}\tau\}\\
=\min_{u:[t,t_f]\rightarrow\mathbb{R}^m}\{h(s(t_f),t_f)+\int_{t+\Delta t}^{t_f}g(s(\tau),u(\tau),\tau)\mathrm{d}\tau\}+\int_{t}^{t+\Delta t}g(s(\tau),u(\tau),\tau)\mathrm{d}\tau\}
\tag{2.7}
$$
​	考虑区间$[t+\Delta t, t_f]$上的最优控制子问题，值函数为
$$
V(s(t+\Delta t),t+\Delta t)=\min_{u:[t+\Delta t,t_f]\rightarrow\mathbb{R}^m}\{h(s(t_f),t_f)+\int_{t+\Delta t}^{t_f}g(s(\tau),u(\tau),\tau)\mathrm{d}\tau\}\tag{2.8}
$$
​	由最优性原理
$$
V(s(t),t)=\min_{u:[t,t_f]\rightarrow\mathbb{R}^m}\{V(s(t+\Delta t),t+\Delta t))+\int_{t}^{t+\Delta t}g(s(\tau),u(\tau),\tau)\mathrm{d}\tau\}\tag{2.9}\label{2.9}
$$

​	由二阶连续可微，泰勒展开得到
$$
V(s(t+\Delta t),t+\Delta t)=(\frac{\partial V}{\partial s}(s(t),t))\cdot[s(t+\Delta t)-s(t)]+(\frac{\partial V}{\partial t}(s(t),t))\Delta t+o(\Delta t)\tag{2.10}\label{2.10}
$$
​	由于
$$
\int_{t}^{t+\Delta t}g(s(\tau),u(\tau),\tau)\mathrm{d}\tau=g(s(t),u(t),t)\Delta t+o(t)\tag{2.11}\label{2.11}
$$
​	将$\ref{2.10},\ref{2.11}$代入$\ref{2.9}$，整理后两边除以$\Delta t$并令$\Delta t\rightarrow 0$可得HJB方程$\ref{2.4}$。

**充分性**：

​	设$u(t),V(s(t),t)$满足HJB方程。

​	1. 首先证明$V(s_0,t_0)$为$u(t)$的性能指标$J(u;s_0,t_0)$. 即$u,V$不相矛盾。
$$
-\frac{\partial V}{\partial t}(s(t), t) = \min_{\xi(t)\in\mathbb{R}^m}\mathcal{H}(s(t),\xi(t),\frac{\partial V}{\partial s}(s(t),t),t)\\
=\mathcal{H}(s(t),u(t),\frac{\partial V}{\partial s}(s(t),t),t)\\
=g(s(t),u(t),t)+(\frac{\partial V}{\partial s}(s(t),t))\cdot f(s(t),u(t),t)
\tag{2.12}
$$
​	移项得到
$$
0=g(s(t),u(t),t)+\frac{\partial V}{\partial t}(s(t), t)+(\frac{\partial V}{\partial s}(s(t),t))\cdot\dot{s}(t)\\
=g(s(t),u(t),t)+\frac{\mathrm{d}V}{\mathrm{d}t}(s(t),t)\tag{2.13}
$$
​	两边在$t\in[t_0,t_f]$上积分得到
$$
0=\int_{t_0}^{t_f}g(s(t),u(t),t)\mathrm{d}t+(V(s(t_f),t_f)-V(s(t_0),t_0))\tag{2.14}
$$
​	移项、代入边界条件$\ref{2.5}$得到
$$
V(s(t_0),t_0)=h(s(t_f),t_f)+\int_{t_0}^{t_f}g(s(t),u(t),t)\mathrm{d}t=J(u;s(t_0),t_0)\tag{2.15}
$$
​	这说明$V(s_0,t_0)$即以$t_0$为初始时刻，$s(t_0)=s_0$为初始状态，$t_f$为终止时刻，控制函数$u$的性能指标。

 2. 其次证明任何控制函数$u'$的性能指标$J(u';s(t_0),t_0)$都不小于$V(s_0,t_0)=J(u;s(t_0),t_0)$.

    在$t$时刻，对**任意**状态$s'(t)$作为初始状态的最优控制问题，由HJB方程
    $$
    -\frac{\partial V}{\partial t}(s'(t), t) = \min_{\xi(t)\in\mathbb{R}^m}\mathcal{H}(s'(t),\xi(t),\frac{\partial V}{\partial s'}(s'(t),t),t)\\
    \le\mathcal{H}(s'(t),u'(t),\frac{\partial V}{\partial s'}(s'(t), t),t)\\
    =g(s'(t),u'(t),t)+(\frac{\partial V}{\partial s'}(s'(t),t))\cdot f(s'(t),u'(t),t)\tag{2.16}
    $$
    由$\ref{2.3}$，移项得到
    $$
    0\le g(s'(t),u'(t),t)+\frac{\mathrm{d}V}{\mathrm{d}t}(s'(t),t)\tag{2.17}
    $$
    两边在$t\in[t_0,t_f]$积分得到
    $$
    0\le\int_{t_0}^{t_f}g(s'(t),u'(t),t)\mathrm{d}t+(V(s'(t_f),t_f)-V(s'(t_0),t_0))\tag{2.18}
    $$
    令$s'(t_0)=s_0$，由边界条件$\ref{2.5}$，性能指标定义$\ref{2.1}$
    $$
    V(s_0,t_0)\le J(u';s_0,t_0)
    $$
    证毕。

### Fictitious Play求解纳什均衡

​	使用Fictitious Play求解纳什均衡需要将连续的时间离散化处理。

​	MFG的纳什均衡包含两个部分：环境状态分布序列$\mu$和策略函数$\pi$。其中$\mu$为$T$（时间步数）维向量，各维度为环境状态在各个时刻的分布。策略函数$\pi$将对象状态映射到动作的概率分布。



<center><img src="C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220304142157122.png" width=600></img></center>

​	**论文的理论贡献**：证明了给定条件下MFG算法收敛到唯一Nash均衡的充分条件，不同迭代步的计算结果与纳什均衡结果的误差界。

​	**论文实验**：使用给定状态转移ODE的MFG，使用DDPG计算上述算法中的行3，使用MC抽样计算上述算法中的行5，运行上述算法，得到纳什均衡$(\mu,\pi)$同该MFG的解析解对比，验证收敛性。
