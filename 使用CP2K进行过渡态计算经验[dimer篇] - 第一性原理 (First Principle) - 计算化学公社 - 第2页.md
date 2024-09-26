# 使用CP2K进行过渡态计算经验[dimer篇] - 第一性原理 (First Principle) - 计算化学公社 - 第2页
_本帖最后由 djjj148 于 2021-6-4 15:03 编辑_

**1\. 前言**

说到过渡态计算，很多人可能会更倾向于用VASP，QE，但是速度慢是硬伤，且对于大体系更是太吃资源。相比之下，CP2K免费，速度极快。撰写本文的初衷即是想让更多人感受到CP2K的强大。主要是由于我的计算资源极其有限(组里平均一个人只能分到100核)，对于100原子以上的体系算过渡态的话我就基本不会考虑用平面波程序了。而使用CP2K我就敢尝试到300原子体系，各种显式溶剂模型都敢往里塞，反正CP2K收敛性好，速度快。56核的单节点跑一个dimer的过渡态一般7-24个小时就能收敛(250原子体系)，就算用24核的小节点，也是时间多了一倍左右而已。  
**言归正传，此贴会分享用DIMER跑过渡态任务的整个过程，包括：初末结构的准备，过渡态结构的准备和验证，过渡态任务的实时监控，过渡态的验证。每个过程用到的脚本也会分享出来或留下出处。**   
PS： 可能有人会问为什么不介绍主流的CI-NEB方法。首先，我个人觉得CI-NEB太费时，就算是用CP2K。在计算资源相同下，插N个点的用时相当于跑N个DIMER计算，而且跑过渡态都用更稳健的CG方法，这是一个离子步需要多个SCF收敛的，相当于再一次扩大了CI-NEB和DIMER的用时差距。再者，我早期也用CI-NEB跑几步得到较好的DIMER任务的初猜结构再用DIMER精收敛。后面发现只要初猜得当(下文会详细讲)，直接跑DIMER就行了(起码对于我目前所研究的对象是这样的)。如果直接上DIMER发现计算实在无法步入正轨，也是推荐用CI-NEB+DIMER组合的，但不太建议一上来就全程CI-NEB(土豪请随意)。

**2.1 初末结构的准备**  
我个人喜欢把inp文件和结构文件分开，这样方便单独对结构文件进行处理，本文的操作都是基于这样的。如果用Multiwfn生成一体化的inp也比较方便，届时请自行更改我的脚本。涉及到的python脚本都是基于python3的。  
准备：配置好Multiwfn，VASPKIT  
首先建好反应物和产物的初猜结构is.cif和fs.cif文件放在不同目录，借助Multiwfn和shell**处理为VASP格式的POSCAR文件，**比如对与is.cif：  
这会生成POSCAR文件和CP2K的结构输入文件coord.inc。计算的话这里附上一个输入文件的模板geo-opt.inp，就不详细说了。  
计算结束后在两个计算目录分别运行x2c脚本，会调用VASPKIT和shell生成last和last.cif文件，分别是CP2K优化的最后一帧结构的POSCAR格式和cif格式文件。

**2.2 过渡态结构的准备和验证**  
准备：  
1\. 安装

pymatgen和pymatgen-diffusion  

pip install pymatgen pymatgen-diffusion

_复制代码_

