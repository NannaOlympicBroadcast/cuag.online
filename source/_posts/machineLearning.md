---
title: 机器学习期末复习
date: 2025-01-05 13:31:07
tags:
  - 机器学习
  - AI
  - 大模型应用
  - 人工智能时代
  - 数据科学
  - 数据结构与算法
  - 第四次科技革命
---
# 无监督学习之「聚类算法」
## 聚类的一般步骤
1. 算法
```text
1. 选取初始点
2. 更新聚类中心
3. 计算样本点到聚类中心的距离
4. 进行分类
5. 重复2-5直到分类
```
2. 数据结构
```text
POINTS:SET<POINT>//所有点的集合
CENTERS:SET<POINT>//聚类中心
CLUSTER:MAP<POINT,SET<POINT>>//分类情况
```
满足以下关系
```text
CLUSTER.KEYS()=CENTERS
CLUSTER.VALUES().JOIN()=POINTS-CENTERS
```
3. 可以使用并行或者GPU加速的步骤：计算最远、最近、最大、最小的距离
## K-means算法
```text
# STEP 0: 随机从数据集中选出 K 个点作为初始中心
CENTERS = RANDOM_INITIALIZE(POINTS, K)

REPEAT:
    # STEP 1: 分配（Assignment）
    CLUSTER = NEW MAP<CenterID, List<Point>>()

    FOR point IN POINTS:
        nearest_center = FIND_NEAREST_CENTER(point, CENTERS)  # 距离最小的中心
        CLUSTER[nearest_center].PUSH(point)
    
    # STEP 2: 更新（Update）
    NEW_CENTERS = {}
    FOR c IN CENTERS:
        NEW_CENTERS[c] = AVERAGE(CLUSTER[c])  # 针对每个簇求均值得到新中心

    # STEP 3: 判断是否收敛
    IF NEW_CENTERS == CENTERS:
        BREAK
    ELSE:
        CENTERS = NEW_CENTERS
UNTIL convergence
```
### 特点
1. 已知类别数（K）
2. 试探K和选取分类中心
3. 孤立较远效果极佳
### K-MEANS++ 算法
```text
CENTERS.PUSH(POINTS.RANDOM() AS CENTER0)
DO
    FOR POINT IN POINTS-CENTERS:
        DX,CENTER = CENTERS.MAP(CENTER=>D(CENTER,POINT)).MIN.FIND()
        CLUSTER[CENTER].PUSH(POINT)
        DX_SET[POINT] = DX
    NEXT_CENTER = DX_SET.RANDOM_SELECT(POINT,DX=>RANDOM(0,1)<=DX^2/SUM(DX_SET.KEYS().MAP(DX=>Dx^2)))//距离越长，更有可能被随机选择为下一个聚类中心
    CENTERS.PUSH(NEXT_CENTER)
WHILE CENTERS.LEN<7
```
为什么？初始聚类中心应该分的越开越好
## ISODATA算法
```text
CENTERS = POINTS.RANDOM(K0)
WHILE NOT REACH MAXIMUM_ITERATION:
    POINTS.CLASSIFY_BY_MIN_DISTANCE(CENTERS)
    K = K0
    FOR CENTER,ELEMENTS IN CLUSTER.WHERE(POINTS_SET.LEN()<Nmin):
        CLUSTER[CENTERS.FIND_NEAREST(CENTER)].JOIN(ELEMENTS)
        CLUSTER.REMOVE_KEY(CENTER)
        K--;
    POINTS.ADJUST_CENTER(METHOD=MEAN)
    IF K < K0 / 2:
        GOTO SEPARATE 
    IF K > 2 * K0:
        GOTO JOINING
DEFINE SUBPROCESS SEPARATE:
    D[][] = CENTERS.GET_DISTANCES()
    WHILE D.EXISTS(ELEMENT<D_MIN).FIND AS FIRST,SECOND:
        CLUSTER[(FIRST.AVG()*LEN(FIRST)+SECOND.AVG()*LEN(SECOND)) / ( LEN(FIRST) + LEN(SECOND) )] = JOIN(FIRST,SECOND)
        CLUSTER.REMOVE_KEY(FIRST)
        CLUSTER.REMOVE_KEY(SECOND)
        D[][] = CENTERS.GET_DISTANCES()
DEFINE SUBPROCESS SEPARATE:
    FOR CENTER,POINTS IN CLUSTER:
        VARIANCE_STORE[CENTER] = POINTS.GET_VARIANCE_IN_EACH_DIM()
    //如果某个类别的𝜎𝑚𝑎𝑥 > 𝑆𝑖𝑔𝑚𝑎并且该类别所包含的样本数量𝑛𝑖 ≥ 2𝑛𝑚𝑖𝑛,则可以进行分裂操作，如果不满足上述条件则退出分裂操作； 
```
### 与KMEANS算法的比较
1. 相同点：
- 都使用均值确定分类中心
2. 不同点
- 加入一些试探的步骤（结合人机交互）
3. 缺点：需要很多人为参数，比较复杂所以并未投入使用
## 聚类的评价的指标
- 对环状评价较差：戴维森堡丁指数/分类适确性指标、邓恩指数
- 只能簇内：CP
- 只能簇间：SP
# 贝叶斯判别器
## 核心思想
现实世界中P(c|x)很难获得
## 生成式模型和判别式模型
- 判别式模型：P(c|x)建模
- 判别式模型：P(c|x) = P(c,x) / P(x)，先对P(x,c)建模（比如大语言模型先对于下一token在上一token输入情况下出现概率建模，然后再对token本身概率建模（由概率分布来word2vec），最终由上一token预测下一token）
## 极大似然估计
1. 核心思想：先假设某种概率分布形式，再训练样例对参数的估计
2. 缺点：估计结果的准确性严重依赖于所假设的概率分布形式是否符合潜在的真实分布
3. 为什么使用对数似然：连乘会造成下溢
## 朴素贝叶斯分类器
1. 原因：所有属性上的联合概率难以从有限训练样本估计获得
## 半朴素贝叶斯分类器
1. 原因：现实世界很难获得朴素贝叶斯分类器那种完全不依赖其他属性的属性。
2. 策略
- SPODE：找到「超父」，（有点像虚基类）
- TAN：「互信息」构建完全图，依赖关系为权重，再用最大带权生成树，保留强相关依赖性
## 贝叶斯网
1. 高阶依赖的坏处：训练样本指数级增加、
2. 组成：**0有向无环**图

