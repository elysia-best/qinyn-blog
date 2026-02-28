---
title: 基于SoM的多模态通信感知
date: '2026-01-08 10:41:56'
updated: '2026-02-28 16:41:39'
permalink: /post/multimodal-communication-perception-based-on-som-p7lzt.html
comments: true
toc: true
---



# 基于SoM的多模态通信感知

SoM这个概念是由北京大学程翔教授团队在2024年的《Intelligent Multi-Modal Sensing-Communication Integration: Synesthesia of Machines》一文中提出 (X. Cheng and H. Zhang et al. 2024)[^1] 。

作为助力实现“万物智联”的核心技术之一，通信与感知融合技术是6G无线通信的一项关键技术。然而，现阶段主流的通信感知融合技术局限于通信与单一射频感知的融合（射频通感一体化），且局限于静态、低速场景，无法支撑6G典型应用场景的需求。为满足通信与感知系统极高的性能要求，需要挖掘通信与多模态感知之间存在的潜在关联，发挥“通”与“感”相互辅助的能力，促进通信与多模态感知的智能融合。

# 1. SoM 定义及介绍

SoM (Synesthesia of Machine) 机器联觉是仿照人类“通感”这一现象提出的。当人们听见某种声音时，首先想到的可能是颜色。回忆起某个记忆的时候，首先想起来的可能是味道。这就是“联觉”（Synesthesia），又称“通感”。显而易见，可以把人类从多种器官获取感知这一过程类比为机器从不同类型的传感器中获取不同类型的数据。同样，在人脑中，“联觉”的产生依赖于大脑中互相连接的神经元进行的，如果负责传递某种感官信号的神经元被真实刺激激活，它们可能会自发地引发负责传递另一种感官信号的神经元的交叉激活。也就是说，大脑中处理不同类型感觉的区域具有相似的结构，并且彼此相互连接 (X. Cheng and H. Zhang et al. 2024)[^1] 。将人脑的神经元连接用人工神经网络替换，也可能做到将来自不同传感器的信息关联起来，并用作对通信系统的指导。

![image](/images/siyuan-note-publish/image-20260114151632-h1ex4wz.png)

总体而言， SoM 可以分为三种类型，分别为：SoM-evoke、SoM-enhance 和 SoM-concert，文章中称之为三种不同的“Operation Models”

