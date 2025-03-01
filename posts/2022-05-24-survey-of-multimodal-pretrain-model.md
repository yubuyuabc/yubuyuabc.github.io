---
title: 多模态预训练语言模型总结
date: 2022-05-24 07:01:02
---







![自然语言处理中预训练模型](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111418304.jpg)

自从18年BERT预训练语言模型在NLP领域的发展，许多研究者也看到了多模态预训练的发展机会。从LXMERT、VLBERT、ViLBRET、UNITER、UNIMO、OSCAR、VisualBert、VLP到去年的ViLT、VinVL、SOHO、SimVLM、METER等，许多关于多模态预训练的文章也应运而生。





![image-20220318142258517](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111418798.png)

多模态预训练语言模型按照结构分为单流和双流。单流模型中，视觉特征和文本特征一开始就拼接在一起，然后直接输入到编码器中；双流模型就是将视觉特征和文本特征首先在两个独立的编码器中进行编码，然后再输入到 cross attention 进行多模态特征的融合。

![image-20220315102311377](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111418103.png)

> 单流模型的定义：只使用self-attention来学习模态内和模态间的联系。
>
> 双流模型的定义：专注于学习模态之间的联系，比如cross-attention，co-attention。基本上，transformer时代到来之前，都是双流模型。

多模态预训练语言模型的主要研究方向分为三种：模型结构、预训练任务和预训练数据。模型对比如下：

|    模型    | 时间 | 预训练数据                                                   | 模型结构                                                     | 预训练任务                                                   | 下游任务                                                     | 算力花费          |
| :--------: | ---- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------- |
|  ViLBERT   | 19   | ConCeptual Captions                                          | 双流结构、通过 co-attentional transformer 层将图像和文本信息进行结合 | MLM、NSP、multi-modal alignment prediction                   | VQA、visual commonsense resoning、referring expression Comprehension、image-test retrieval | 8 * TitanX        |
| VisualBERT | 19   | COCO caption                                                 | 单流结构                                                     | MLM、sentence-image prediction                               | VQA、VCR、NLVR、image-test retrieval                         | 8 * TitanX        |
|   LXMERT   | 19   | MSCOCO、VG、GQA等，总共91.8M 图像-文本对，180K不同的图片     | 双流结构、通过Cross-Modality Encoder将图像和文本编码结果进行结合 | MLM、Masked Object Prediction、Cross-Modality Matching、Image Question Answering | VQA、GQA、NLVR                                               | 10天 5 * Titan Xp |
|   UNITER   | 19   | COCO、VG、SBU、ConCeptual Captions                           | 单流结构                                                     | MLM、ITM、MRM                                                | VQA、Visual Entailment、NLVR、visual commonsense resoning、referring expression Comprehension、image-test retrieval |                   |
|   Oscar    | 20   | COCO、ConCeptual Captions等，总共6.5M图片-文本-标签对，4.1M的图片 | 单流结构                                                     | Oscar                                                        | Image-Text retrieval、VQA、NLVR、GQA、Image Captioning on COCO | 64 * V100         |
| ERNIE-ViL  | 20   | Conceptual Caption 、 SBU Caption                            | 双流结构                                                     | BERT采用MLM预训练（knowledge masking strategy）；三个多模态的场景图预测（Scene Graph Peddiction）；物体检测（Attribute Prediction）；关系预测（Relationship Prediction）;MLM、Masked Region Prediction、Image-Text Matching | Visual Commonsense Reasoning（VCR）、VQA、Grounding Referring Expressions、Image-Text Retrieval |                   |
|   UNIMO    | 20   | BookWiki、OpenWebText、OpenImage、COCO、Visual Genome、Conceptual Captions、SBU Captions | 单流结构                                                     | Text Rewriting、Image-Text Retrieval、bidirectional prediction、Seq2Seq generation | 生成式对话型问答（CoQA）、生成式摘要（CNN/DM）、句子压缩（Gigaword）、情感分析（SST）、自然语言推理（MNLI）、语言可接受性分析（CoLA）、语义相似度分析（SST-B）、VQA（VQAv2.0）、看图说话（Microsoft COCO Captions）、视觉隐含（SLNI-VE）、image-test retrieval（Flickr20k） | 64 * V100         |
|    ViLT    | 21   | MSCOCO、VG、SBU、ConCeptual Captions，总共4M图片、奖金10M的文本 | 单流结构                                                     | ITM、MLM                                                     | VQA、NLVR、image-test retrieval                              | 1*P40             |
|   VinVL    | 21   | 图像字幕数据集（COCO、Conceptual Captions， SBU captions、Flicker 30K）;VQA数据集（GQA、VQA、VG-QAs）；图像标记数据集（OpenImages的子集） | 单流结构                                                     | oscar的预训练任务                                            | VQA、GQA、Image Captioning、Image-Text Retrieval、NLVR2      |                   |
|  E2E-VLP   | 21   | COCO、VG，总共6.01M图片-文本对，180K不同的图片               | 单流结构                                                     | MLM、ITM                                                     | VQA、NLVR2、Image-Text Retrieval                             | 8 * V100          |
|    SOHO    | 21   | MSCOCO、VG                                                   | 单流结构                                                     | MLM、ITM、Masked Visual Modeling                             | VQA、NLVR、SNLI-VE、Text Retrieval、Image Retrieval          | 32 * Tesla V100   |
|   SimVLM   | 21   | ALIGN：1.8B图片-文本对，C4：纯文本数据集                     | 单流结构                                                     |                                                              | VQA、NLVR2、SNLI-VE、COCO Caption、NoCaps、                  | 512 *             |
|   METER    | 21   |                                                              |                                                              |                                                              |                                                              |                   |



