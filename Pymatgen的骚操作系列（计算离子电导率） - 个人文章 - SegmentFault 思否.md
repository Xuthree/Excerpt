ASE 在 DFT 计算中的骚操作令人印象深刻。类似 ASE 的科研工具还有很多，比如 VASPKIT 和 QVASP。熟练使用这些科研工具肯定会助力大家快速完成科研任务，发出更好更高质量的文章。

此次，本文向大家隆重介绍另一款出色的科研工具：Pymatgen。

Pymatgen 是 python materials genomics 的缩写，它是一款基于 python 的、开源的、强大的材料分析软件（[https://pymatgen.org/）。](https://link.segmentfault.com/?enc=K18EPbBPNdVWSJPerQIIuQ%3D%3D.bWWRl2KTgj4q1d0U5bzWsiRZv39B0bi7RIoD%2FXLG7dMpb7d2lROb3w%2FXPkX8MP15)

Pymatgen 包含一系列能够表示元素（Element）、位点（Site）、分子（Molecule）、和结构（Structure）的类（Class）。它具有为很多计算软件提供前处理和后处理的能力。这些计算软件包括VASP，ABINIT，exciting，FEFF，QCHEM，LAMMPS，ADF，AIIDA，ASE，Gaussian，Lobster，Phonopy，Shengbte，Pwscf，和Zeo++等等。它能实现科研狗的众多后处理需求，包括生成相图（Phase diagram）和布拜图（Pourbaix diagrams），分析态密度和能带等等。

Pymatgen 还提供了很多数据库（Materials Project REST API，Crystallography Open Database，and other external data sources）的接口，方便大家从数据库中查询结构和其他数据。

真是科研狗快乐科研之利器呀！

> 以下是Pymatgen官网提供的后处理的例子：

[![](https://segmentfault.com/img/remote/1460000039716677)
](https://link.segmentfault.com/?enc=8NCcsWZmobMULydMVpSTbA%3D%3D.K2H0QucTgMwENEWJ%2FA264iSMS1s8twHqWvKCD%2BFh0I3DN0Uj%2FHld9HFg1A0KqNUusfqtnER5JuAHrnx%2BxZmNRA%3D%3D)

**此次，本文就介绍一下如何使用 Pymatgen 的 DiffusionAnalyzer 类去计算锂离子固态电解质中锂离子电导率。** 

计算离子电导率的理论与公式
-------------

目前，比较准确的计算离子电导率的方法是先用NVT系综第一性原理分子动力学（**AIMD**，\_ab initio\_ molecular dynamics）模拟材料中离子在不同温度下的运动，然后计算出离子的平均（average）均方位移（**MSD**，mean square displacement），再计算出自扩散系数（Ds，self-diffusion coefficient），最后求得离子在某温度下的电导率（σ，conductivity）。

如何进行AIMD计算

AIMD计算通常非常耗时，所以，为了减少计算成本，我们可以适当放宽计算精度。如果用 VASP 进行计算，具体的，大家可以

*   采用较小的截断能。氧化物用 400 eV，硫化物用 280 eV，硒化物用 270 eV
*   采用Gamma点作为K点设置，并使用gam版本的 VASP 进行计算
*   采用单胞计算，如果材料的单胞包含比较多的原子
*   采用合适的步长，比如2 fs，即 POTIM = 2

![](https://segmentfault.com/img/bVcQOg7)

现在我们通过一个 Li\_Sn\_S 材料的例子来详细了解一下整个计算和处理的过程。该材料的结构显示如下：

本例中采用单胞做计算，INCAR 设置如下：  

![](https://segmentfault.com/img/bVcQOfX)

```
\[test@ln0%tianhe2 li\_sn\_s\]$ vi INCAR  
ISTART = 0  
ICHARG = 2  
IBRION = 0  
ISIF = 2  
NPAR = 8  
NSW = 30000  
TEBEG = 900 #还要设置成 1500K 等等  
PREC = N  
POTIM = 2  
SMASS = 0.0  
NELMIN = 4  
LWAVE = F  
LCHARG = F  
IALGO = 48  
LREAL = A
```

AIMD 计算结束之后会得到 XDATCAR 文件。很多时候，由于超算的时间限制，一个完整的AIMD计算需要提交两三次，从而产生两三个 XDATCAR 文件，这时，我们只要把它们按顺序通过 cat 命令合并在一起就行。例如我们有三个 XDATCAR 文件，分别命名成 XDATCAR01，XDATCAR02，和 XDATCAR03。

![](https://segmentfault.com/img/bVcQOgs)

\[test@ln0%tianhe2 li\_sn\_s\]$ cat XDATCAR01 XDATCAR02 XDATCAR03 > XDATCAR

**新得到的XDATCAR文件，注意删掉重复的与晶格信息相关的行**，一般续算的次数也不多，在使用上面命令的时候，手动把`XDATCAR02`, `XDATCAR03` 中的删除即可。

Pymatgen 大显身手

安装pymatgen

首先让我们安装 pyamtgen，推荐大家参考官网，使用 anaconda 安装，否则会出现问题。安装好了anaconda之后，不管是 linux 还是 windows, 安装 pyamtgen 的指令是一样的。下面以吕梁天河超算为例：

\[test@ln0%tianhe2 li\_sn\_s\]$ conda install --channel conda-forge pymatgen

安装完成后，我们可以试着运行 python，导入 Pyamtgen 模块，如果像下面一样没有出错，就是安装成功了。

```
> > > \[test@ln0%tianhe2 li\_sn\_s\]$ python  
> > > Python 3.7.3 (default, Mar 27 2019, 22:11:17)  
> > > \[GCC 7.3.0\] :: Anaconda, Inc. on linux  
> > > Type "help", "copyright", "credits" or "license" for more information.
> > > 
> > > import pymatgen
```

查看 DiffusionAnalyzer 的类

大家可以通过官方文档（\[[https://pymatgen.org/pymatgen...](https://link.segmentfault.com/?enc=zpT7lao1Lrs5p2XIwprvAg%3D%3D.c%2B38gJ4qF47P6sQ8CL%2Bevqz0chy2lyhiYs5hTTIk7CkQpjKho6t3iCuhkITPLUt2%2BTMCB0ZkFlsh9ofyV44QXw%3D%3D)）查看接下来要使用的类，熟悉一下代码的用法。

```python
class DiffusionAnalyzer(MSONable):  
def \_\_init\_\_(self, structure, displacements, specie, temperature,  
time\_step, step\_skip, smoothed="max", min\_obs=30,  
avg\_nsteps=1000, lattices=None):
```

这段代码显示，运行这个类需要一系列的输入信息，包括材料结构（structure），位移（displacements），要研究的离子（specie），温度（temperature）等等。

但是这个类提供了很多方法让大家可以通过读取 XDATCAR 或者 vasprun 文件的方式来实例化，例如

![](https://segmentfault.com/img/bVcQOgK)

好了，废话不多说，直接上代码，开始进行后处理。

代码示例

新建一个文件，名字为`li_conductivity.py`  

![](https://segmentfault.com/img/bVcQOgO)

在终端运行该文件

![](https://segmentfault.com/img/bVcQOgZ)

\[test@ln0%tianhe2 li\_sn\_s\]$ python li\_conductivity.py

一段时间后就会得到MSD图像和离子电导率  

![](https://segmentfault.com/img/bVcQOg1)

可见，该材料在 900K 时的锂离子电导率为 884.05 mS/cm。

例子下载：

链接：[https://pan.baidu.com/s/1WGzOVJBoe6Ym8mvR1uWanA](https://link.segmentfault.com/?enc=1cHrXPYhswsHSclD%2Fod68Q%3D%3D.mon2FXfQ3jcxZSXHlLAMxKpPTsN4xStMXK0dlBVwmGLMT%2B594oHnESakcZeQlc%2Fq)  
提取码：jhc5

思考

*   简短几行代码就可以计算出离子电导率，那么如何得出材料在300K下的电导率呢？
*   如何计算离子在材料中的迁移势垒？
*   如何可视化离子在材料中的扩散路径？

本文只是浅谈离子电导率的计算，欢迎大家指出计算过程中的不足之处。转载：[https://www.bigbrosci.com/202...](https://link.segmentfault.com/?enc=c1e40rV2VNwYYvLtXm%2FH8A%3D%3D.BV3Rk40SM1mUI9%2FtjnWO%2FoQ2fd94czkpUZ2avT7Ct2GZEieGVoGblDfM192WLxha)