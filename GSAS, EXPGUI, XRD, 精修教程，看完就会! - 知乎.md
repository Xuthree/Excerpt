友情提示：已开通咨询，有需要的欢迎提问！

以下是原文

\---------------------------------------------------------------------

相信搜索到这篇文章的小伙伴们对结构精修多少有些了解。这里简单提一下精修的作用：

**a. 对材料相结构进行深度分析，包括相组成，[晶胞参数](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E6%99%B6%E8%83%9E%E5%8F%82%E6%95%B0&zhida_source=entity)，尺寸，密度，原子占位和占比等；**

**b. 可分析晶粒大小，择优取向，内部应力等；**

**c. 对于多相物质能分析各相的占比；**

**d. 分析陶瓷材料掺杂对结构的影响等。** 

**e. last but not least 可以提升论文质量！**

\--------------------------------分---割---线---------------------------------

本文主要聊一下GSAS-EXPGUI精修软件及其使用方法，不涉及理论，请上车的小伙伴们坐稳扶好。

**1\. EXPGUI下载安装**
------------------

GSAS (General Structure Analysis System)是一款数据精修软件，开发者是A.C. Larson 和 R.B. Von Dreele\[1\]。GSAS可用来处理粉末XRD，[单晶衍射](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E5%8D%95%E6%99%B6%E8%A1%8D%E5%B0%84&zhida_source=entity)，中子衍射等类型数据。初代GSAS基于DOS窗口，通过输入指令控制精修过程。

随着Windows的普及，[DOS控制](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=DOS%E6%8E%A7%E5%88%B6&zhida_source=entity)对一般用户而言变得不友好。因此2001年，B.H. Toby在GSAS基础上推出了用户交互界面的EXPGUI软件\[2\]。EXPGUI通过鼠标和键盘即可[控制参数](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E6%8E%A7%E5%88%B6%E5%8F%82%E6%95%B0&zhida_source=entity)，使用起来非常方便，此外，软件所提供的画图功能也可实时追踪精修进度，因此受到学者们广泛好评。EXPGUI下载地址

网站提供了Windows, Mac和Linux版本的下载通道。点击对应的版本即可进入下载。

