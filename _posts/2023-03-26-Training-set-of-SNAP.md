---
layout: post
title: SNAP势函数训练集构型的选择
subtitle: 机器学习势函数入门
tags: [potential,势函数]
comments: true
---
使用VASP进行第一性原理计算，从而进行势函数拟合已经成为一种routine。Shyue Ping Ong教授在2019年的文章《Unravelling Complex Strengthening Mechanisms in the NbMoTaW Multi-Principal Element Alloy with Machine Learning Potentials》中使用 spectral neighbor analysis potential（SNAP），一种基于原子局部环境的机器学习势函数拟合了多组元合金的势。尤为可贵的是他们公布了拟合使用的训练集，见https://github.com/materialsvirtuallab/snap 这个工作很好地展示了应当如何有效地选择训练集，具有借鉴意义。

该数据集使用JSON格式储存第一性原理计算数据，包括计算设置、原子位置、原子受力、体系总应力和体系能量，其中原子位置使用经典坐标系和分数坐标系两种方法给出。将json文件转换成可视化软件OVITO可读取的软件，然后就能看到各种构型设置。

对于纯合金，例如Cu和Ni，数据集包含四部分。以Cu为例：
- 弹性变形，即施加单轴、多轴的拉压剪切组合。包含约120个数据点，模型尺寸为108原子。
- 空位，即删除一个原子。包含40个数据点，模型为107原子。此处有一个问题，既然所有原子都是全同的，那么，在不施加应变时删除其中任意一个原子，得到的数据应当是完全相同的。为什么需要如此多的数据点？难道是微调了周围原子的位置？统计模型中原子间距，发现说一个比较宽的分布，而且模型尺寸比完美晶体模型在各个方向上增加了0.13%，应当是考虑了温度。
- 表面，即插入真空层，使得模型“不满”。包含12个数据点，真空层厚度为9\~17埃，不知道是依据什么取的。模型原子数为6\~48，单胞形状也不再是立方结构。猜测是选取了低指数晶面，但目前很难快速标定原子模型中的晶面与晶向。
- 从头算分子动力学（AIMD），即让原子动起来。包含120个数据点。每个模型包含108原子。注意到不同模型中的原子并没有对应关系，可能只是添加了随机扰动，然后计算单点能。
对于以上所有的计算，势函数为Cu_pv，截断能为520ev，K_prod(也即Kpoints与模型对应方向尺寸的乘积)为40。

对于两组元体系，以Ni-Mo为例，数据集包括三部分：
- Ni中Ni被Mo置换；包含1668个数据点。数据点分为“未弛豫”与“已弛豫”两组，未弛豫即基于Ni晶格直接置换，已弛豫中单胞的形状与体积均有变化。置换原子数量从1到32不等，较多的有：4，5，9，13，17，21，25。
- Mo中Mo被Ni置换；包含918个数据点。数据点分为“未弛豫”与“已弛豫”两组。模型大小有16和54两种。对于16大小的模型，置换数量有0，2，4，6，8，10，12，14，16。对于54模型，置换数量有2，3，4。
- Ni-Mo金属间化合物