1. SoM-evoke：此模式无需系统中通信模块的参与，即通信模块处于关闭或不活动状态。此时可以利用SoM的方法，采集并处理多种传感器收集到的数据，并据此提升通信系统的性能。比如，利用多传感器信息，可以获取到信道状态信息，并由此信息可以为后续的通信模块提供支持，如帮助确定信道质量、链路状态等等。（P.S. 文中的信道状态信息 CSI 包含了许多信息，如障碍物的位置和移动速度、发射机和接收机的信息等）『此处有个疑问，尽管描述为无通信系统的参与，但由融合信息所得出的结论最终还是会用于指导通信系统进行更优的传输，这就和下面第二点中的enhance模式有些类似了。:(』
2. SoM-enhance：在这个模式当中就有通信系统的参与了，主要是增加通信系统的性能，因此叫做“enhance"吧。比如，利用激光雷达和深度相机对用户的位置进行精确的定位，将获取到的位置信息用于精确指导基站的预编码设计和毫米波波束对准。
3. SoM-concert：多传感器的融合也可以增强感知任务的性能。比如，RGB图像中丰富的语义信息与LiDAR点云中精确的结构信息可以协同提升目标检测与分类的性能，从而增强环境感知的可靠性。（P.S. 这里就和以前我们说的多传感器融合是一个道理。）

下面的图片就展示了这个几个模块之间的联系。

![image](/images/siyuan-note-publish/image-20260114202528-i7o9n1h.png)

---

> 文中提出了，目前被广泛研究的RF-ISAC是这提出的SoM的一个特例。文中指出，RF-ISAC中仅仅利用了射频这一个功能所提供的信息，而SoM中则是利用了多种传感器进行感知

> 显而易见，多种传感器数据的融合优化必然是非线性的问题，此类问题适合用神经网络来处理！除此之外，生成大量带标注数据集可以用深度强化学习，提高网络的泛化性能、降低训练开销则可以利用元学习（meta-learning）。除此之外还有联邦学习，保护隐私啥的（可以用在用户设备上，并且不传输数据到计算中心）。

#### Objectives of SoM

SoM的目标与潜在挑战，这里主要是分析了前面提出的SoM的科学性和可行性以及所提出的ANN方案的理论基础。而挑战简单而言就是，多种传感器采集到到的信息含义不同，格式不同，传递的信息也不同，如何设计一个模块使得他们可以共同用于同一个目标的决策or优化上。

# 2. SoM的基础：多模态数据集

文中给数据集的分类如下：

- Communicaton Only（COM）
- Non-RF Uni-Modal （NUM）

‍

![Mobile scattering clusters and fixed scattering clusters. ](/images/siyuan-note-publish/image-20260127113541-xif0e3v.png "Mobile scattering clusters and fixed scattering clusters. ")

# 3. 基于SoM数据集的FMs

Foundation Model ── 基础模型。Foundation Model 是基于大量无线和SoM数据集预训练出来的模型 (X. Cheng and B. Liu et al. 2026)[^3]。这点就类似与图像识别领域的YOLO系列以及近年来的各类大语言模型。FM 可以通过并少量样本学习或零样本学习来灵活适配到多种与 SoM 相关的任务中。

目前，SoM数据集在设计和训练不同模型的主要困难有

1. Heterogeneity of modalities 模型的异构性。CSI, channel impulse response (CIR), and IQ (In-phase and Quadrature) signals 和 LiDAR 等数据格式不同，表达的信息也不同。
2. Complexity of tasks 任务的复杂性。与LLM在自然语言处理（NLP）的应用不同，通信领域的任务，如CSI 反馈，预编码矩阵生成等任务的预期输出不能简单建模为token的生成。
3. Scarcity of datasets 缺少数据集。如前文描述，可用的数据集种类较少，且不一定满足所期望的场景需求。（有部分数据集，如DeepSense 6G等）

文中提出(X. Cheng and B. Liu et al. 2026)[^3]，可以用自监督学习的模式，对通信相关的模型进行训练。

在模型选择方面，主要有两种：直接基于LLM进行fine tuning──**通用型（LLM）和领域专用型（无线基础模型）。** 

## 3.1 LLM赋能SoM系统设计（Roadmap 1）

在SoM场景中使用LLM进行设计，要考虑两个问题：**用什么LLM**和**如何对选择的LLM进行调整**？其模型大体架构如下图所示 (X. Cheng and B. Liu et al. 2026)[^3]：

![LLM赋能SoM系统设计框架&提出的两种方案](/images/siyuan-note-publish/image-20260129211802-0qzh9xa.png)

1. LLM 选择

    1. *按照任务特点*：现有的预训练大型语言模型并非专门为SoM机制探索任务设计。因此，我们选择具备充分通用性和可扩展性的成熟大型语言模型。（Note：时序依赖任务（如信道预测）应选用时序FM（Timer、Time-MoE）而非语言LLM。）

        > TODO：关于大模型时间序列预测，后续阅读https://zhuanlan.zhihu.com/p/676256783做研究
        >
    2. *按照模型大小*：需兼顾迁移学习中的模型泛化能力及其推理开销，以满足SoM机制探索任务的高精度要求。文中选择了轻量级的GPT-2
2. 适配技术选择

    1. *微调（Fine-tuning）* ：适用于非语言任务，如 LLM4CP (X. Cheng and B. Liu et al. 2026)[^3] 在跨频段泛化中仅需30个CSI样本即超越传统模型。
    2. *提示工程（Prompt Engineering）* ：保留LLM语义理解能力，适用于多模态特征传输等任务。(Note：不太适合信道感知任务）
    3. 多模态信息融合：使用编码器！模态编码器作为多模态大模型（MLLM）的核心组件，其核心功能在于将原始异构数据（例如视觉图像或语音信号）转化为高语义密度的特征向量，实现跨模态的语义对齐与信息融合‌。

        > 文中举例：针对RGB-D图像，我们提取物理环境特征，并采用特征级融合将这些特征映射到自然语言领域。针对激光雷达点云，我们通过体素化预处理、片段划分及位置编码，将数据转换为与LLM特征空间兼容的标记序列。同时，输出模块将GPT-2生成的标记序列转化为所需的信道衰落信息——包括LLM4PG方案中的路径损耗与LLM4SG方案中的散射体信息​​
        >

基于上述理论，作者提出了基于采用GPT-2 + LN Tuning（仅训练LayerNorm参数）的架构。

## 3.2 无线基础模型（Wireless FM）赋能SoM系统设计（Roadmap 2）

主要步骤如下  
​![image](/images/siyuan-note-publish/image-20260202095827-2jlbn57.png)这个之前参考的VA-VAE的模型结构很类似了，其实。

所提的无线基础模型 (Boxun LIU and Shijian GAO et al. 2025)[^5]，比较创新的点在于其embedding部分，将三位信息嵌入编码，结构如下

![image](/images/siyuan-note-publish/image-20260202104334-9gcc7na.png)

这里使用了位置编码Positional Encoding，来源自原始论文《Attention is All You Need》

>  https://zhuanlan.zhihu.com/p/1935006199297859841

![image](/images/siyuan-note-publish/image-20260202104820-m3oas8k.png)

# 参考文献

[1] X. Cheng *et al.* , ‘Intelligent Multi-Modal Sensing-Communication Integration: Synesthesia of Machines’, *IEEE Communications Surveys & Tutorials*, vol. 26, no. 1, pp. 258–301, Firstquarter 2024, doi: [10.1109/COMST.2023.3336917](https://doi.org/10.1109/COMST.2023.3336917).

[2] X. Cheng, B. Liu, X. Liu, E. Liu, and Z. Huang, ‘Foundation Model Empowered Synesthesia of Machines (SoM): AI-Native Intelligent Multi-Modal Sensing-Communication Integration’, *IEEE Transactions on Network Science and Engineering*, vol. 13, pp. 762–782, 2026, doi: [10.1109/TNSE.2025.3587238](https://doi.org/10.1109/TNSE.2025.3587238).

[3] Boxun LIU, Shijian GAO, Xuanyu LIU, Xiang CHENG, and Liuqing YANG, ‘WiFo: wireless foundation model for channel prediction’, *SCIENCE CHINA Information Sciences*, vol. 68, no. 6, p. 162302, May 2025, doi: [10.1007/s11432-025-4349-0](https://doi.org/10.1007/s11432-025-4349-0).

‍

‍

[^1]: # x.chengIntelligentMultiModalSensingCommunication2024

    User Data[^2]

    X. Cheng, H. Zhang, J. Zhang, S. Gao, S. Li, Z. Huang, L. Bai, Z. Yang, X. Zheng, L. Yang, "Intelligent Multi-Modal Sensing-Communication Integration: Synesthesia of Machines,"  2024. Available: [On Zotero](zotero://select/library/items/NZ4KMFW7)

    # User Data

    ‍


[^2]: # User Data

    ‍


[^3]: # x.chengFoundationModelEmpowered2026

    User Data[^4]

    X. Cheng, B. Liu, X. Liu, E. Liu, Z. Huang, "Foundation Model Empowered Synesthesia of Machines (SoM): AI-Native Intelligent Multi-Modal Sensing-Communication Integration,"  2026. Available: [On Zotero](zotero://select/library/items/9JHKEJ2H)

    # User Data

    ‍


[^4]: # User Data

    ‍


[^5]: # boxunliuWiFoWirelessFoundation2025

    User Data[^6]

    Boxun LIU, Shijian GAO, Xuanyu LIU, Xiang CHENG, Liuqing YANG, "WiFo: wireless foundation model for channel prediction,"  2025. Available: [On Zotero](zotero://select/library/items/35GVGWBD)

    # User Data

    ‍


[^6]: # User Data

    ‍
