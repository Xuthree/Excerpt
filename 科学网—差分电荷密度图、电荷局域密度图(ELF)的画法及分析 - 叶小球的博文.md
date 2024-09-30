# 科学网—差分电荷密度图、电荷局域密度图(ELF)的画法及分析 - 叶小球的博文
关注：

1) 电荷密度图、差分电荷密度图及ELF的原理
2) 计算方法及参数设置

   影响ELF准确性的参数有哪些？  在计算效率与准确性/精确性之间权衡。

   Ecut, KPOINTS?

  3) 画法及单位问题

4) 差分电荷电荷密度图与ELF判断成键情形的各自优缺点

5)  软件开发->**背后物理化学意义的深入认识->具体应用**

**（软件开发者，这三方面都做得非常好；其他使用者，应将关注重点放在后面两个部分）**

       mulitiwfn, calypso,dfpt by Dr Huang

6) Isosurface 、slice、ELF、charge density 物理意义  

算法：

(1) 价电荷态密度图计算时，必须将NGX/Y/Z注释掉，不然结果有误

   ```bash
SYSTEM= scf for band and dos

PREC=Accurate

EDIFF= 1e-5

ENCUT= 600.0

ISTART= 0 ; IBRION = 2  

ISMEAR= 1 ; SIGMA = 0.2

#NGX = 24

#NGY = 24

#NGZ = 48
```

**题记(Thanks to Ruizhi)：** 
通常电荷密度图、差分电荷密度图及ELF看原子间 成键与否的效果都差不多， 也即用电荷密度图(来自自洽计算的CHGCAR，因算的是价电子，因此也称价电荷密度图)即可看出一般的成键情况，只是成键非常不明显时，才看差分电荷密度图及ELF。

       Slice是平面图，截取某一个面上的电荷密度分布；---**电荷密度等高线的2维投影**

      isosurfaces是立体图，晶格立体图中等电荷密度线(isosurface确定的值)的立体显示。--**电荷密度等高线**

        (理解电荷密度图背后的原理才是王道。)

单位问题：

单位都是a.u.，即electron/bohr^3。不管你用gview、VMD、chemcraft、molekel等任何可视化程序看，程序都不会自动修改格点数据值，单位也因此都是a.u.。