//TODO
# 高斯混合模型
## 实质
两个高斯分布的加权
## 模型
Z -> X
- 如何计算？

获得所有数据、计算各个参数
## 最大似然估计
### 似然函数的定义
L(thita|X) = p(X|thita)

thita是参数，传入X，获得似然值（thita为特定值时输出概率与X的关系）

X 固定，求thita（argmax）
### EM算法
1. E：使用X和估计的thita估计更好的Z
2. M：利用X和当前估计的Z，估计更好的thita
3. 重复1和2值直到收敛
### 和K-means的关系
1. 样本可以看作X
2. 标签簇看作隐藏变量Z
3. 簇中心、聚类均值看作参数thita
4. 准则看作thita的似然函数
### 大语言模型中的EM思想
1. 输入句子向量空间可以看作X
2. 得到的输出的向量空间可以看作隐藏变量Z
3. 神经网络权重可以看作参数thita
4. 对于每一种特定的神经网络权重结构，输出向量空间和输入向量空间对应关系可以视作似然函数
### （重要）提炼出一般机器（深度学习模型）的思想
- 输入为一堆离散的数据
- 通过训练一个函数的参数，使得输入序列能够预测到对应输出标签的概率最大
- 根据每一部的预测结果，调整参数，并重复上一步，直至结果稳定或者用户主动停止
# 支持向量机SVM
## 什么是支持向量、支持向量机
- 支持向量：与分界超平面具有最小距离的点称为支持向量
## 核函数
就是形如f(wx+b)的函数
## 多分类问题
1. 转化为二分类问题，每两个类构造一个分类器，然后由分类结果给每个样本「投票」，最终票数最多属于那一类
2. 多类支持向量机
# 神经网络
## 基础概念不再介绍，因为大家多少听过一点
## 从感知机到隐藏层
### 感知机是**最简单的人工前馈神经网络**
一种二元线性分类器，使用特征向量作为输入，把矩阵上的输入实数值向量）映射到输出值上（一个二元的值）
### 感知机学习
- 期望输出和实际输出相同，不改变权值
- 实际输出为-1，期望输出为+1，则增加2\*学习率\*xi;
- 实际输出为+1，期望输出为-1，则减少2\*学习率\*xi;
```text
FOR INPUT,EXPECTED IN DATASET:
    IF NN(INPUT)!=EXPECTED:
       FOR I IN 0..INPUT.DIM:
            NN.WEIGHT[I] += 2*LEARNING_RATE*SIGN(EXPECTED)
```
#### 缺点
- 训练的样本必须是线性可分的
### 多层感知机
1. 为什么需要隐藏层？
   隐藏层通过一系列非线性变换，能够从输入数据中提取出更加复杂和抽象的特征。多层前馈网络有强大的表示能力
2. 工作流程：
- 向前：传播数据，逐层输入并计算输出
- 向后：分阶段逐层调整权值
3. 隐藏层的数量：没有定论，需要试探
### 重头戏：反向传播法则
逐层求导，变化如何反向传播
```text
d(wi) = LEARNING_RATE*(d(E)/d(ACTIVATE))*(d(ACTIVATE)/d(wix))*wi
```
#### 标准BP和累计BP：单个和整个再更新
#### 缓解过拟合
1. 早停：验证错误率高，训练误差低，且连续a轮变化小于b
2. 正则化：增加网络复杂度作为评价函数
# 深度学习部分
## 张量
n阶张量代表n维数组
## 深度学习区别于普通机器学习的特征：学习层次特征
- 优点：学习良好的非线性特征
## CNN
- 卷积层：进行线性变换，减少参数
- 池化层：作为缓冲部分接受卷积层的结果，其shape和原数据经过卷积层运算以后的矩阵shape一致
- FC层：特征抽取
- Softmax层：执行分类
- dropout：随机省略几个神经元，保证了每个神经元都有较强的正则化的约束
- Batch-Normalization：减少内部方差偏移或避免梯度扩散

