# <span style="background-color: #ff666680">MBPO -- When to Trust Your Model: Model-Based Policy Optimization</span>

## <span style="background-color: #f1983780">BibTeX</span>

    @article{janner2019trust,
      title={When to trust your model: Model-based policy optimization},
      author={Janner, Michael and Fu, Justin and Zhang, Marvin and Levine, Sergey},
      journal={Advances in neural information processing systems},
      volume={32},
      year={2019}
    }

## <span style="background-color: #f1983780">Motivations</span>

## <span style="background-color: #f1983780">Contributions</span>

branched rollout的理论基础

## <span style="background-color: #f1983780">Background and Related Works</span>

## <span style="background-color: #f1983780">Methodology</span>

定义policy在真实MDP和model下的期望回报关系为

$$
\eta[\pi]\geq{\hat{\eta}}[\pi]-C 
$$

则如果policy能在model下回报提升不少于C，则能保证policy在真实MDP下的回报得到提升（感觉不严谨，除非$\eta[\pi]\leq{\hat{\eta}}[\pi]$ ?）

下面通过理论分析推导出C

### Lemma 1

假定有两个联合分布$p_{1}(x,y)\;=\;p_{1}(x)p_{1}(y|x)$ 以及 $p_{2}(x,y)\;=\;p_{2}(x)p_{2}(y|x)$，那么有：

$$
D_{T V}(p_{1}(x,y)||p_{2}(x,y))\leq D_{T V}(p_{1}(x)||p_{2}(x))+\operatorname*{max}_{x}D_{T V}(p_{1}(y|x)||p_{2}(y|x)) 
$$

和一个更紧的界：

$$
D_{T V}(p_{1}(x,y)||p_{2}(x,y))\leq D_{T V}(p_{1}(x)||p_{2}(x))+E_{x\sim p_{1}}[D_{T V}(p_{1}(y|x)||p_{2}(y|x))] 
$$

Proof:

![\<img alt="" data-attachment-key="C5DC2T7X" width="774" height="391" src="attachments/C5DC2T7X.png" ztype="zimage">](attachments/C5DC2T7X.png)

### Lemma 2

假定两个MDP状态转移分布的TVD在时序上的上界定义为$\operatorname*{max}_{t}E_{s\sim p_{1}^{t}(s)}D_{TV}(p_{1}^t(s^{\prime}|s)||p_{2}^t(s^{\prime}|s))\;\le\;\delta$, 并且两分布的初始状态分布相同，则某时刻MDP状态访问分布的TVD上界为:

$$
D_{T V}(p_{1}^{t}(s)||p_{2}^{t}(s))\leq t\delta 
$$

Proof：

先定义时刻t的状态访问分布TVD上界为 $\boldsymbol{\epsilon}_{t}~=~ D_{T V}(p_{1}^{t}(s)||p_{2}^{t}(s))$

先用t-1时刻的状态访问分布表示t时刻的状态分布差，用的还是lemma1的技巧

![\<img alt="" data-attachment-key="CIDC33HA" width="798" height="270" src="attachments/CIDC33HA.png" ztype="zimage">](attachments/CIDC33HA.png)

由此可以进行递归推导：

![\<img alt="" data-attachment-key="UWWLVS5E" width="747" height="316" src="attachments/UWWLVS5E.png" ztype="zimage">](attachments/UWWLVS5E.png)

最后一步是因为初始状态分布相同，$\boldsymbol{\epsilon}_{0}=0$

**Lemma 3**

假定两个MDP下转移分布时序上的TVD上界表示为$\mathrm{max}_{t}\,E_{s\sim p_{1}^{t}(s)}D_{TV}(p_{1}(s^{\prime}|s,a)||p_{2}(s^{\prime}|s,a))\,\leq\,\,\epsilon_{m}$，并且定义两策略TVD表示为$\operatorname*{max}_{s}D_{T V}(\pi_{1}(a|s)||\pi_{2}(a|s))\le\epsilon_{\pi}$ ，则有：

$$
|\eta_{1}-\eta_{2}|\leq\frac{2R\gamma(\epsilon_{\pi}+\epsilon_{m})}{(1-\gamma)^{2}}+\frac{2R\epsilon_{\pi}}{1-\gamma} 
$$

