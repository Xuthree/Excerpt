# 谈谈RDF - 分子模拟 (Molecular Modeling) - 计算化学公社
| _本帖最后由 丁越 于 2022-6-14 13:23 编辑_  


**谈谈RDF**

  

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*  
2022.6.14注： 重新改写了脚本  
\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*  


RDF（radial distribution function）径向分布函数（或称对相关函数），其含义是每个球层位置的被计算组的原子的数密度与其平均数密度的比值，描述密度如何因距离参考粒子的距离而变化。其中被计算组的平均数密度为被计算组粒子的个数除以盒子的体积。给出的g(r)~r图中横坐标是距参考粒子的距离，纵坐标指相对于盒子里被计算的粒子的平均密度的比值。计算RDF时需要定义盒子的周期性。

  

根据RDF的含义，我们将RDF公式定义如下：

$$
g_{ab}(r)=\frac{\rho_{ab}(r)}{N_b/V}
$$

**1.2\_ Radial Distribution Function - Chemistry LibreTexts.png**_

[下载附件 Download](forum.php?mod=attachment&aid=NDkzOTZ8N2JiN2JkMTV8MTcyNzY3MzgzNnwxMzQ3OXwyOTcxNQ%3D%3D&nothumb=yes) 

其中，rho\_ab(r)的含义为距参考粒子r处的球层内计算组粒子的数密度，分母项是计算组粒子的平均数密度。我们将这一公式进一步扩展，

$$
 g_ {ab} (r)= \frac {V}{N_ {a}\cdot N_ {b}}  \sum_{i=1}^{N_a} \sum _ {j=i+1}^{N_b} 
 \left<  \delta (r - | \overrightarrow {r}_{i}(t) - \overrightarrow {r}_j(t) |)\right>_t
$$


**rdf1.png** _(20.17 KB, 下载次数 Times of downloads: 48)_

[下载附件 Download]

