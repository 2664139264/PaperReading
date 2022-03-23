<h1 align=center>Finding Friend and Foe in Multi-Agent Games</h1>

<center>Jack Serrino; Max Kleinman-Weiner; David C.Parkes; Joshua B.Tenenbaum</center>
<center> NIPS 2019 (Harward, MIT)</center>
<center>报告人：汪永毅</center>

## 1. Avalon 游戏介绍

**故事背景**：[以「阿瓦隆」，一窺亞瑟王傳奇 – Flere 瞻前顧後 (wordpress.com)](https://flerethinking.wordpress.com/2018/05/02/以「阿瓦隆」，一窺亞瑟王傳奇/)

**阵营划分**：

​	玩家共5人，分为2个阵营：<font color=blue>正方3人（1 Merlin & 2 Resistance）</font>，<font color=red>反方2人 (1 Spy & 1 Assasin)</font>。

**角色特点**：

​	各个角色都知道自己的身份。

​	<font color=blue>Merlin</font>知道<font color=red>反方2人</font>，<font color=blue>Resistance</font>仅知道自己的身份。

​	<font color=red>Spy & Assasin</font>互相知道身份。

​	<font color=red>Assasin</font>可以在游戏结束后指认<font color=blue>Merlin</font>，若指认正确则<font color=red>反方</font>取得胜利。

**游戏流程**：

 1. 随机发放5张身份牌<font color=blue>M(erlin), R(esistance), R(esistance)</font>, <font color=red>S(py), A(ssasin)</font>。

 2. 通过睁眼闭眼以及竖大拇指的方式让<font color=blue>M</font>知道<font color=red>反方2人</font>，<font color=red>S & A</font>互认。

 3. 随机选择一人作为起始队长，指定2-3人组队做任务，可以包含或不包含队长自己。

    （任务执行至多5次，每次队长需指定人数分别是2,3,3,2,3)。

 4. 5人同时投票，按照多数决定是否同意队长指定的队伍来执行此次任务。

    	1. 如果决定要执行，则队伍中人秘密投票决定任务成功或失败。逆时针下一个人接任队长，开启下一次任务。

     （队伍中<font color=blue>正方</font>成员必须投成功，<font color=red>反方</font>成员可投成功或失败。任务成功当且仅当所有票都是成功。仅公布计票结果）。

     2. 如果决定不执行，则由逆时针下一个人当队长，来重新指定此次任务的队伍。

     （不执行最多可以连续进行4次，当第5人指定队伍时，不投票强制执行任务）。

**胜负判定**：

​	任务成功次数达到3且<font color=blue>Merlin</font>未被<font color=red>Assasin</font>认出则<font color=blue>正方</font>胜。

​	任务失败次数达到3或者<font color=red>Assasin</font>成功指认出<font color=blue>Merlin</font>则<font color=red>反方</font>胜。

<img src="C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220210013134167.png"></img>

<center><font color=green>各Round组队人数分别为2,3,3,2,3</font></center>
<center><font color=green>Round中轮流当队长提出Proposal，投票决定是否执行任务，执行则进入下一Round</font></center>

**游戏特点**：

​	非完美信息（同时决策）、非完全信息（阵营未知导致支付函数不确定）。

​	信息集数目$10^{56}$，大于有限注德扑$10^{14}$，国际象棋$10^{47}$。

**与传统5人Avalon的区别**：

1. 为便于算法运行，不考虑玩家发言阶段。
2. 身份称呼：（<font color=blue>先知-梅林，骑士-派西维尔，忠臣-盖亚思，</font><font color=red>假先知-莫甘娜，刺客-阿德规文</font>）。
3. 传统Avalon<font color=blue>正方</font>有<font color=blue>骑士-派西维尔</font>，可以看到<font color=blue>先知-梅林</font>和<font color=red>假先知-莫甘娜</font>，但无法区分二者。

<div STYLE="page-break-after: always;"></div>

## 2. 论文概述

​	论文提出了DeepRole算法，用于求解5人Avalon游戏。

​	**算法思路**：

​		在DeepStack的基础上改进：CFR+规划算法结合逻辑推理、用DNN为信息集估值减小博弈树规模。	

​		将Avalon划分为$9\times5=45$个子问题（按照任务成功失败数目分为$9$种情况，每种情况可能经历$5$个不同的队长提议组队过程）。

<center><figure><img src="C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220210060533107.png" width=600></img><font color=green>9*5=45个子问题</font></figure></center>

每个子问题对应一个神经网络，接收历史与${{5}\choose{1,2,1,1}}=60$种角色分配情况的公共信念分布$b$，输出各玩家各信息集的价值估计。

<center><figure><img src=C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220210060844968.png width=750></img><font color=green>60种身份分配，15种信息集</font></center></figure>

上图中计算角色分配情况的公共信念分布$b$采用贝叶斯定理：
$$
b(\rho|h)=\frac{Pr\{\rho,h|\sigma\}}{Pr\{h|\sigma\}}\propto b(\rho)Pr\{h|\rho,\sigma\}= b(\rho)(1-\mathbb{1}(h\vdash\neg\rho))\prod_{i\in\{1,...,p\}}\pi_i^\sigma(I_i(h,\rho))
$$
网络训练方法：随机生成数据，自后向前逐阶段训练。

<div STYLE="page-break-after: always;"></div>

## 3. 算法细节

![image-20220210070922574](C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220210070922574.png)

![image-20220210071039342](C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220210071039342.png)

![image-20220210071127257](C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220210071127257.png)

![image-20220210071148817](C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220210071148817.png)

<div STYLE="page-break-after: always;"></div>

## 4. 总结

**创新亮点**：

​		1. DeepRole算法中博弈树上的历史$h$不仅包括可见动作$a$，也可以包括观测结果$o$，以解决Avalon任务阶段队内投票各玩家只知计票结果，不知具体动作的问题。观测结果$o$到具体动作的映射，由逻辑推理模块完成。

​		2.  DeepRole允许考虑同一个信息集上多个玩家需要决策的情况，以适应Avalon中的投票。

**实验效果**：

​		效果拔群。![image-20220210070622369](C:\Users\Warnar\AppData\Roaming\Typora\typora-user-images\image-20220210070622369.png)

​	