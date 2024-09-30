<chem>LiFePO4</chem> 是典型的可重复充电的锂离子电池电极材料，成本低，环境友好\[1,3\]。在本教程中，将使用 ATK-DFT 估算锂离子沿 <chem>LiFePO4</chem> 不同晶向的扩散速率。教程主要包括如下内容：

*   导入 <chem>LiFePO4</chem> 晶体结构
    
*   优化 <chem>LiFePO4</chem> 晶格参数
    
*   创建 Li1−xFePO4 结构
    
*   优化初始、末态结构
    
*   创建 NEB 轨迹（IDPP插值）
    
*   优化 Li 扩散路径
    
*   使用简谐近似过渡态理论 (HTST) 计算反应速率
    

#### 提示

**本教程使用特定版本的QuantumATK创建，因此涉及的截图和脚本参数可能与您实际使用的版本略有区别，请在学习时务必注意。** 

导入 LiFePO4 晶体结构
---------------

VNL 内部数据库没有 <chem>LiFePO4</chem> 的晶体结构。您可以从其他数据库下载结构文件，一般而言 CIF 文件通用性比较强。

**方法1.** 直接从[Crystallography Open Database](http://www.crystallography.net/cod/ "http://www.crystallography.net/cod/")检索、下载 CIF 文件，导入 VNL。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=650&tok=7b9e7c&media=atk:cod.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:cod.png "atk:cod.png")

下载并拷贝 [CIF文件](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?media=atk:4001845.zip "atk:4001845.zip (3.2 KB)")到 Project 文件夹，这样 **LabFloor** 将会自动加载它。其它方式，例如导入XYZ、CIF、CAR、VASP文件，参考[VNL指南](http://docs.quantumwise.com/tutorials/vnl_io_import/vnl_io_import.html#import-xyz-cif-car-vasp-files-in-vnl "http://docs.quantumwise.com/tutorials/vnl_io_import/vnl_io_import.html#import-xyz-cif-car-vasp-files-in-vnl")。

**方法2.** 从2016版开始，VNL图形用户界面可以直接检索COD数据库。在 Builder 界面里，选择 Add → from plugin → Crystallography Open Database。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=400&tok=1972e6&media=atk:add-from-cod.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:add-from-cod.png "atk:add-from-cod.png")

优化LiFePO4晶格参数
-------------

将 <chem>LiFePO4</chem> 导入到 **Builder** 中，可以进一步仔细查看晶体结构。

注意：

为了更好地与参考文献\[4\]对比，可以使用Bulk Tools ‣ Swap Axes ‣ C↔A和Z↔X交换晶向和坐标轴，使其与文献一致。但这样的话，结构会失去对称性类型的信息，本例中会变成简单正交晶系。使用 Bulk Tools ‣ Lattice Paramters 将 _Lattice type_ 设置为 _Simple orthorhombic_。

为了优化晶格参数，将 <chem>LiFePO4</chem> 结构从 **Stash** 送入到 **Scripter** 中设置新的 DFT 计算任务，如下：

1，添加[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=20&tok=b5f565&media=atk:calculator.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:calculator.png "atk:calculator.png")**New Calculator**，并修改如下参数：

*   SGGA-PBE 交换相关势
    
*   7x5x3 k-point sampling
    

（我们将使用默认的FHI赝势以及DZP基组）。

2，添加[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=20&tok=05f9ec&media=atk:optimization.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:optimization.png "atk:optimization.png")**OptimizeGeometry**，并修改：

*   Max forces：0.02 eV/Å；
    
*   Max stress：0.001 eV/Å3；
    
*   去掉 **Constrain cell**的☑️，允许晶格的优化；
    
*   勾选 **Save trajectory**，并修改名字为 _LiFePo4\_opt.nc_
    

3，将默认输出文件名改为 _LiFePo4\_opt.nc_。

最后将脚本送入 **JobManager** 并运行该计算。

用 Builder 打开优化之后得到的名为 _glD002_ 的 _BulkConfiguration_，检查优化之后的晶格参数与实验数值仅有 0.1% 的差别。

创建Li(1-x)FePO4结构
----------------

本文中将会研究 Li 缺陷 Li1−xFePO4 中 Li 原子的扩散。具体而言，将会删除晶体结构中四个 Li 原子中的一个。应注意到，扩散能垒与Li 浓度有关\[2\]。

在下一节，设置 Li 原子沿着两个不同方向扩散的计算，因此为同一个 Li 原子需要创建一个初始结构和两个末态结构分别沿着 B、C 方向扩撒。

### 创建沿 B 方向扩散的初、末态结构

1，右键点击 **Stash** 中的结构，并复制两次这样就有了三个一摸一样的结构。

2，从第一个结构中，像下图所示一样，删除一个 Li 原子，作为 NEB 计算的初始结构。可以将其重命名为 _initial_。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=650&tok=52d4a9&media=atk:initial_builder.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:initial_builder.png "atk:initial_builder.png")

