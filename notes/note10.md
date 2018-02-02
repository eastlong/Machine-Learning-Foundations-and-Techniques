# note10

上一节课，我们介绍了Linear Regression线性回归，以及用平方错误来寻找最佳的权重向量w，获得最好的线性预测。本节课将介绍Logistic Regression逻辑回归问题。

## Logistic Regression Problem

一个心脏病预测的问题：根据患者的年龄、血压、体重等信息，来预测患者是否会有心脏病。很明显这是一个二分类问题，其输出y只有{-1,1}两种情况。

二元分类，一般情况下，理想的目标函数f(x)>0.5，则判断为正类1；若f(x)<0.5，则判断为负类-1。

但是，如果我们想知道的不是患者有没有心脏病，而是到底患者有多大的几率是心脏病。这表示，我们更关心的是目标函数的值（分布在0,1之间），表示是正类的概率（正类表示是心脏病）。这跟我们原来讨论的二分类问题不太一样，我们把这个问题称为软性二分类问题（’soft’ binary classification）。这个值越接近1，表示正类的可能性越大；越接近0，表示负类的可能性越大。

对于软性二分类问题，理想的数据是分布在[0,1]之间的具体值，但是实际中的数据只可能是0或者1，我们可以把实际中的数据看成是理想数据加上了噪声的影响。

如果目标函数是f(x)=P(+1|x)∈[0,1]的话，我们如何找到一个好的Hypothesis跟这个目标函数很接近呢？

首先，根据我们之前的做法，对所有的特征值进行加权处理。计算的结果s，我们称之为’risk score’：

但是特征加权和s∈(−∞,+∞)，如何将s值限定在[0,1]之间呢？一个方法是使用sigmoid Function，记为θ(s)。那么我们的目标就是找到一个hypothesis：h(x)=θ(wTx)。

Sigmoid Function函数记为θ(s)=11+e−s，满足θ(−∞)=0，θ(0)=12，θ(+∞)=1。这个函数是平滑的、单调的S型函数。则对于逻辑回归问题，hypothesis就是这样的形式：


h(x)=11+e−wTx
那我们的目标就是求出这个预测函数h(x)，使它接近目标函数f(x)。

## Logistic Regression Error

现在我们将Logistic Regression与之前讲的Linear Classification、Linear Regression做个比较：

这三个线性模型都会用到线性scoring function s=wTx。linear classification的误差使用的是0/1 err；linear regression的误差使用的是squared err。那么logistic regression的误差该如何定义呢？

先介绍一下“似然性”的概念。目标函数f(x)=P(+1|x)，如果我们找到了hypothesis很接近target function。也就是说，在所有的Hypothesis集合中找到一个hypothesis与target function最接近，能产生同样的数据集D，包含y输出label，则称这个hypothesis是最大似然likelihood。

logistic function: h(x)=θ(wTx)满足一个性质：1−h(x)=h(−x)。那么，似然性h:


likelihood(h)=P(x1)h(+x1)×P(x2)h(−x2)×⋯P(xN)h(−xN)
因为P(xn)对所有的h来说，都是一样的，所以我们可以忽略它。那么我们可以得到logistic h正比于所有的h(ynx)乘积。我们的目标就是让乘积值最大化。

如果将w代入的话：

为了把连乘问题简化计算，我们可以引入ln操作，让连乘转化为连加：

接着，我们将maximize问题转化为minimize问题，添加一个负号就行，并引入平均数操作1N：

将logistic function的表达式带入，那么minimize问题就会转化为如下形式：

至此，我们得到了logistic regression的err function，称之为cross-entropy error交叉熵误差：

## Gradient of Logistic Regression Error

我们已经推导了Ein的表达式，那接下来的问题就是如何找到合适的向量w，让Ein最小。

Logistic Regression的Ein是连续、可微、二次可微的凸曲线（开口向上），根据之前Linear Regression的思路，我们只要计算Ein的梯度为零时的w，即为最优解。

对Ein计算梯度，学过微积分的都应该很容易计算出来：

