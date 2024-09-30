# 使用QE计算ELF和DORI - 第一性原理 (First Principle) - 计算化学公社
| _本帖最后由 欧拉角 于 2021-2-5 20:15 编辑_  
  
本文原创非首发，未经同意不得转载内容。  
  
引言  
  
原子、离子或分子间结合成分子或晶体，其结合形式与物质的结构和物理化学性质有密切的联系\[1\]。共价键是通过原子间共享电子而形成，这些电子呈现出较强的定域性。那么什么是电子的定域性呢？电子的定域性并非指电子静止在某个空间点，而是指电子在某个区域内不断地运动，如共价键的电子通常就是在两个原子之间巡游\[2\]。为了定量展现电子的这种定域特性，Savin等提出了电子定域化函数 (electron localization function, ELF)\[3\]：  

![](forum.php?mod=attachment&aid=MzIxMDR8ZThkMTQ0YWR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**Eq1.jpg** _(3.08 KB, 下载次数 Times of downloads: 60)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMDR8ZThkMTQ0YWR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:27 上传 Uploaded

  
其中，_D_为精确动能密度与Weizsäcker动能密度之差，_D0_为无相互作用电子的动能密度。ELF为无量纲物理量，其数值范围由0到1，数值越大意味着空间中某处的电子的定域性越强。其中ELF等于1对应于电子的完全定域化；等于0则表示电子完全变为均匀电子气。我们往往关注的是ELF数值大于0.5的区域，其对应于成键区域、孤对电子、原子的壳层和内核。  
  
另一种讨论原子间相互作用的方式是由Silva和Corminboeuf提出的密度重叠区域指示符(density overlap regions indicator, DORI)\[4\]：  

![](forum.php?mod=attachment&aid=MzIxMDV8ZmZiN2I0NzB8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**Eq2.jpg** _(4.34 KB, 下载次数 Times of downloads: 74)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMDV8ZmZiN2I0NzB8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:28 上传 Uploaded

  
DORI能够反映出原子间电子密度重叠的程度，它不仅能体现共价相互作用，同时也能将非共价相互作用区域呈现出来。DORI可以通过类似于上一篇文章中\[5\]所介绍的RDG那样进行sign(λ2)ρ填色，与ELF互补来进一步讨论所研究体系的相互作用类型。为了对上述两种函数在分析共价相互作用方面的应用进行说明，本文将展示若干Quantum ESPRESSO的计算实例。  
  
准备工作  
  
计算程序采用Quantum ESPRESSO 6.7\[6\]和VESTA 3.1.8\[7\]  
测试体系为：  
1\. 来源于Materials Project \[8\]的结构GaAs(mp-2534)、NiO(mp-19009)  
2\. H2O2@PMCS来源于文献\[9\]  
3\. 在进行下一步计算前建议先阅读我的上一篇文章\[5\]，一些类似的操作本文将不再赘述。  
  
计算参数  
  
| 交换相关泛函 | PBE |
| 赝势 | PSLibrary PAW |
| 动能截断 | 60 Ry |
| 密度截断 | 480 Ry |
| K网格 | GaAs 8×8×8  
NiO 10×10×10  
H2O2@PMCS 2×2×1 |
| 展宽 | 0.02 Ry |  
计算过程和数据处理  
  
A 计算过程  
A1~A4计算过程可以参考\[5\]，只是在A3的过程中需要通过plot\_num=8产生ELF格点，以及我们修改过的pp.x程序plot\_num=123\[注1\]产生DORI格点。  
B DORI填色图处理  
B1~B4也是与\[5\]相同的，只是将B2过程中导入的RDG格点数据换成DORI格点数据，并且将DORI的等值面取为0.95。  
C ELF切片填色图处理  
C1 打开VESTA程序，将A2中out文件产生的xsf文件拖入VESTA窗体。  

![](forum.php?mod=attachment&aid=MzIwOTd8YmQzNGNiNDB8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**1.png** _(99.65 KB, 下载次数 Times of downloads: 70)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIwOTd8YmQzNGNiNDB8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:20 上传 Uploaded

  
C2 选中我们关心的平面上的三个原子。  

![](forum.php?mod=attachment&aid=MzIwOTh8MWNmYjM3MGR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**2.png** _(134.39 KB, 下载次数 Times of downloads: 61)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIwOTh8MWNmYjM3MGR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:20 上传 Uploaded

  
C3 在菜单栏中Edit->Lattice Planes->Add Lattice Planes->New建立新的切片，在New按钮的上方的Calculate the best plane for the selected atoms使得表面尽可能贴合选中的原子。  

![](forum.php?mod=attachment&aid=MzIwOTl8N2NhYTA4NDR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**3.png** _(51.53 KB, 下载次数 Times of downloads: 60)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIwOTl8N2NhYTA4NDR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:20 上传 Uploaded

  
C4 在菜单栏中的Objects->Properties->Sections->Sections and slices->Saturation levels中的Min和Max分别改为0和1。  

![](forum.php?mod=attachment&aid=MzIxMDB8ZmY3ZmVkNDR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**4.png** _(40.35 KB, 下载次数 Times of downloads: 58)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMDB8ZmY3ZmVkNDR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:21 上传 Uploaded

  
C5 调整其他显示部分，如原子的颜色、连接等。  
  
案例  
  
GaAs是一种原子晶体，通常作为太阳能电池材料，左下角为其晶体结构和ELF切片填色图。从图中我们可以发现，对于Ga-As之间的区域，电子是具有定域性的；右下角的图为NiO的晶体结构和ELF切片图，图中则体现出Ni-O之间的区域电子不具备定域性，因此我们可以认为GaAs中的Ga-As形成了共价键，而NiO中的Ni-O并不是共价键。  

![](forum.php?mod=attachment&aid=MzIxMDF8ZmU0N2FlNDd8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**5.png** _(203.75 KB, 下载次数 Times of downloads: 66)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMDF8ZmU0N2FlNDd8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:21 上传 Uploaded

  
H2O2@PMCS是我们上一篇文章\[5\]所讨论过的例子，下图为其ELF切片填色图，切片分别选取了过氧化氢的两个氧原子和锌原子以及PMCS上的三个碳原子构成的两个平面。图中的PMCS上的C-N、C-C原子间和H2O2的O-O、O-H之间的区域都呈现较强的定域性，表明这些原子是通过共价作用结合的。H2O2的O原子不与氢连接的区域也显示出定域性，这是由于O原子带有孤对电子。Zn原子与其他原子间的区域为蓝色，意味着Zn原子与其他原子并非通过共价作用结合，但是无法区分这些作用是离子键还是弱相互作用。  

![](forum.php?mod=attachment&aid=MzIxMDJ8YmJmOGIwOTV8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**6.png** _(294.1 KB, 下载次数 Times of downloads: 68)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMDJ8YmJmOGIwOTV8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:21 上传 Uploaded

  
我们通过DORI对H2O2@PMCS做进一步分析。下图中能够得到与RDG类似的结论，H2O2的O原子与PMCS上的Zn原子为静电作用，H2O2的O-H与Zn-N4的N原子为氢键作用。但不同于RDG，PMCS上的C-N、C-C原子间和H2O2的O-O、O-H之间的共价作用能够同时清晰地被展现出来，Zn-N之间的sign(λ2)ρ已经小于-0.1\[注2\]，我们可以认为Zn-N是离子键。图中可以看到，DORI并不能判断定域性，无法展现孤对电子也无法区分共价键和离子键，但能够得到与ELF互补的信息。  

![](forum.php?mod=attachment&aid=MzIxMDN8MGU2ZDdkZWR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**7.png** _(368.3 KB, 下载次数 Times of downloads: 65)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMDN8MGU2ZDdkZWR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:21 上传 Uploaded

  
  
总结  
  
本文通过QE和VESTA，以GaAs和NiO为例讨论了ELF判断共价作用的方法，又通过H2O2@PMCS为例，对ELF和DORI各自的局限性和互补性进行了讨论。我们建议可以通过ELF对共价作用或非共价作用进行初步判断，若为非共价作用再使用DORI进一步对离子键、氢键、vdW作用等进行进一步的区分。希望本文能够为大家的科研工作助力添彩，同时也欢迎大家共同参与讨论互相进步。  
  
注释  
  
\[1\] 这个版本是由我们自己修改增加了DORI的函数以及修正了一些bug，数值上的实现可以参考本文附录，修复bug并增加DORI的代码我们已经通过gitlab提交给QE官方，后续版本可能会加入。如果要使用DORI，我们暂时将plot\_num指定为123。  
\[2\] 按照杨伟涛教授的文章\[10\]所述，密度小于0.005的区域对应于弱作用，密度介于0.005~0.05的区域则为强非共价作用。  
  
参考文献  
  
\[1\] 黄昆. 固体物理学\[M\]. 北京: 高等教育出版社, 1988.  
\[2\] [https://www.reed.edu/chemistry/ROCO/Resonance/delocalized.html.](https://www.reed.edu/chemistry/ROCO/Resonance/delocalized.html.)  
\[3\] Silvi B, Savin A. Classification of chemical bonds based on topological analysis of electron localization functions. Nature. 1994, 371, 683–686. DOI: 10.1038/371683a0.  
\[4\] De Silva P, Corminboeuf C. Simultaneous visualization of covalent and noncovalent interactions using regions of density overlap. J. Chem. Theory Comput. 2014, 10, 3745–3756. DOI: 10.1021/ct500490b.  
\[5\] [固体和表面的非共价相互作用分析](http://bbs.keinsci.com/thread-21376-1-1.html)  
\[6\] [https://github.com/QEF/q-e/releases.](https://github.com/QEF/q-e/releases.)  
\[7\] [http://www.jp-minerals.org/vesta/en/download.html.](http://www.jp-minerals.org/vesta/en/download.html.)  
\[8\] [https://www.materialsproject.org.](https://www.materialsproject.org.)  
\[9\] Xu B, Wang H, Wang W, Gao L, Li S, Pan X, Wang H, Yang H, Meng X, Wu Q, Zheng L. A Single-atom nanozyme for wound disinfection applications. Angew. Chem. 2019, 131, 4965–4970. DOI: 10.1002/anie.201813994.  
\[10\] Johnson E R, Keinan S, Mori-Sánchez P, et al. Revealing noncovalent interactions\[J\]. J. Am. Chem. Soc, 2010, 132(18): 6498-6506.  
DOI: 10.1021/ja100936w  
\[11\] [http://sobereva.com/367](http://sobereva.com/367)  
\[12\] [http://sobereva.com/63](http://sobereva.com/63)  
  
附录1 基于FFT算法的数值微分方法  
  
假定f(r)是一个光滑的三维周期函数，存在Fourier变换对（忽略常数）  

![](forum.php?mod=attachment&aid=MzIxMTB8NTc0YTIxMGR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**Eq5.jpg** _(6.23 KB, 下载次数 Times of downloads: 84)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMTB8NTc0YTIxMGR8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:33 上传 Uploaded

  
**G**是倒格矢\[1\]， ![](forum.php?mod=attachment&aid=MzIxMTN8YjQ5YmYwNDh8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**Eq7.jpg** _(5.93 KB, 下载次数 Times of downloads: 66)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMTN8YjQ5YmYwNDh8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:34 上传 Uploaded

，将上述关系代入就能够将微分形式转换为一般的代数形式  

![](forum.php?mod=attachment&aid=MzIxMTR8ZjJmZDdhYjZ8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&noupdate=yes)

**Eq8.png** _(6.49 KB, 下载次数 Times of downloads: 56)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMTR8ZjJmZDdhYjZ8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:34 上传 Uploaded

  
如果用**F**和**F**^(-1)分别表示Fourier变换和Fourier逆变换，那么上面的公式则可以表达为一个简单的形式  

![](static/image/common/none.gif)

**Eq9.jpg** _(2.95 KB, 下载次数 Times of downloads: 63)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMTZ8MDBkYTI1OTF8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:35 上传 Uploaded

  
这意味着，f(**r**)的梯度计算可以依次对不同分量先进行Fourier变换，然后与该分量对应的倒格矢乘积运算后进行Fourier逆变换即可。实际编写代码时Fourier正逆变换可以直接使用如FFTW或FFTPACK等数学库，上述算法在QE中对应于QE目录下\\Modules\\gradutils.f90中的fft\_gradient\_r2r子程序。▽▽f(**r**)也可以按上述过程进行类似推导，此处不再赘述，其对应的是同一个源码文件中的fft\_laplacian子程序。上述算法不仅能用于QE，对于其他程序也是可行的，包括cube或CHGCAR格式等，例如SIESTA程序的Util\\Grid文件夹下有个cdf\_laplacian.F90，这段代码也采用了相同的方法。  
  
附录2 DORI的数值实现  
  
按照定义，  

![](static/image/common/none.gif)

**Eq10.jpg** _(4.43 KB, 下载次数 Times of downloads: 80)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMTd8ZmE4ZjAzNzJ8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:36 上传 Uploaded

  
我们先对分号的分子进行展开，这个只需要利用链式求导法则即可  

![](static/image/common/none.gif)

**Eq11.jpg** _(13.4 KB, 下载次数 Times of downloads: 57)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMTh8MTg1YWQ3MWF8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:36 上传 Uploaded

  
其中▽ρ(**r**)为电子密度在**r**处的梯度，▽▽ρ(**r**)则是电子密度在**r**处的Hessian矩阵，可以使用附录1的方法进行计算。接着将分子分母同时乘以ρ(**r**)^6，就能得到以下公式：  

![](static/image/common/none.gif)

**Eq12.jpg** _(13.54 KB, 下载次数 Times of downloads: 75)_

[下载附件 Download](forum.php?mod=attachment&aid=MzIxMTl8YjU3MWEwZjZ8MTcyNzY3MzQ4OXwxMzQ3OXwyMTU0Ng%3D%3D&nothumb=yes)

2021-2-5 17:37 上传 Uploaded

  
实际计算时，我们在分母的|▽ρ(**r**)|^2加1e-5，即在不影响定性结果的基础上，来防止在体系边缘上因分母更快趋于0而使得DORI为1。  
  
 |