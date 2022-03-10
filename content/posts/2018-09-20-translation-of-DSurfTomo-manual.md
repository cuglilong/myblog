---
title: "DSurfTomo（V1.3)用户手册"
date: 2018-09-20T22:00:21+08:00
draft: true
tags: [地震程序]
comments: true
---

#### 本文为方洪健、姚华建DSurTomo英文用户手册翻译，仅供个人学习使用，原文及程序可从[方洪健Github主页]( https://github.com/HongjianFang/DSurfTomo)下载
## 1 描述
*DSurfTomo* 是面波反演程序，它能从面波频散数据直接反演获得3D横波速度而不需要构建相速度或群速度分布图的中间步骤。程序使用了快速推进算法（FMM）计算面波每个周期源和接受台站之间的面波旅行时和射线路径，这就避免了面波层析成像中常使用的但对于复杂介质不合适的（面波）大圆弧传播假定。方法详细描述请参考方洪建2015年GJI文章。
## 2 程序安装
此程序在CentOS、Debian、Ubuntu及MacOS平台上基于gfortran/ifort编译器测试成功。详细步骤如下：</br>
1.新建文件夹存放DSurfTomo源码以及实例，命令如下：</br>
*mkdir DSurfTomo*</br>
2.从Github上下载源码，命令如下：</br>
*cd DSurfTomo*</br>
*git clone https://github.com/HongjianFang/DSurfTomo*</br>
3.安装，命令如下：</br>
*./configure* **or** *python2 configure*</br>
成功编译后，可执行文件将会再/bin目录下生成。
## 3 数据准备('SurfTomo.dat')
频散数据格式如下：</br>
#25.1 121.5 1 2 0 #(源)纬度 经度 周期索引？ 波类型（2：瑞雷波 1：勒夫波) 速度类型（0：相速度 1：群速度)</br>
25.1 121.4 0.7990 (接收台站)纬度 经度 相速度/群速度（面波频散计算）</br>
'#'号行表示源，分别是源纬度、源经度、周期索引（整数）、波类型和速度类型（后面解释）</br>
每个源后是接收台站数据：前两行是接收台站纬度和经度，第三行为相速度或群速度（面波频散计算？）</br>
*周期索引(整数)* ：参数文件**DSurrfTomo.in**(后面解释)中列出的周期矢量索引</br>
*波类型* ：2表示瑞雷波，1表示勒夫波</br>
*速度类型* ：0表示相速度，1表示群速度</br>
(在数据集例子中，我在*script*目录下写了一个Python脚本**ExtractData.py**来从频散数据文件中提取数据 **（？）**。运行***python ExtractData > surfdataTB.dat*** 来准备此例子中的数据。但是你也可以使用任何方法 **（？）**，只要你所准备的数据格式正确。)</br>
## 4 初始模型（MOD) </br>
初始模型文件名必须是 **MOD** ，其内容如下：</br>
```
 0.0 0.2 0.4 0.6 0.8 1.1 1.4 1.8 2.5
 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900
0.900 0.900 0.900 0.900
 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900 0.900\
0.900 0.900 0.900 0.900
......
```
第一行为网格点在垂直方向上的位置(km).</br>
接着为横波速度值，其顺序为纬度、经度然后深度 **？** </br>
每行表示不同纬度、同一经度、确定深度的横波速度，后面就是下一经度和深度。</br>
在三维初始速度模型的情况下，在这个文件中就包含边界值。初始点为西北角。</br>
(你可以使用 ***scripts*** 目录下的 **GenerateIniMOD.py** 脚本来产生一个简单的初始模型（需要一些简单的修改），运行 ***python GenerateIniMOD.py*** **or** ***python2 GenerateIniMOD.py*** 。对于三维初始模型，记住按照正确的顺序**?**)
## 5 反演输入文件(**DSurfTomo.in**)
```
cccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccc
c INPUT PARAMETERS
cccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccc
surfdata.dat			 c: data file
18 18 9                          c: nx ny nz (grid number in lat lon and depth direction)
25.2  121.35                     c: goxd gozd (upper left point,[lat,lon])
0.015 0.017                      c: dvxd dvzd (grid interval in lat and lon direction)
20                              c: nsrc*maxf
4.0  0.0                         c: weight damp
3                                c: nsublayer (numbers of sublayers for each grid interval:grid --> layer)
0.5 2.8                          c: minimum velocity, maximum velocity
10                               c: maxiter (iteration number)
0.1                              c: sparsity fraction
26                               c: kmaxRc (followed by periods)
0.5 0.6 0.7 0.8 0.9 1.0 1.1 1.2 1.3 1.4 1.5 1.6 1.7 1.8 1.9 2.0 2.1 2.2 2.3 2.4 2.5 2.6 2.7 2.8 2.9 3.0
0                                c: kmaxRg
0                                c: kmaxLc
0                                c: kmaxLg
1                                c: synthetic flag(0:real data,1:synthetic)
0.02                             c: noiselevel
2.5				 c: threshold
```
该文件（参数）某种程度上是不言自明的，然而跟进一步的解释如下：</br>
第六行：初始点的经度和纬度，注意纬度方向为从北到南，经度方向从西到东。我（作者）总是画出所有的源点和接收点来确定这个初始点以保证反演区域包括全部源点和接收点，否则程序出错。</br>
第七行：纬度和经度的网格间距。</br>
确保所有的源点和接收点都在区域 **goxd-(nx-3)*dvxd~goxd** 和 **gozd~gozd+(ny-3)*dvzd**，否则程序将会报错并停止运行。</br>
第八行：源点和接收点最大数</br>
第九行： **"weight"** 是平衡数据拟合项和光滑正则化项的参数，选择一个合适的参数是有一定的技巧的，因为L曲线法并不总有效。以我（作者）的经验，一个合适的"weight"参数每次迭代可以获得合理的速度变化。在开始的几次迭代过程中，速度变化能够达到0.4。如果速度变化过大，表明你选择的"weight"参数太小，反之亦然。通常来说，一些试验就足够能选择一个好的"weight"参数。在最后几次迭代时，速度变化将会减小，因为反演开始收敛，否则程序将会报错并产生一些垃圾信息，如"no roots can be found..."。 **"damp"** 是 **'lsar'** 的输入参数，它控制反演参数的振幅。</br>
第十行： **'sublayers'** 表示为计算深度核你想设置的从网格转换到层的子层数，我（作者）一般设置为3。</br>
第十一行：最小和最大速度是研究区域的先验信息，如果你没有这个信息可以设置一个大的网格间距，它对于最终结果不会有太大影响。</br>
第十二行：反演迭代次数，一般来说10次足够。
稀疏分数参数表明敏感度矩阵的稀疏成都，对于大多数情况2~10%足够。</br>
**KmaxRg,kmaxRc,kmaxLc,KmaxLg** 分别为：瑞雷波相速度的周期数、瑞雷波群速度的周期数、勒夫波相速度的周期数和勒夫波群速的周期数。注意如果周期数为0，就没有必要在周期数下写出周期，例如，**kmaxRg**。</br>
**"synthetic flag"** : **0** 表示反演使用的是实际数据，**1** 表示反演使用的是合成数据；当标签为 **1** 时(**flag = 1**)就需要一个名为 ***MOD.true*** 的文件，否则程序将会停止运行。</br>
(你也可是使用 **/scripts**目录下的 ***GenerateTrueMOD.py*** 脚本来生成 ***MOD.true*** 文件，脚本是用于检测板的，为控制 **'checkers'** 的数目和大小需要做一些修改)</br>
***python2 GenerateTrueMOD.py*** </br>
**"noise level"** 表明加入到合成数据中的高斯噪声，例如，0.02表示加入了均值为0，标准差为2%的随机高斯噪声。</br>
最后一行的 **'threshold'（阈值）** 控制计算的权重以避免离群值对最终结果的影响，**weights** 有如下表达式：</br>
$$weights=\frac{1.0}{1+0.05\times{exp(x^2\times{threshold})}} $$ </br>
其中，**x** 是残差。下图为对于两个不同 **threshold** 值对应的 **weight** 值，对于 **threshold=0.5** ，残差大于4s的 **weight** 减小到0. </br>
</br>
在特定目录下准备好这三个文件后,可以在同一目录下运行此程序:</br>
***../src/DSurfTomo DSurfTomo.in*** </br>
## 6 输出文件
最终速度模型(**DSurfTomo.inMeasure.dat**): </br>
第一列:经度 </br>
第二列:纬度 </br>
第三列:深度 </br>
第四列:横波速度 </br>
我使用 **/scripts** 目录中的 ***plotslice.gmt*** 以快速检验结果:</br>
***csh plotslice.gmt DSurfTomo.inMeasure.dat depth1 depth2 depth3 depth4***
其中 **depth[1234]** 是垂直网格的一些深度. </br>
你可以插值到你想要的深度,并使用你自己的脚本绘图. </br>
</br>
最终模型的射线分布(**raypath.out**):</br>
#射线路径段的序号 </br>
纬度 经度 </br>
... </br>
</br>
每次迭代的速度模型(**DSurfTomo.inMeasure.dat.iter**):</br>
与最终速度模型(**DSurfTomo.inMeasure.dat**)的数据格式一致,这些文件可以用于检测运行中间结果.</br>
</br>
最初/最终迭代残差(**residual*.dat**):</br>
数据格式如下:</br>
**Distance(距离)** **ForwardT** **ObserveT** **weightedForwardT** **weightedObseverT** **weight(权重)**</br>
... </br>
## 参考文献
Fang,H.,Yao,H.,Zhang,H.,Huang,Y.C.,& van der Hilst,R.D.(2015).Direct inversion of surface wave dispersion for three-dimensional shallow crustal structure based on ray tracing:methodology and application. Geophysical Journal International,201(3),1251-1263.[PDF](http://ddl.escience.cn/ff/enau) </br>
Rawlinson,N.& Sambridge,M.,2004. Wave front evolution in strongly heterogeneous layered media using the fast marching method, *Geophys.J.Int.*,156(3),631-647.[PDF](http://ddl.escience.cn/ff/enav)
