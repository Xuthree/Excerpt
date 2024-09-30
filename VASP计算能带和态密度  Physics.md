## 前言

  本文以二维InSe材料为例，详细介绍了二维材料的结构优化、静态自洽，能带计算和态密度计算的计算过程和注意事项。  
  为了对二维材料的晶格常数进行充分优化，且不对真空层厚度做优化，需要修改vasp安装包～/vasp.5.4.4/src/constr\_cell\_relax.F源文件，修改的具体说明可以参考清华大学刘锦程博士的[博文](http://web.archive.org/web/20201026044340/http://blog.wangruixing.cn/2019/05/05/constr/)，编译时直接覆盖掉原文件即可，新的constr\_cell\_relax.F文件如下：

```
!-----------------------------------------------------------------------
!
! At present, VASP does not allow to relax the cellshape selectively
! i.e. for instance only cell relaxation in x direction.
! To be more precisse, this behaviour can not be achived via the INCAR
! or POSCAR file.
! However, it is possible to set selected components of the stress tensor
! to zero.
! The most conveninent position to do this is the routines 
! CONSTR_CELL_RELAX  (constraint cell relaxation).
! FCELL contains the forces on the basis vectors.
! These forces are used to modify the basis vectors according
! to the following equations:
!
!      A_OLD(1:3,1:3)=A(1:3,1:3) ! F90 style 
!      DO J=1,3
!      DO I=1,3
!      DO K=1,3
!        A(I,J)=A(I,J) + FCELL(I,K)*A_OLD(K,J)*STEP_SIZE
!      ENDDO
!      ENDDO
!      ENDDO
! where A holds the basis vectors (in cartesian coordinates).
!
!-----------------------------------------------------------------------

      SUBROUTINE CONSTR_CELL_RELAX(FCELL)
      USE prec
      REAL(q) FCELL(3,3)

!     just one simple example
!     relaxation in x directions only
!      SAVE=FCELL(1,1)
!      FCELL=0   ! F90 style: set the whole array to zero
!      FCELL(1,1)=SAVE
!     relaxation in z direction only
!      SAVE=FCELL(3,3)
!      FCELL=0   ! F90 style: set the whole array to zero
!      FCELL(3,3)=SAVE

      LOGICAL FILFLG
      INTEGER ICELL(3,3)
      INQUIRE(FILE='OPTCELL',EXIST=FILFLG)
      IF (FILFLG) THEN
         OPEN(67,FILE='OPTCELL',FORM='FORMATTED',STATUS='OLD')
         DO J=1,3
            READ(67,"(3I1)") (ICELL(I,J),I=1,3)
         ENDDO
         CLOSE(67)
         DO J=1,3
         DO I=1,3
            IF (ICELL(I,J)==0) FCELL(I,J)=0.0
         ENDDO
         ENDDO
      ENDIF

      RETURN
      END SUBROUTINE
```

使用方法：在INCAR中设置ISIF = 3，同时在计算目录下新建OPTCELL文件，该文件为3×3矩阵，在对称性满足的情况下可以对体系的9个自由度做弛豫。例如对于正交的二维材料，OPTCELL文件可写为：

```
100  # 优化x方向
010  # 优化y方向
000  # 不优化z方向
```

## 结构优化

### 输入文件

#### INCAR

```
   SYSTEM = 2D_InSe
   ISTART = 0
   NWRITE = 2   
   PREC   = Accurate
   ENCUT  = 500
   GGA    = PE
   NSW    = 200
   ISIF   = 3
   ISYM   = 2
   NBLOCK = 1   
   KBLOCK = 1
   IBRION = 2
   NELM   = 80     
   EDIFF  = 1E-05
   EDIFFG = -0.01 
   ALGO   = Normal
   LDIAG  = .TRUE.
   LREAL  = .FALSE.
   ISMEAR = 0
   SIGMA  = 0.02
   ICHARG = 2
   LPLANE = .TRUE.
   NPAR   = 4
   LSCALU = .FALSE.
   NSIM   = 4
   LWAVE  = .FALSE.
   LCHARG = .FALSE.
   ICORELEVEL =  1
```

#### POSCAR

```
2D_InSe
1.0
        4.0836000443         0.0000000000         0.0000000000
       -2.0418000221         3.5365013772         0.0000000000
        0.0000000000         0.0000000000        25.3775005341
   In   Se
    2    2
Direct
     0.666670026         0.333330010         0.589979992
     0.666670026         0.333330010         0.478789968
     0.333329978         0.666669998         0.428439986
     0.333329978         0.666669998         0.640340007
```

#### KPOINTS

```
Monkhorst Pack of Gamma centered
0
Gamma
 13    13    1
 0.0   0.0   0.0
```

#### POTCAR

```
cat In_d/POTCAR Se/POTCAR &gt; POTCAR
```

#### OPTCELL

```
100
110
000
```

### 结果分析

-   判断是否达到收敛精度
    
    ```bash
    grep "reached required accuracy" log
    ```
    
    ```
    reached required accuracy - stopping structural energy minimisation
    ```
    
-   产看体系总能
    
    ```bash
    grep TOTEN OUTCAR | tail -n 1
    ```
    
    ```
    free  energy   TOTEN  =       -14.21463638 eV
    ```
    
-   对比优化前后晶格常数的变化

—优化前—

```
4.0836000443         0.0000000000         0.0000000000
-2.0418000221         3.5365013772         0.0000000000
0.0000000000         0.0000000000        25.3775005341
```

—优化后—

```
4.0830388054550770    0.0000000000000000    0.0000000000000000
-2.0415069539304227    3.5360297046777429    0.0000000000000000
0.0000000000000000    0.0000000000000000   25.3775005341000011
```

分析：对比发现优化后a轴和b轴方向的晶格常数略微发生了变化，而c轴方向的真空层厚度并没有发生变化，说明使用固定基矢的方法是可以对二维材料做充分优化的

-   现使用只优化原子位置、不优化晶格常数（ISIF=2）的方法对二维InSe重新进行优化，对比体系总能变化情况

—ISIF=3—

```
free  energy   TOTEN  =       -14.21463638 eV
```

—ISIF=2—

```
free  energy   TOTEN  =       -14.21464001 eV
```

分析：对比发现，两个总能几乎没有差别，说明对于初始结构比较好的体系来说，两种优化方式基本没有差别；但是对于初始结构不是很好的体系，这两种优化方法应该是有差别的，本人推荐使用固定基矢的方法充分优化二维材料晶格；另外，对于材料施加应变，且需要考虑泊松效应时，固定基矢优化是非常好的方式

## 截断能和k网格点收敛性测试

  在第一性原理计算过程中，截断能和k网格点的选取非常重要，选的太小容易造成体系没有达到最低能量状态，选的太大会耗费大量计算资源，尤其对于计算资源比较紧张的课题组，大量浪费计算资源会引起同学和导师的不满。在实际计算中对于截断能和k点选取的方法，一是参考已发表的论文值，这样有一定的说服力；二是需要自己去测试，一般保证体系总能变化范围在0.01~0.02 eV之间，就可认为截断能和k点达到了我们的计算要求。

### 二维InSe的截断能测试

  首先对二维InSe的截断能做测试，测试范围是400-900 eV，能量间隔为50 eV。

#### 输入文件

  在这一系列计算中，只需对INCAR文件做修改，其他文件无需修改；且计算完成后自动将体系总能随截断能的变化写入toten\_vs\_encut.dat文件，只需画图即可。实现方法是将INCAR以及一系列操作命令写入提交任务的脚本文件中。计算文件夹中包括：

```
KPOINTS POSCAR OPTCELL POTCAR vasp5.4.4.job
```

—vasp5.4.4.job—

```bash
#!/bin/bash #PBS -N 2D_InSe #PBS -l nodes=1:ppn=16 #PBS -m abe #PBS -j n ##PBS -o job.log ##PBS -e job.err #PBS -l walltime=250:00:00 cd $PBS_O_WORKDIR touch toten_vs_encut.dat for ENCUT in $(seq 400 50 800); do # generate INCAR cat > INCAR << EOF SYSTEM = 2D_InSe ISTART = 0 NWRITE = 2 PREC = Accurate ENCUT = $ENCUT GGA = PE NSW = 200 ISIF = 3 ISYM = 2 NBLOCK = 1 KBLOCK = 1 IBRION = 2 NELM = 80 EDIFF = 1E-05 EDIFFG = -0.01 ALGO = Normal LDIAG = .TRUE. LREAL = .FALSE. ISMEAR = 0 SIGMA = 0.02 ICHARG = 2 LPLANE = .TRUE. NPAR = 4 LSCALU = .FALSE. NSIM = 4 LWAVE = .FALSE. LCHARG = .FALSE. ICORELEVEL = 1 EOF date "+01 Today's date is: %D. The time execution %T" >> time.info mpirun -np 16 /your direction/vasp.5.4.4/bin/vasp_std > log date "+02 Today's date is: %D. The time finish %T" >> time.info echo -e "$ENCUT \c" >> toten_vs_encut.dat grep TOTEN OUTCAR | tail -n 1 | \ awk '/TOTEN/{print $(NF-1)}' >> toten_vs_encut.dat done
```

#### 计算结果

—toten\_vs\_encut.dat—

```
400 -14.21435689
450 -14.21513620
500 -14.21463059
550 -14.21432926
600 -14.21451881
650 -14.21462419
700 -14.21461680
750 -14.21457254
800 -14.21462319
```

![encut](D:\BaiduSyncdisk\Typora\images\encut.png)

**encut**

—encut.gnu—

```
set term post eps color enh solid 
datafile='toten_vs_encut.dat'
set output 'encut.eps'
set title 'toten\_vs\_encut' font 'Times_New_Roman,24'
set xlabel 'encut' font 'Times_New_Roman,18'
set ylabel 'TOTEN' font 'Times_New_Roman,18'
plot datafile  with linespoints lt 7 lw 4 pt 9 pointsize 2
set output
```

### 二维InSe的k点测试

#### 输入文件

  在这一系列计算中，只需对KPOINTS文件做修改，其他文件无需修改；且计算完成后自动将体系总能随k点的变化写入toten\_vs\_kpoints.dat文件，只需画图即可。实现方法是将kpoints以及一系列操作命令写入提交任务的脚本文件中。计算文件夹中包括：

```
INCAR POSCAR OPTCELL POTCAR vasp5.4.4.job
```

—vasp5.4.4.job—

```bash
#!/bin/bash #PBS -N 2D_InSe #PBS -l nodes=1:ppn=16 #PBS -m abe #PBS -j n ##PBS -o job.log ##PBS -e job.err #PBS -l walltime=250:00:00 cd $PBS_O_WORKDIR touch toten_vs_kpoints.dat for KPOINTS in $(seq 5 2 29); do # generate KPOINTS cat > KPOINTS << EOF Monkhorst Pack of Gamma centered 0 Gamma $KPOINTS $KPOINTS 1 0.0 0.0 0.0 EOF date "+01 Today's date is: %D. The time execution %T" >> time.info mpirun -np 16 /your direction/vasp.5.4.4/bin/vasp_std > log date "+02 Today's date is: %D. The time finish %T" >> time.info echo -e "$KPOINTS \c" >> toten_vs_kpoints.dat grep TOTEN OUTCAR | tail -n 1 | \ awk '/TOTEN/{print $(NF-1)}' >> toten_vs_kpoints.dat done
```

#### 计算结果

—toten\_vs\_kpoints.dat—

```
5 -14.19602773
7 -14.21266556
9 -14.21439462
11 -14.21461257
13 -14.21463638
15 -14.21464405
17 -14.21464831
19 -14.21464821
21 -14.21464741
23 -14.21465053
25 -14.21464752
27 -14.21464720
29 -14.21464820
```

![kpoints](D:\BaiduSyncdisk\Typora\images\kpoints.png)

**kpoints**

—kpoints.gnu—

```
set term post eps color enh solid 
datafile='toten_vs_kpoints.dat'
set output 'kpoints.eps'
set title 'toten\_vs\_kpoints' font 'Times_New_Roman,24'
set xlabel 'kpoints' font 'Times_New_Roman,18'
set ylabel 'TOTEN' font 'Times_New_Roman,18'
plot datafile  with linespoints lt 7 lw 4 pt 9 pointsize 2
set output
```

### 整体分析

  整体分析发现，对于二维InSe的截断能和k点取值分别为500 eV和13×13×1已经足够精确;与[Meng Wu](http://web.archive.org/web/20201026044340/https://pubs.rsc.org/en/content/articlelanding/2018/NR/C8NR03172J#!divAbstract)等人报道的计算细节基本一致，说明测试是合理的。

## 静态自洽

### 前言

  VASP做静态自洽的目的主要是为了得到好的电子密度和波函数文件，为了使后续的性质计算可以读取电子密度和波函数，进而增加收敛速度。

### 输入文件

—INCAR—

```
SYSTEM = 2D_InSe
ISTART = 0
NWRITE = 2   
PREC   = Accurate
ENCUT  = 500
GGA    = PE 
NSW    = 0
ISIF   = 2
ISYM   = 2
NBLOCK = 1   
KBLOCK = 1
IBRION = -1 
NELM   = 80   
EDIFF  = 1E-05
EDIFFG = -0.01 
ALGO   = Normal
LDIAG  = .TRUE.
LREAL  = .FALSE.
ISMEAR = 0
SIGMA  = 0.02
ICHARG = 2
LPLANE = .TRUE.
NPAR   = 4
LSCALU = .FALSE.
NSIM   = 4
LWAVE  = .TRUE.
LCHARG = .TRUE.
ICORELEVEL =  1
```

—POSCAR—  
将优化完后的CONTCAR直接拷贝为POSCAR即可  
—POTCAR—  
与优化时的POTCAR文件相同  
—KPOINTS—  
自洽计算要求有密的k网格点，在计算条件允许的情况下要求自洽的k网格点大致为优化时的2倍左右。

```
Monkhorst Pack of Gamma centered
0
Gamma
 25    25    1
 0.0   0.0   0.0
```

## 态密度

### 前言

  态密度是固体物理中一个非常重要的概念，在实验上可利用X射线发射光谱方法测定，在理论上可使用第一性原理计算软件计算得出；可以用于分析材料材料能隙特性、体系成键的特性、体系的自旋极化、相邻原子之间的作用强弱、电子比热和顺磁磁化率等性质。

### 输入文件

—INCAR—

```
SYSTEM = 2D_InSe
ISTART = 1
NWRITE = 2
PREC   = Accurate
ENCUT  = 500
GGA    = PE
NSW    = 0
ISIF   = 2
ISYM   = 2
NBLOCK = 1
KBLOCK = 1
IBRION = -1
NELM   = 80
EDIFF  = 1E-05
EDIFFG = -0.01
ALGO   = Normal
LDIAG  = .TRUE.
LREAL  = .FALSE.
ISMEAR = -5
SIGMA  = 0.02
ICHARG = 11
LPLANE = .TRUE.
NPAR   = 4
LSCALU = .FALSE.
NSIM   = 4
LWAVE  = .FALSE.
LCHARG = .FALSE.
ICORELEVEL =  1
LORBIT = 11  # output PDOS
```

—KPOINTS—  
DOS计算要求非常密集的k网格点，至少要求与自洽时k网格点相同，或可适当增加。

```
Monkhorst Pack of Gamma centered
0
Gamma
 29    29    1
 0.0   0.0   0.0
```

POSCAR、POTCAR文件与自洽时相同；并保证在计算目录下有自洽时产生的CHGCAR和WAVECAR文件，以供读取。

### 结果分析

#### 总的态密度

![TDOS](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/TDOS.png)

**TDOS**

#### 局域态密度

![LDOS](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/LDOS.png)

**LDOS**

#### Se的分波态密度

![Se_PDOS](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/Se_PDOS.png)

**Se\_PDOS**

#### In的分波态密度

![In_PDOS](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/In_PDOS.png)

**In\_PDOS**

### 前言

  能带理论是研究材料性质的重要方法，现利用VASP软件计算二维InSe的能带。

### 输入文件

—INCAR—

```
SYSTEM = 2D_InSe
ISTART = 1
NWRITE = 2
PREC   = Accurate
ENCUT  = 500
GGA    = PE
NSW    = 0
ISIF   = 2
ISYM   = 2
NBLOCK = 1
KBLOCK = 1
IBRION = -1
NELM   = 80
EDIFF  = 1E-05
EDIFFG = -0.01
ALGO   = Normal
LDIAG  = .TRUE.
LREAL  = .FALSE.
ISMEAR = 0
SIGMA  = 0.02
ICHARG = 11
LPLANE = .TRUE.
NPAR   = 4
LSCALU = .FALSE.
NSIM   = 4
LWAVE  = .FALSE.
LCHARG = .FALSE.
ICORELEVEL =  1
LORBIT = 11
```

—KPOINTS—

```bash
k-points along high symmetry lines 100 Line-mode Rec 0.3333333 0.3333333 0 !K 0 0 0 !GAMMA 0 0 0 !GAMMA 0.5 0 0 !M 0.5 0 0 !M 0.3333333 0.3333333 0 !K
```

POSCAR、POTCAR文件与自洽时相同；并保证在计算目录下有自洽时产生的CHGCAR和WAVECAR文件，以供读取。

### 结果分析

二维InSe能带  

![band.png](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/band.png)

**band.png**

漂亮的二维InSe能带

![band.png](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/new_band.png)

**band.png**

二维InSe按原子投影的能带图

![PBAND.png](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/PBAND.png)

**PBAND.png**

二维InSe按原子的s、p、d轨道投影的能带图  
In原子

![PBANDInspd.png](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/PBANDInspd.png)

**PBANDInspd.png**

Se原子

![PBANDSespd.png](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/PBANDSespd.png)

**PBANDSespd.png**

InSe

![PBNDSeInspd.png](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/PBNDSeInspd.png)

**PBNDSeInspd.png**

二维InSe按原子的s、px、py、pz轨道投影的能带图  
In原子

![PBANDInspxpypz.png](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/PBANDInspxpypz.png)

**PBANDInspxpypz.png**

  

![PBANDInspxpypz.png](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/PBANDIn.png)

**PBANDInspxpypz.png**

Se原子

![PBANDSespxpypz.png](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/PBANDSespxpypz.png)

**PBANDSespxpypz.png**

  

![PBANDSespxpypz.png](http://web.archive.org/web/20201026044340im_/https://yh-phys.github.io/image/PBANDSe.png)

**PBANDSespxpypz.png**

Note：通过做投影能带图，与分波态密度做对比发现，对于带边位置的贡献是一致的