VESTA中是e/bohr^3,      [http://muchong.com/html/201208/4789432.html](http://muchong.com/html/201208/4789432.html)

**手册**

  CHG stores lattice vectors, atomic coordinates, and total charge densities multiplied by

the unit-cell volume, V . PAW one-center occupancies are added to them in CHGCAR.

Though both CHG and CHGCAR provide us with the same information on the valence

charges, the file size of CHG is smaller than that of CHGCAR owing to the lower accuracy

of the numerical data.

PARCHG has the same format as CHG has, storing partial charge

densities of a particular k-point and/or band. When these files are read in to visualize

isosurfaces and sections, **data values are divided by V in the unit of bohr3. The unit of**

**charge densities input by VESTA is, therefore, bohr−3.**

参数设置与比较测试：

/db/home/yexq/work/elec-dos/elf/sch10-cmcm/lowk-ecut  

```shell
cat  INCAR
```
```bash

SYSTEM=Static- self-consistent

PREC = Accurate    

ENCUT = 400

EDIFF = 1e-6

ISTART = 0; IBRION = 2

POTIM = 0.1

ISMEAR =1; SIGMA = 0.2

LELF=T

SYSTEM=Static- self-consistent

PREC = Accurate    

ENCUT = 600

EDIFF = 1e-6

ISTART = 0; IBRION = 2

POTIM = 0.1

ISMEAR =1; SIGMA = 0.2

LELF=T
```

./kpointstoMS POSCAR

The three qualities of k-point separation for CASTEP (1/Angstrom) >>>

          coarse.le..08 & medium.le..05 & fine.le..04

Please input the quality of Monkhorst-Pack grid (Default is .035) >>>

0.03  vs  0.04 vs 0.05

**问题：** 

(1) 不同晶面的差分电荷密度图如何画？

Utilities->2D Data Display->Coutours->Slice->hkl  
然后还有distance from origin可以调【？】

(2) VESTA 讀入 CHGCAR 後 會將所有的值除以體積，且單位是用 Bohr?

(3) **做出 3D isosurface 可以配合 Edit --> Lattice Planes 做剖面分析**  

**差分电荷密度图的画法**

[http://sanwen.net/a/netatqo.html](http://sanwen.net/a/netatqo.html)

使用的软件：VASP, VESTA, SshClient  

Definition

*   差分电荷密度 (deformation charge density) : 成键后的电荷密度与对应的点的原子电荷密度之差。通过差分电荷密度的计算和分析，可以清楚地得到在成键和成键电子耦合过程中的电荷移动以及成键极化方向等性质。
    
*   二次差分电荷密度 （difference charge density） ：指同一个体系化学成分或者几何构型改变之后电荷的重新分布。
    

一 差分电荷密度计算

Charge density difference of system AB: **∆ρ = ρAB − ρA − ρB**

> NOTE:
> 
> 1.  AB, A, B 需放在相同大小的空间格子。
>     
> 2.  In calculation of the latter two quantities, the atomic positions are fixed as those they have in the AB system.
>     
> 3.  Some NGX NGY NGZ
>     

**结构优化后**的自洽计算

mkdir ABmkdir Amkdir BINCAR fileSYSTEM=XISTART=0ENCUT=350EDIFF=1E-5IBRION=-1 POTIM=0.25NSW=0 # Only electronic-SC loops are performedEDIFFG=-1E-2ISMEAR=0SIGMA=0.05PREC=ACCURATEISIF=2NPAR=4#LWAVE=FALSE#LCHARG=FALSE # CHGCAR is writtenLREAL=Auto#IALGO=48ISYM=0NGX=x # 增加NGY=y # 增加NGZ=z # 增加

*   NGZ NGY NGZ 的获取：在结构优化的 OUTCAR 文件中搜索，设置环境变量后输入ngx获得
    

vi ~/.bashrcalias ngx="grep -A3 'NGX' OUTCAR"source ~/.bashrcPOSCAR filecp opt/CONTCAR POSCAR

A, B 各自保留优化后的 POSCAR 部分，编辑删除 POSCAR 文件

vi POSCAR:set nu # 获取编辑行数:x,y d # 删除 x 行至 y 行:wq

POTCAR 与 POSCAR 信息相符即可，KPOINTS 与自洽计算时相同，提交作业，计算完成后，获得三个 CHGCAR 文件，使用 SshClient 导出。

二 图像处理

1.  使用软件 VESTA 打开 AB 的 CHGCAR
    
2.  Edit - Edit Data - Volumetric Data - Import (A CHGCAR) - Select Substract from current data - OK
    
3.  Edit - Edit Data - Volumetric Data - Import (B CHGCAR) - Select Substract from current data - OK
    
4.  Edit - Bonds - New - search molecules 和 Do not search atoms… - Max.length=3 - OK
    
5.  取消勾选 Volumetic date 下的 Show sections
    
6.  Click Properties… to modify the isosurface
    
7.  Select the format whatever you like and save.
    

**原理：VASP中电荷密度差的计算-用来分析成键前后电荷转移**

[http://pan.baidu.com/share/link?shareid=683727537&uk=287581123](http://pan.baidu.com/share/link?shareid=683727537&uk=287581123)

[http://emuch.net/bbs/viewthread.php?tid=7392808&fpage=1](http://emuch.net/bbs/viewthread.php?tid=7392808&fpage=1)

在文献中，经常见到利用电荷密度差(charge density difference)来分析成键的过程或是结构弛 豫前后电荷的转移，当然，也有人用来分析基态和激发态的电荷分布差别等情况。不过，尽 管似乎都叫“电荷密度差”，但具体的定义是不大一样的。    
     这里主要是讨论在 VASP 中如何得到用来分析成键前后电荷转移的电荷密度差。    此时电荷密度差定义为：    
     delta\_RHO = RHO\_sc - RHO\_atom  

其中 RHO\_sc 为自洽的面电荷密度【什么叫面电荷密度？！】，

而 RHO\_atom 为相应的非自洽的面电荷密度，是由理想的原子周围电荷分布堆彻得到的，即为原子电荷密度的叠加(a superposition of atomic charge densities)。  
  
 不同晶面的 RHO\_sc 可由自洽计算的CHG或CHGCAR得到；

 而计算 RHO\_atom 所需的 CHG 或 CHGCAR 可由下述非自洽计算得到:  
 仍使用原来自洽计算时的四个输入文件，但INCAR中需要设置 ICHARG=12 和 NELM=0，其他设置不变。  

【设置ICHARG=12，产生原子的电荷密度，见手册介绍：

ICHARG=12:   Non-selfconsistent calculations for a superposition 【重叠/ 叠加】of atomic charge densities.  This is in the spirit of the  non-selfconsistent Harris-Foulkes functional.  】  
      需要特别注意的，应保持前后两次计算（自洽和非自洽）中的 FFT-mesh 一致。因为，只有维数一样，我们才能对两个RHO作相应的矩阵相减。不过，只要按上一段提到的设置方法做就行了，无须特别增加别的设置。

  
  数据处理(后面详细叙述)：  

      (1) 矩阵相减：使用 MatLab 或是自已写个小程序。    作图：Origin 或 MatLab。

      (2) VESTA绘图

**计算步骤：**   

1) 总的CHGCAR就是静态计算得到的

(不同晶面的 RHO\_sc 可由自洽计算的CHG或CHGCAR得到)

SCF-INCAR

SYSTEM= scf for band and dos  
PREC=Accurate  
EDIFF= 1e-5  
ENCUT= 600.0  
ISTART= 0 ; IBRION = 2    
ISMEAR= 1 ; SIGMA = 0.2  
#NGX = 24  
#NGY = 24  
#NGZ = 48

2) 分的电荷密度(CHGCAR)的计算

(  仍使用原来自洽计算时的四个输入文件，但INCAR中需要设置 ICHARG=12 和 NELM=0，其他设置不变。)

  SYSTEM= scf for band and dos  
PREC=Accurate  
EDIFF= 1e-5  
ENCUT= 600.0  
ISTART= 0 ; IBRION = 2    
ISMEAR= 1 ; SIGMA = 0.2  
ICHARG=12    # a superposition of atomic charge densities  
NELM=0  
\# NGX =   28   【有时需指定NGX, NGY, NGZ值与前面自洽计算中一致】  
\# NGY =   28  
\# NGZ =   28

**数据处理及差分电荷密度图画法：** 

先导入总的电荷密度文件，然后分别减去单独的电荷密度，Edit-Edit Data-volumetric Data。

[http://muchong.com/html/201505/8951521.html](http://muchong.com/html/201505/8951521.html)

注意：

1）导入vesta的时候把CHGCAR改为CHGCAR.vasp

2）报错：Number of data grids in the 3D data sets do not match each other

NGX, NGY, NGZ, NGXF, NGYF, NGZF的值要固定

CHGCAR就是静态计算得到的，分别计算分的电荷密度时INCAR参数的设置，以及KPOINTS文件都要与静态计算一致。（ 但INCAR中需要设置 ICHARG=12 和 NELM=0，其他设置不变。有时，需指定NGX, NGY, NGZ值与前面自洽计算中一致）

3） 你说的那个e/V^3是e/埃^3还是e/波尔半径^3  

![](https://blog.sciencenet.cn/static/js/grey.gif)

![](https://blog.sciencenet.cn/static/js/grey.gif)

![](https://blog.sciencenet.cn/static/js/grey.gif)

**电荷密度切面(Slice）的画法-**电荷密度等高线的2维投影****

如下图及下面的步骤描述

(1) 之前，可按shift加选择箭头，选定三个原子;

(2) Utilities->2D Data Display->Coutours->Slice->hkl  
(3) 再摁，Calculate the best plane for the selected atoms，则hkl，自动出现所选择原子对应的晶面指数

(4) 可扩胞，即在该平面(010)的x,z方向扩胞，得到包含更多原子的电荷密度图，便于截取

(5) 可参考Z(min)和Z(max)数值，通过Saturation levels调节左边Color bar 的显示范围,使得图像清晰，并使得刻度好平均分配，便于标记

(6) 也可使得ELF以线的形式显示，点击Contours，勾选Draw contour lines，调节间隔Interval，可得到一系列圆圈线，最红色的底圈线代表Max，其外围每一圈线，以Interval递减；可选Linear， 或log格式的刻度标识

**![](https://blog.sciencenet.cn/static/js/grey.gif)** 

**等电荷密度面(Isosurfaces）的画法-**电荷密度等高线图****

**1）首先是3D显示，可以如下修改或者调整：** **Objects->Properties->Isosurfaces->Isosurface level处的数值做适当调整，你将显示出漂亮的电荷密度等高线图****。** 

**2）Utilities->2D data display将使你可以显示出电荷密度等高线的2维投影，输入你想显示的晶面指数，当然你可以实现选定一个原子，输入晶面指数之后，再点击Calculate the best plane for the selected atoms，你将获得通过这个原子，且是你选定晶面上的2D电荷密度。** 

**![](https://blog.sciencenet.cn/static/js/grey.gif)**

**对比分析-my own：** 

**![](https://blog.sciencenet.cn/static/js/grey.gif)** 

![](https://blog.sciencenet.cn/static/js/grey.gif)

![](https://blog.sciencenet.cn/static/js/grey.gif)
  

![](https://blog.sciencenet.cn/static/js/grey.gif)

**处理步骤：【需要商议的步骤】---另一种定义电荷密度差**  

[http://muchong.com/html/201512/9799282.html](http://muchong.com/html/201512/9799282.html)  【是这样做的吗？ ask Ruizhi】

用VESTA 做VASP差分电荷（C-A-B）图方法：  
1、导入 C 的CHGCAR（拖拽到vesta）面板框中  
2、Edit——>Edit data——>Volumetric Data————>Import (选择A 的CHGCAR)——>**Subtract  
from current data**——>OK

  
3、Edit——>Edit data——>Volumetric Data————>Import (选择B 的CHGCAR)——>Subtract  
from current data——>OK  
注意：A,B,C三个文件的格点要一一对应。

**文献分析：** 

(1)Cr合金化Mg\_2Ni氢化物能量与电子结构的第一性原理研究

      为了更直观更形象地理解Mg2NiH4和Mg(I)CrNiH4中原子间的成键情况，选取2 个晶胞(002)面  
的差分电荷密度图(是指原子---组成体系(团簇)之后电荷的重新分布， 可以很清楚地看出体系中原子的成  
键情况)如图4 所示。

     发现在Mg2NiH4晶胞中，H-Ni间的电荷密度高，而H-Mg 及Ni-Mg 间的电荷密度低，故H-Ni 之间存在较强的相互作用，而H-Mg 及Ni-Mg 间的作用不明显；Cr 合金化Mg2Ni 氢化物(见图4b)后，发现H-Ni 间的电荷密度降低，这说明H-Ni 键的作用减弱, 故有利于释氢， 体系的解氢能力增强。

![](https://blog.sciencenet.cn/static/js/grey.gif)

（２）　放热型金属合金化对钒基贮氢材料性能影响的理论研究

　　在氢化物 VH2 中加入放热型金属 Mg、 Ca、 Ti、 Zr 以后,V51M12H64(M=Mg、 Ca、 Ti、 Zr)中最 中心的 V和 H 之间电荷密度重叠性增强, 表明 V-H 之间的相互作用增加; 同时 M(M=Mg、 Ca、 Ti、 Zr)和 H 之间的电荷密度重叠性较大, 表明 M-H(M=Mg、 Ca、 Ti、 Zr)之间也有较强的相互作用。所以, 放热型 金属合金化后, 由于放热型金属本身 Mg、 Ca、 Ti、 Zr 可以吸氢, 同时又使 V-H 之间的相互作用增强, 氢 化物可以结合更多的氢, 从而增加了贮氢量。但氢 化物不容易放出氢, 即吸放氢性能减弱。这与实验 结果及前面的结论一致。

（３）金属氢化物力学性能的第一性原理研究

　    金属氢化物 的稳定性与Fermi能级处电子态密度的分布密切相关。 不稳定的金属氢化物如TiH2和HfH2在Ferm能级处一般有电子态密度尖峰出现。

       如果电子被束缚在H原子的局域势场附近，则体系的稳定性变差(可由差分电荷密度图判断)。

       当金属氢化物体系总的电子态密度在Fermi能级处出现奇异性的尖峰，或者电子被束缚在H原子的局域势场附近时，体系的稳定性差， 有可能发生相变。

     在低温下MH2(M=La,Nd,Gd,Tb,Y,Dy,Ho,Er,Lu,Sc)具有稳定的氟化钙结构，而MH2(M=Ti,Zr,Hf)的氟化钙结构不稳定。对于稳定的金属氢化物而言， 抵抗体积形变、切应变和拉伸(或压缩)形变的能力由弱递增的顺序为LaH2，NdH2,GdH2,TbH2,YH2,DyH2,HoH2,ErH2,LuH2,ScH2。

![](https://blog.sciencenet.cn/static/js/grey.gif)

![](https://blog.sciencenet.cn/static/js/grey.gif)

（4） Hydride-Based Electride Material, LnH2 (Ln = La, Ce, or Y)

Figure 3a shows the electron  density map of LaH2 in the energy region from −1.5 eV to EF.

 Surprisingly, conduction electrons populate the cage site and  show strong anisotropic shape into the six neighboring La ions.

        As seen in Figure 3b, the (110) plane obviously contains a large  contribution from La 5dz2 (eg, red part). Thus, we may regard  LaH2V as an inorganic electride in which **the electrons reside in  the center of the Ht8 cube**. However, the electrides reported to  date are usually metal-rich compounds having cages surrounded  by cations.2,3  

Thus, we need to check the calculated electron  distribution carefully by estimation of the electron localization  function (ELF) map in order to get information about  unshared electrons.

 Figure 3c shows the (110) plane in the  ELF map. It shows clearly the ionic bonding in the compound  and that there are no lobes【叶片】 in the cage originated from lone pair  (LP) electrons. Td site symmetry of the anion【阴离子，H- ions】 site prevents the  appearance of the LP state. 【为什么】

     ![](https://blog.sciencenet.cn/static/js/grey.gif)

（5） 差分电荷密度图--透明钠：Transparent dense sodium

   ![](https://blog.sciencenet.cn/static/js/grey.gif)

Difference charge density (e  Å\-3）; crystal density minus superposition of isolated atomic densities) plotted in the (110) plane at 320GPa (6.50Å\-3  per atom)

Thus, the insulating state in Na-hP4 arises from the strong localization of valence electrons in the interstices of Na-hP4 (Fig. 4c and Supplementary Fig. 6) enabled by p–d hybridization.

（5） Pressure-stabilized superconductive yttrium hydrides

![](https://blog.sciencenet.cn/static/js/grey.gif)

Valence electrons localization was found  between the two neighbouring H2 atoms while absent between H1 and H2 atoms (Fig. 2e). This indicates  the presence of both molecular “H2” and monoatomic H in tI10-YH4.

Despite the longer distance,  covalent interaction between H atoms is clearly visible from the localized valence electrons between the  H atoms (Fig. 2f).

![](https://blog.sciencenet.cn/static/js/grey.gif)

 The empirical consideration  is support from quantitative calculations of the difference of the electron density of tI10-YH4 to that of  a hypothetical structure consisting only H sublattice.

  It is clearly shown in Fig. 3(a,b), that there is no  electron density (covalent bond) between the two H2 atoms in the **pure H structure**. However, when the  Y atoms were present, localized electrons are found between two H2 atoms. Therefore, the charge transfer from Y to H2 is responsible to the formation of molecular “H2” in YH4.

 Similarly, the foramtion of “H6”  hexagons in YH6 results from the accommodation by H of excess electrons from Y atom (Fig. 3c,d).

For comparison, a survey of molecular “H2” in most hydrogen-rich compounds only show a slightly  elongated H-H bond length than pure solid H2. Examples are 0.87Å  in GeH4  , 0.79Å in SnH4  , 0.84Å in SiH2(H2)2  , 0.76 Å in LiH2  and 0.8 Å in NaH9  .

![](https://blog.sciencenet.cn/static/js/grey.gif)
  

**网络问答：** 

(1) AA:: 如何在vaspview中画电荷密度等高线，图是我画出的电荷密度，想在上面添加等高线，求大能帮助  
QQ:::

vesta:  
Utilities->2D Data Display->Coutour  

(2) AA::

怎么调节切片方向呢？ 我切出来是这样的，调节ranges of fractional coordinates中参数都不管用，是不是调节其他参数啊  

QQ:::

**Utilities->2D Data Display->Coutours->Slice->hkl  
然后还有distance from origin可以调 \[距离原点的距离？\]**  

QQ:::

用lev00来分析也很好。。。比较方便。  

对的，你安装好lev00后，里面会有很多选项，包括可以任意指定切面。。  
然后再用origin画。  

(3) vaspview和vesta比较

比较了一下，感觉还是vesta出来的图美观和操作方便些。vaspview里面的功能太少了。vesta切slice也很方便，不管z轴还是x轴还是任意方面的面都可以切。vesta中你的线消失是因为你切的面没落在原子上当然等高线就出不来了。

       切时先用shift选中三个原子，切面就容易些。貌似切出的面只能存成图片格式，export 2D data不知为什么是灰色，懂vesta的讲下  

QQ:::可切任意面，注意切面到原点的距离，就那么几项，仔细看看，你一定能搞定  

(4) 电荷密度差显示时怎么把结构放在里面呢？  

终于搞定了，原因出在CHG与CHGCAR格式的差异上，原来做差时用的是CHGCAR，差值也按CHGCAR的格式保存的，但是VESTA认的是CHG的格式，用CHGCAR的格式就出现了上述情况，现在按CHG格式保存电荷密度差，结果就正常了。  
(5)  与MS的比较

其实没有 ms 方便 ，但是我经常在 linux 下，所以我感觉  VESTA+isotropy 是目前最好的选择了  

     建议调整模型就不要用VESTA了， 周期性结构建模也不要用gaussview，用MS。  
第一个问题就可以在MS中把晶胞调整参数后rebuilt一下就可以了；第二个问题建模用MS的话就更方便了，建晶胞--切表面--做真空层--建超胞  

QQ:::   boundary建立模型，然后再POSCAR中将相应原子删除  

（6） 能不能告诉我具体第几章第几节谈到了**为什么不能导出2D电荷密度数据的问题？**我看了Manual上面13章仅仅一笔带过说这个选项是导出数据。  

QQQ:::**"15.7 Exporting 2D data**  
To export 2D data of the specied slice as a text le, choose File" menu ) Export 2D Data. . . "  
in the 2D Data Display window. No 2D data les can be output on selection of style (hkl) plane  
in the bounding box" in the Slice Properties dialog box."  

（7） 我现在想用脚本来处理态密度，之前用过论坛上的dos-procar.f，但是都是处理s,p,和 d的，这个脚本没有针对s,py,pz,px,dxy,dyz,dz2,dxz,dx2的，所以请大家帮帮忙，我知道送金币很俗，但也只有金币能聊表谢意了  

(8)   VESTA中怎么能调出等高线图的标尺呢，就是右边的标尺怎么获得呢？谢谢

**【如何把Slice图放到结构当中，像下面这样？】**   
[![](https://blog.sciencenet.cn/static/js/grey.gif)
](http://data.keyan.cc/edu/01/59/842155_1304247302_870.jpg)  

QQQ:::可以呀 在2D视图里面，可以设置等高线和色彩的  

QQQQ3::::

[![](https://blog.sciencenet.cn/static/js/grey.gif)
](http://data.keyan.cc/edu/6b/06/995125_1304340231_375.jpg)  
看到那个saturation levels 吗？最大的是2.98023（100%），最小的是0.5871（0%），而右边的标尺是根据那个把最大和最小分成十份，你自己算一下标上去就行了  

**（9） 立体图中，就是Isosurface，比如at a value of 0.75；画出来之后没找到标尺的选项啊，请问能给出立体图的标尺吗？谢谢比如说下图**

****【如何把Slice图放到结构当中，像下面这样？】****  
[![](https://blog.sciencenet.cn/static/js/grey.gif)
](http://data.keyan.cc/edu/65/b2/842155_1304343238_845.jpg)  

QQQQ::

这个图可能不是vesta做的  
        我只能在截取一个晶面时显示相应的isosurface【isosurface的物理含义？】，但此时section中**小于该值的就不会显示。** 要做可能也是两张合成的。对于标尺是针对section的，这个是不变的，你在2D里面看好数据，把标尺弄出来，就可以了。  
对于这个图可以这么做：  
1，先从2D里面吧标尺弄出来放在图片一中，,  
2，然后在boundary里面设置晶体的晶面，返回带properties里面选择isosurfaces，在这里修改isosurface level为0（里面的颜色不会随你的这个值而改变，设置为零（你的电荷的最低值，也就是你标尺最下端得值）一般能把所有的数据显示出来），这样可以得到图二，  
3，返回主界面，把Volumetric data 里面的 show section前面的钩去掉，再到properties里面选择isosurfaces，在这里修改isosurface level为设置为你想要的值，但此时这个颜色会和那个标尺不一样（默认的是黄色）。你可以现在标尺中找出你想要值的颜色，使用其他的程序读取RGB值，然后再在color中填上就可以。如果你都不了那个RGB值的话，就靠你的眼睛去判断，点后面那个按钮。可以选择你认为是一样的值。得到图三  
4，在画图版中，使用那个白色透明模式（工具栏中最下面有两个大的黄色的下面的一个），把这几张图合并起来就行。  
在整个过程中你不要去调图像看看图像怎么样，或者你看好之后用orientation，回到以前的视图角度，这样可以保证两张图是可以完全重合的  

（10）空位、间隙原子 密度图的处理

刚刚接触VESTA，想用VESTA处理VASP的运算结果，有两个问题求教虫友：  
（a）vasp算出CHGCAR后可以导入VESTA，给出整个超胞的电荷密度图，但我只关心其中某几个原子附近的电荷密度分布，求教高手，能否只显示我关心的这几个原子上的电荷密度，如果能够，如何实现？  
（b）超胞计算中引入了空位，能否在图示中用虚线球给出该空位的理想位置，并且用虚线表示出其本来的成键情况，如果能够，如何操作？  
非常感谢！  

QQQ:::vesta 似乎是没有办法只显示几个原子的电荷密度的，只关心几个原子，是不是可以考虑差分电荷分析？就是把你要关注的几个原子作为一个体系（去掉其他原子,仍用原来晶格）保持坐标不动，单独算一下，得到CHGCAR1， 然后把其他部分的原子（去掉关心的原子）保持坐标不变再单独算一下，得到CHGCAR2， 然后用总体系的CHGCAR-CHGCAR1-CHCAR2 得到CHGCAR——difference。 然后再用vesta 做CHGCAR-difference。但和你的要求可能有点区别。

引入空位的话，vesta 似乎不能做到你的要求，但是你可以在vesta里面作结构图，然后导出图片，在ppt或者Photoshop 中修改图片。可以得到满足你要求的图片

QQQ：：：：

只显示几个原子而不显示其他原子的电荷密度很奇怪的，没有太多意义。我建议你采用截面的方法来显示某几个原子附近的电荷密度分布  

（11）再用VESTA画**部分电荷密度（partial charge density）的时候，isosurface level 一般取值为多**少。有什么原则吗,比如最大值的多少分之一。我刚刚学不清楚。请高手指点下，提前谢  

（12）A：：：选择适当的平面使要观察的原子的ELF落在这个平面上

在用VESTA做ELFCAR的2D图的时候要选择适当的平面使要观察的原子的ELF落在这个平面上，我的问题是用VESTA作2D图时显示不出原子，那么怎么自己加原子呢？比如下面两个图：图1是3D图，其中紫色的是Zn，灰色是O，黄色是cu，图2选取的001面，那么在图2的那些区域，怎样确定那个是In产生的哪个是粗cu产生的呢？  
又或者：怎样让001面上只显示出cu及其周围的Zn和O？  
Q：：：记得slice里面不仅可以选哪个面，还可以定义面的位置，比如在最底下（z=0），或者中间（z=0.5）……  
应该是可以定义的。

你图片上看到“slice”这个键了吗？点击就出现那个对话框里，h、k、l 三个指数定义面，下面有个“Range of projection”，计算定义原点到面的距离，一个的以angstrom为单位，另一个是分数坐标吧。  
不知道怎么回事，上传不了图，不好意思啊。  

（13）电荷密度图单位问题：CHGCAR的单位应该是e, 要获得真正的电荷密度需要再除以晶包体积【怎样除以晶胞体积】

 VESTA读取的CHGCAR单位是e/bohr^3 如果想要改成e/A^3 就要在Edit--> Edit data--> volumetric  data --> import...  ---> convert the unit 里面选择bohr^3 to  angstrom^3    
我想问的是有没有简便的设置不用每次都这样改？太麻烦了！  

QQ：：

问一下楼主，你从哪里知道CHGCAR的单位就是e/Bohr^3呢？  
我最近看了一些相关资料知道，CHGCAR的单位应该是e, 要获得真正的电荷密度需要再除以晶包体积，如果你晶胞体积采用的是Bohr为单位，那么得到的电荷密度就是以e/Bohr^3为单位i，反之，如果晶胞采用A为单位，那么电荷密度就应该是以e/A^3为单位。

VASP说明书上有这样一句话：  
“CHGCAR file  
This file contains the lattice vectors, atomic coordinates, the total charge density multiplied by the volume  on the fine FFT-grid (NG(X,Y,Z)F)”  
“ Please do not forget to divide by the volume before visualizing the file!”

**(14) 差分电荷密度的另一种算法：Δρ=ρ(AB)-ρ(A)-ρ(B)**  

根据教程算了电荷密度，就是Δρ=ρ(AB)-ρ(A)-ρ(B)，用vesta扣除A的时候提示点数不一样: Number of data grids in the 3D data sets do not match each other。算AB、A和B的CHGCAR时候INCAR和KPOINTS设置都是完全一样的，A和B的POSCAR和POTCAR分别由AB的输入文件删掉B或A得到的，感觉方法没有错误，扣除A的时候提示上面那个错误，扣除B的时候没有提示![](https://blog.sciencenet.cn/static/js/grey.gif)
，不知何解？

Q：：：

会不会是因为INCAR里面某些参数是使用默认值，而这些值是根据POTCAR读来的，结果因为POTCAR不同而导致INCAR也发生了变化？比如ENCUT是可以不写的，如果3个INCAR里面都没有这一行，而是采用PREC=Accurate的设置的话，那么AB那一次计算的ENCUT会跟ENMAX比较高的那个（比如B）的一致，而ENMAX比较低的那个（比如A）算出来的就是另一回事了。不妨把那些可以手动设置的参数自行设置一下，比如ENCUT、NGX、NGY、 NGZ和NGXF、 NGYF、NGZF之类的…当然我理解也不是很深，不确定是不是这种情况。  

**VESTA软件的用法**讨论：摘自大家[identation](http://emuch.net/bbs/space.php?uid=842155)：

http://emuch.net/bbs/viewthread.php?tid=6422652&fpage=1  

请大家交流一下VESTA软件的用法  

这个取值是你自己取得，就看画出的图能不能描述出问题的所在，比如你中心原子电荷是0.5，外部空间是0.2，那你就不能将0-1取为一个颜色，因为这样你无法分辨外部中心原子和外部空间。VESTA作图的单位我还真不清楚，我做密度图的时候都是用lev00然后导出数据用origin画的，不过好像VESTA的单位和VASP的单位不太一样，这个你自己查一下  

**a）首先，VESTA的作用可以是--画晶体结构，当你知道了晶体的空间群，原子的Wyckoff坐标之后，就可以用VESTA画结构了：** 

(1)File->New Structure->Unit cell->Space group处输入空间群的编号，**Setting处可以对该空间群编号下的不同表述方式进行调整，例如No. 62号空间群属于正交晶系，输入62在Space group处之后，如果setting是1，则显示为Pnma，如果setting为3，则显示为Pbnm，实际上这两个空间群是等价的。** 

（2）接下来，structure parameters处，点击New，然后在Symbol处选择你想输入的元素，Label处输入你想显示的标签，同时把x，y和z后面的空格处填上该元素的坐标，则该原子输入完毕，再点击New，输入其他原子坐标，直至完成。

（3）完成之后，你将看到你所画的晶体结构图。

（4）对于该图，你可以有多种显示方式，例如键线式，八面体或四面体等等。这需要如下步骤来做到--点击Edit->Bonds->点击New，然后将A1和A2处填上成键的原子，例如考虑体系BiFeO3，如果想显示Bi-O八面体，则可把A1处的原子写成Bi，A2处写成O，然后注意调整Min. length和Max. length处的数值，这两个值的意思是，如果A2原子到A1原子的距离在Min. length和Max. length数值之间，则认为这两个原子成键，两原子直接连一线。然后点击OK即可。

（5）如果要八面体或者四面体显示该晶体，在完成前4步之后，**点击Style按钮，选上Polyhedral，则可以以多面体显示**。

（6）Objects->Properties处可以改变显示的风格--例如可以改变多面体的颜色，可以使用超晶报显示等等。

      完成上述步骤后，点击File->Export data，你将把你画的晶体以不同格式导出，包括cif格式以及vasp格式等等，vasp格式导出的结果，第一行是你造的晶体的名称，由你来取，默认为New structure；第二行一般为1.0，是基矢的缩放系数，接下来的三列是基矢，也就是格子的abc轴的描述矢量；接下来是元素种类，接下来是不同种类元素的原子个数，接下来是原子坐标--导出的这部分数据可以作为VASP的POSCAR，也可以经过拷贝黏贴之后修改格式作为PWSCF,ABINIT,ELK等软件的晶体坐标输入。

此外，也可以将所画晶体用图片格式导出，可以导出矢量图也可以导出普通的图片，请点击File->Export Raster Image或File->Export Vector Image

  
**（b）VESTA可以显示电荷密度，**

        VASP的计算结果，**直接用VESTA打开CHGCAR或CHG文件，**你将获得显示其原子以及电荷密度。这个也可以调整，（1）首先是**3D显示**，可以如下修改或者调整：Objects->Properties->Isosurfaces->**Isosurface level处的数值做适当调整**，你将显示出漂亮的电荷密度等高线图。

（2）Utilities->**2D data display  将使你可以显示出电荷密度等高线的2维投影，输入你想显示的晶面指数**，当然你可以实现选定一个原子，输入晶面指数之后，再点击Calculate the best plane for the selected atoms，你将获得通过这个原子，且是你选定晶面上的2D电荷密度。

（3）你同样可以试着在Edit->Lattice planes处做类似操作，获得在原晶体结构上显示的某一晶面的2D电荷密度投影。

**（c）改变基矢：** 

     对于一个已经用VESTA打开的晶体结构，你可以点击Edit->Edit data->unit cell，然后点击Option按钮，在弹出对话框的Rotation matrix P**处输入基矢的变换矩阵**，你将对晶体的基矢作一定的变换。

最最简单的玩法是，当你把Rotation matrix P第三行第三列的那个数值由1改成2的时候，你将获得z方向的超晶报；同理如果把第二行第二列的数值由1改为2，你将或者y方向的超晶胞。

VESTA是一个功能很强大的晶体建模显示工具，我个人认为其基本上可以囊括MS的可视化模块，而且显示起来的图更好看。也希望大家多多交流，说说你所知道的VESTA的用法或作用。有金币悬赏！

**(d) 講一些關於 isosurfaces 分析: **這是因為 VESTA 讀入 CHGCAR 後 會將所有的值除以體積，且單位是用** **Bohr?**  
1) VESTA 可以直接讀取 vasp 的 CHGCAR，意思當然就是說只要檔案格式內容跟 CHGCAR 一樣  
    VESTA都可以直接讀，比方  
              ELFCAR (electron localization function)  
              LOCPOT (local potential)  
              PARCHG (partial charge density)    再延伸出去，對任何空間相關資料只要把格式轉成像 CHGCAR 般~即可給 VESTA 讀取  
    (若發生讀取錯誤，請把副檔名改成 .vasp 即可)  
2) 在 isosurfaces 分析過程有許多參數可以調整，東調西調~調完一大堆參數後  
    建議將它存檔~存成VESTA 自己的 .vesta 檔 (File --> Save As)  
    下次打開只要開這個 .vesta 檔就可以出現調整後的結果  
    這個 .vesta 檔是可以讀取，裡面有一大堆資料  
    第九行 IMPORT\_DENSITY 1 的下方~第十行  
    就是你所給予的 CHGCAR ~  
   也就是說~如果你今天要分析 PARCHG，你可能有很多檔PARCHG.0001, ... PARCHG.xxxx  
    每個檔案的結構是一樣，但電荷分部不一樣  
    這時你只要先處理好一個檔案~接著把這個檔案複製成許多檔案  
    然後修改每個檔案的第十行~來改變 import file position  
  就可以非常快速地分析 partial charge3) 在 VESTA , Properties --> Isosurfaces 介面  
    會顯示 isosurfaces 的最大與最小值  
    這個值跟你所給予的 CHGCAR 內容是不一致的  
    這是因為 VESTA 讀入 CHGCAR 後  
    會將所有的值除以體積，且單位是用 Bohr  
    (VASP 所有長度單位都是用 Angstrom)  
    因此 VESTA 顯示的最大最小值，是除以 體積(unit in Bohr^3) 後的最大最小值  
4) 把 CHGCAR 丟入 VESTA 之後  
    VESTA 很貼心的會立刻幫你選了某個 isovalue  
    這個值不是隨便選的  
    值的公式在手冊的 16.7 Default isosurface level  
    在這公式內有個參數 n ，手冊上說明這個 n is a parameter to adjust d(iso)  
    n 可以在 Edit --> Preferences 內的 Default isosurface level 調整  
5) 做出 3D isosurface 可以配合 Edit --> Lattice Planes 做剖面分析  
    這個切面也是可以調整設定  
    在 Properties --> Sections**

**VESTA画局域电荷密度图 ELF**

**[http://muchong.com/html/201508/9267921.html](http://muchong.com/html/201508/9267921.html)**

**VESTA可以切片**  
Utilities---2D DATE   选择面指数、厚度等，调整等值面，就可以了

就在VESTAT中调整啊    
1、体材料上，调整Isosurface level  
2、切面：只能调整显示    调整sauration levels，一般按照后边的百分比调整（先0-100看看再微调）

**电子局域函数源表征:电子的局域化分布特征:**用于****确定成键类型和找出孤对电子分布****的理由。****

****[http://blog.sina.com.cn/s/blog\_6813cb430100tvka.html](http://blog.sina.com.cn/s/blog_6813cb430100tvka.html)****

**电子局域函数源于 J. Chem. Phys. 92(1990)这篇文章，作者定义了一个ELF（Electron Localization Function，电子局域密度函数），以此来表征电子的局域化分布特征。优点是无需计算局域分子轨道，计算量较小。  
       具体的推导过程请大家自行查看文献，我们主要关心的是它的用途，也就是为什么它能被用于**确定成键类型和找出孤对电子分布**的理由。  
        按照定义，ELF的值在０到1之间，取上限值1表示电子完全局域化，而0值可能表示电子完全离域化（当然也可能是表示该处没有电子![](https://blog.sciencenet.cn/static/js/grey.gif)
 ）而ELF=1/2这个中间值则表示该处电子形成了类似于电子气的电子对分布(原文是electron-gas-like pair probability)。  
        VASP可以计算ELF的分布情况，数据可以在ELFCAR中找到，可以用VASPVIEW或VESTA等软件作图查看。** 

**差分不同的定义：** 

[http://muchong.com/html/201607/10515384.html](http://muchong.com/html/201607/10515384.html)

(1) deformation charge density   【MS中】  
这个的定义是用总的电荷，减去体系中原子电荷，就是他问的你这个电荷是非自洽电荷，is the non-self-consistent charge density

  
(2)  另外，这种的差分一般定义是： scf电荷减去非scf电荷

（2） 差分电荷密度从Gaussian的解释

[http://blog.sina.com.cn/s/blog\_76cd5d7c0102vnzl.html](http://blog.sina.com.cn/s/blog_76cd5d7c0102vnzl.html)

     在量化图形中，电子密度差Δρ尤其重要！它应是传统的原子净电荷概念的扩大和空间化，它直接从MO而来，真实地、科学抽象地表现了电荷在分子空间分布的净变化，不再只将净电荷人为地局限、归结、划分在某个原子上。

      我们如果仅看分子轨道图、电子密度图，象上面的HF分子的σ键电子密度图，它是据HF分子第3个MO即σ键作的电子密度图，它们那么浑圆、憨厚、质朴，很难看出什么。它是一片云，难察其意。

     而一旦作出了电子密度差，成键原子前后变化、分子之间络合、氢健、吸附等，电子增减的净变化，被鲜明地、细微地突出了，可以发现许多新东西、意想不到的东西，从而引人入思。

       如从上面的HF分子的σ键电子密度差图Δρ，并参照MO系数来看,这个σ键可以分析为三个内含：

      第一、原子以基组形式参与成键，这基组或者可以看作是广义的杂化。

         对于成键来说，基组一方面是为了弥合不同AO的波形的差距，融合成平滑、完整、统一的MO，另一方面是为了应对键间电子向键轴的聚集、收缩和原子之间的电子转移。

          基组不仅是计算需要的一组数学表达式，当它们分配了系数、安排了电子，它就形成了一个新的广义杂化的空间AO，是具有能级值或说是电负性的，是有确定物理意义的，它是成键电子的载体、基础。

       第二、电子在不同电负性原子之间转移。

       从HF分子Δρ图可以清楚地看到，H缺失了大量的电子，导致电负性升高；F增加了大量的电子，导致电负性降低，直至二者电负性相等、均衡。【Roald关于电负性讨论的文章】

       有趣的是，通常说sp杂化，用增大的一瓣去成键以增大重叠能力。但在HF中，MO的基组中s、p却是反位相的杂化，即HF以缩小的一瓣去成键，而用增大的一瓣去装转移过来的过多电子，这转移电子妥善安排在离键区远的外面集结。

     第三、电子向键中间中移。

    从HF分子Δρ图看，在H和F之间也有明显的电子聚集。这是人所共知的成键的关键表征。

    此外，这种电子往键中间聚集、形成对二核的屏蔽加强，会减小原子核之间的排斥力，有利于降低体系的总体能量。

       然而，所有上面这一些图形功能，单从gs03 cube并不能直接、方便地得到。

对于G03来说，它的cubegen是能作density和Orbitals等的空间格点的。

但是它的density（ρ）只是全分子的，分子的所有MO的电子密度加和，还自然包含分子中的1S、2S轨道，用图形展示出来是一个圆滚滚的样子，很难说明什么，也不利作密度差。

     我们常常需要考察的是分子成键的单个特定的MO（或者孤对电子MO等）。

而G03的cubegen不能作某个MO的ρ，将某个MO的cube输入到cubman也不能作平方运算作成ρ，因为cubman缺少平方运算。

    而且cubegen的空间数值格点输出文件，是没有坐标的。它的坐标隐含在输出文件的开始部分：空间网格每个边的格点数、空间格点的起始点、格点间隔。除了与其配套的软件外，其它软件很难用这个数据加工或作图。

      Sobereva 博士【大牛哈；从程序开发到化学物理概念理解，到具体应用】专门开发的gsgrid程序就是为解决上述问题制作的，它把格点文件的数据提取出来，附上每个数据点的坐标。可以选取指定的XY/YZ/XZ平面，得到这个平面上的数据点以便做等值线图（MO图、ρ图），或对数据进行各种运算加工（作平方、作差值等），然后再作等值线图（Δρ图）。

gsgrid程序附有详细的使用说明，一看就会。

而进入高超的sigmaplot中作等值线，更是功能强大、得心应手！

**具体计算如下：** 

单个原子的AO的获得，要单算。

如除了算HF外，H和F还要另外单算，而且它们要与HF有同样指定的空间格点规格，且与HF中的H和F有相同的坐标位置，相同的基组。

如果是算整体的ρ来相差，这样得到的三个cube，进入gsgrid作三个xy截面G1、G2、G3，再用HF的G1减去H和F的G2、G3，即得电子密度差。进入sigmaplot作图、调整即得等值线图。

如果想作精细一些，需要在HF中指定一个MO来作，如上面的σ键的MO，它是第3个MO。F也要选定那个单电子的AO，H就是一个了。用指定的MO作三个cube，进入gsgrid作三个xy截面G1、G2、G3，再对这三个xy截面分别作平方使成为电子密度，HF的这个轨道有2个电子，还要乘以2。再用HF的减去H和F的，即得电子密度差。进入sigmaplot作图、调整即得等值线图。

实践表明，因为这时由原子到分子，真正发生电子净变化的主要是σ成键轨道。内层F1s等基本没有变化，相差之后抵消了，而F的孤对电子轨道，虽然因为通过σ键诱导过来部分电子，对有效核电荷的屏蔽加强，使核对孤对电子pi吸引能力减小、能级升高，表现为轨道膨胀、弥散，但由于我们的xy截面是对σ键的中心剖面，对这个截面，孤对电子pz与之是正交的、xy截面是其节点，没有截取到值，另一孤对电子py也因弥散影响甚微。所得到的电子净变化图形，与上面根据σ成键轨道MO作出它的没有原则性的差别。

转载本文请联系原作者获取授权，同时请注明本文来自叶小球科学网博客。  
链接地址：[https://blog.sciencenet.cn/blog-567091-736154.html](https://blog.sciencenet.cn/blog-567091-736154.html)

上一篇：[Useful tools for VASP users](https://blog.sciencenet.cn/blog-567091-736151.html)  
下一篇：[朝花夕拾之---FORTRAN90/95语法概述](https://blog.sciencenet.cn/blog-567091-736393.html)