# VMD初始化文件(vmd.rc)我的推荐设置 - 分子模拟 (Molecular Modeling) - 计算化学公社
| 

**VMD初始化文件(vmd.rc)我的推荐设置**

My recommended settings of VMD initialization file (vmd.rc)  
  
文/Sobereva@[北京科音](http://www.keinsci.com)

First release: 2020-Apr-1   Last update: 2023-Apr-4

  
  
VMD（[http://www.ks.uiuc.edu/Research/vmd/](http://www.ks.uiuc.edu/Research/vmd/)）是用得极为广泛的化学体系可视化程序，由于其极度灵活，有很多技巧可以使其用起来更方便。  
  
VMD启动时会先用初始化文件对一些设置进行初始化，即执行里面的各种命令，用户也可以往里添加额外的命令。对于Windows版来说，这个文件就是VMD目录下的vmd.rc。对于Linux版来说，这个文件叫.vmdrc，VMD会先在当前目录下搜索，没有的话就去找~/.vmdrc，还没有的话去找$VMDDIR/.vmdrc（这里$VMDDIR环境变量是没有预先定义的），如果还找不到此文件，就会用默认设置。  
注：Linux下的.vmdrc文件默认出现在安装目录下，比如以默认路径安装会出现为/usr/local/lib/vmd/.vmdrc，但如果不配置VMDDIR环境变量的话这个文件并不会被VMD在启动时自动载入。Linux下.vmdrc一般都是自行在用户主目录下创建。  
  
在此我将我自己的初始化文件里的设置进行分享，其中额外添加的内容如下（放到原有内容后头即可）。下文的叙述是对撰文时最新版本VMD 1.9.3而言的。  
  
mol default style {Lines 2.0}  
display depthcue off  
#color Display Background white  
#axes location Off  
display rendermode GLSL  
display distance -8.0  
  
proc bw {} {color Display Background white}  
proc bb {} {color Display Background black}  
  
user add key Right {animate next}  
user add key Left {animate prev}  
user add key Up {animate goto \[expr $vmd\_frame(\[molinfo top\])+10\]}  
user add key Down {animate goto \[expr $vmd\_frame(\[molinfo top\])-10\]}  
user add key b {mol bondsrecalc all; topo retypebonds}  
  
proc bt {} {  
global vmd\_frame  
trace variable vmd\_frame(\[molinfo top\]) w updatebond  
}  
proc updatebond {args} {  
mol bondsrecalc all  
topo retypebonds  
}  
proc bn {} {  
global vmd\_frame  
trace vdelete vmd\_frame(\[molinfo top\]) w updatebond  
}  
  
proc fog {} {  
display depthcue on  
display cuemode Linear  
display cuestart 1.75  
display cueend 2.5  
}  
  
下面解释一下做这些设置有什么好处。  
  
程序默认的显示方式是Lines，但是线的粗细太细，往往看不清楚，所以用mol default style {Lines 2.0}将默认的显示方式改为两倍粗细的Lines。  
  
程序默认开着雾化，即让距离镜头越远的物体的颜色混入越多的背景色。这会导致在黑色背景下物体的颜色显得不够鲜艳，而在白色背景下物体又显得有点雾蒙蒙，因此用display depthcue off将雾化效果关掉。  
  
#color Display Background white这行是被注释掉的。如果你想让VMD启动后默认就用白背景，就把#去掉。  
  
#axes location Off这行也是被注释掉的，如果你想让VMD默认不显示坐标轴，就把#去掉。  
  
VMD默认用称作Normal的Rendermode，但此时有些材质的显示效果很差，甚至Transparent材质根本没法正确显示出透明效果。因此通过display rendermode GLSL将默认的Rendermode设为效果好得多的GLSL。  
  
有很多人肯定早已发现画面边缘的物体畸变得特别厉害，很难看。通过display distance -8.0语句可以充分避免。但导致一个问题就是原本在窗口左下方的坐标轴看不到了，需要坐标轴的时候可以选Display - Axes - Origin让坐标轴显示在窗口中央。  
  
下面这两行是自定义命令。在VMD的文本窗口里输入bw（意为background white）就可以令背景立刻变为白的，输入bb就可以令背景立刻变为黑的，非常方便。  
proc bw {} {color Display Background white}  
proc bb {} {color Display Background black}  
  
下面的内容是设置用户自定义快捷键。载入轨迹后，在图形窗口处于被激活的状态时（激活窗口就是鼠标点击这个窗口的意思），按左、右键就可以分别后退1帧、向前1帧，按上、下键就可以分别增加10帧、后退10帧。这使得观看轨迹方便很多。  
user add key Right {animate next}  
user add key Left {animate prev}  
user add key Up {animate goto \[expr $vmd\_frame(\[molinfo top\])+10\]}  
user add key Down {animate goto \[expr $vmd\_frame(\[molinfo top\])-10\]}  
  
关于VMD判断原子间连接关系的问题我在《谈谈VMD可视化程序的连接关系的判断和设置问题》（[http://sobereva.com/534](http://sobereva.com/534)）里有非常详细的说明。为了能很方便地让VMD对当前帧根据当前结构重新判断连接关系，增加了下面这句。图形窗口处于激活状态时按b键（意为bond）就能重新判断连接关系。  
user add key b {mol bondsrecalc all; topo retypebonds}  
  
如果想播放的时候实时自动更新连接关系，而不需要每次都按b键，靠以下语句可以实现。也就是定义了一个bt命令，如果在命令行窗口输入了bt，那么每当当前top体系的帧号发生了变化，就会调用updatebond命令自动来更新连接关系。这样做的代价就是对较大体系，每播放一帧都要根据距离重新判断连接关系，播放时会比较卡。  
proc bt {} {  
global vmd\_frame  
trace variable vmd\_frame(\[molinfo top\]) w updatebond  
}  
proc updatebond {args} {  
mol bondsrecalc all  
topo retypebonds  
}  
  
下面的内容定义bn命令。如果不想自动更新成键方式了，可以输入bn命令取消掉对帧号的跟踪即可。  
proc bn {} {  
global vmd\_frame  
trace vdelete vmd\_frame(\[molinfo top\]) w updatebond  
}  
  
雾化效果绝非毫无意义。只要恰当使用，就可以让近距离的物体完全不受影响，而让偏远的原子恰当地雾化，避免扰乱视觉、妨碍清楚地观看近距离的物体。下面的语句是自定义命令，只要在文本窗口输入fog，就可以打开雾化并且使用在我来看比较合适的雾化设置。如果觉得实际效果不好，需要进一步调节，可以用Display - Display Settings，修改里面的Cue Start和Cue End。  
proc fog {} {  
display depthcue on  
display cuemode Linear  
display cuestart 1.75  
display cueend 2.5  
} |