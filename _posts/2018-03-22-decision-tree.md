---
layout: post
title: "数据挖掘 - 分类 - 决策树归纳"
author: "Yitong Huang"
mathjax: true
tags: Data Mining
---

**决策树归纳**是从有类标号的训练元组中学习决策树。**决策树**是一种类似流程图的结构。决策树种的每个**内部节点**表示一个属性上的测试，每个**分枝**代表该测试的一个输出，每个**叶结点**代表一个类。<!--more-->

下图是一棵典型的决策树：

```flow
graph TD
A(age?)
A -->|youth| B(student?)
A -->|middle_aged| C((yes))
A -->|senior| D(credit_rating?)
B -->|no| E((no))
B -->|yes| F((yes))
D -->|excellent| G((no))
D -->|fair| H((yes))
```

## 一、算法基本策略

* 用三个参数*D*（数据分区，训练元组和对应类标号），Attribute_list（描述元组属性的列表）和Attribute_selection_method（选择属性的启发式过程）调用该算法。
* 树从单个节点N开始，N代表*D*中的训练元组（*步骤1*）。
* 如果*D*中的元组都为同一类，则节点N变成树叶，并用该类标记它（*步骤2~3*）。
* 否则，调用Attribute_selection_method确定**分裂准则**（*步骤6*）。分裂准则通过确定划分*D*中元组的“最好”的方法。分裂准则制定**分裂属性**，并且指出**分裂点**或**分裂子集**。期望是结果分区尽可能“纯”。这里对于“纯”的计算采用**ID3**、**C4.5**或**Gini**等贪心方法。
* 节点N用分裂准则标记作为节点上的测试（*步骤7*）。对分裂准则的每个输出，由节点N生长一个分枝。*D*中元组据此进行划分（*步骤10~11*）。
* 对于*D*的每个结果分区\\(D_j\\)上的元组，算法使用同样过程递归生成决策树。
* 递归划分步骤当且仅当以下条件之一成立时停止：  
    （1）分区*D*的所有元组都属于同一个类（*步骤2~3*）。  
    （2）没有剩余属性可以进一步划分元组（*步骤4*）。可以将N转化成叶节点并用**多数表决**标记（*步骤5*），也可以直接存放节点元组的类分布。  
    （3）给定的分支没有元组，即分区\\(D_j\\)为空。这种情况下用*D*中的多数创建叶节点。
* 返回决策树。

```
算法：Generate_decision_tree。由数据分区D中的训练元组生成决策树

输入：D，数据分区
     Attribute_list，候选属性的集合
     Attribute_selection_method，“最好”地划分数据元组的分类准则的过程
     
输出：一棵决策树

方法：
     （1） 创建一个节点N
     （2） if D中的元组都在同一类C中 then
     （3）     返回N作为叶节点，标记为类C;
     （4） if Attribute_list为空 then
     （5）     返回N作为叶节点，标记为D中的多数类;
     （6） 使用Attribute_selection_Method(D, Attribute_list)，找出“最好的”Splitting_criterion;
     （7） 用Splitting_criterion标记节点N;
     （8） if Splitting_attribute是离散值，并且允许多路划分 then
     （9）     Attribute_list <- (Attribute_list - Splitting_attribute)
     （10）for Splitting_criterion的每个输出j
     （11）    设Dj是D中满足输出j的数据元组的集合;
     （12）    if Dj为空 then
     （13）        加一个树叶到节点N，标记为D中的多数类;
     （14）    else 加一个由Generate_decision_tree(Dj, Attribute_list)返回的节点到N;
     （15）返回N;

```

## 二、分裂准则

