<h1 align=center>Fictitious Self-Play in Extensive-Form Games</h2>

<center>Johannes Heinrich, Marc Lanctot, David Silver.</center>
<center>ICML 2015</center>
<center>报告人：汪永毅</center>

## 1. Fictitious Play

​	两人猜拳：A出招（![img](file:///C:\Users\Warnar\AppData\Local\Temp\SGPicFaceTpBq\16468\010E4053.png)），B反制（![img](file:///C:\Users\Warnar\AppData\Local\Temp\SGPicFaceTpBq\7124\006354B6.png)），A再反制（![img](file:///C:\Users\Warnar\AppData\Local\Temp\SGPicFaceTpBq\16468\010E6A90.png)），B也反制（![img](file:///C:\Users\Warnar\AppData\Local\Temp\SGPicFaceTpBq\16468\010E87CC.png))……

​	**问题**：策略不能收敛到Nash均衡，而是在石头剪刀布之间轮转。

​	**解决方法**：每次对对手的历史平均策略取最优对策，则双方的历史平均策略收敛到Nash均衡。$\color{red}{\Rightarrow}$ **Fictitious Play**	

​	**适用范围**：双人零和博弈、位势博弈 (Potential game) : $\exists f\in \mathbb{R}^A, \forall i:f(a'_i,a_{-i})-f(a_i,a_{-i})=u_i(a'_i,a_{-i})-u_i(a_i,a_{-i})$ ……

## 2. XFP (Full Width Extensive-Form Fictitious Play)

​	**Fictitious play的局限性**：只能用于Normal-form game，即矩阵博弈。

​	**如何用于Extensive-form game（扩展型博弈）**：转为矩阵表示？丢失时序信息、混合策略表示消耗空间过大。

$\color{red}\Rightarrow$ 改变策略表示方式，使用<font color=red>Behavioural strategy（行为策略）</font>来替代展开为矩阵后的混合策略表示。

​	**扩展型博弈的混合策略与行为策略**：

<center><figure><img src=C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220119050824862.png width="30%"></img><img src=C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220119050923164.png width="40%"></img></figure></center>

<center><font color=green>行为策略：信息集下动作的概率分布</font></center>  
<center><font color=green>混合策略：路径的概率分布（仅考虑某一玩家）</font></center>

$\color{red}\Rightarrow$ <font color=red>行为策略比混合策略更容易表示</font>

<img src="C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220119051618478.png" width="65%"></img>

<center><font color=green>混合策略可直接进行线性组合</font></center>

$\color{red}\Rightarrow$ <font color=red>混合策略可直接线性组合，而行为策略不可（逐点混合会导致出现两种策略下都不会产生的路径）</font>

​	**使用行为策略表示计算策略线性组合**：行为策略和混合策略可一一对应，结果等价。

设$\Pi,\Beta$是玩家$i$的两个混合策略，则与$\Mu:=\lambda_1\Pi+\lambda_2\Beta$（$\lambda_1+\lambda_2=1$）等价的行为策略为：
$$
\mu(u)(a)=\frac{\lambda_1 x_\pi(\sigma_u)\pi(u)(a)+\lambda_2 x_\beta(\sigma_u)\beta(u)(a)}{\lambda_1 x_\pi(\sigma_u)+\lambda_2 x_\beta(\sigma_u)}\tag{1}
$$
其中$u$为$i$的信息集，$a$为该信息集下的动作；$x_\pi(\sigma_u),x_\beta(\sigma_u)$分别表示行为策略$\pi,\beta$下到达$u$的概率（仅考虑$i$的决策）。

​	**XFP算法**：

<img src="C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220119054951797.png" width="50%"></img>

<center><font color=green>注：定理7即使用行为策略表示，计算最优对策和平均策略的线性组合</font></center>
<center><font color=green>组合系数可随迭代步变化，但需满足一定条件才可收敛</font></center>

<div STYLE="page-break-after: always;"></div>

## 3. FSP (Fictitious Self-Play)

​	**XFP存在的问题**：XFP需要对每个状态递归计算出精确的最优对策，难以用于大的博弈树。

​	**FSP针对XFP的改进**：分别用**强化学习**和**监督学习**方法代替XFP中**ComputeBRs**和**UpdateAVGStrategies**.

使用强化学习方法(Fitted Q Iteration, FQI)来近似计算最优对策$\beta$，监督学习方法（统计加权转移频数）计算平均策略$\pi$.

<img src="C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220119070136190.png" width="60%"></img>

<center><font color=green>Tuple Memory一部分用于保存对手决策，RL计算最佳应答</font></center><center><font color=green>另一部分用于保存自身决策，SL计算自己的历史平均策略</font></center>

## 4. 总结：

​	FSP的意义在于提供了一种理想的自对弈RL训练框架，用以近似求解部分扩展式博弈的Nash均衡。

​	优点：适用于较大规模的扩展式博弈，使用近似代替XFP的递归求解，提高了效率。	

​	缺点：对一般的扩展式博弈，不保证收敛性。