## 多模态预训练模型结构

近期的研究工作主要基于 Transformer 的各种变体，大多数的研究使用统一的 Transformer 对图像和文本进行建模。ViBERT 和 LXMERT 基于双流跨模态 Transformer 这样的方案使得文本和图像获得了更加具体的表示。



## 多模态预训练数据

与可以利用大量自然语言数据的文本预训练模型不同，跨模态预训练任务需要高质量的对齐的图文数据，而这些数据很难获得。Conceptual Captions 数据集和SBU Captions 数据集是使用最广泛的两个图文预训练数据集，分别包含3.0M和1.0M条图像文本对。这两个数据集对于CV的下游任务来说是域外的数据集，与下游任务相关域内数据集有 MS-COCO 数据集和 Visual-Genome 数据集。

### Conceptual Captions 数据集



### SBU Captions 数据集



### MS COCO 数据集



### Visual-Genome 数据集





## 多模态预训练任务

### Masked Cross-Modality LM

![image-20220315143747171](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419230.png)

在 Masked Cross-Modality LM 预训练任务中，需要在 sentence tokens 中随机 MASK 掉一些 token，然后模型基于其他的本文 token 和所有的图像 token 来预测这些被 mask 掉的 token。

### MRM预训练任务

![image-20220315144450419](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419580.png)

在 Masked Region Classification（MRC）预训练任务中，需要在 region token 中随机 mask 掉一些 token，被 mask 的部分的特征全置为0，然后根据其他的图片 token 和所有的文本 token 来预测这些被 mask 的 token。具体来说就是，每个 region 都会有 Faster R-CNN 得到一个 label，模型需要预测 mask token 的类别，使之和 Faster R-CNN 的 label 相同。

### MRM预训练任务（MRC-KL）

![image-20220315144613678](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419397.png)

在 Masked Region Classification-KL Divergence（MRC-KL）预训练任务中，同样是随机 mask region token，但是不同的这里不是做分类任务，而是需要计算 Faster R-CNN 特征和 Mask region 的分布差异，使得 Mask region 的分布和 Faster R-CNN 特征的分布尽可能相似，所以损失函数用的是 KL 散度。

### ITM预训练任务

![image-20220315144647653](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419884.png)

Image-Text Matching（ITM）中，需要对输入的 Image-Text Pair 随机替换 Image 或者 Text，最后预测输入的 Image 和 Text 是否有对应关系，所以这是一个二分类的问题。

