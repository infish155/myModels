# 实验相关问题与技巧
参考视频：https://www.bilibili.com/video/BV17j41147j8/

## 消融实验
假如做了一个yolov5的改进，改进点分别是 DyHead, SimOTA, MobileNetV2
- 适用于大多数的模式（堆叠模式）
    - 实验1：基准模型baseline，也就是原版yolov5
    - 实验2：yolov5 + MobileNetV2
    - 实验3：yolov5 + MobileNetV2 + DyHead
    - 实验4：yolov5 + MobileNetV2 + DyHead + SimOTA
- 适用于要求较高的模式（组合模式）
    - 实验1：基准模型baseline，也就是原版yolov5
    - 实验2：yolov5 + MobileNetV2
    - 实验3：yolov5 + DyHead
    - 实验4：yolov5 + SimOTA
    - 实验5：yolov5 + MobileNetV2 + DyHead
    - 实验6：yolov5 + MobileNetV2 + SimOTA
    - 实验7：yolov5 + DyHead + SimOTA
    - 实验8：yolov5 + MobileNetV2 + DyHead + SimOTA

## 对比实验
在实验开始前先选择部分模型控制好超参数进行跑baseline,比如选择efficientnet，yolov5，yolov7，yolov8，retinanet，fcos，假如最高的是yolov7，那么就选择yolov7进行改进，这样可以避免在消融实验做完之后，上面再要求增加跟其他模型的对比，然后发现其他模型比改进后的模型精度都要高的情况。
**学习率，batchsize，epoch，机器环境等都要保持一致性**

## 指标
常用：Param, FLOPs, Size, FPS, Precision, Recall, mAP50, mAP75, mAP50-90

## 随机种子
一般来说，开源的源码基本已经固定好随机种子，但是某些改进结构的结果可能没办法固定，这种情况下有能力可以多跑几次取平均（严谨），没能力的话就跑两三次取最高

## 模块（注意力...）对比实验怎么做
- **证明自己的替换方式最好**
可以做比如说 模块替换一个卷积、两个卷积、三个卷积以及不同位置的卷积的效果对比，结果可以说明自己为什么要这么替换 *如果已经有提点了强烈建议做完实验*

## 数据集分配
建议尽量分为 训练 验证 测试
比例一般 6:2:2 或者 7:1:2
最严谨的方式：训练过程使用训练集和验证集，每个epoch都跑出结果，最后使用得到最好的模型在测试集上面跑
