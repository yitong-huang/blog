---
layout: post
title: "数据挖掘 - 贝叶斯信念网络"
author: "Yitong Huang"
tags: "Data Minig"
---

贝叶斯信念网络是一种概率的图模型。与朴素贝叶斯分类不同的是它允许表示属性子集之间的依赖关系。贝叶斯信念网络说明联合条件概率分布，允许在变量的子集间定义类条件独立性。
<!--more-->

## 定义

信念网络由两个成分定义：*有向无环图*和*条件概率表*。有向无环图的每个节点代表一个随机变量。他们可能对应于给定数据中的实际属性或隐藏变量（多个属性构成的综合属性）。而每条弧表示一个概率依赖。如果一条弧由节点Y到Z，则Y是Z的**双亲**或**直接前驱**，而Z是Y的**后代**。*给定其双亲，每个变量条件独立于图中它的非后代。*下图是一个6变量的简单信念网络，图中的弧可以表示因果知识：

```flow
graph TD
A(FamilyHistory) -->C(LungCancer)
B(Smoker)-->C
B-->D(Emphysema)
C-->E(PositiveXRay)
C-->F(Dyspnea)
D-->F
```

对于每个变量，信念网络有一个**条件概率表**。变量Y的条件概率表说明条件分布\\(P(Y\|Parents(Y))\\)。以下是变量LungCancer（LC）的条件概率表：

||FH,S|FH,~S|~FH,S|~FH,~S
LC|0.8|0.5|0.7|0.1
~LC|0.2|0.5|0.3|0.9

<br/>

信念网络的联合概率用下式表示：

$$P(x_1, ..., x_n)=\prod^n_{i=1}P(x_i|Parent(x_i))$$

其中，\\(P(x_1, ..., x_n)\\)是X的特定组合的概率，而\\(P(x_i\|Parent(s_i))\\)是\\(x_i\\)的条件概率表值。

信念网络可以用来回答实证式查询的概率（例如，倘若给定一个人PositiveXRay和Dyspnea，他患肺癌的概率有多大）和最可能的查询解释（例如，哪些人群最可能PositiveXRay和Dyspnea）。

## 例子

以下是一个信念网络：

|E=Yes|
|0.7|

---

|D=Healthy|
|0.25|

```flow
graph TD
A["Exercise(E)"]-->C["Heart Disease(HD)"]
B["Diet(D)"]-->C
B-->D["Heartburn(Hb)"]
C-->E["Blood Pressure(BP)"]
C-->F["Chest Pain(CP)"]
D-->F
```

||HD=yes|
|E=Yes, D=Healthy|0.25|
|E=Yes, D=Unhealthy|0.45|
|E=No, D=Healthy|0.55|
|E=No, D=Unhealthy|0.75|

---

||Hb=yes|
|D=Healthy|0.85|
|D=Unhealthy|0.2|

---

||BP=high|
|HD=Yes|0.85|
|HD=No|0.2|

---

||CP=yes|
|HD=Yes, Hb=Yes|0.8|
|HD=Yes, Hb=No|0.6|
|HD=No, D=Yes|0.4|
|HD=No, D=No|0.1|

<br/>

求下列情况下患有Heart Disease的概率：

|Exercise|Diet|Heartburn|Blood Pressure|Chest Pain|Heart Disease|
|?|?|?|?|?|?|

$$
\begin{align*}
P(HD=Yes) &=\sum_{x\in(Yes,No)}\sum_{y\in(Healthy,Unhealthy)}P(HD=Yes|E=x,D=y)\times P(E=x)\times P(D=y) \\
&=0.25*0.7*0.25+0.45*0.7*0.75+0.55*0.3*0.25+0.75*0.3*0.75 \\
&=0.49 \\
P(HD=No) &=1-P(HD=Yes) \\
&=1-0.49 \\
&=0.51
\end{align*}
$$

|Exercise|Diet|Heartburn|Blood Pressure|Chest Pain|Heart Disease|
|?|?|?|High|?|?|

$$
\begin{align*}
P(BP=High) &=\sum_{x\in(Yes,No)}P(BP=High|HD=x)\times P(HD=x) \\
&=0.85*0.49+0.2*0.51 \\
&=0.5185 \\
P(HD=Yes|BP=High) &=\frac{P(BP=High|HD=Yes)\times P(HD=Yes)}{P(BP=High)} \\
&=\frac{0.85*0.49}{0.5185} \\
&=0.8033 \\
P(HD=No|BP=High)&=1-P(HD=Yes|BP=High) \\
&=1-0.8033 \\
&=0.1967
\end{align*}
$$

||Exercise|Diet|Heartburn|Blood Pressure|Chest Pain|Heart Disease|
|Yes|Healthy|?|High|?|?|

$$
\begin{align*}
&P(HD=Yes|BP=High,D=Healthy,E=Yes) \\
=&\frac{P(BP=High|HD=Yes,D=Healthy,E=Yes)\times P(HD=Yes|D=Healty,E=Yes)}{P(BP=High|D=Healthy,E=Yes)} \\
=&\frac{P(BP=High|HD=Yes)\times P(HD=Yes|D=Healty,E=Yes)}{\sum_{x\in(Yes,No)}P(BP=High|HD=x)P(HD=x|D=Healthy)} \\
=&\frac{0.85*0.25}{0.85*0.25+0.2*0.75} \\
=&0.5862 \\
 \\
&P(HD=No|BP=High,D=Healthy,E=Yes) \\
=&1-P(HD=Yes|BP=High,D=Healthy,E=Yes) \\
=&0.4138
\end{align*}
$$

## 训练

先挖坑，后面填。

---

参考资料：  
《数据挖掘·概念与技术》 9.1
