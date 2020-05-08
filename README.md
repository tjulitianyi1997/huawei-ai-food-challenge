# 华为的美食识别入门比赛，第二名方案分享
## 比赛简介
共十类美食，数据分布均衡，比赛方提供了5000张图片的数据集，采用9:1的比例划分为训练集和验证集，最终以预测准确率为评价指标。

本方案使用单模型，在测试集上的的acc为99.2%，排行第二。
## 代码说明
1、新建images和checkpoint文件夹，分别为数据集和模型保存位置。数据未上传，需自己准备，将十类数据的子文件夹放在images下

2、运行run.py，自动完成三阶段训练并生成最终模型
## 方案简介
模型方面采用的是efficientnet-b5，在原始b5模型中增加了cbam注意力模块，数据增强方面使用了随机裁切、翻转、auto_augment、随机擦除以及cutmix, 损失函数采用CrossEntropyLabelSmooth，训练策略方面采用了快照集成（snapshot）思想。

第一阶段训练，图像输入尺寸为400，使用LabelSmooth和cutmix，采用带学习率自动重启的CosineAnnealingWarmRestarts方法，获得5个模型快照，选择val_acc最高的模型，作为第一阶段的训练结果。

第二阶段训练，图像输入尺寸为500，适当调整随机裁切和随机擦除的参数，增加weight_decay，在第一阶段模型的基础上训练获得5个模型快照，选择val_acc最高的模型，作为第二阶段的训练结果。

第三阶段训练，图像输入尺寸为500，关闭cutmix，损失函数采用CrossEntropyLoss，在第二阶段模型的基础上训练获得5个模型快照，选择val_acc最高的模型，作为最终的训练结果。