### multi-modal alignment prediction

ViLBERT使用的预训练任务，判断文本与图片是否对齐。正样本采用电影图片及对应字幕，负样本随机对图片进行替换。

![image-20220316112110157](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419133.png)



## 多模态预训练下游任务

### Visual Question Answering（VQA）

VQA任务是介于CV任务和NLP任务的交集。VQA 的目的是开发出一种系统来回答有关输入图像的特定问题。答案可以采用以下任何形式：单词，短语，二元答案，多项选择答案或文本填空。

QA任务和MRC任务是两种不同的任务，但有的时候两个可以相互结合。MRC任务强调能够阅读文本的能力，而QA任务强调能够回答给出的问题。完成QA任务需要用到不同的技术，有时需要用到MRC的技术。并不是所有的MRC技术都可以解决QA问题，MRC是让系统通过阅读学会理解文本的能力，这种能力可以通过不同的任务进行测试，也可以应用在不同的NLP任务中。

![img](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419287.png)

MRC的主要任务类型分为：完形填空（CLoze Style）、多项选择（Multiple Choice）、片段抽取（Span Prediction）和自由作答（Free-form Answer）。大多数MRC任务都是QA任务，也成为典型的机器阅读理解的任务（Typical MRC）。Pure VQA任务一般是没有引入额外的context，只是单纯的有（图， 问句， 回答）三元组，而Multimodal MRC任务，实际上就只是引入了额外的context作为VQA任务的知识，并且更加注重于自然语言的理解。

![image-20220315145121765](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419739.png)

VQA 就是对于一个图片回答图片内容相关的问题。将图片和问题输入到模型中，输出是答案的分布，取概率最大的答案为预测答案。

#### DAQUAR （Dataset for Question Answering on Real World Images 真实世界图像问答数据集）

DAQUAR数据集包含1449张图像，12468个问答对和2483个独特问题。通过人工注释生成问题；并使用NYU-Depth数据集的注释将问题限制在9个问题模板中。包含了6795张训练数据和5673张测试数据，所有图像来自于数据集NYU-DepthV2 Dataset。该数据集质量较差，一些图像杂乱无章，分辨率低，并且问题和回答有明显的语法错误。

#### COCO-QA

COCO-QA数据集比DAQUAR大得多。它包含123,287张来自可可数据集的图片。所有问题的答案都是一个单词，只有435个独一无二的答案。

#### VQA v1



#### VQA v2

ViLBERT采用的数据集是VQA2.0数据集，包含1.1M关于COCO数据集的问题，每个问题包含10个答案。



### Visual Entailment