2\. 去官网下载vtst脚本 [http://theory.cm.utexas.edu/vtsttools/installation.html](http://theory.cm.utexas.edu/vtsttools/installation.html)，解压后设置环境变量。注意运行有些脚本可能会出现报错，用dos2unix转一下一般能解决。  
**过渡态结构的准备：**   
新建一个目录，将is和fs目录的last文件分别移到该目录下并改名为POSCARis和POSCARfs。用vtst的dist.pl脚本判断要插的点数：  

1.  dist.pl POSCARis POSCARfs

_复制代码_

  
这会返回一个值，一般插点的数目可以是这个值除以0.8。  
假设要插4个点。这里用许楠博士写的idpp.py非线性插点脚本，可得到比较理想的插点结构：  

1.  idpp.py POSCARis POSCARfs 4

_复制代码_

再用vtst的脚本把插点的结构合并成一个xyz文件：  
生成movie.xyz文件，可用VMD查看插点的轨迹，并根据轨迹确定选哪一个结构作为DIMER计算的输入结构。比如想计算一个甲醇的亚甲基氢(H1)从C1上转移到一个表面模型的N1上的过渡态，就可以直接显示H1和C1的键长，以及H1和N1的键长，一般取两个键长大致相等的结构作为过渡态计算的初猜。或者对比is和fs的能量看看是吸热还是放热反应，灵活选取初猜。  
接下来要准备DIMER\_VECTOR文件，定义初始的dimer方向。先把插点的每个POSCAR文件分别复制成xyz文件：  

1.  for i in {0..5};do cp "0"$i/POSCAR.xyz $i".xyz";done

_复制代码_

假设选取第二个插点的结构(不算is和fs)用做计算：  

1.  cp 2.xyz coord.inc  
    
2.  sed -i '1,2d' coord.inc

_复制代码_

这里开始要用到赵亚帆博士写的一些python代码，年份较老，是基于python2的，我自己转成了python3并修改了一点点，加上自己写的一些shell脚本打包成了dimer.tar.gz。解压后，假设所在目录为$HOME/cbin/dimer，则需要设置环境变量和pythyon的环境变量：  

1.  export PATH=$PATH:$HOME/cbin/dimer  
    
2.  export PYTHONPATH=$PYTHONPATH:$HOME/cbin/dimer

_复制代码_

用反应物和产物生成DIMER\_VECTOR文件：  
至此，DIMER计算的所有文件都准备好了，可以用dcc脚本生成一个DIMER振动方向的xyz文件：  
dcc中最后一行的sz命令是因为我自己用的是windows系统的Xshell，需要把Linux服务器上的文件传到windows用sz，不需要的可以直接注释掉，下同。  
把dimmode.xyz文件用VMD打开即可观察到DIMER计算的方向。  
确认振动方向是自己心中的反应方向无误后即可提交计算，这里传一个模板dimer.inp。

**2.3 过渡态任务的实时监控**  
计算中需要监控能量和DIMER的方向，发现不对劲及时调整以免白白等待。  
对于能量，直接cp2kdimerfe.py cp2k.out && transform\_fe.py && dimer\_opt\_eV即可(cp2k.out是我指定的out文件名，不同的提交任务命令会不同)，注意这里会调用gnuplot画图，所以需要安装gnuplot。我个人很喜欢gnuplot，速度快。gnuplot的编译很简单，网上一搜即可，这里不细说了。  
运行成功后会出现这样的窗口：

 ![](http://bbs.keinsci.com/forum.php?mod=attachment&aid=MzU0MDJ8MDVjMWNiOTJ8MTcyNzMyMTc1NXwxMzQ3OXwyMzUxNg%3D%3D&noupdate=yes)   
这里是跑了200多步的DIMER，每一步的能量变化(单位是eV，我以前写的是a.u.这里没改过来，请忽略)，理想情况下能量都是单调递减的，像上图这种突然有一步上升了就要注意了，可能优化方向已经偏离。

对于DIMER的方向，运行dm脚本即可，会生成dimmode.xyz文件，这是最后一步的DIMER振动方向的xyz文件，放到VMD里可视化即可。如果想查看其中一步的也可以，比如上图在191步能量升高了，可以运行dm2 cp2k-1\_191.restart查看第191步的DIMER方向。  
如果DIMER方向和能量都没问题，即可放心继续跑任务。

**2.4 过渡态的验证**  
判断过渡态是否收敛，可以直接

1.  grep "Informations at step =" cp2k.out -A20

_复制代码_

  
会输出类似的内容：  
 ![](http://bbs.keinsci.com/forum.php?mod=attachment&aid=MzU0Mzl8ZTk4NmY2NzR8MTcyNzMyMTc1NXwxMzQ3OXwyMzUxNg%3D%3D&noupdate=yes)   
这里显示的是第214步的收敛信息，和Gaussian很像，4个收敛标准都是yes即收敛。  
DIMER计算结束后，跑个频率计算即可(该固定的原子记得固定)。可以直接用Multiwfn处理cp2k-1.restart生成频率计算的inp文件。详见[http://sobereva.com/587](http://sobereva.com/587)(使用Multiwfn非常便利地创建CP2K程序的输入文件)  
可以grep -E "Freq" cp2k.out查看频率，目录下还出现了freq-VIBRATIONS-1.mol，里面用molden格式记录了振动矢量，可以用Molden程序观看振动模式。  
如果振动模式连接反应物和产物，且只有一个较大的虚频，过渡态计算成功。

**3\. 总结**  
这里借用了很多VASP的预-后处理脚本和程序，因为它们在结构处理上都是通用的。另外此贴可能不适合萌新，过渡态计算的基础知识公社里已经有很多帖子了，可以自行查看互补。

\============================================================  
**能力有限，请批评指正。**
