# RebNet主要亮点
https://arxiv.org/abs/1711.01243v3

1.使用“残差二值”方法作为激活函数，并且达到了将激活值量化为M位(M一般小于等于3)的效果，而权重则仅用1位表示。

![resign](./images/resign.png)

“残差二值”方法不仅用作量化，而且可以用来充当激活函数，形状有点像hard tranh函数

![activation_function](./images/activation_function.png)

2.对sign函数在反向求导时候进行近似：

![sign](./images/sign_grad.png)
![sign2](./images/sign_grad2.png)

但是在对M位的“残差二值”求导时，论文中的叙述有些不严谨，论文中给出的“残差二值”的求导公式是：

![resign_grad](./images/resign_grad.png)

我们假设M=2，来计算一下“残差二值”真正的导数：

![resign_grad_true](./images/resign_grad_true1.png)
![resign_grad_true](./images/resign_grad_true2.png)

RebNet论文程序对“残差二值”的求导是用Tensorflow自动求导得到的，没有用论文中给出的求导公式，经过多次测试，Tensorflow对“残差二值”的自动求导结果和我们**人工计算的**求导结果完全一致。
# Pytorch框架下的RebNet
模型来源|M=1|M=2|M=3|优化方法|学习率
-|-|-|-|-|-
论文附带模型|78.6|84.6|85.2|adam|0.01
torch复现|79.2|85.4|86.7|adam|0.01