3，双击激活第二个结构，之后重命名为 _final\_1_。选择与初始结构中删除的相同的 Li 原子，打开[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=20&tok=1fb73c&media=atk:modifyelement02.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:modifyelement02.png "atk:modifyelement02.png")**Periodic Table** 工具，点击金元素，从而将 Li 原子替换为 Au。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=650&tok=b49ae2&media=atk:final_1_periodic_table.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:final_1_periodic_table.png "atk:final_1_periodic_table.png")

小贴士：

这里是在用一个小技巧将 Li 原子移动到特定位置。在本例中，Li 空位到位置通过这个 Au 原子来确定了。实际上也有几种其它的方法可以创建末态结构。例如，通过拷贝删除掉的那个 Li 原子的坐标。非常欢迎大家在这里是用其它相关的，你喜欢的方式在此处使用。

4，选择 27 号 Li 原子（也就是近原子沿着 B 方向相邻的那个原子），使用 [![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=20&tok=563131&media=atk:move02.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:move02.png "atk:move02.png")**Move** 工具，把它移动到 Au 原子上（重合），如下图所示，确保 **Snap** 被勾选了。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=650&tok=9feee6&media=atk:final_1_move_overlap.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:final_1_move_overlap.png "atk:final_1_move_overlap.png")

5，最后，使用 Fuse 选项，删除掉 Au 原子，这样末态结构就好了。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=650&tok=fda35f&media=atk:final_1.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:final_1.png "atk:final_1.png")

### 创建沿C方向扩散的初、末态结构

用同样的过程，创建第二个末态结构，如下图所示（本例中是 24 号 Li 原子）

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=650&tok=64b874&media=atk:final_2.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:final_2.png "atk:final_2.png")

优化初、末态结构
--------

将每一个结构：initial、final\_1、and final\_2 送到 **Scripter** 中，三者使用与 <chem>LiFePO4</chem> 完全相同的参数设置结构优化计算。不过本例中不要优化晶格参数，因此勾选 _Constrain cell_，如下图所示：

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=250&tok=d18285&media=atk:initial_geometry_optimization.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:initial_geometry_optimization.png "atk:initial_geometry_optimization.png")

另外，本例中可以使用 unpolarized GGA.PBE 泛函。这样计算速度会快很多，对本例而言，不影响结果的正确性。这里可以下载三个输入脚本:[initial.zip](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?media=atk:initial.zip "atk:initial.zip (1.5 KB)")、[final\_1.zip](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?media=atk:final_1.zip "atk:final_1.zip (1.5 KB)")和[final\_2.zip](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?media=atk:final_2.zip "atk:final_2.zip (1.5 KB)")。

创建NEB路径的初始猜测
------------

[NEB方法研究Pt在Pt表面的扩散](https://v.fermitech.com.cn/wiki/doku.php?id=atk:neb%E6%96%B9%E6%B3%95%E7%A0%94%E7%A9%B6pt%E5%9C%A8pt%E8%A1%A8%E9%9D%A2%E7%9A%84%E6%89%A9%E6%95%A3 "atk:neb方法研究pt在pt表面的扩散")教程中，演示了如何设置NEB，这里只将主要步骤列出来。

1，末态结构都优化完成之后，找到**LabFloor**加载进来的优化后的结构_gID002_，将两个_BulkConfiguration_直接拖到**Builder**里面。

2，在**Builder**中打开Builders ‣ Nudged Elastic Band，并从**Stash**中分别将初始结构和对应B方向扩散的末态结构，也就是_initial.nc_和_final\_1.nc_分别拖入其中。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=650&tok=2d3be4&media=atk:neb_builder1.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:neb_builder1.png "atk:neb_builder1.png")

