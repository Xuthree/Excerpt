# 如何优化TiO2_anatase晶格常数（a , c）？ - 第一性原理 (First Principle) - 计算化学公社
| _本帖最后由 去离子水 于 2015-10-27 19:15 编辑_  
  
对一系列固定体积的结构进行弛豫，然后根据birch-murnaghan拟合E-V曲线，得到基态的平衡体积V0。知道平衡体积之后何获得a和c的值呢？用到的脚本如下，采用PBE泛函。  
  
INCAR.relax  

1.  ENCUT = 520 eV # energy cut-off for the calculation  
    
2.  PREC = High  
    
3.  LREAL = .FALSE # real space projection .FALSE. or Auto  
    
4.  NSW = 999  
    
5.  IBRION = 2 # CG algorithm  
    
6.  ISIF = 4 # forces and stress are optimized

_复制代码_

INCAR.static  

1.  PREC = High  
    
2.    
    
3.  ISTART = 0  
    
4.  ICHARG = 2  
    
5.  ISMEAR = -5  
    
6.    
    
7.  ENCUT = 520 eV  
    

_复制代码_

KPOINTS  

1.  Automatic mesh  
    
2.    0  
    
3.  Gamma  
    
4.    5  5  5  
    
5.    0  0  0  
    

_复制代码_

vasp.pbs  

1.  #!/bin/bash  
    
2.  #$ -S /bin/bash  
    
3.  #$ -cwd  
    
4.  #$ -j y  
    
5.  #$ -N test\_opt  
    
6.  #$ -pe make 12  
    
7.    
    
8.  source /share/apps/intel/Compiler/11.1/073/bin/iccvars.sh intel64  
    
9.  source /share/apps/intel/Compiler/11.1/073/bin/ifortvars.sh intel64  
    
10.  source /share/apps/intel/impi/3.2.0.011/bin64/mpivars.sh  
    
11.    
    
12.  for a in 3.74 3.75 3.76 3.78 3.79 3.80 3.81 3.82 3.83 3.84  
    
13.  do  
    
14.  cat > POSCAR <<!  
    
15.  Anatase  
    
16.  1.0  
    
17.          $a         0.0000000000         0.0000000000  
    
18.          0.0000000000         $a         0.0000000000  
    
19.          0.0000000000         0.0000000000         9.737  
    
20.     Ti    O  
    
21.      4    8  
    
22.  Direct  
    
23.       0.000000000         0.000000000         0.000000000  
    
24.       0.500000000         0.500000000         0.500000000  
    
25.       0.000000000         0.500000000         0.250000000  
    
26.       0.500000000         0.000000000         0.750000000  
    
27.       0.000000000         0.000000000         0.208059996  
    
28.       0.500000000         0.500000000         0.708060026  
    
29.       0.000000000         0.500000000         0.458059996  
    
30.       0.500000000         0.000000000         0.958060026  
    
31.       0.500000000         0.000000000         0.541939974  
    
32.       0.000000000         0.500000000         0.041940004  
    
33.       0.500000000         0.500000000         0.291940004  
    
34.       0.000000000         0.000000000         0.791939974  
    
35.  !  
    
36.  cp INCAR.relax INCAR  
    
37.  echo "a = $a"; mpirun -r ssh -np 12 ~/bin/vasp5.3.3  
    
38.    
    
39.  cp CONTCAR POSCAR  
    
40.  cp INCAR.static INCAR  
    
41.  echo "a = $a"; mpirun -r ssh -np 12 ~/bin/vasp5.3.3  
    
42.    
    
