---
layout: post
title: "数据挖掘 - 贝叶斯分类"
author: "Yitong Huang"
tags: Data Mining
---

贝叶斯分类法是基于贝叶斯原理的统计学分类方法。可以预测类隶属关系的概率，如一个给定的元组属于一个特定类的概率。分类效果可以与决策树和经过挑选的神经网络分类器相媲美。
朴素贝叶斯分类法嘉定一个属性值在给定类上的影响独立于其他属性的值。这一假定称为**类条件独立性**，我们也因此称为“朴素的”。
<!--more-->

## 贝叶斯原理

设*X*是数据元组，用*n*个属性集的测量值描述。令*H*为某种假设，如数据元组*X*属于某个特定类*C*。对于分类问题，希望确定给定数据元组*X*，假设*H*成立的概率\\(P(H\|X)\\)，即**在给定*X*的属性描述，找出元组*X*属于类*C*的概率**。

\\(P(H\|X)\\)是**后验概率**，\\(P(H)\\)是**先验概率**。\\(P(X)\\)、\\(P(H)\\)和\\(P(X\|H)\\)都可以有给定的数据计算。

**贝叶斯原理**提供了一种由\\(P(X)\\)、\\(P(H)\\)和\\(P(X\|H)\\)计算\\(P(H\|X)\\)的方法：

$$P(H|X)=\frac{P(X|H)P(H)}{P(X)}$$

## 朴素贝叶斯分类

**朴素贝叶斯分类法**工作过程如下：

1. 设*D*是训练元组和它们关联的类标号的集合。通常，每个元组用一个n维属性向量\\(X=\\{x_1, x_2, ..., x_n\\}\\)表示，描述由n个属性\\(A_1, A_2, ..., A_n\\)对元组的n个测量。

2. 假定有m个类\\(C_1, C_2, ..., C_m\\)。给定元组*X*，分类法将预测*X*属于具有最高后验概率的类。也就是说，朴素贝叶斯分类法预测*X*属于类\\(C_i\\)，当且仅当满足下式：

    $$P(C_i|X)>P(C_j|X)\qquad 1\leq j\leq m,\quad j\not=i$$
    
    即最大化\\(P(C_i\|X)\\)。根据贝叶斯定理：
    
    $$P(C_i|X)=\frac{P(X|C_i)P(C_i)}{P(X)}$$
    
3. 由于P(X)对所有类都是常数，所以只需要最大化\\(P(X\|C_i)P(C_i)\\)即可。

4. 给定具有许多属性的数据集，计算\\(P(X\|C_i)\\)的开销会非常大。为降低计算量，可以做类条件独立的朴素假定。因此：

    $$P(X|C_i)=\prod^n_{k=1}P(x_k|C_i)=P(x_1|C_i)P(x_2|C_i)...P(x_n|C_i)$$
    
    * 如果\\(A_k\\)是离散属性，则\\(P(x_k\|C_i)\\)是*D*中属性\\(A_k\\)的值为\\(x_k\\)的\\(C_i\\)类的元组数除以*D*中\\(C_i\\)类的元组数\\(\|C_{i,D}\|\\)。
    * 如果\\(A_k\\)是连续值属性，则可以假定连续值属性服从均值为\\(\mu\\)、标准差为\\(\sigma\\)的高斯分布：
    
        $$P(x_k|C_i)=g(x,\mu,\sigma)=\frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(x-\mu)^2}{2\sigma^2}}$$
        
5. 为了预测*X*的类标号，对每个类\\(C_i\\)，计算\\(P(X\|C_i)P(C_i)\\)。该分类法预测输入元组*X*的类为\\(C_i\\)，当且仅当

    $$P(C_i|X)>P(C_j|X)\qquad 1\leq j\leq m,\quad j\not=i$$

## 例子

RID|age|income|student|credit_rating|CLASS:buys_computer
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

希望分类的元组为：

$$X=(age=youth,income=medium,student=yes,credit_rating=fair)$$

在该例子中，类标号属性buys_computer有两个不同的值{yes, no}。设\\(C_1\\)对应类buys_computer=yes，\\(C_2\\)对应类buys_computer=no。我们的目标是最大化\\(P(X\|C_i)P(C_i),i=1,2\\)。

每个类的先验概率\\(P(C_i)\\)可以根据训练元组计算：

$$
P(buys_computer=yes)=9/14=0.643 \\
P(buys_computer=no)=5/14=0.357
$$

为了计算\\(P(X\|C_i)\\)，计算下面的条件概率：

$$
P(age=youth|buys_computer=yes) =2/9=0.222 \\
P(age=youth|buys_computer=no) =3/5=0.600 \\
P(income=medium|buys_computer=yes) =4/9=0.444 \\
P(income=medium|buys_computer=no) =2/5=0.400 \\
P(student=yes|buys_computer=yes) =6/9=0.666 \\
P(student=yes|buys_computer=no) =1/5=0.200 \\
P(credit_rating=fair|buys_computer=yes) =6/9=0.666 \\
P(credit_rating=fair|buys_computer=no) =2/5=0.400
$$

由此可以计算：

$$
P(X|buys_computer=yes)=0.222\times 0.444\times 0.666\times 0.666=0.044 \\
P(X|buys_computer=no)=0.600\times 0.400\times 0.200\times 0.400=0.019 
$$

接着计算\\(P(X\|C_i)\\)，并找出最大值对于的类：

$$
P(X|buys_computer=yes)P(buys_computer=yes)=0.044\times 0.643=0.028 \\
P(X|buys_computer=no)P(buys_computer=no)=0.019\times 0.357=0.007
$$

可见，对于给定的元组*X*，使用朴素贝叶斯分类为buys_computer=yes。

## 其他

* 对于可能遇到零概率值的处理方法：可以对每个计数加1，在样本很大的情况下基本不影响概率估算结果。

* 贝叶斯分类法理论上讲具有很小的错误率，但实际上并非总是很理想，主要是由于假设（类条件独立）不一定成立，或者数据量不足造成的。

---

参考资料：  
《数据挖掘·概念与技术》 8.3