其中$\eta_1$是$\pi_1$在$p_1$下的回报，$\eta_2$同理

Proof:

![\<img alt="" data-attachment-key="XNVL3EBV" width="499" height="321" src="attachments/XNVL3EBV.png" ztype="zimage">](attachments/XNVL3EBV.png)

先推导$\delta=\epsilon_{m}+\epsilon_{\pi}$

$$
\begin{array}{c}
{{\sigma = \operatorname*{max}_{t}E_{s\sim p_{1}^{t}(s)}D_{TV}(p_{1}(s^{\prime}|s)||p_{2}(s^{\prime}|s))  }}\\

{{\le \operatorname*{max}_{t}E_{s\sim p_{1}^{t}(s)}\frac{1}{2}\sum_{s'}|\sum_a (p_1(s'|s,a)\pi_1(a|s)-p_2(s'|s,a)\pi_2(a|s))| }} \\

{{\le \operatorname*{max}_{t}E_{s\sim p_{1}^{t}(s)}\frac{1}{2}\sum_{s',a}| (p_1(s'|s,a)\pi_1(a|s)-p_2(s'|s,a)\pi_2(a|s))| }} \\

{{\le \operatorname*{max}_{t}E_{s\sim p_{1}^{t}(s)}\frac{1}{2}\sum_{s',a}| (p_1(s',a|s)-p_2(s',a|s)| }} \\

{{\le \operatorname*{max}_{t}E_{s\sim p_{1}^{t}(s)}D_{TV}((p_1(s',a|s)||p_2(s',a|s)) }} \\

{{\le \operatorname*{max}_{t}E_{s\sim p_{1}^{t}(s)}(D_{TV}(\pi_1(a|s)||\pi_2(a|s))+E_{a\sim\pi_1}D_{TV}(p_1(s'|s,a)||p_2(s'|s,a))) }} \\

{{\le \epsilon_{m}+\epsilon_{\pi} }}

\end{array}
$$

从而可以得到：

$$
D_{T V}(p_{1}^{t}(s)||p_{2}^{t}(s))\leq t(\epsilon_{m}+\epsilon_{\pi}) 
$$

再依据Lemma 1:

$$
D_{T V}(p_{1}^{t}(s,a)||p_{2}^{t}(s,a))\leq t(\epsilon_{m}+\epsilon_{\pi})+\epsilon_{\pi} 
$$

代回最开始的式子：

$$
\begin{array}{c}{{\left|\eta_{1}-\eta_{2}\right|\le r_{\mathrm{max}}\displaystyle\sum_{t}\displaystyle\sum_{s,a}\gamma^{t}|p_{1}^{t}(s,a)-p_{2}^{t}(s,a)|}}\\ {{\le2r_{\mathrm{max}}\displaystyle\sum_{t}\gamma^{t}t(\epsilon_{m}+\epsilon_{\pi})+\epsilon_{\pi}}}\\ {{\le2r_{\mathrm{max}}\displaystyle(\frac{\gamma(\epsilon_{\pi}+\epsilon_{m})}{(1-\gamma)^{2}}+\frac{\epsilon_{\pi}}{1-\gamma})}}\end{array}
$$

**Theorem 1**

假定model-based算法收集数据的策略为$\pi_D$，定义泛化误差$\operatorname*{max}_{t}E_{s\sim\pi_{D},t}\left[D_{T V}(p(s^{\prime}|s,a)\right]\left|\hat{p}(s^{\prime}|s,a)\right) ]\le\epsilon_{m}$，分布偏移为$\operatorname*{max}_{s}D_{T V}(\pi_{D}(a|s)||\pi(a|s))\leq\,\epsilon_{\pi}$，则：

$$
\eta[\pi]\geq\hat{\eta}[\pi]-\frac{2\gamma r_{\mathrm{max}}(\epsilon_{m}+2\epsilon_{\pi})}{(1-\gamma)^{2}}-\frac{4r_{\mathrm{max}}\epsilon_{\pi}}{(1-\gamma)} 
$$

Proof:

直接应用Lemma3只会推导出关于model误差的下界（policy相同），所以引入$\pi_D$ ：

![\<img alt="" data-attachment-key="LC24MRI6" width="427" height="74" src="attachments/LC24MRI6.png" ztype="zimage">](attachments/LC24MRI6.png)

L1存在分布偏移，没有model误差

$$
L_{1}\geq-\frac{2r_{\operatorname*{max}}\gamma\epsilon_{\pi}}{(1-\gamma)^{2}}-\frac{2r_{\operatorname*{max}}\epsilon_{\pi}}{1-\gamma} 
$$

L2既有分布偏移又有model误差

$$
L_{2}\geq-\frac{2r_{\operatorname*{max}}\gamma(\epsilon_{m}+\epsilon_{\pi})}{(1-\gamma)^{2}}-\frac{2r_{\operatorname*{max}}\epsilon_{\pi}}{1-\gamma} 
$$

加起来就是目标

![\<img alt="" data-attachment-key="H2SBWBD2" width="489" height="114" src="attachments/H2SBWBD2.png" ztype="zimage">](attachments/H2SBWBD2.png)

通过这个结果我们可以得到以下启发，当model error很大时：

*   C会很大，可能无法保证策略回报提升超过C

*   完全依赖model进行rollout会使得model error扩展，这是因为级数求和引入的

    $\frac{1}{(1-\gamma)}$

于是本文提出一种branched rollout：先利用上一次更新得到的策略$\pi_D$在真实MDP中rollout采样数据，某一时刻切换到当前策略$\pi$，再从当前trajectory上继续采样k步

下面推导branched rollout情况下的下界：

**Lemma 4**

先按对branched rollout的定义，提出pre-branch阶段和post-brach阶段。再提出两个阶段的误差：$ \mathrm{max}_{t}\,E_{s\sim p_{1}^{t}(s)}D_{TV}(p_{1}^{\mathrm{pre}}(s^{\prime}|a,s)\vert\vert p_{2}^{\mathrm{pre}}(s^{\prime}|a,s))\;\leq\;\;\epsilon_{m}^{\mathrm{pre}}  $, $\mathrm{max}_{t}\,E_{s\sim p_{1}^{t}(s)}D_{TV}(p_{1}^{\mathrm{post}}(s^{\prime}|a,s)\vert\vert p_{2}^{\mathrm{post}}(s^{\prime}|a,s))\;\leq\;\;\epsilon_{m}^{\mathrm{post}}$ ，策略误差定义同理，可以得到：

$$
|\eta_{1}-\eta_{2}|\leq2r_{\mathrm{max}}\left[\frac{\gamma^{k+1}}{(1-\gamma)^{2}}(\epsilon_{m}^{\mathrm{pre}}+\epsilon_{\pi}^{\mathrm{pre}})+\frac{k}{1-\gamma}(\epsilon_{m}^{\mathrm{post}}+\epsilon_{\pi}^{\mathrm{post}})+\frac{\gamma^{k}}{1-\gamma}\epsilon_{\pi}^{\mathrm{pre}}+\frac{1}{1-\gamma}\epsilon_{\pi}^{\mathrm{post}}\right] 
$$

因为pre-brach阶段路径长度是不确定的，所以推导的时候假定先进行post-brach k步，再pre-branch采样下去

也就是说，在前k时刻：

$$
D_{T V}(d_{1}^{t}(s,a)||d_{2}^{t}(s,a))\leq t(\epsilon_{m}^{\mathrm{post}}+\epsilon_{\pi}^{\mathrm{post}})+\epsilon_{\pi}^{\mathrm{post}}\leq k(\epsilon_{m}^{\mathrm{post}}+\epsilon_{\pi}^{\mathrm{post}})+\epsilon_{\pi}^{\mathrm{post}} 
$$

之后：

$$
D_{T V}(d_{1}^{t}(s,a))[d_{2}^{t}(s,a))\leq(t-k)(\epsilon_{m}^{\mathrm{pre}}+\epsilon_{\pi}^{\mathrm{pre}})+k(\epsilon_{m}^{\mathrm{post}}+\epsilon_{\pi}^{\mathrm{post}})+\epsilon_{\pi}^{\mathrm{pre}}+\epsilon_{\pi}^{\mathrm{post}} 
$$

沿时间求和：

![\<img alt="" data-attachment-key="MSWBA3AR" width="907" height="305" src="attachments/MSWBA3AR.png" ztype="zimage">](attachments/MSWBA3AR.png)

参考Lemma 3，再乘以$ \frac{2r_{\operatorname*{max}}}{1-\gamma}  $就得到了目标

**Theorem 2**

Theorem1在branched rollout的情况下，可以得到下界（$ \mathrm{max}_{t}\,E_{s\sim\pi_{t}}[D_{T V}(p(s^{\prime}|s,a)||\hat{p}(s^{\prime}|s,a))]\,\le\,\epsilon_{m^{\prime}}  $ ）：

![\<img alt="" data-attachment-key="BTWNT5LS" width="618" height="76" src="attachments/BTWNT5LS.png" ztype="zimage">](attachments/BTWNT5LS.png)

Proof：

同样为了在结果中引入分布偏移误差，定义$ \eta^{\pi_{D},\pi}  $表示前半段在真实MDP使用$\pi_D$采样，后半段在真实MDP使用$\pi$进行采样得到的回报

![\<img alt="" data-attachment-key="57XDKIDJ" width="487" height="81" src="attachments/57XDKIDJ.png" ztype="zimage">](attachments/57XDKIDJ.png)

L1只有分布偏移没有model误差：

$$
\left|\eta[\pi]-\eta^{\pi_{D},\pi}\right|\leq2r_{\mathrm{max}}\left[\frac{\gamma^{k+1}}{(1-\gamma)^{2}}\epsilon_{\pi}+\frac{\gamma^{k}}{1-\gamma}\epsilon_{\pi}\right] 
$$

L2只有model误差（注意这里的model误差是在$\pi$中采样得到的）：

$$
|\eta[\pi]-\eta^{\pi_{D},\pi}|\leq2r_{\mathrm{max}}\left[\frac{k}{1-\gamma}\epsilon_{m^{\prime}}\right] 
$$

L1+L2就是目标

**Theorem 3**

使用$\pi_D$采样修正L2中的model error（$ \mathrm{max}_{t}\,E_{s\sim\pi_{D},t}\left[D_{T V}(p(s^{\prime}|s,a)\vert\vert\hat{p}(s^{\prime}|s,a))\right]\,\leq\,\epsilon_{m}  $ ，可以得到：

$$
\eta[\pi]\geq\eta^{\mathrm{branch}}[\pi]-2r_{\mathrm{max}}\left[\frac{\gamma^{k+1}\epsilon_{\pi}}{(1-\gamma)^{2}}+\frac{\gamma^{k}+2}{(1-\gamma)}\epsilon_{\pi}+\frac{k}{1-\gamma}(\epsilon_{m}+2\epsilon_{\pi})\right] 
$$

Proof:

定义$ \eta^{\pi_{D},\hat{\pi}_D}  $是前半段在真实MDP，后半段在model采样，都使用* *$\pi_D$得到的回报

![\<img alt="" data-attachment-key="LDYQA4AL" width="541" height="89" src="attachments/LDYQA4AL.png" ztype="zimage">](attachments/LDYQA4AL.png)

L3:

$$
\left|\eta^{\pi_{D},\pi}-\eta^{\pi_{D},\hat{\pi}_{D}}\right|\le2r_{\mathrm{max}}\left[\frac{k}{1-\gamma}(\epsilon_{m}+\epsilon_{\pi})+\frac{1}{1-\gamma}\epsilon_{\pi}\right] 
$$

L4:

$$
|\eta^{\hat{\pi}_{D},\pi_{D}}-\eta^{\mathrm{branch}}|\leq2r_{\mathrm{max}}\left[\frac{k}{1-\gamma}(\epsilon_{\pi})+\frac{1}{1-\gamma}\epsilon_{\pi}\right] 
$$

通过Theorem 3我们可以得到以下事实：

*   k的增加会减小policy的分布偏移误差
*   model的泛化误差会随着k线性增加

式子中第三项简单的把model误差和分布偏移误差加在一起，而没有考虑到model的泛化能力

作者通过实验发现model误差确实会随着分布偏移误差的增加而增加

![\<img alt="" data-attachment-key="INNMCQZ4" width="940" height="323" src="attachments/INNMCQZ4.png" ztype="zimage">](attachments/INNMCQZ4.png)

然而model误差增加的速率是和训练model的数据集大小有关系的（测量$\epsilon_{\pi}=0$处的斜率 ）：

![\<img alt="" data-attachment-key="T2QRPNGP" width="837" height="254" src="attachments/T2QRPNGP.png" ztype="zimage">](attachments/T2QRPNGP.png)

这说明增加数据集的大小可以增加model的泛化能力，即使使用$\pi_D$以外的数据评估，也具有较小的model error

之前是通过在$\pi_D$中采样数据来衡量model error，上面的实验启发我们估计一个model error和$\epsilon_{\pi}$的关系，使用线性函数近似估计：

![\<img alt="" data-attachment-key="3H7XYQ75" width="240" height="69" src="attachments/3H7XYQ75.png" ztype="zimage">](attachments/3H7XYQ75.png)

也就是Theorem 2

![\<img alt="" data-attachment-key="Y3TRW2W4" width="624" height="82" src="attachments/Y3TRW2W4.png" ztype="zimage">](attachments/Y3TRW2W4.png)

这个式子启发我们当model error很小的情况下，有

![\<img alt="" data-attachment-key="ZER9JEH7" width="465" height="52" src="attachments/ZER9JEH7.png" ztype="zimage">](attachments/ZER9JEH7.png)

也就是随着model error的降低，我们可以采用更长的rollout

可以得出一个算法

![\<img alt="" data-attachment-key="VQMWH793" width="924" height="320" src="attachments/VQMWH793.png" ztype="zimage">](attachments/VQMWH793.png)

## <span style="background-color: #f1983780">Experiments</span>

### Settings

*   predictive model: 使用ensemble随机模型，rollout时每次随机选取一个model
*   policy optimization：SAC

![\<img alt="" data-attachment-key="H9K3SVEY" width="948" height="462" src="attachments/H9K3SVEY.png" ztype="zimage">](attachments/H9K3SVEY.png)

### Performance

![\<img alt="" data-attachment-key="HDXM85ZL" width="887" height="563" src="attachments/HDXM85ZL.png" ztype="zimage">](attachments/HDXM85ZL.png)

简直降维打击

![\<img alt="" data-attachment-key="HSEC6BP6" width="933" height="334" src="attachments/HSEC6BP6.png" ztype="zimage">](attachments/HSEC6BP6.png)

### No model

mbpo中梯度更新次数和采样环境步数之间的比例很高，在SAC中用同样大的比例进行测试，SAC性能没有明显提升，说明model提高了采样效率

### Rollout horizon

只使用一步rollout具有非常好的效果，印证了理论

### Value expansion

使用MVE和STEVE的方法估计Q函数

![\<img alt="" data-attachment-key="X6GTC5YW" width="506" height="90" src="attachments/X6GTC5YW.png" ztype="zimage">](attachments/X6GTC5YW.png)

虽然比SAC好，但是相较于MBPO没有提升

### Model exploitation

首先对比policy在真实环境和model训练下的效果，发现获得的回报非常一致，说明在短的rollout的情况下，model可以较好表示真实MDP

![\<img alt="" data-attachment-key="Q4GG2332" width="280" height="300" src="attachments/Q4GG2332.png" ztype="zimage">](attachments/Q4GG2332.png)

同时又可视化了rollout效果，a是在真实环境rollout 450步，b是使用同样的动作序列在model中rollout 1000次，阴影部分表示方差，方差随着时间序列的增大表示了model error的累积

![\<img alt="" data-attachment-key="TF52G88K" width="568" height="305" src="attachments/TF52G88K.png" ztype="zimage">](attachments/TF52G88K.png)

通过在其他环境试验，本文还发现**MBPO倾向于低估环境奖励**

## <span style="background-color: #f1983780">Discussion and Future</span>

可以说是model-based领域必读
