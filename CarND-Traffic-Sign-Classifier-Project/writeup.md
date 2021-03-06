

# Traffic_Sign_Classifier项目

## 总体思路
本项目的目标是构建一个交通标志识别器，项目提供了40000多张32*32尺寸的交通标志图片，需要将它们归类到共43种交通标志类别中。项目的核心是多层卷积神经网络，根据LeNet的主要思想，将图片重复两次通过卷积层加池化层，主要目的是减小图像的长宽，增加图像的深度；之后再用两层全连接层将图像降至一维，输出分类结果。  


## 各模块实现

### Step 1：Dataset Summary & Exploration
该步骤为了使我们大致了解我们需要分类的对象的特征，包括图像共分为三个数据集：训练集、验证集、测试集；图像的尺寸：32*32*3；每个训练集中图像的数量。  
并且我们可以随机查看一些图像的内容，可以发现这些数据集经过精心整理，每个标志牌基本都占据32*32图像中差不多的面积。且由于图像已经是32*32的尺寸，故不需要再进行填充像素以应用LeNet网络。  

### Step 2: Design and Test a Model Architecture
#### Pre-process the Data Set (normalization, grayscale, etc.)
这一步主要是数据集的预处理，本项目中仅应用了像素的正则化，尽可能使像素值的均值为0，且全部位于-1~1的区间内。

#### Model Architecture
模型构建，完全按照LeNet的神经网络层级搭建，其中修改了部分参数以适应本项目，如下：  
1）原LeNet网络的输入对象是32*32*1的尺寸，本例中须改为32*32*3，因为图像有三通道。  
2）原来的卷积核为5*5*6，经过多次测试发现用3*3的卷积核识别率会高一点，故本模型全部使用3*3的卷积核。  
3）适当增加卷积核的深度，主要考虑到LeNet的分类是10个，本项目是43，需要增加一定数量的参数以避免特征遗漏。  
4）最后几处全连接层调整为1152————400————200————43。  
5）我在模型中增加了多个dropout函数，以避免出现模型过拟合现象。

#### Train, Validate and Test the Model
首先将y值转换成One hot编码，交叉熵的计算采用tensorflow的softmax_cross_entropy_with_logits函数计算，优化器选择用AdamOptimizer替代梯度下降算法。   
准确率的计算通过对比每张图片标签与从神经网络输出的分类结果，统计出准确率百分比。  
在模型训练时采用分批定量的方式，batch size尝试过128，256，512，1024等数值，评估下来感觉128和512的效果要好一些。  

### Step 3: Test a Model on New Images  
打乱test集的图像顺序，选择5张图片来运行神经网络计算出logits，经过softmax函数后打印至显示器。如：  

[13  9 13 33 11]  
Test Accuracy = 0.800  
TopKV2(values=array([[  1.00000000e+00,   0.00000000e+00,   0.00000000e+00],  
       [  1.00000000e+00,   5.53296374e-23,   6.55222506e-26],  
       [  1.00000000e+00,   6.43948195e-18,   7.92906225e-28],  
       [  1.00000000e+00,   4.71623529e-21,   2.02200767e-21],   
       [  9.25378025e-01,   7.01692402e-02,   2.16618925e-03]], dtype=float32), indices=array([[13,  0,  1],  
       [ 9, 10, 16],  
       [13, 15, 25],  
       [33, 14, 38],  
       [27, 41, 18]], dtype=int32))  

这5张图片的标签为[13  9 13 33 11]，计算准确率为0.8，说明有一张图片识别错误。然后根据下面输出的矩阵，按序显示了最大概率的分类所在的索引，可以发现第五张图片的标签显示为11，但分类器的输出是27。  


## 小结
我的最终模型在训练集上大概训练了1000多次，最终在测试集上的分类准确率为0.869    
准确率客观来说确实不高，可能有以下几个因素（因时间原因未对以下因素做完整测试）：    
1）训练的学习率和次数，过大的学习率会导致比较差的模型，但是学习率太低会花费较多的时间训练模型。由于我之前未接触过神经网络，无法判定一个模型的训练大概需要经过多少数量级的训练量才能成型，但是基于时间原因我在本模型上并未使用很小的学习率，以及大致1000多次的训练次数。  
2）模型准确率低可能和参数数量不足有关，毕竟需要分出43个种类，可能在模型设计时对weights和biases的数量设计的过于保守，导致丢失了一定数量的特征。  
3）或许可以通过额外增加卷积层的方式来提高准确率。  
4）还有原因可能是数据集的总量并不是太多，但是分类太多，多达43个，而且每个分类上的图片数量非常不均匀。  