1. 信息增益ID3

    对*D*中的元组分类所需要的期望信息：
    
    $$Info(D)=\sum\limits^N_{i=0}p_{i}\log_{2}(p_{i}))$$
    
    其中，\\(p_i\\)是*D*中任意元组属于类\\(C_i\\)的非零概率，使用\\(|C_{i,D}|/|D|\\)估计。\\(Info(D)\\)又称为*D*的**熵**。
    假设我们按某属性A划分*D*，其中属性A具有*v*个不同值\\(\\{a_1, a_2, ..., a_v\\}\\)。可以用属性A将*D*划分成*v*个子集\\(\\{D_1, D_2, ..., D_v\\}\\)。理想情况下，我们希望每个分区都是“纯”的，但实际并不都如此，所以我们需要衡量分区的“纯度”，采用下式度量：
    
    $$Info_A(D)=\sum\limits^v_{j=1}\frac{|D_j|}{D}\times Info(D_j)$$
    
    信息增益定义为原来的信息需求与新的信息需求（划分后）之间的差：
    
    $$Gain(A)=Info(D)-Info_A(D)$$
    
    我们选择最大的信息增益//(Gain(A)//)的属性A作为节点N的分裂属性。

2. 增益率C4.5

    信息增益度量偏向具有许多输出的测试。C4.5使用一种称为**增益率**的信息增益扩充来克服这种偏差。用**分裂信息**将信息增益规范化：
    
    $$SplitInfo_A(D)=-\sum^v_{j=1}\frac{|D_j|}{|D|}\times\log_2(\frac{|D_j|}{|D|})$$
    
    **增益率**定义为：
    
    $$GainRate(A)=\frac{Gain(A)}{SplitInfo_A(D)}$$
    
    我们选择具有醉倒增益率的属性作为分裂属性。

4. 其他

    还有多种属性选择度量方法，不再一一列举。

## 三、例子

RID|age|income|student|credit_rating|CLASS:buss_computer
---|---|------|-------|-------------|-------------------
1 |youth     |high  |no |fair     |no
2 |youth     |high  |no |excellent|no
3 |middle_age|high  |no |fair     |yes
4 |senior    |medium|no |fair     |yes
5 |senior    |low   |yes|fair     |yes
6 |senior    |low   |yes|excellent|no
7 |middle_age|low   |yes|excellent|yes
8 |youth     |medium|no |fair     |no
9 |youth     |low   |yes|fair     |yes
10|senior    |medium|yes|fair     |yes
11|youth     |medium|yes|excellent|yes
12|middle_age|medium|no |excellent|yes
13|middle_age|high  |yes|fair     |yes
14|senior    |medium|no |excellent|no

### 1. ID3

先计算*D*中元组的期望信息：

$$Info(D)=-\frac{9}{14}\log_2\frac{9}{14}-\frac{5}{14}\log_2\frac{5}{14}$$

再计算每个属性的期望信息，从age开始：

$$
\begin{align*}
Info_{age}(D) &=\frac{5}{14}\times (-\frac{2}{5}\log_2\frac{2}{5}-\frac{3}{5}\log_2\frac{3}{5}) \\
&+\frac{4}{14}\times (-\frac{4}{4}\log_2\frac{4}{4}-\frac{0}{4}\log_2\frac{0}{4}) \\
&+\frac{5}{14}\times (-\frac{3}{5}\log_2\frac{3}{5}-\frac{2}{5}\log_2\frac{2}{5}) \\
&=0.694
\end{align*}
$$

$$Gain(age)=Info(D)-Info_{age}(D)=0.940-0.694=0.246$$

同样的，我们可以计算其他属性的Gain值。选择最大的Gain值对应的属性进行分裂。

```flow
graph TD
A(age?)
A -->|youth| B[D1]
A -->|middle_aged| C[D2]
A -->|senior| D[D3]
```

### 2. C4.5

属性income的增益率计算过程：

$$SplitInfo_income(D)=-\frac{4}{14}\times \log_2\frac{4}{14}-\frac{6}{14}\times \log_2\frac{6}{14}-\frac{4}{14}\times \log_2\frac{4}{14}=1.557$$

$$GainRatio(income)=\frac{Gain(income)}{SplitInfo_A(D)}=0.029/1.557=0.019$$

---

参考文献：  
《数据挖掘·概念与技术》 8.2