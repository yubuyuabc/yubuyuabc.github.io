---
title: 谢谢你， BART ！
date: 2022-05-23 20:24:01
---





由于平行数据的缺乏导致文本风格迁移模型在保存内容方面成功率很低，作者尝试了在与训练语言模型 GPT-2 和 BART 进行微调。实验证明，预训练语言模型即使在数据有限的情况下也能提高内容保存能力。作者使用强化学习的策略，把风格和内容两方面作为预训练模型（GPT-2 和 BART）的奖励。实验证明模型在数据集上取得 SOTA 。

## 论文模型

![image-20220523202056657](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202205232020061.png)

论文提出了一个在预训练语言模型基础上的强化学习框架。该框架主要包含两个部分：在平行语料库上对预训练语言模型进行微调；加入奖励机制，确保数据风格变换和内容保存。在预训练语言模型部分，论文采用 GPT-2 和 BART 。

GPT-2 是一个基于 Transformer 解码器的语言模型。对于给定 token 序列 $x = \{x_1, x_2, ..., x_l \}$ ，语言模型的作用是最小化负对数似然：

![image-20220524142215508](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202205241422685.png)

其中 $k$ 为上下文窗口大小。为使得 GPT-2 能够以目标风格重构输入文本，需要将原本的输入 $<source, target>$ 视为用三个特殊 token 分割开的一个文本。 起始 token 为 $[BOS]$ ，结束 token 为 $[EOS]$ ，源文本和目标文本用 $[SEP]$ 分隔开。

BART 模型则是需要最小化解码器的输出和目标语句之间的交叉熵损失函数：

![image-20220524145847760](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202205241458798.png)

源文本和目标文本只需要在句首和句尾增加对应的特殊 token 。



模型设置的奖励（reward）包含两种，可以单独使用又可以一起使用。一种是增强样式强度（Style Classification Reward）和内容保留（BLEU 分数奖励）。

在 Style Classification Reward 部分，论文使用 分类置信度奖励（classification confidence reward）鼓励 样本分类器（style classifier, SC）的**置信度**发生更大的改变。论文训练了 TextCNN 模型用来评估迁移后的句子与目标风格的匹配程度。SC 的置信度公式为：

![image-20220524152630073](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202205241526113.png)

其中 $i = \{1, 2\}$ ，分别代表源样式和目标样式。 $\theta$ 是 SC 的参数，在微调阶段保存不变。奖励 设置为：

![image-20220524162845702](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202205241628819.png)

其中 $y'$ 是在解码的每个时间步上从模型分布中采样生成的目标句子。对于基于 GPT-2 的模型，论文还增加了分类置信度奖励：

![image-20220524165655550](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202205241656602.png)



BLEU Score Reward 部分，按照 Thibault Sellam 的论文 [BLEURT](https://doi.org/10.18653/v1/2020.acl-main.704) 的思路设置奖励。其中 $y'$ 是在每个时间步上通过最大化模型输出分布而得到的目标风格文本，$y^s$ 是从该分布中的采样。

![image-20220524170351419](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202205241703471.png)



policy gradient 的公式为：

![image-20220524170827002](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202205241708061.png)

其中 $R$ 是 SC 奖励 或者 BLEU 奖励（也有可能是两个奖励），$y^s$ 每个解码时间步上模型输出分布的采样。对于基于 GPT-2 的模型需要给 SC 奖励的原句加上 policy gradient 。

最终的训练目标是各个模型的 loss 和 不同奖励的 policy gradient 。





## 模型实验

实验数据集采用 Grammarly’s Yahoo Answers Formality Corpus (GYAFC) 数据集。数据集属于 Entertainment & Music (E&M)  和 Family & Relationship (F&R) 两个领域，包含 正式文本和非正式文本的平行语料。

![image-20220524173021837](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202205241730877.png)

模型使用 Adam 优化器，batch size 采用 32，初始学习率为 5e-5 (GPT-2) 和 3e-5 (BART) 。SC 的 $\lambda$ 设置为 1，BLEU 验证的值设置为 0.2 。

![image-20220525111226776](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202205251112897.png)