![https://pic3.zhimg.com/80/v2-7dad23489417389abef0d10e8199ab56_720w.jpg](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419042.jpg)

在 Visual Entailment 中，Image 是前提，Text 是假设，模型的目标是预测 Text 是不是“Entailment Image”，一共有三中 label，分别是 Entailment、Neutral 和 Contradiction。

![https://pic2.zhimg.com/80/v2-5a74beed5c7b20e47639aa02394a064d_720w.jpg](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419520.jpg)

在 pipeline 中，将 Image 和 Text 输入到模型中，输出是三个 label 中的一个作为预测分类。

### Natural Language for Visual Reasoning

![https://pic3.zhimg.com/80/v2-bb5c835cede597e5952e4eda40093f26_720w.jpg](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419522.jpg)

NLVR（Natural Language for Visual Reasoning）任务中，需要同时输入两张 Image 和一个描述，输出是描述与 Image 的对应关系是否一致，label 只有两种（true/false）。

![https://pic4.zhimg.com/80/v2-9b75d31c1b9c1c8feade61c9ff17d303_720w.jpg](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111419567.jpg)

在 pipeline 中，将 Images 和 Text 输入到模型中，输出是两个 label 中的一个作为预测分类。

### Visual Commonsense Reasoning（VCR）

VCR任务包含两个问题，一个是 visual question  answering（Q->A），一个是 answering justification（QA->R），这两个问题都是一多项选择的问题。

![https://pic3.zhimg.com/80/v2-b629f7ffcf05a08bcf05d895c495cb2e_720w.jpg](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420728.jpg)

Visual Commonsense Reasoning 中，任务是以选择题形式存在的，对于一个问题有四个备选答案，模型必须从四个答案中选择出一个答案，然后再从四个备选理由中选出选择这个答案的理由。

![https://pic2.zhimg.com/80/v2-760c8445c5aba95c383e415da34be54d_720w.jpg](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420184.jpg)

在训练的过程中，我们将问题和四个备选答案连接到一起再分别与图片输入到模型中，输出为四个得分，得分最高的为预测答案。选择理由是过程也是类似。

Visual Commonsense Reasoning数据集包含290K个多项选择QA问题，问题来源于110K个电影场景。

### Referring Expression Comprehension

![https://pic1.zhimg.com/80/v2-256ae4e9b57501f209b1e78f5accfb20_720w.jpg](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420144.jpg)

Referring Expression Comprehension 任务中，输入是一个句子，模型要在图片中圈出对应的 region。

![https://pic4.zhimg.com/80/v2-143b954a48b4c260748b5f481c2bc7b7_720w.jpg](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420919.jpg)

对于这个任务，我们可以对每一个 region 都输出一个 score，score 最高的 region 作为预测 region。

###  Image-Text Retrieval

![https://pic1.zhimg.com/80/v2-ac006f7ce607782326dda8f440023534_720w.jpg](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420334.jpg)

在 Image-Text Retrieval 任务中，就是给定一个模态的指定样本，在另一个模态的 DataBase 中找到对应的样本。

![https://pic1.zhimg.com/80/v2-a1fdb678ce69e8e5ec44bc7a59f2312c_720w.jpg](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420571.jpg)

这个任务 Image-Text Matching 任务非常相似，所以在 fine-tune 的过程中就是选择 positive pair 和 negative pair 的方式来训练模型。

## 多模态预训练的研究工作

### ViLBERT

![image-20220315113201357](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420986.png)

ViLBERT采用双流的模型结构分别对图像和文本进行 embed ，再使用 co-attention 模型进行整合。

![image-20220315170446641](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420835.png)

co-attention 通过交换多头注意力的key-value对，使得模型结构能够使得图像特征和文本特征进行融合。

BERT模型部分采用MLM和NSP的预训练任务，而ViLBERT采用了相似的预训练任务，masked multi-modal modelling 和 multi-modal alignment prediction。

论文在四个下游任务上进行了实验，分别是VQA、VCR、Referring Expressions 和 caption-Based Image Retrieval。

![image-20220316093427802](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420309.png)

论文对比实验设计上，在VQA上对比DFAF模型，数据集采用VQA2.0数据集、VCR任务上对比R2C模型，RefCOCO+任务上对比MAttNet模型，caption-based image retrieval 任务上对比SCAN模型。







### 百度 ERNIE-ViL

论文针对预训练任务中对于细节信息（对象、对象的属性和对象之间的关系）遗漏的问题，提出了一种结合场景图获取结构化知识提高视觉语言跨模态联合表示的多模态模型ERNIE-ViL 。ERNIE-ViL 利用视觉场景的场景图，在预训练阶段构建场景图预测任务，即对像预测、属性预测和关系预测。具体来说，这些预测任务是通过预测从场景中解析出来的场景图中不同类型的节点来实现的。因此，ERNIE-ViL 可以学习跨视觉和跨语言的细节语义对齐的联合表征。在对大规模图文对齐数据集进行预训练后，ERNIE-ViL在5个跨模式下游任务上取得最好的效果。

![img](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420714.jpg)

图中为 Flick 30K 数据集中的相似场景，在一些细节上存在不同，而这些细节决定了对场景的解释。（a）图中对象不同，分别为狗和猫。（b）图中对象的属性不同，狗玩的玩具具有不同的颜色。（c）图中人和自行车的关系不同，分别为骑行和修理。

![img](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420048.jpg)

场景图预测任务如上图所示。给定图像检测区域和文本标记序列，ERNIE-ViL使用双流跨模态 Transformer 对图像和文本的联合表示进行建模。基于使用场景图解析器从文本中解析出场景图，构建对象预测、属性预测和关系预测任务，以学习跨模态的详细语义对齐。

![img](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111420500.jpg)

模型在域外数据集 CC 和 SBU 数据集上进行预训练后，在5个下游任务上都取得了最好的效果。在视觉推理任务上， ERNIE-ViL_large 比 VLBERT_large 在VCR上午上有6.60%的显著提高。在 Visual grounding 任务中，ERNIE-ViL_large 在test A 和 test B 上比 VLBERT_large 提高了2.40%。在跨模态检索任务上，Ernie-VIL-BASE在图像检索的R@1上比Unicoder-VL-BASE在R@1上提高了2.94%，在文本检索上提高了0.50%。

### Facebook UniT

Transformer架构在自然语言处理和其他领域的机器学习(ML)任务中表现出了巨大的成功，但大多仅限于单个领域或特定的多模态领域的任务。例如，ViT专门用于视觉相关的任务，BERT专注于语言任务，而VILBERT-MT只用于相关的视觉和语言任务。

一个自然产生的问题是：能否建立一个单一的Transformer，能够在多种模态下处理不同领域的广泛应用？最近，Facebook的一个人工智能研究团队进行了一个新的统一Transformer(UniT) encoder-decoder模型的挑战，该模型在不同的模态下联合训练多个任务，并通过一组统一的模型参数在这些不同的任务上都实现了强大的性能。

![img](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111421107.jpg)

Transformer首先应用于sequence-to-sequence模型的语言领域。它们已经扩展到视觉领域，甚至被应用于视觉和语言的联合推理任务。尽管可以针对各种下游任务中的应用对预先训练好的Transformer进行微调，并获得良好的结果，但这种模型微调方法会导致为每个下游任务创建不同的参数集。

Facebook的人工智能研究人员提出，一个Transformer可能就是我们真正需要的。他们的UniT是建立在传统的Transformer编码器-解码器架构上，包括每个输入模态类型的独立编码器，后面跟一个具有简单的每个任务特定的头的解码器。输入有两种形式：图像和文本。首先，卷积神经网络骨干网提取视觉特征，然后BERT将语言输入编码成隐藏状态序列。然后，Transformer解码器应用于编码的单个模态或两个编码模态的连接序列(取决于任务是单模态还是多模态)。最后，Transformer解码器的表示将被传递到特定任务的头，该头将输出最终的预测。

![img](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111421862.png)

评估UniT的性能，研究人员进行了实验，需要共同学习来自不同领域的许多流行的任务：COCO目标检测和 Visual Genome数据集，语言理解任务的GLUE基准(QNLI, QQP、MNLI-mismatched SST-2)，以及视觉推理任务VQAv2 SNLI-VE数据集。

![img](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111421315.png)

![img](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111421186.png)

多任务训练的UniT性能优于单独训练的目标检测和VQA。

![img](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111421011.jpg)

基于UniT模型的目标检测与VQA的分析。

![img](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204111421741.jpg)

具有共享解码器的UniT模型的预测。

结果表明，所提出的UniT 模型同时处理8个数据集上的7个任务，在统一的模型参数集下，每个任务都有较强的性能。强大的性能表明UniT有潜力成为一种领域未知的transformer 架构，向更通用的智能的目标迈进了一步。



### CPT: Colorful Prompt Tuning for Pre-trained Vision-Language Models

论文一作是清华大学的 Yuan Yao 和 NUS 的 Ao Zhang。

#### 论文动机

多模态预训练语言模型在预训练和微调阶段存在明显的差异（gap）。原因在于模拟下游任务需要大量的标签数据。为解决这一问题，论文提出了 Cross Prompt Tuning (CPT) 。





### ViT: A Image is Worth 16x16 Words Transormers for Image Recognition at Scale

论文一作是 Alexey Dosovitskiy 、 Kucas Beyer 、 Alexander Kolesnikov 、 Drik Weissenborn 、Xiaohua Zhai

机构是 Google Reasearch, Brain Team

论文是 ICLR 2021 的论文

论文只开源了 [Fine-tuning 代码](https://github.com/google-research/vision_transformer)



Transformer 在 CV 领域的使用存在局限（2020年），注意力机制一般用来连接卷积神经网络，或者用来替换卷积神经网络一定的组件而不改变整体的结构。这篇论文证明卷积神经网络不是必要的，而且一个纯 Transformer 能够直接将图片序列化（拆成一个个区域pathes），并在分类任务上取得很好的效果。论文提出的 ViT 模型在大量的图片数据上进行预训练，并将其迁移至下游的图像识别任务上，取得了最好的效果。

论文作者将图片切分为一个个区域，并给这些区域提供一个线性的序列作为 Transformer 的输入。这些区域等同于 NLP 领域的 token。当使用同等规模数据集（中等规模的数据集，ImageNet）进行训练时，没有强正则的 Transformer 模型比 ResNet 低几个点。可能是由于卷积具有 Transformer 不具备的归纳偏置（inductive bias）[^6]，即平移等价性（tranlation equivariance）和局部性（locality）。因此在数据量不足的情况下，模型表现不佳。

而当数据集很大的情况（14M-300M）下，论文发现大规模的数据训练能胜过卷积神经网络的归纳偏置。ViT在 ImageNet-21K 数据集和 JFT-300M 数据集上进行预训练后，在多个图像识别基准上超过了现有水平（ImageNet 88.55%，Image-ReaL 90.72%，CIFAR-100 94.55%，VTAB 77.63%）。







### ViLT: Vision-and-Language Transformer Without Convolution or Region Supervision



目前参数量最小的多模态Transformer方法。ViLT使用预训练的ViT来初始化交互的transformer，这样就可以直接利用交互层来处理视觉特征，不需要额外增加一个视觉encoder（如Faster-RCNN）[^4]。

![image-20220414185453297](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204141854384.png)











### CLIP



### BLIP: Bootstrapping Language-Image Pre-training forUnified Vision-Language Understanding and Generation





### FILIP: Fine-grained Interactive Language-Image Pre-Training



### M6: A Chinese Multimodal Pretrainer

#### 论文贡献[^6]：

1.  构建了最大的中文多模态预训练数据集。它覆盖广泛的领域，由超过 1.9TB 的图像和 292GB 的文本组成。
2.  提出了一种跨模态预训练方法 M6，用于对单模态和多模态的数据进行统一预训练，并构建了最大的中文预训练模型，参数规模 10B/ 100B。
3.  一系列下游应用展示了其出色的性能。此外，专门设计了一个由文本生成图像的下游任务，并表明微调的M6可以创建高分辨率和丰富细节的高质量图像。
4.  通过精心设计的大规模分布式训练优化，M6在训练速度上具有明显优势，并大大降低了训练成本，为更广泛应用多模态预训练创造了可能

#### 模型结构

![image-20220418103631595](https://raw.githubusercontent.com/Moriarty12138/PictureBed/main/img/202204181036761.png)



### Switch Transformers: Scaling to Trillion Parameter Models with Simple and Efficient Sparsity









## 参考资料

[^1]:[从VQA到多模态综述-Part1](https://zhuanlan.zhihu.com/p/468960316)
[^2]:[从VQA到多模态综述-Part2](https://zhuanlan.zhihu.com/p/471359218)
[^3]:[从VQA到多模态综述-Part3](https://zhuanlan.zhihu.com/p/475687261)
[^4]:[从VQA到多模态综述-Part4](https://zhuanlan.zhihu.com/p/480029003)

[^5]:["未来"的经典之作ViT：transformer is all you need!](https://zhuanlan.zhihu.com/p/356155277)
[^6]:[如何理解Inductive bias？](https://www.zhihu.com/question/264264203)
[^7]:[多模态预训练模型——阿里M6](https://zhuanlan.zhihu.com/p/412793235)
[^8]:[M6: A Chinese Multimodal Pretrainer阅读笔记](https://zhuanlan.zhihu.com/p/355452724)

[^9]:[多模态预训练技术的探索和实践](https://zhuanlan.zhihu.com/p/435252076)
