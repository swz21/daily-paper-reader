---
title: "Compression as Adaptation: Implicit Visual Representation with Diffusion Foundation Models"
title_zh: 压缩即适配：基于扩散基础模型的隐式视觉表示
authors: "Zongyu Guo, Jiajun He, Zhaoyang Jia, Xiaoyi Zhang, Jiahao Li, Xiao Li, Bin Li, José Miguel Hernández-Lobato, Yan Lu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/fae01b854c27847e80f492f24fb93914d999a323.pdf"
tags: ["query:diff-video"]
score: 4.0
evidence: 利用冻结扩散基础模型作隐式视觉表示，而非生成视频
tldr: 像素、潜变量或token等传统视觉表示无法直接利用扩散基础模型的大规模先验。本文提出一种新的隐式表示框架，把信号编码为附着在冻结生成模型上的低秩适配函数，可将81帧视频压成单一紧凑向量。该方法在极低比特率下实现强感知压缩，并因其函数式特性支持压缩之外的复用，为扩散模型用于通用视觉表示提供了新途径。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有视觉表示外置于模型，无法利用视觉扩散模型中丰富的先验知识进行紧凑存储和复用。
method: 提出将信号编码为函数，用冻结视觉生成模型上的低秩适配参数化，使视频可哈希为单一紧凑向量。
result: 在极低比特率下实现强感知视频压缩，且函数式表示支持后续任务。
conclusion: 展示扩散基础模型可作为通用隐式表示先验，用于压缩与视觉理解。
---

## Abstract
Modern visual generative models acquire rich visual knowledge through large-scale training, yet existing visual representations (such as pixels, latents, or tokens) remain external to the model and cannot directly exploit this knowledge for compact storage or reuse. 
In this work, we introduce a new visual representation framework that encodes a signal as a function,  which is parametrized by low-rank adaptations attached to a frozen visual generative model.
Such implicit representations of visual signals, \textit{e.g.}, an 81-frame video, can further be hashed into a single compact vector, achieving strong perceptual video compression at extremely low bitrates.
Beyond basic compression, the functional nature of this representation enables inference-time scaling and control, allowing additional refinement on the compression performance. 
More broadly, as the implicit representations directly act as a function of the generation process, this suggests a unified framework bridging visual compression and generation.

---

## 论文详细总结（自动生成）

# 压缩即适配：基于扩散基础模型的隐式视觉表示

## 1. 论文的核心问题与整体含义

- **研究背景**：现代视觉生成模型通过大规模训练获得了丰富的视觉先验知识，但现有视觉表示方式（如像素、潜在变量、token）都是“外置”于模型之外的，无法直接利用这些先验来实现更紧凑的存储或更灵活的重用。
- **核心问题**：如何让视觉表示本身“内化”到生成模型中，从而借助扩散基础模型的强大先验完成高压缩比下的视觉信号存储与理解？
- **整体含义**：本文提出一种全新的视角——**压缩即适配**，将任意视觉信号（例如一段81帧的视频）编码为冻结扩散模型上的一个低秩适配函数，进而可将整个信号哈希为单个紧凑向量。这不仅实现了极低比特率下的强感知压缩，还因表示的函数属性将视觉压缩与生成统一起来。

## 2. 论文提出的方法论

- **核心思想**：将视觉信号表示为“函数”而非静态张量。该函数由**冻结的视觉生成模型**（如扩散基础模型）加上**低秩适配（Low-Rank Adaptation, LoRA）**参数化，适配参数即编码了信号的信息。
- **关键技术细节**：
  - 输入信号（例如视频帧序列）通过优化低秩适配参数被“拟合”到冻结生成模型上，使得该适配模型能够重建或生成原始信号。
  - 这些适配权重可以进一步经过哈希或压缩，形成**单一紧凑向量**，用于极低比特率存储。
  - 由于表示本身是一个函数（生成过程的一部分），可以在推理时对其施加控制或迭代细化，从而实现**推理时缩放（inference-time scaling）**，进一步提升压缩性能。