![img](http://bbs.keinsci.com/forum.php?mod=attachment&aid=NDkzOTl8YWQ4OTgyMjN8MTcyNzgzMzEyMHwxMzQ3OXwyOTcxNQ%3D%3D&noupdate=yes)

**图片2.png** 

便得到了如何由轨迹文件求得RDF，上面公式来源于该篇文献（J. Chem. Inf. Model. 2011, 51, 2007–2023）。上式中，a为参考组粒子，b为计算组粒子，V是盒子的体积。式中第一项求和符号循环所有的参考组粒子，第二个求和符号循环所有的计算组粒子。在t时刻，距参考粒子a\_i半径为r，厚度为dr的球壳内粒子b的平均数密度是通过遍历所有b粒子的δ(dr)函数然后取平均求得（b粒子如果在dr厚度球层内统计数目加1，否则为0）。如果要对整个轨迹求平均的话代码中需要嵌套循环所有帧。接着再给最外层套上循环所有参考粒子a，然后再除以a粒子数目Na求取平均，这样就到了了距a半径为r处发现b粒子的平均数密度，最后再除以b粒子的平均数密度（N\_b/V）便得到了g(r)的值。（PS：公式中δ函数是统计数目的，但是写代码时得除以球层体积来得到球层中b的数密度，我不清楚公式中为什么不写上除以球层体积？）。弄明白RDF计算的基本原理后，利用VMD求解RDF的TCL语言代码如下所示：

```tcl
#This script is used to calculate the pair radial distribution function.
#NOTE: PBC conditions must be correctly setted in VMD otherwise the results will be meaningless.

#**********PARAMETERS***********
set refatm "element O"
set calcatm "element O"
set dr 0.1
set maxr 5
set begframe 0
set endframe -1
set stride 1
set fileid [open rdf.dat w]
#*******************************
#                PART 1-calculate RDF
#*******************************
set nframe [molinfo top get numframes]
if { $endframe == -1 } {set endframe [expr $nframe-1]}
set totframe [expr int(double($endframe-$begframe)/$stride)+1]
set refsel [atomselect top "$refatm"]
set Nref [$refsel num]
set calcsel [atomselect top "$calcatm"]
set Ncalc [$calcsel num]
set nbin [expr int(double($maxr)/$dr)]
set rtmp 0
set pi 3.1415926

set l [molinfo top get a]
set w [molinfo top get b]
set h [molinfo top get c]
set V [expr $l*$w*$h]
set rho_calc [expr double($Ncalc)/$V]

for {set i 1} {$i<=$nbin} {incr i} {
        set N($i) 0 
}

for {set i $begframe} {$i<=$endframe} {incr i $stride} {
        puts -nonewline [format "      Now is calculating %i_th frames.....\r" $i]
        for {set j 1} {$j<$nbin} {incr j} {
                foreach k [$refsel get index] {
                        set sel1 [atomselect top "$calcatm && pbwithin $rtmp of index $k"]
                        set sel2 [atomselect top "$calcatm && pbwithin [expr $rtmp+$dr] of index $k"]
                        $sel1 frame $i
                        $sel2 frame $i
                        $sel1 update
                        $sel2 update
                        set N($j) [expr [$sel2 num]-[$sel1 num]+$N($j)]
                }
                set Vtmp [expr 4.0/3.0*$pi*(3.0*($rtmp)**2 * $dr + 3.0*($dr)**2 * $rtmp + ($dr)**3)]
                set rmid [expr ($j-0.5)*$dr]
                set avgN($j) [expr double($N($j))/$Nref]
                set (gtmp$rmid,frame$i) [expr double($avgN($j))/($rho_calc*$Vtmp)]
                set rtmp [expr $j*$dr]
        }
        set rtmp 0
        for {set h 1} {$h<$nbin} {incr h} {
                set N($h) 0
                set avgN($h) 0
        }
}

for {set i 1} {$i<$nbin} {incr i} {
        set rmid [expr ($i-0.5)*$dr]
        set g($rmid) 0
}

for {set i 1} {$i<$nbin} {incr i} {
        set rmid [expr ($i-0.5)*$dr]
        for {set j $begframe} {$j<=$endframe} {incr j $stride} {
                set g($rmid) [expr $g($rmid)+$(gtmp$rmid,frame$j)]
        }
        puts $fileid "$rmid \t [expr double($g($rmid))/$totframe]"
}
close $fileid

#******************************
#                PART 2-integral rdf 
#******************************
set fileid2 [open int_rdf.dat w]
set fileid3 [open rdf.dat r]
#******************************
set iline 0

while { [gets $fileid3 lval] > 0 } {
        set iline [expr $iline+1]
        set lval1 [lindex $lval 0]
        set lval2 [lindex $lval 1]
        set tmp1($iline) $lval1
        set tmp2($iline) $lval2        
}

set tmp1(0) 0
set tmp2(0) 0
set intval 0
foreach i [lsort -increasing -int [array names tmp1]] {
        if { $i < [lindex [lsort -decreasing -int [array names tmp1]] 1] } {
                set valtmp1 [expr 0.5*($tmp1($i)+$tmp1([expr $i+1]))]
                set valtmp2 [expr 0.5*($tmp2($i)+$tmp2([expr $i+1]))]
                set intvaltmp [expr 4*$pi*($valtmp1)**2*$dr*$valtmp2*$rho_calc]
                set intval [expr $intvaltmp+$intval]
                puts $fileid2 "$valtmp1 \t $intval"
        }
}
close $fileid2
close $fileid3
```

代码中34~55行是求解RDF的关键步骤，此处设定了三个循环，第一个for循环循环r从0到rmax的径向距离，第二个foreach循环我们遍历所有的参考组粒子a\_i。第三个最内层for循环我们循环所有帧,然后累加在r\_i处，以a\_i为参考粒子，dr厚度球层内粒子的数目。统计球层粒子的数目我们使用了pbwithin原子选择语法，先用pbwithin得到r\_i半径内粒子的数目，然后再得到r\_i+dr半径内粒子数目，二者做差就是dr球层内粒子的数目。下面是一个单层Fe原子的测试样本，我们顺便对比了一下和VMD自带的RDF插件统计结果的差异：

![](http://bbs.keinsci.com/forum.php?mod=attachment&aid=NDk0MDN8OTlmN2RiZTl8MTcyNzY3MzgzNnwxMzQ3OXwyOTcxNQ%3D%3D&noupdate=yes)

**pic.png** _(53.21 KB, 下载次数 Times of downloads: 50)_

[下载附件 Download](forum.php?mod=attachment&aid=NDk0MDN8OTlmN2RiZTl8MTcyNzY3MzgzNnwxMzQ3OXwyOTcxNQ%3D%3D&nothumb=yes)

2022-5-18 17:06 上传 Uploaded

![](http://bbs.keinsci.com/forum.php?mod=attachment&aid=NDk0MDR8MDNjNTQ0MTN8MTcyNzY3MzgzNnwxMzQ3OXwyOTcxNQ%3D%3D&noupdate=yes)

**result.png** _(105.5 KB, 下载次数 Times of downloads: 35)_

[下载附件 Download](forum.php?mod=attachment&aid=NDk0MDR8MDNjNTQ0MTN8MTcyNzY3MzgzNnwxMzQ3OXwyOTcxNQ%3D%3D&nothumb=yes)

2022-5-18 17:06 上传 Uploaded

图. Fe-Fe的RDF。蓝色线的为脚本计算结果。黑色线为VMD的RDF插件计算结果。


由上图中可以看出每个Fe原子存在着三种不同配位形式的Fe原子，即相邻距离不等的Fe原子（图中相邻Fe原子间距分别为2.58，3.65，4.47），因而RDF图中出现了三种峰。  

最后再讨论一下RDF中统计的最远径向距离rmax的问题，即取值半径rmax不能超过盒子最小边长的一半。下图中蓝色圈是r=lattice/2，红色圈大小超过了盒子最小边长的一半，此时半径内包含了镜像粒子（如红色圆圈中两个互为镜像的粒子）。所以当r<lattice/2时，在任意ri处统计的粒子均是属于盒子内的粒子本身而不含有其镜像粒子。而当r>lattice/2时,在g(ra)处统计的计算组粒子数目中额外计入了一个和rb处粒子互为镜像的粒子，显然这样的统计是没有意义的。不知道我理解的对不对，欢迎大家的讨论！  

![img](http://bbs.keinsci.com/forum.php?mod=attachment&aid=NDk0MDd8NTgzOTdkYjF8MTcyNzgzMzEyMHwxMzQ3OXwyOTcxNQ%3D%3D&noupdate=yes)

**屏幕截图 2022-05-18 173258.png** _(585.28 KB, 下载次数 Times of downloads: 29)_

[下载附件 Download](forum.php?mod=attachment&aid=NDk0MDd8YzU0MDk3Y2F8MTcyNzY3MzgzNnwxMzQ3OXwyOTcxNQ%3D%3D&nothumb=yes)