3，选择_Image Dependent Pair Potential_插值方法，使用包括末态结构在内的9个映像。

4，使用_initial.nc_和_final\_2.nc_创建Li沿着C方向扩散的初始路径。

优化 Li 扩散路径
----------

现在可以设置并优化 L i在 <chem>LiFePO4</chem> 中的扩散路径。

1，分别将两个 NEB任务从 **Stash** 送到 **Scripter** 中。

2，添加并设定一个 [![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=20&tok=b5f565&media=atk:calculator.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:calculator.png "atk:calculator.png") **New Calculator**，参数选择与末态结构计算保持一致。

3，添加一个 [![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=20&tok=05f9ec&media=atk:optimization.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:optimization.png "atk:optimization.png") **OptimizeGeometry**，并确保选择了 _Climbing Image Method_，因为需要用这个方法去寻找正确的过渡态，用于简谐近似过渡态理论分析（下文有介绍）。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=250&tok=51853d&media=atk:neb_scripter.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:neb_scripter.png "atk:neb_scripter.png")

4，最后运行两个 NEB 计算 [neb\_b.zip](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?media=atk:neb_b.zip "atk:neb_b.zip (6.7 KB)") 和 [neb\_c.zip](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?media=atk:neb_c.zip "atk:neb_c.zip (6.9 KB)")。注意，如同 [NEB方法研究Pt在Pt表面的扩散](https://v.fermitech.com.cn/wiki/doku.php?id=atk:neb%E6%96%B9%E6%B3%95%E7%A0%94%E7%A9%B6pt%E5%9C%A8pt%E8%A1%A8%E9%9D%A2%E7%9A%84%E6%89%A9%E6%95%A3 "atk:neb方法研究pt在pt表面的扩散") 中描述的那样，可以使用 MPI 多进程并行计算。尤其是 NEB 计算各个image的时候，各个进程本身就是并发的。QuantumATK 会尝试检测最佳的并行化策略，并在 log 文件中输出一小段相关的报告。本例中能够看到程序检测到一个关于负载均衡的问题。可以修改 MPI 进程数，从而达到最佳性能。

```
+------------------------------------------------------------------------------+
|                                                                              |
| Atomistix ToolKit 2015.1 \[Build 5b36b25\]                                     |
|                                                                              |
+------------------------------------------------------------------------------+
+------------------------------------------------------------------------------+
| NEB image level parallelism enabled.                                         |
+------------------------------------------------------------------------------+
| Total number of interior images: 7                                           |
| Total number of processes: 16                                                |
| Processes per image: 2                                                       |
| Images calculated in parallel: 8                                             |
|                                                                              |
| Process group \# 0 will calculate images: 1                                   |
| Process group \# 1 will calculate images: 2                                   |
| Process group \# 2 will calculate images: 3                                   |
| Process group \# 3 will calculate images: 4                                   |
| Process group \# 4 will calculate images: 5                                   |
| Process group \# 5 will calculate images: 6                                   |
| Process group \# 6 will calculate images: 7                                   |
| Process group \# 7 will be idle!                                              |
| WARNING: Load balance problem detected.                                      |
|          The number of interior images calculated in parallel is not a       |
|          divisor of the number of interior images. This will greatly         |
|          reduce parallel performance. Either modify the parameter            |
|          "processes\_per\_image" or the number of MPI processes.               |
|                                                                              |
|          With 2 processes\_per\_image ideal performance will be obtained       |
|          by using 14 MPI processes.
```

#### 小贴士

通过设置比较大的力收敛阈值，比如 0.08 eV/Å，这样计算会使用简单 NEB 计算，从而很快就会完成，不会启用 _Climbing Image_ 方法。这样一般能够更快地给出一个能垒和扩散路径的粗略猜测。之后，可以设置更精确的 NEB 计算，并使用 _Climbing Image_ 方法完成结果。

分析结果
----

只要计算正确收敛，就能在 **LabFloor** 中找到加载的内容中找到优化后的 _NudgedElasticBand_。

#### 小贴士

计算结束后，一定不要忘记检查log文件，尤其是要检查是否收敛：

```
+------------------------------------------------------------------------------+
| NEB Optimization using the LBFGS optimizer                                   |
+------------------------------------------------------------------------------+
| Iteration   Step Length    Max. Force  Max. Energy Image    Max. Energy      |
+------------------------------------------------------------------------------+
|         0     0.000e+00     1.473e+00                  4       0.925917      |
|         1     4.825e-02     1.362e+00                  4       0.794101      |
...
|        23     1.220e-02     2.772e-01                  4       0.405770      |
|        24     3.731e-03     4.938e-02                  4       0.404796      |
+------------------------------------------------------------------------------+
| NEB optimization converged after 24 steps.                                   |
+------------------------------------------------------------------------------+
```

下面两个图中，使用 Movie Tool 显示了优化之后的 Li 原子沿着 B 和 C 方向扩散过程。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=330&tok=85f3ed&media=atk:neb_results.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:neb_results.png "atk:neb_results.png")

发现 Li 原子沿 C 方向移动需要跨越一个非常高的能垒，2.3eV；而沿着 B 方向，一个 Li 原子一个通道，移动则容易得多。这些结果与参考文献一致\[2\]。

使用简谐近似过渡态理论计算反应速率
-----------------

关于如何计算反应速率的细节，以及简谐近似过渡态理论本身，请参考 **[用简谐近似下的过渡态理论研究反应速率](https://v.fermitech.com.cn/wiki/doku.php?id=atk:%E7%94%A8%E5%87%86%E8%B0%90%E8%BF%91%E4%BC%BC%E7%90%86%E8%AE%BA%E7%A0%94%E7%A9%B6%E5%8F%8D%E5%BA%94%E9%80%9F%E7%8E%87 "atk:用准谐近似理论研究反应速率")**。

分析过程如下：

1，打开 **Scripter**。

2，双击 **Analysis from file**，选择 _neb\_B.nc_ 中的 _glD001_。

3，添加 HTSTEvent 分析，并设置 prefactor为 1e+13 1/s。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=250&tok=152a38&media=atk:htstevent_scripter.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:htstevent_scripter.png "atk:htstevent_scripter.png")

4，运行分析任务，一分钟左右完成。脚本文件：[HTST\_analysis.py](http://docs.quantumwise.com/_downloads/HTST_analysis.py "http://docs.quantumwise.com/_downloads/HTST_analysis.py")

为了将 HTSTEvents 分析结果可视化，选择 **LabFloor** 中对应的对象，选中窗口右侧的 _HTST Rates_ 插件。

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=250&tok=00b242&media=atk:htst_rates.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:htst_rates.png "atk:htst_rates.png")

这里，可以为扩散机制计算正向和你想反应速率常数。本例中，两个值非常接近，因为初始结构和末态结构基本上是等价的。

这里，也可以设置 Arrhenius 点图选项：

[![](https://v.fermitech.com.cn/wiki/lib/exe/fetch.php?w=350&tok=15bf32&media=atk:arrhenius_plot.png)
](https://v.fermitech.com.cn/wiki/lib/exe/detail.php?id=atk%3A%E7%94%B5%E6%B1%A0%E6%9D%90%E6%96%99lifepo4%E4%B8%AD%E9%94%82%E7%A6%BB%E5%AD%90%E7%9A%84%E6%89%A9%E6%95%A3&media=atk:arrhenius_plot.png "atk:arrhenius_plot.png")

下表总结了本例中的结果：

| Direction | Barrier | kHTST |
| B | 0.41 | 1.6×106 |
| C | 2.31 | 1.5×10−26 |

参考文献
----

*   \[1\] M. Saiful Islam and Craig A. J. Fisher. Lithium and sodium battery cathode materials: computational insights into voltage, diffusion and nanostructural properties. Chem. Soc. Rev., 43(1):185, 2014. doi:10.1039/C3CS60199D.
    
*   \[2\] Chuying Ouyang, Siqi Shi, Zhaoxiang Wang, Xuejie Huang, and Liquan Chen. First-principles study of li ion diffusion in lifepo 4. Physical Review B, 2004. doi:10.1103/PhysRevB.69.104303.
    
*   \[3\] William Davidson Richards Yan Wang. Design principles for solid-state lithium superionic conductors. Nature Materials, 14(10):1026, 2015. doi:10.1038/nmat4369.
    
*   \[4\] Dhamodaran Santhanagopalan Yuri Janssen. Reciprocal salt flux growth of lifepo 4 single crystals with controlled defect concentrations. Chemistry of Materials, 25(22):4574, 2013. doi:10.1021/cm4027682.