- **算法流程（文字说明）**：
  1. 给定目标视觉信号（如视频）。
  2. 保持扩散基础模型参数完全冻结，仅训练/优化一组低秩适配矩阵。
  3. 通过最小化重建损失（例如感知损失或扩散目标），使该适配函数能够重建或生成目标信号。
  4. 将最终得到的低秩参数编码为紧凑代码，作为该信号的隐式表示。
  5. 解码时，用该代码初始化/恢复适配参数，并通过冻结生成模型生成重建信号；推理时可额外进行迭代优化增强质量。

## 3. 实验设计

- **目标场景**：视觉压缩，尤其是**极低比特率下的视频压缩**；同时也涉及表示该表示的可复用性（压缩之外的后续任务）。
- **数据集/基准**：由于提供的文本只有摘要，未明确列出具体数据集名称（如 UCF101、Kinetics、UVG 等）和标准基准（如 PSNR、MS-SSIM、LPIPS、FVD 等）。无法从现有信息确认。
- **对比方法**：也未具体说明。但根据问题设定，推测会与传统视频编码标准（如 H.264/H.265/VVC）、神经压缩方法、基于隐式神经表示的方法进行对比。

## 4. 资源与算力

- **未明确说明**：提供的文本中没有提及 GPU 型号、数量、训练时长、参数量或能耗等信息。
- 因此无法评估该方法的实际计算开销；但“冻结扩散基础模型 + 低秩适配”通常比全量微调更节省显存与存储资源，属于轻量级适配方案。

## 5. 实验数量与充分性

- **已知信息有限**：摘要提到在“极低比特率”下实现强感知视频压缩，并展示了“推理时缩放和控制”以及“压缩之外的复用”，暗示了至少包含**多类实验**（压缩性能、推理时增强、下游任务复用）。
- **充分性评估**：仅凭摘要无法判断实验的覆盖面和公平性。没有提供：
  - 具体数据集及数目；
  - 基线方法及配置；
  - 消融实验细节（例如低秩秩的大小、哈希方法、冻结模型规模等）；
  - 统计显著性和误差条。
- 因此，实验是否充分、客观、公平需查阅完整论文后才能得出结论。当前摘要层面无法证实。

## 6. 论文的主要结论与发现

- **结论一**：扩散基础模型可以作为通用视觉先验，用于隐式视觉表示，而不仅仅是生成样本。
- **结论二**：将信号编码为冻结生成模型上的低秩适配函数，可以把81帧视频压缩为单一紧凑向量，在极低比特率下保持强感知质量。
- **结论三**：函数式表示天然支持推理时缩放与控制，可进一步优化压缩效果。
- **结论四**：该框架统一了视觉压缩与视觉生成，为扩散模型在通用表示学习中的应用开辟了新途径。

## 7. 优点

- **概念新颖**：提出“压缩即适配”的范式，将传统视觉表示从数据依赖转变为模型依赖，是压缩与生成统一的有力尝试。
- **灵活性高**：由于表示是函数，可进行附加调试、推理时优化、条件控制等，超越固定张量表示的局限。
- **高效紧凑**：利用冻结基础模型和低秩适配，避免了训练完整生成网络，同时实现极高的压缩率（81帧→单向量）。
- **潜在广泛适用性**：不仅用于视频压缩，还可拓展到图像、3D、音频等视觉/多模态信号；同时表示可复用，服务于下游理解任务。

## 8. 不足与局限

- **信息不足**：当前论文摘要未提供充足的实验细节，无法验证其声称的“强感知压缩”的可复现性。
- **算力与存储开销**：虽然低秩适配相对轻量，但扩散基础模型本身较大，解码时耗用较多计算资源；实际部署效率未知。
- **低秩容量限制**：单一紧凑向量需要编码81帧的信息，信息瓶颈可能导致复杂运动、纹理细节丢失，尤其在高分辨率或长时间视频场景中的表现不明确。
- **评估指标与公平性**：未明确感知度量的选择（如LPIPS vs FVD）、比特率计算方式（是否包含模型参数/哈希映射）等，存在基准设定偏差风险。
- **表示的可解释性与泛化**：将信号映射为适配函数，其语义可解释性较弱；且对未见域（非自然视频、医学图像等）的泛化能力未讨论。
- **缺乏与生成质量的博弈分析**：压缩性能与生成先验之间可能存在冲突，论文未展示对“先验偏见”的系统分析。

（完）