最终得到的梯度表达式为：

为了计算Ein最小值，我们就要找到让∇Ein(w)等于0的位置。

上式可以看成θ(−ynwTxn)是−ynxn的线性加权。要求θ(−ynwTxn)与−ynxn的线性加权和为0，那么一种情况是线性可分，如果所有的权重θ(−ynwTxn)为0，那就能保证∇Ein(w)为0。θ(−ynwTxn)是sigmoid function，根据其特性，只要让−ynwTxn≪0，即ynwTxn≫0。ynwTxn≫0表示对于所有的点，yn与wTxn都是同号的，这表示数据集D必须是全部线性可分的才能成立。

然而，保证所有的权重θ(−ynwTxn)为0是不太现实的，总有不等于0的时候，那么另一种常见的情况是非线性可分，只能通过使加权和为零，来求解w。这种情况没有closed-form解，与Linear Regression不同，只能用迭代方法求解。

之前所说的Linear Regression有closed-form解，可以说是“一步登天”的；但是PLA算法是一步一步修正迭代进行的，每次对错误点进行修正，不断更新w值。PLA的迭代优化过程表示如下：

w每次更新包含两个内容：一个是每次更新的方向ynxn，用v表示，另一个是每次更新的步长η。参数(v,η)和终止条件决定了我们的迭代优化算法。

## Gradient Descent

根据上一小节PLA的思想，迭代优化让每次w都有更新：

我们把Ein(w)曲线看做是一个山谷的话，要求Ein(w)最小，即可比作下山的过程。整个下山过程由两个因素影响：一个是下山的单位方向v；另外一个是下山的步长η。

利用微分思想和线性近似，假设每次下山我们只前进一小步，即η很小，那么根据泰勒Taylor一阶展开，可以得到： 

Ein(wt+ηv)≈Ein(wt)+ηvT∇Ein(wt)
关于Taylor展开的介绍，可参考我另一篇博客： 
多元函数的泰勒(Taylor)展开式

迭代的目的是让Ein越来越小，即让Ein(wt+ηv)<Ein(wt)。η是标量，因为如果两个向量方向相反的话，那么他们的内积最小（为负），也就是说如果方向v与梯度∇Ein(wt)反向的话，那么就能保证每次迭代Ein(wt+ηv)<Ein(wt)都成立。则，我们令下降方向v为： 

v=−∇Ein(wt)||∇Ein(wt)||
v是单位向量，v每次都是沿着梯度的反方向走，这种方法称为梯度下降（gradient descent）算法。那么每次迭代公式就可以写成： 

wt+1←wt−η∇Ein(wt)||∇Ein(wt)||


下面讨论一下η的大小对迭代优化的影响：η如果太小的话，那么下降的速度就会很慢；η如果太大的话，那么之前利用Taylor展开的方法就不准了，造成下降很不稳定，甚至会上升。因此，η应该选择合适的值，一种方法是在梯度较小的时候，选择小的η，梯度较大的时候，选择大的η，即η正比于||∇Ein(wt)||。这样保证了能够快速、稳定地得到最小值Ein(w)。

其中： 
η′=η||∇Ein(wt)||
总结一下基于梯度下降的Logistic Regression算法步骤如下：

初始化w0
计算梯度∇Ein(wt)=1N∑Nn=1θ(−ynwTtxn)(−ynxn)
迭代跟新wt+1←wt−η∇Ein(wt)
满足∇Ein(wt+1)≈0或者达到迭代次数，迭代结束

## 总结

我们今天介绍了Logistic Regression。首先，从逻辑回归的问题出发，将P(+1|x)作为目标函数，将θ(wTx)作为hypothesis。接着，我们定义了logistic regression的err function，称之为cross-entropy error交叉熵误差。然后，我们计算logistic regression error的梯度，最后，通过梯度下降算法，计算∇Ein(wt)≈0时对应的wt值。

## 参考

1. [台湾大学林轩田机器学习基石课程学习笔记10 -- Logistic Regression](http://blog.csdn.net/red_stone1/article/details/72229903)