![](https://pic2.zhimg.com/v2-dc1793bce0cde405f471cbf49cb868b7_b.jpg)

此外这里提供了**Windows**版本的连接，如果上述网页打不开的小伙伴[请移步](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E8%AF%B7%E7%A7%BB%E6%AD%A5&zhida_source=entity)百度网盘：

链接: [https://pan.baidu.com/s/1nh4\_34BIKX45ECKaSMwYwg](https://pan.baidu.com/s/1nh4_34BIKX45ECKaSMwYwg) [提取码](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E6%8F%90%E5%8F%96%E7%A0%81&zhida_source=entity): xdcb

目前最新版本是GSASII，基于python语言由美国[橡树岭国家实验室](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E6%A9%A1%E6%A0%91%E5%B2%AD%E5%9B%BD%E5%AE%B6%E5%AE%9E%E9%AA%8C%E5%AE%A4&zhida_source=entity)开发\[3\]。新版软件提供了更加丰富的用户界面，更完善的模型工具和更高的[数据兼容性](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E6%95%B0%E6%8D%AE%E5%85%BC%E5%AE%B9%E6%80%A7&zhida_source=entity)。需要的小伙伴可以猛戳下面地址下载：

由于本文主要针对EXPGUI精修操作的讲解，暂不涉及GSASII，本质上两者使用的是相同的[计算方法](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E8%AE%A1%E7%AE%97%E6%96%B9%E6%B3%95&zhida_source=entity)，以后会抽时间讲解GSASII的使用。

EXPGUI安装简单，Windows用户直接双击下载的软件包，按照安装教程操作即可。

![](https://picx.zhimg.com/v2-ac025666a87fa2a199b10896a03b05a9_b.jpg)

安装成功后，桌面上出现EXPGUI图标

![](https://picx.zhimg.com/v2-b6f52daa2e9f8365a6e829000857c7f3_b.jpg)

\-----------------------------分---割---线------------------------

**2\. 精修必备文件（以粉末XRD数据为例）**
--------------------------

在开始使用EXPGUI之前，需要准备以下数据：**XRD数据，模型文件Cif，仪器参数prm文件**。那位说了："你快别说了，XRD我知道，但是这个Cif文件是什么？仪器参数文件又是啥？"

别急，一个一个来。

（1）[XRD数据](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=3&q=XRD%E6%95%B0%E6%8D%AE&zhida_source=entity) .gsas

常见的实验室xrd数据格式有raw, xy, xrdml, gsas等，这里需要的是gsas格式的数据。如果你手里不是该格式数据，则需要转换格式。这里有一个[免费软件](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E5%85%8D%E8%B4%B9%E8%BD%AF%E4%BB%B6&zhida_source=entity)Powdll converter可供使用。点击红色框区域下载安装即可。安装完成后讲xrd数据拖进软件，选择输出格式为gsas。

![](https://pica.zhimg.com/v2-91a9832c74a3f8f4cf10b258068a6954_b.jpg)

（2）Cif模型文件 .cif

Cif文件是一种常见的提供结构信息的文件。它的全称是Crystallographic Information File。该文件格式由[国际晶体学会](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E5%9B%BD%E9%99%85%E6%99%B6%E4%BD%93%E5%AD%A6%E4%BC%9A&zhida_source=entity)指定，提供[晶体结构](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E6%99%B6%E4%BD%93%E7%BB%93%E6%9E%84&zhida_source=entity)信息。一个完整的Cif文件应该包括以下重要信息：

![](https://pic3.zhimg.com/v2-2bcc3e0cc63b762693efe16e64d18272_b.jpg)

![](https://picx.zhimg.com/v2-4045664c1993ef0f1f0999ae386253af_b.jpg)

Cif文件下载地址有很多，如ICSD 库(用学校账户登录)，COD(免费)等.

知道了在哪获取Cif文件，在精修之前需要对自己的材料结构比较了解，知道**[空间群](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E7%A9%BA%E9%97%B4%E7%BE%A4&zhida_source=entity)和原子位置**。可以通过查阅文献，对比Jade PDF卡片库等方式获取，这里需要提的是，前人报道的结构不一定完全正确，需加以甄别，这也是晶体结构学至今仍然在发展的原因。如果知道了空间群，可以登录上述文章内提供的[数据库](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E6%95%B0%E6%8D%AE%E5%BA%93&zhida_source=entity)，这里以ICSD库为例：输入化学式中元素种类和个数，点击Run Query。

![](https://pica.zhimg.com/v2-60d7a0eecba1bc1439d27f045bf0f25c_b.jpg)

找到对应的Cif文件，点击下载即可。

![](https://pic1.zhimg.com/v2-52da67bcf08f0f726797a491053f57a8_b.jpg)

（3）仪器参数 .prm

EXPGUI自带一个仪器参数文件，inst\_xry.prm，在C:\\GSAS\\example路径下，如果你选择自动安装的话，否则需要更换路径中的盘符，例如E:\\GSAS\\example。仪器参数文件非常重要，原则上需要根据实际测样的仪器参数来修改该文件，它包括Kα1、Kα2波长，样品[零点漂移](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E9%9B%B6%E7%82%B9%E6%BC%82%E7%A7%BB&zhida_source=entity)等。

准备好以上三个文件之后，将它们放入同一个文件夹，方便取用。

\--------------------分---割---线---------------------------------

3\. 精修之旅
--------

此次精修我以SnO2为例，该gsas数据文件已放在网盘，需要的同学请尽情下载使用：

链接: [https://pan.baidu.com/s/1xnXDAWv2PPeIev3xzIc4Aw](https://pan.baidu.com/s/1xnXDAWv2PPeIev3xzIc4Aw) 提取码: 467v

3.1 建立项目

双击打开桌面图标，看到以下界面，选择好[工作路径](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E5%B7%A5%E4%BD%9C%E8%B7%AF%E5%BE%84&zhida_source=entity)之后，输入[项目名称](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E9%A1%B9%E7%9B%AE%E5%90%8D%E7%A7%B0&zhida_source=entity)，注意名称不能有空格和特殊符号，否则报错。完成后点击Read。在弹出窗口上选择Create.

![](https://pic3.zhimg.com/v2-7d2a66019396d14c7d56a7ca8ba57338_b.jpg)

随后输入一个名称，可以是样品名，这里我使用SnO2为例。

![](https://picx.zhimg.com/v2-e9248261b4a86c57a25ff9097c44c259_b.jpg)

选择Continue，接下来看到一个如下对话框，选择Add [phase](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=phase&zhida_source=entity)。

![](https://pic3.zhimg.com/v2-e73884848ae251e89393ed3434b276ce_b.jpg)

之后弹出对话框，可以选择手动输入晶胞参数信息，亦可通过读取Cif文件自动输入。这里选择读取，在弹出的列表中选择第二个Crystallographic information file (cif), 找到刚才下载的[cif](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=3&q=cif&zhida_source=entity)文件所在路径，选择open。

![](https://picx.zhimg.com/v2-1371dc6f8c5d18c14d0c90d6f2d67ba1_b.jpg)

自动弹出如下对话框，选择Continue之后，在弹出的框中继续选择Continue。

![](https://pic2.zhimg.com/v2-1e180b3e0904516e1ec36e91d8117737_b.jpg)

点击[add atoms](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=add+atoms&zhida_source=entity)

![](https://picx.zhimg.com/v2-121c3f9b45343be86a827762f7079e11_b.jpg)

获得如下界面，此时选择Powder

![](https://pic1.zhimg.com/v2-e7a69384d7ebe69694ec6df7acad79ee_b.jpg)

进入Powder界面后选择Add new histogram

![](https://pic4.zhimg.com/v2-6953cc6957ccc290c71c491e1a528b8d_b.jpg)

在弹出界面选择Select file，分别添加gsas数据和[prm文件](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=2&q=prm%E6%96%87%E4%BB%B6&zhida_source=entity)。

![](https://pic3.zhimg.com/v2-705f81bb6a19ffa00372881e8b58865e_b.jpg)

添加好数据之后，点击add完成。

![](https://pic1.zhimg.com/v2-950cafe8d61501c9b7e285d9ce1dd142_b.jpg)

现在一切就绪，可以开始精修了，小伙伴你是不是已近等不及了呢？说实话码字码到这里我自己都等不及了, 23333333333。

3.2 开始精修

坐稳啦各位，现在要做的是选择Edit Background, 在弹出界面Function type选1，Number of terms选6-24任意数字（并不一定越大越好，需要视情况而定），然后勾上Refine Zero之后的小方框。

![](https://pica.zhimg.com/v2-e7c108be9d23ac370214f80bd19b26f6_b.jpg)

![](https://pic1.zhimg.com/v2-3061aa3c6de9668813b52cb97703a61a_b.jpg)

点击Powpref，弹出对话框，按任意键继续。选择Load New。重复该步骤两次，然后点击旁边的genles。

![](https://pic1.zhimg.com/v2-ac9a10674a53608c93975951f99c7bae_b.jpg)

一开始wRp值比较高，没关系，可以多点几次，任意键继续。选择Load New。

![](https://pic1.zhimg.com/v2-a9b6e528b545e0fe38918e4338963eb8_b.jpg)

重复genles过程直到出现收敛。

![](https://pica.zhimg.com/v2-09e32548928551c795ac66ca8ec27fa0_b.jpg)

选择Phase, 在[refine cell](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=refine+cell&zhida_source=entity)后打勾，重复genles步骤直到收敛。这时需要再重复一次Powpref-genles过程。点击Live plot查看精修效果图

![](https://pic1.zhimg.com/v2-703247897e94b96ea6b860d5752afe30_b.jpg)

![](https://picx.zhimg.com/v2-e1897060d7d4c7ef53bf313e681718af_b.jpg)

可以看到峰位置没问题，峰形需要进一步改善。此时选择Profile，在GU和LX前打勾，并执行genles。

![](https://pic1.zhimg.com/v2-4cf77ff25283543a504ab8ca302d3248_b.jpg)

可以看到[wRp值](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=2&q=wRp%E5%80%BC&zhida_source=entity)明显降低。继续执行genles直到收敛。

![](https://pic3.zhimg.com/v2-870ee13348a67496c0afadc8cbfb368a_b.jpg)

此时选择Phase, 用鼠标拖拽选中所有原子，并在u前方打勾。之后执行genles。

![](https://pica.zhimg.com/v2-c4d387ae82229d3d48db39e6b1e4223e_b.jpg)

wRp进一步降低，查看Liveplot，看到精修图明显改善。

![](https://pic3.zhimg.com/v2-8717b5da79954faf9ce3e75007cd81c4_b.jpg)

![](https://picx.zhimg.com/v2-26f8c19117bbac67953298cdb1314f45_b.jpg)

至此本文大部分工作已经结束，精修结果储存在同路径下 .lst文件中，使用[记事本](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E8%AE%B0%E4%BA%8B%E6%9C%AC&zhida_source=entity)即可打开，直接翻到最后部分，就是最后一次refine的结果。包括晶胞参数，晶胞体积，原子位置，热值，晶胞密度等等

有以下几点说明：

由于示例中这个样品相对简单，故不需要设置约束条件。同时由于XRD对轻原子不灵敏，因此在精修过程中不建议修[氧原子](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E6%B0%A7%E5%8E%9F%E5%AD%90&zhida_source=entity)或氢原子位置，很大概率会报错。如果遇到报错，点击expedt，输入d，回车，然后输入y，回车即可向前返回一步。其它参数如原子吸收，择优取向等则需视情况而定。对多相材料，也可进行精修，过程差不多，只不过麻烦一些。

GSAS软件整体用起来感觉界面简洁，收敛计算快速。小伙伴们可以自己探索一番，利用[示例数据](https://zhida.zhihu.com/search?content_id=170429403&content_type=Article&match_order=1&q=%E7%A4%BA%E4%BE%8B%E6%95%B0%E6%8D%AE&zhida_source=entity)看能不能进一步改进精修效果。

有问题的小伙伴欢迎留言或者私信我，我看到就会回复。

码字不易，且看且珍惜。

最后祝大家论文发表顺利。

参考文献：

\[1\] A.C. Larson and R.B. Von Dreele, General Structure Analysis System (GSAS), Los Alamos National Laboratory Report, LAUR 86-748 (2004).

\[2\] B.H. Toby, EXPGUI, a graphical user interface for GSAS, J. Appl. Cryst. 34, 210 (2001).

\[3\] Toby, B. H., & Von Dreele, R. B. (2013). "GSAS-II: the genesis of a modern open-source all purpose crystallography software package". Journal of Applied Crystallography, 46(2), 544-549.