43.  E=\`grep "TOTEN" OUTCAR | tail -1 | awk '{printf "%12.6f \\n", $5 }'\`  
    
44.  V=\`grep "volume" OUTCAR | tail -1 | awk '{printf "%12.4f \\n" , $5}'\`  
    
45.  echo $a $V $E >> SUMMARY.dat  
    
46.    
    
47.  done

_复制代码_

  
  
得到的结果  
SUMMARY.dat  

1.  3.74 136.2000 -105.833996  
    
2.  3.75 136.9300 -105.865334  
    
3.  3.76 137.6600 -105.892136  
    
4.  3.78 139.1300 -105.928546  
    
5.  3.79 139.8600 -105.944722  
    
6.  3.80 140.6000 -105.955402  
    
7.  3.81 141.3400 -105.960574  
    
8.  3.82 142.0900 -105.960563  
    
9.  3.83 142.8300 -105.954437  
    
10.  3.84 143.5800 -105.949877  
    

_复制代码_

  
  
根据birch-murnaghan状态方程拟合的曲线  
![](forum.php?mod=attachment&aid=MjU3OHwzYjY4NmI1NHwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&noupdate=yes)

**figure\_1.png** _(22.95 KB, 下载次数 Times of downloads: 33)_

[下载附件 Download](forum.php?mod=attachment&aid=MjU3OHwzYjY4NmI1NHwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&nothumb=yes)

E-V

2015-10-27 18:37 上传 Uploaded

  
V0=141.863A^3  
  
  

> 1\. Select one volume and relax from starting structure keeping the volume fixed (ISIF=4 see sec. 6.24; ISMEAR=0 or 1, see section 6.38).  
> 2\. Start a second relaxation from previous CONTCAR file (if the initial cell shape was reasonable this step can be skipped, if the cell shape is kept fixed, you never have run VASP twice).  
> 3\. As a final step perform one more energy calculation with the tetrahedron method switched on (ISMEAR=-5), to get very accurate unambiguous energies (no relaxation for the final run).

  
计算流程参考：[http://cms.mpi.univie.ac.at/vasp/vasp/Accurate\_bulk\_relaxations\_with\_internal\_parameters\_two.html](http://cms.mpi.univie.ac.at/vasp/vasp/Accurate_bulk_relaxations_with_internal_parameters_two.html)  
脚本文件参考：[http://cms.mpi.univie.ac.at/vasp/vasp/Bulk\_calculations\_with\_internal\_parameters.html](http://cms.mpi.univie.ac.at/vasp/vasp/Bulk_calculations_with_internal_parameters.html) & 侯老师VASP入门教程  
  
  
  
得到平衡体积V0=141.863A^3，在POSCAR里面如果第二行为负，则代表体积，所以我又这样试了一下：  
  
INCAR  

1.  ENCUT = 520 eV # energy cut-off for the calculation  
    
2.  PREC = High  
    
3.  LREAL = .FALSE # real space projection .FALSE. or Auto  
    
4.  NSW = 999  
    
5.  IBRION = 2 # CG algorithm  
    
6.  ISIF = 4 # forces and stress are optimized

_复制代码_

  
POSCAR  

1.  Anatase  
    
2.  \-141.863  
    
3.          3.7844998837         0.0000000000         0.0000000000  
    
4.          0.0000000000         3.7844998837         0.0000000000  
    
5.          0.0000000000         0.0000000000         9.5143003464  
    
6.     Ti    O  
    
7.      4    8  
    
8.  Direct  
    
9.       0.000000000         0.000000000         0.000000000  
    
10.       0.500000000         0.500000000         0.500000000  
    
11.       0.000000000         0.500000000         0.250000000  
    
12.       0.500000000         0.000000000         0.750000000  
    
13.       0.000000000         0.000000000         0.208059996  
    
14.       0.500000000         0.500000000         0.708060026  
    
15.       0.000000000         0.500000000         0.458059996  
    
16.       0.500000000         0.000000000         0.958060026  
    
17.       0.500000000         0.000000000         0.541939974  
    
18.       0.000000000         0.500000000         0.041940004  
    
19.       0.500000000         0.500000000         0.291940004  
    
20.       0.000000000         0.000000000         0.791939974  
    

_复制代码_

  
KPOINTS  

1.  Automatic mesh  
    
2.    0  
    
3.  Gamma  
    
4.    5  5  5  
    
5.    0  0  0  
    

_复制代码_

  
vasp.pbs  

1.  #!/bin/bash  
    
2.  #$ -S /bin/bash  
    
3.  #$ -cwd  
    
4.  #$ -j y  
    
5.  #$ -N isif4  
    
6.  #$ -pe make 12  
    
7.    
    
8.  source /share/apps/intel/Compiler/11.1/073/bin/iccvars.sh intel64  
    
9.  source /share/apps/intel/Compiler/11.1/073/bin/ifortvars.sh intel64  
    
10.  source /share/apps/intel/impi/3.2.0.011/bin64/mpivars.sh  
    
11.    
    
12.    
    
13.  mpirun -r ssh -np 12 ~/bin/vasp5.3.3

_复制代码_

最后得到的CONTCAR  

1.  Anatase                                   
    
2.     1.01350313575973       
    
3.       3.7709606715685475    0.0000000000000000    0.0000000000000000  
    
4.       0.0000000000000000    3.7709606715685475    0.0000000000000000  
    
5.       0.0000000000000000    0.0000000000000000    9.5827430546962322  
    
6.     Ti   O  
    
7.       4     8  
    
8.  Direct  
    
9.    0.0000000000000000  0.0000000000000000  0.0000000000000000  
    
10.    0.5000000000000000  0.5000000000000000  0.5000000000000000  
    
11.    0.0000000000000000  0.5000000000000000  0.2500000000000000  
    
12.    0.5000000000000000  0.0000000000000000  0.7500000000000000  
    
13.    0.0000000000000000  0.0000000000000000  0.2066888233929389  
    
14.    0.5000000000000000  0.5000000000000000  0.7066888533929343  
    
15.    0.0000000000000000  0.5000000000000000  0.4566888233929389  
    
16.    0.5000000000000000  0.0000000000000000  0.9566888533929343  
    
17.    0.5000000000000000  0.0000000000000000  0.5433111466070657  
    
18.    0.0000000000000000  0.5000000000000000  0.0433111766070611  
    
19.    0.5000000000000000  0.5000000000000000  0.2933111766070611  
    
20.    0.0000000000000000  0.0000000000000000  0.7933111466070657  
    
21.    
    
22.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
23.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
24.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
25.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
26.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
27.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
28.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
29.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
30.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
31.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
32.    0.00000000E+00  0.00000000E+00  0.00000000E+00  
    
33.    0.00000000E+00  0.00000000E+00  0.00000000E+00

_复制代码_

体积是能对上的：V0=141.863=1.01350313575973^3 \* 3.7709606715685475 \* 3.7709606715685475 \* 9.5827430546962322  
  
所以最后a=1.013\*3.771=**3.820**, c=1.013\*9.583=**9.707**  
文献计算值a=3.786, c=9.737（Phys. Rev. B 2001, 63 (15), 155409.）  
实验值a=3.782, c=9.502（J. Am. Chem. Soc. 1987, 109 (12), 3639–3646.）  
这样对吗？  
  
  
  
  
  
  
补充：侯老师VASP入门教程里得到平衡体积之和，不知如何得到循环变量$i的值？？ ![](forum.php?mod=attachment&aid=MjU4MXwxOGVhMzNkNnwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&noupdate=yes)

**1.png** _(172.86 KB, 下载次数 Times of downloads: 36)_

[下载附件 Download](forum.php?mod=attachment&aid=MjU4MXwxOGVhMzNkNnwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&nothumb=yes)

2015-10-27 18:54 上传 Uploaded

  
  
![](forum.php?mod=attachment&aid=MjU3OXxiNjIyYjM3Y3wxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&noupdate=yes)

**1.png** _(187.23 KB, 下载次数 Times of downloads: 41)_

[下载附件 Download](forum.php?mod=attachment&aid=MjU3OXxiNjIyYjM3Y3wxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&nothumb=yes)

2015-10-27 18:45 上传 Uploaded

  
![](forum.php?mod=attachment&aid=MjU4MHwxM2E4NDRkNXwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&noupdate=yes)

**2.png** _(116.74 KB, 下载次数 Times of downloads: 32)_

[下载附件 Download](forum.php?mod=attachment&aid=MjU4MHwxM2E4NDRkNXwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&nothumb=yes)

2015-10-27 18:46 上传 Uploaded

  
  
  
![](forum.php?mod=attachment&aid=MjU4Mnw4MWQwODExZnwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&noupdate=yes)

**2.png** _(158.75 KB, 下载次数 Times of downloads: 29)_

[下载附件 Download](forum.php?mod=attachment&aid=MjU4Mnw4MWQwODExZnwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&nothumb=yes)

2015-10-27 18:54 上传 Uploaded

  
![](forum.php?mod=attachment&aid=MjU4M3wyYTRhM2JhOHwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&noupdate=yes)

**3.png** _(99.86 KB, 下载次数 Times of downloads: 24)_

[下载附件 Download](forum.php?mod=attachment&aid=MjU4M3wyYTRhM2JhOHwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&nothumb=yes)

2015-10-27 18:54 上传 Uploaded

  
![](forum.php?mod=attachment&aid=MjU4NHxiY2YzNGQ4ZHwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&noupdate=yes)

**4.png** _(112.2 KB, 下载次数 Times of downloads: 24)_

[下载附件 Download](forum.php?mod=attachment&aid=MjU4NHxiY2YzNGQ4ZHwxNzI3NjczNTk1fDEzNDc5fDE5ODI%3D&nothumb=yes)

2015-10-27 18:54 上传 Uploaded

  
  
  
 |