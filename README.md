## CVPR2023多任务大模型挑战赛第7名解决方案

### **赛题背景**

对于设计精良的网络结构和损失函数，多个任务共同训练能大幅提升模型的泛化性。由于特定任务的数据存在noise，仅使用单一任务的数据进行训练，存在过拟合的风险。统一多任务大模型通过将多个任务的数据整合进行统一训练，能够对不同任务的noise做一个平均，进而使模型学到更好的特征。为了进一步探索统一多任务大模型的能力上限，本赛道以交通场景典型任务为题，联合分类、检测、分割三项CV任务三大数据集至单一大模型中，使得单一大模型具备能力的同时获得领先于特定单任务模型的性能。

### **原理介绍**

之前主流的视觉模型生产流程，通常采用单任务 “train from scratch” 方案。每个任务都从零开始训练，各个任务之间也无法相互借鉴。由于单任务数据不足带来偏置问题，实际效果过分依赖任务数据分布，场景泛化效果往往不佳。近两年蓬勃发展的大数据预训练技术，通过使用大量数据学到更多的通用知识，然后迁移到下游任务当中，本质上是不同任务之间相互借鉴了各自学到的知识。基于海量数据获得的预训练模型具有较好的知识完备性，在下游任务中基于少量数据 fine-tuning 依然可以获得较好的效果。不过基于预训练+下游任务 fine-tuning 的模型生产流程，需要针对各个任务分别训练模型，存在较大的研发资源消耗。

### **赛题任务**

本赛道旨在通过多任务联合训练来提升模型的泛化能力，同时解决多任务、多数据之间冲突的问题。本赛题基于交通场景，选择了分类、检测、分割三大代表性任务进行AllInOne联合训练。

任务定义：根据给出的分类、检测、分割三任务的数据集，使用统一大模型进行AllInOne联合训练，使得单一模型能够具备分类、检测、分割的能力。

### **解决方案**

- 在不使用更大backbone的基础上，最终达到了score79
- 使用vit-base-dino预训练权重，在全量数据上训练
- 更大的训练尺度
  - seg 1280-720
  - cls 448
  - det 736-1120
- 更长的训练周期 120e-130e
- 通过增加seg头的hidden_dims以及det头的embedding_dims减少通道降维带来的信息损失
  - hidden_dims 256-384
  - embedding_dims 256-384

### 训练步骤

预训练权重放在pretrained/

模型推理权重放在pretrained/

```python
# 训练
sh scripts/train.sh
# 推理
sh scripts/test.sh
```
