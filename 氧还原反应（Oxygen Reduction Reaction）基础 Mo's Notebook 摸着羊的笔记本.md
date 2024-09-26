# 氧还原反应（Oxygen Reduction Reaction）基础 | Mo's Notebook | 摸着羊的笔记本
为了更深入地理解文献中所出现的各种电化学方法和指标背后的物理意义，笔者对使用到的基本电化学知识做了一个总结。虽然笔者努力保证所整理知识的正确性（添加引用），但由于笔者水平所限，难免错漏，望各位老师同学不吝赐教！

[](#SCE（Saturated-calomel-electrode，饱和甘汞）电极 "SCE（Saturated calomel electrode，饱和甘汞）电极")SCE（Saturated calomel electrode，饱和甘汞）电极
-----------------------------------------------------------------------------------------------------------------------------

Ref: Han S, Hu X, Wang J, et al. Novel Route to Fe‐Based Cathode as an Efficient Bifunctional Catalysts for Rechargeable Zn–Air Battery\[J\]. Advanced Energy Materials, 2018, 8(22): 1800955.

[](#Ag-AgCl电极 "Ag/AgCl电极")Ag/AgCl电极
-----------------------------------

Ref: Ferrero G A, Preuss K, Marinovic A, et al. Fe–N-doped carbon capsules with outstanding electrochemical performance and stability for the oxygen reduction reaction in both acid and alkaline conditions\[J\]. ACS nano, 2016, 10(6): 5922-5932.

[](#一般氢电极，NHE-Normal-Hydrogen-Electrode "一般氢电极，NHE (Normal Hydrogen Electrode)")一般氢电极，NHE (Normal Hydrogen Electrode)
---------------------------------------------------------------------------------------------------------------------

一般氢电极的定义为“铂电极浸在浓度为1当量浓度\*（Normal Concentration, N）的一元强酸中并放出压力约一个标准大气压的氢气”。因其较标准氢电极易于制备，故为旧时电化学常用标准电极。但由于这样的电极并不严格可逆，故电压并不稳定，现在已经被弃用。

**注：** 对于氢离子而言，1当量浓度=1摩尔浓度，即1 N = 1 M

[](#标准氢电极，SHE-Standard-Hydrogen-Electrode "标准氢电极，SHE (Standard Hydrogen Electrode)")标准氢电极，SHE (Standard Hydrogen Electrode)
---------------------------------------------------------------------------------------------------------------------------

标准氢电极的定义为“铂电极在氢离子活度为1 M的理想溶液中，并与100 kPa压力下的氢气平衡共存时所构成的电极”。此种电极即当前电化学所规定的一级标准电极，其标准电极电势被人为规定为零（其绝对电势在25下为4.44±0.02 V）。此电极反应完全可逆，但“氢离子活度为1 M的理想溶液”实际中并不存在，故而该电极只是一个理想模型。当列举其他参比电极的电势时，如无特别说明，应该都是相对于标准氢电极的电势，标注应为“vs. SHE”。

[](#可逆氢电极，RHE-Reversible-Hydrogen-Electrode "可逆氢电极，RHE (Reversible Hydrogen Electrode)")可逆氢电极，RHE (Reversible Hydrogen Electrode)
---------------------------------------------------------------------------------------------------------------------------------

可逆氢电极为标准氢电极的一种。其与标准氢电极在定义上的唯一区别便是可逆氢电极并没有氢离子活度的要求，所以可逆氢电极的电势和pH有关。利用能斯特方程（Nernst Equation）可以很容易地推导出可逆氢电极电势的具体表达式：

[](#RHE-Reversible-Hydrogen-Electrode "RHE (Reversible Hydrogen Electrode)")RHE (Reversible Hydrogen Electrode)
---------------------------------------------------------------------------------------------------------------

**Notes:** SHE = Standard Hydrogen Electrode. 标准氢电极就是标准的可逆氢电极。换句话说，**标准氢电极一定是可逆的，可逆氢电极不一定是标准的**. 标准氢电极通常是将镀有一层海绵状铂黑的铂片，浸入到活度为1的酸溶液中，在298.15K时不断通入压力为100kPa的纯氢气，使铂黑吸附气体至饱和，这是铂片就好像是用氢制成的电极一样. 在酸性条件下，只要保证氢气的压力为0.1Mpa，氢离子活度为1，就是标准氢电极。否则只能算是可逆氢电极. 当然**碱性条件下不存在标准氢电极**.

以上关于不同氢电极的论述引自：\[材料牛特邀编辑Tianyu Liu\](http://www.cailiaoniu.com/72084.html)

![](http://mozheyang.top/images/Electrocatalysis/TypicalORRpara.png)

Ref: Ge, X.; Sumboja, A.; Wuu, D.; An, T.; Li, B.; Goh, F. W. T.; Hor, T. S. A.; Zong, Y.; Liu, Z. Oxygen Reduction in Alkaline Media: From Mechanisms to Recent Advances of Catalysts. ACS Catal. 2015, 5 (8), 4643–4667.

其中，

为极限扩散电流密度（diffusion-limiting current density），某电流密度对应的电位与平衡电位之差为某电流密度下的过电势

. 一般地，可以定义极化曲线上的某些性能指标如下图所示：

![](http://mozheyang.top/images/Electrocatalysis/ORRdefinition.png)

Ref: 周学俊. M-Nx/C类非贵金属氧还原催化材料的构筑及其电化学性能研究\[D\]. 东华大学, 2016.

[](#Onset-potential-起始点位-amp-Half-wave-potential-半波电位 "Onset potential 起始点位 & Half-wave potential 半波电位")Onset potential 起始点位 & Half-wave potential 半波电位
-------------------------------------------------------------------------------------------------------------------------------------------------------

*   Onset potential 起始点位 ()
*   Half-wave potential 半波电位 ()

如上图所示，起始电位和半波电位是衡量ORR催化剂的最主要性能指标。**起始电位可定义为电流密度为0.1 时所对应的电位，半波电位可定义为极限扩散电流的一半。** 关于起始电位，还有另外的定义方法，即通过水平线与下降斜率分别作切线，相交于一点，而那一点所对应的电位即为起始电位，如下图所示。

![](http://mozheyang.top/images/Electrocatalysis/onset-potential-cutdef.png)

两种起始电位的定义没有物理本质区别，都是为了衡量化学反应何时发生。但由于电化学测试的影响因素较多（电化学测试的敏感性），实际上不能保证不同条件下测试得到的数据具有可比性。因此，如果要判别哪个催化剂的电催化性能更好，最好的办法还是在同一条件下进行**对比实验**。

部分文献指出，对于碳基催化剂，其起始电位实际上会受到催化剂负载量的影响。而且一般来说，催化剂负载量较大时能够体现出更好的催化性能。

![](http://mozheyang.top/images/Electrocatalysis/carbon_loading_Dai.png)

Ref: Carbon-based metal-free catalysts: design and applications\[M\]. John Wiley & Sons, 2018.

有时为了突出对比非贵金属催化剂与贵金属催化剂的电位性能，可以

加大非金属催化剂的负载

，以实现二者的清晰对比。

![](http://mozheyang.top/images/Electrocatalysis/loading-diff.png)

Ref: Li, Q.; Chen, W.; Xiao, H.; Gong, Y.; Li, Z.; Zheng, L.; Zheng, X.; Yan, W.; Cheong, W.; Shen, R.; et al. Fe Isolated Single Atoms on S , N Codoped Carbon by Copolymer Pyrolysis Strategy for Highly Efficient Oxygen Reduction Reaction. 2018, 1800588, 1–6.

[](#Diffusion-limited-current-density-DLCD-极限扩散电流密度 "Diffusion-limited current density (DLCD) 极限扩散电流密度")Diffusion-limited current density (DLCD) 极限扩散电流密度
---------------------------------------------------------------------------------------------------------------------------------------------------------

这是受到**传质控制**的反应所特有的性能指标（关于传质控制，请查看以下关于旋转圆盘电极的讨论）。极限扩散电流密度越大，说明单位面积单位时间内可通过的电流越大，反应动力学迅速。作为燃料电池阴极催化剂，就可能使得燃料电池拥有更大的功率。

需要声明的是，DLCD也有可能受到催化剂负载量的影响。随着催化剂负载量的增大，电极表面的催化剂膜会增厚，从而增大双电层电流，致使总的测试电流增大。

![](http://mozheyang.top/images/Electrocatalysis/carbon_loading_Wan.png)

Ref: Wan K, Tan A, Yu Z, et al. 2D nitrogen-doped hierarchically porous carbon: Key role of low dimensional structure in favoring electrocatalysis and mass transfer for oxygen reduction reaction\[J\]. Applied Catalysis B: Environmental, 2017, 209: 447-454.

[](#Electron-transfer-number-电子转移数 "Electron transfer number 电子转移数")Electron transfer number 电子转移数
--------------------------------------------------------------------------------------------------

碱性环境中的氧还原反应（ORR）有两种反应途径。一种是四电子过程：

一种是二电子过程：

在燃料电池中，**四电子过程**可以提供更大的功率，因此在燃料电池的氧电极催化剂设计中我们主要考虑四电子过程催化剂。电子转移数主要是通过旋转圆盘电极实验，依据Koutecky–Levich方程求得的。

### [](#Koutecky–Levich方程 "Koutecky–Levich方程")Koutecky–Levich方程

其中，. 从而可以将上式拟合得到电子转移数n.

![](http://mozheyang.top/images/Electrocatalysis/KLplot.png)

[](#Tafel-slope-塔菲尔斜率 "Tafel slope 塔菲尔斜率")Tafel slope 塔菲尔斜率
-----------------------------------------------------------

Tafel斜率的数值体现了**反应的动力学特性**，即具有不同塔菲尔斜率的反应，其**速控步**不同。具体原因可参考下文中电化学测试方法中的Tafel plot.

[](#Durability-耐久性 "Durability 耐久性")Durability 耐久性
--------------------------------------------------

在实际应用中，催化剂寿命十分重要。在非贵金属催化剂中，由于碳在空气氛围中的活泼性质，**碳基催化剂**所面临的最大挑战便是其寿命的提高。博主主要看到过两种耐久测试的方法：

### [](#循环耐久测试 "循环耐久测试")循环耐久测试

将催化剂在一定的电位区间来回进行循环伏安扫描，观察起始电位、半波电位和极限扩散电流的衰减。

![](http://mozheyang.top/images/Electrocatalysis/durability-circle.png)

Ref: Bard et al., Electrochemical Methods: Fundamentals and Applications; wiley New York, 1980; Vol. 2.

### [](#恒电位耐久测试 "恒电位耐久测试")恒电位耐久测试

将催化剂在一定的电位下保持恒电位，观察电流密度衰减。

![](http://mozheyang.top/images/Electrocatalysis/durability-constant.png)

Ref: Bard et al., Electrochemical Methods: Fundamentals and Applications; wiley New York, 1980; Vol. 2.

催化剂抗甲醇和抗一氧化碳能力的测试也勉强属于Durability的范畴，基本方法就是对照试验：加毒化物种和空白对照组，在这就不赘述了。

[](#Overpotential-过电位 "Overpotential 过电位")Overpotential 过电位
-----------------------------------------------------------

过电位等于氧化还原反应中由热力学理论确定的反应发生电势，与实际实验中所观测到的反应发生电势之间的差值。一般来说，由于浓差极化、电极表面特性等原因，该差值不可能为零。而在电催化剂研究中，我们往往希望减小这种差值，以提高电能的利用效率。

[](#RDE-Rotating-Disk-Electrode-旋转圆盘电极 "RDE (Rotating Disk Electrode) 旋转圆盘电极")RDE (Rotating Disk Electrode) 旋转圆盘电极
------------------------------------------------------------------------------------------------------------------

![](http://mozheyang.top/images/Electrocatalysis/RDE.png)

Ref: Bard et al., Electrochemical Methods: Fundamentals and Applications; wiley New York, 1980; Vol. 2.

以上是旋转圆盘装置及旋转圆盘电极。为什么氧还原反应需要旋转圆盘装置呢？

旋转圆盘电极通过电极的旋转，保证电极表面的氧气浓度保持**动态稳定**（如上图最右）。为什么说是动态稳定呢？

而且氧还原反应中电极表面为什么不处于稳态？

![](http://mozheyang.top/images/Electrocatalysis/diffusion-limited.png)

Ref: Bard et al., Electrochemical Methods: Fundamentals and Applications; wiley New York, 1980; Vol. 2.

首先，电极反应最基本的可以分为五个步骤，分别是：传质->吸附->表面反应->脱附->传质。氧气在电极表面的反应十分迅速，这就导致靠近电极处氧气浓度迅速减小（如上图右下角，纵坐标浓度比，横坐标距电极距离，不同反应时间曲线）；随反应时间延长，曲线向下走。这也就是说，电极附近的氧气浓度是随时间变化的（**非稳态**）。那么我们测得的循环伏安曲线便是带有峰形状的：

![](http://mozheyang.top/images/Electrocatalysis/CV-peak.png)

解释如下：当氧气不稀缺时，法拉第电流（由氧化还原反应引起的电流）随着过电势（偏离平衡电位的电势）的增大而增大，这时候的电流是由**反应动力学控制**的；一旦氧气稀缺，法拉第电流会立即减小，即使过电位更大，更利于氧还原反应发生，但反应物（氧气）已经没了，巧妇难为无米之炊！这时候在电位扫描曲线上会出现一个电流峰值，这时候的电流就是由氧气的**传质控制**的。

电极旋转的速度决定了氧气浓度保持“动态稳定”层（扩散层）的厚度，容易理解，越快的旋转速度，电极表面的扩散层越小。而扩散层越小，体相中的氧气越能快速的扩散到电极表面来，也就是提供了更丰富的反应物。如此，反应电流就会更大。如下图，2500r/min (rpm, rotation per minute)旋转的电极要明显比100r/min旋转的电极极限扩散电流（密度）大。

![](http://mozheyang.top/images/Electrocatalysis/rotate-speed.png)

Ref: Li, Y.; Jia, B.; Fan, Y.; Zhu, K.; Li, G.; Su, C.-Y. Bimetallic Zeolitic Imidazolite Framework Derived Carbon Nanotubes Embedded with Co Nanoparticles for Efficient Bifunctional Oxygen Electrocatalyst. Adv. Energy Mater. 2017, 1702048, 1702048.

[](#RRDE-Rotating-Ring-Disk-Electrode-旋转环-盘电极 "RRDE (Rotating Ring-Disk Electrode) 旋转环-盘电极")RRDE (Rotating Ring-Disk Electrode) 旋转环-盘电极
---------------------------------------------------------------------------------------------------------------------------------------

旋转环盘电极（Rotating Ring-Disk Electrodes，RRDE）结构上是在圆盘外围同一平面上安置一个共心圆环，圆盘与圆环电极之间用绝缘材料隔开，如下图所示。

![](http://mozheyang.top/images/Electrocatalysis/RRDE.png)

Ref: Ge, X.; Sumboja, A.; Wuu, D.; An, T.; Li, B.; Goh, F. W. T.; Hor, T. S. A.; Zong, Y.; Liu, Z. Oxygen Reduction in Alkaline Media: From Mechanisms to Recent Advances of Catalysts. ACS Catal. 2015, 5 (8), 4643–4667.

圆盘电流（Disk current）和圆环电流（Ring current）分别反映了四电子过程和二电子过程所占总反应过程的百分比。

其经典的图像如下所示：

![](http://mozheyang.top/images/Electrocatalysis/RRDE-mes.png)

* * *

[](#EIS-Electrochemical-impedance-spectroscopy-电化学阻抗谱 "EIS (Electrochemical impedance spectroscopy) 电化学阻抗谱")EIS (Electrochemical impedance spectroscopy) 电化学阻抗谱
---------------------------------------------------------------------------------------------------------------------------------------------------------------

该方法的基本思想是将所研究的电化学系统视为一个电气电子学中的**等效电路**，通过对该电路施加一定频率、小振幅的交流信号，观测其输出，便可探知等效电路的具体结构。由于不同的等效电路也可能对交流信号有相同的反馈，所以电化学阻抗谱方法是**不能唯一确定一个等效电路**的。如果电化学阻抗谱能够解释你的实验现象，那好，皆大欢喜；如果不能，兄弟（姐妹），放宽心，大家也都差不多。

### [](#等效电路 "等效电路")等效电路

我们讨论一个最简单的等效电路如下图。在本文不做具体推导，博主将在未来更新详细的电化学阻抗谱推导过程。

![](http://mozheyang.top/images/Electrocatalysis/eq-circuit.png)

以上的电路元件是会在下面的图中展现的。

### [](#Nyquist-plot "Nyquist plot")Nyquist plot

奈奎斯特图（Nyquist plot）是以阻抗的实部为横轴，虚部的**负数**为纵轴，图中的每个点代表不同的频率，左侧的频率高，称为高频区；右侧的频率低，称为低频区。由于其可以在图中直接读出一些比较关键的物理量（如溶液电阻、电子转移阻抗），因此在电催化分析中，也主要使用的是该图。

![](http://mozheyang.top/images/Electrocatalysis/Nyquist-plot.png)

Ref: Bard et al., Electrochemical Methods: Fundamentals and Applications; wiley New York, 1980; Vol. 2.

从图中主要可以看到两部分的信息：电子转移部分和传质部分。最近一篇锂电方向的电化学测试方法的综述文章叙述了从Nyquist图中能看出的几条信息：

*   电子转移阻抗半圆或圆弧的直径；
*   半圆或圆弧的数量电容的数量；
*   低频区（传质控制区）直线的斜率是类电容或电池（capacitive or battery-like）性质的定性标志。

Ref: Yang X, Rogach A L. Electrochemical Techniques in Battery Research: A Tutorial for Nonelectrochemists\[J\]. Advanced Energy Materials, 2019: 1900747.

对低频区直线斜率的意义，也有文章比较直白：认为斜率越大说明传质效果越好（越快）。虽然目前博主还没有找到相应的模型可以支持这一观点。希望有了解的读者提醒提醒我哈。注：本文通讯作者是戴黎明教授，碳基材料ORR的开创者之一，其观点可信度还是比较高的。

Ref: Hu C, Dai L. Multifunctional carbon‐based metal‐free electrocatalysts for simultaneous oxygen reduction, oxygen evolution, and hydrogen evolution\[J\]. Advanced Materials, 2017, 29(9): 1604942.

### [](#Bode-plot "Bode plot")Bode plot

阻抗波特图（Bode plot）由两条曲线组成。一条曲线描述阻抗的模（Magnitude, ）随频率的变化关系，称为Bode模图；另一条曲线描述阻抗的相位角（Phase, ）随频率的变化关系，称为Bode相图。通常，Bode模图和Bode相图要同时给出，才能完整描述阻抗特征。

![](http://mozheyang.top/images/Electrocatalysis/Bode-plot.png)

Ref: Giovannitti A, Nielsen C B, Sbircea D T, et al. N-type organic electrochemical transistors with stability in water\[J\]. Nature communications, 2016, 7: 13066.

[](#Tafel分析 "Tafel分析")Tafel分析
-----------------------------

可以从电化学动力学角度理解**Tafel斜率**，Butler-Volmer公式

考虑正向（forward）或逆向（backward）反应速率明显大的情况，可将Butler-Volmer公式简化

因此

此即Tafel公式。式中过电位和动力学电流密度（或）均取绝对值(即正值)，和称为塔菲尔常数，它的大小和电极材料的性质、电极表面状态、溶液组成及温度等因素有关。

> 注：可以通过换底公式处理和的关系，博主可没有写错哦。

需要注意的是，**以上通过Butler-Volmer公式对Tafel斜率进行理解的方式是可能存在问题的**。因为Butler-Volmer公式只在很小的范围内适用：必须要存在一种情况使得电化学反应界面应处于平衡状态，满足和’，此时正负反应速率相等，，也就有正逆反应速率常数相等，。在考虑覆盖率的情况下，覆盖率依赖于过电势，因此反应速率“常数”将不存在，而成为反应速率“变量”（potential dependent）。这种情形下，依赖于反应速率常数定义的Butler-Volmer公式是不适用的，但Tafel分析仍然可以解析出一定的动力学信息。

Ref: Shinagawa T, Garcia-Esparza A T, Takanabe K. Insight on Tafel slopes from a microkinetic analysis of aqueous electrocatalysis for energy conversion\[J\]. Scientific reports, 2015, 5: 13801.

应用举例：Tafel分析在HER中应用非常广泛，对于**确定反应决速步**具有重要的价值：30 mV dec\-1的Tafel斜率对应的是Tafel速控步，40 mV dec\-1的Tafel斜率对应的是Heyrovsky速控步，而120 mV dec\-1的Tafel斜率对应的是Volmer速控步。但在HER以外的其他反应中，都会存在覆盖率的问题。因此若作为反应机理的决定性证据，Tafel分析便显得不太有力了。

Ref: Shinagawa T, Garcia-Esparza A T, Takanabe K. Insight on Tafel slopes from a microkinetic analysis of aqueous electrocatalysis for energy conversion\[J\]. Scientific reports, 2015, 5: 13801.

另一方面，塔菲尔曲线也是一个直观地判别电催化剂活性的依据，对于OER和ORR而言，一般地有Tafel斜率越小，说明其活性越好（相同电流下过电位低）。