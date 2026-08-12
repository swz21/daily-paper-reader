---
title: Temporal-aware Flow Matching for Video Generation with Temporally Coherent Motion
title_zh: 时间感知流匹配：支持时序连贯运动的视频生成
authors: "Zirui Pan, Xin Wang, Yipeng Zhang, Yuwei Zhou, Wenwu Zhu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/2353dd72ba2db602d82f7e1a530e20640cd45ad5.pdf"
tags: ["query:diff-video"]
score: 10.0
evidence: 面向视频生成的时间感知流匹配
tldr: 现有视频生成模型将视频视为帧序列并直接沿用图像流匹配目标，缺乏对运动先验和时间依赖的显式建模，导致运动不连贯。本文提出时间感知流匹配（TFM），在流目标中嵌入帧间约束，从而显式建模时序关系并生成时序连贯的运动。实验表明该方法在视频生成中显著提升时间一致性和运动真实感，为视频生成提供了新的训练范式。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 图像流匹配目标直接用于视频生成会忽略时间依赖和运动先验，导致时序不连贯。
method: 在流匹配目标中加入帧间约束，提出时间感知流匹配训练范式，显式建模运动。
result: 生成的视频运动更连贯真实，时间一致性显著提升。
conclusion: 为视频生成提供了一种新的流匹配训练范式，改善了时序建模。
---

## Abstract
Despite rapid advances in text-to-video generation, state-of-the-art generative models still suffer from producing temporally incoherent and unrealistic motion for videos. The key weakness of existing works is that they commonly treat videos as frame sequences and directly adopt Flow Matching (FM) objectives, which are originally designed for images. This practice fails to explicitly model motion priors or temporal dependencies, resulting in suboptimal dynamics that may appear incoherent and unrealistic. To solve this problem, we propose Temporal-aware Flow Matching (TFM), a novel training paradigm that embeds inter-frame constraints into the flow objective, leading to temporally coherent motion modeling in video generation. More specifically, the proposed TFM enforces temporal correlations across frames while retaining the desirable properties of FM, and further introduces a residual-type loss that aligns naturally with this new flow. We theoretically prove that models trained with TFM are able to exhibit remarkably enhanced temporal perception ability. Notably, TFM imposes no additional cost during inference and is applicable to any model using FM. Extensive experiments demonstrate that our TFM can significantly improve motion realism across diverse motion types. Generated videos are presented at https://pzrain.github.io/tfm.

---

## 论文详细总结（自动生成）

# 时间感知流匹配：支持时序连贯运动的视频生成——论文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究背景**：文本到视频生成技术发展迅速，但当前最先进的生成模型仍普遍存在**时序不连贯（temporally incoherent）** 和**运动不真实（unrealistic motion）** 的问题。
- **核心问题**：现有主流方法将视频简单视为帧序列，直接采用为图像设计的**流匹配（Flow Matching, FM）** 训练目标。这一做法忽视了视频特有的**运动先验（motion priors）** 和**时间依赖关系（temporal dependencies）**，导致模型学习到的动态信息质量不佳，生成的视频在时间维度上不稳定、运动不符合物理规律。
- **论文的意义**：该工作指出当前视频生成训练范式在时序建模上的结构性缺陷，并提出专门面向视频特性的训练目标，为视频生成任务提供了一种新的训练思路，有望推动视频生成模型在运动真实感方面的整体提升。

## 2. 论文提出的方法论

- **核心思想**：提出**时间感知流匹配（Temporal-aware Flow Matching, TFM）**，一种将**帧间约束（inter-frame constraints）** 直接嵌入流匹配目标函数的训练范式，从而在保留流匹配原有优良性质的同时，显式建模视频帧间的时序关系。
- **方法要点**：
  - 在传统 FM 目标中引入**帧间约束项**，强制模型学习相邻帧（或跨帧）之间的运动一致性，使生成过程感知时间维度上的连续性。
  - 引入一种**残差型损失（residual-type loss）**，该损失形式与新的时间感知流结构天然对齐，能够更自然地捕捉帧间差异和运动变化。
  - 方法具有**通用性**：任何使用 FM 的视频生成模型都可以直接套用 TFM，无需修改模型架构。
  - **理论保证**：论文在理论上证明了使用 TFM 训练的模型具有显著增强的时间感知能力。
- **推理开销**：TFM **不增加任何推理阶段的计算成本**，其作用仅体现在训练过程中。

## 3. 实验设计

- **实验概述**：论文开展了大量实验，宣称在**多种运动类型（diverse motion types）** 上显著提升了运动真实感，并提供了生成的视频示例（项目主页： https://pzrain.github.io/tfm ）。
- **数据集与 benchmark**：受限于所提供材料（仅为摘要），论文中**未明确**列出具体使用的视频数据集名称、评测基准（如 UCF-101、MSR-VTT、EvalCrafter 等）以及对比方法的具体列表。
- **对比方法**：文中未说明，但作为 ICML 级别的工作，通常推测会与标准 FM 基线及其他主流视频生成模型进行对比。建议查阅论文正文获取完整实验设定。

## 4. 资源与算力

- **算力信息**：论文**未明确说明**所使用的 GPU 型号、数量、训练时长等具体硬件配置信息。
- **说明**：这一信息缺失在摘要中属正常现象，详细资源配置通常位于论文正文的实验设置部分，需查阅全文获取。

## 5. 实验数量与充分性

- **实验规模**：摘要称开展了"大量实验（Extensive experiments）"，覆盖多种运动类型，并包含生成视频的定性展示。
- **已知实验维度**：
  - 时间一致性（temporal consistency）评估；
  - 运动真实感（motion realism）评估；
  - 多类运动场景下的表现。
- **充分性评价**：仅从摘要来看，**无法判断**实验是否涵盖了足够的基线对比、消融研究（如不同帧间约束形式、残差损失的作用等）以及广泛的数据集验证。若论文正文包含跨数据集的多组实验和多组消融，则实验设计较为充分；但当前摘要信息有限，需结合全文评估其客观性与公平性。

## 6. 论文的主要结论与发现

- TFM 通过显式建模帧间关系，显著提升了视频生成中的**时间连贯性**和**运动真实感**。
- 理论分析与实验验证共同表明，TFM 增强了模型的时间感知能力。
- TFM 作为一种新的训练范式，**无需额外推理成本**，且可即插即用式地应用于任意基于 FM 的视频生成模型。

## 7. 优点

- **方法简洁且普适**：不改变模型结构，仅调整训练目标，适用范围广，实用价值高。
- **理论支撑扎实**：不仅有实验验证，还提供了理论证明，增强了方法的可信度。
- **零推理开销**：训练阶段引入约束，推理阶段无额外负担，部署友好。
- **问题定位精准**：直击视频生成中"时序不连贯"这一核心痛点，与图像生成范式做出明确区分。

## 8. 不足与局限

- **实验细节信息有限**：所提供材料（仅摘要）中未列出具体数据集、对比方法、评测指标和消融实验信息，难以从当前内容全面评估实验的充分性与公平性。
- **理论证明与实证的关联性**：理论证明阐明了"时间感知能力提升"，但该能力与最终视频质量（如美学、语义一致性）之间的因果关系尚需更深入的实验探讨。
- **评估维度可能有限**：运动真实感和时间一致性是重要维度，但视频生成还涉及文本对齐、图像质量、生成多样性等指标，摘要中未提及这些方面是否同样受益或不受影响。
- **应用边界未讨论**：TFM 对长视频生成、多物体复杂交互、高动态场景等极端情况的表现，以及在不同基础模型上的稳定性，均有待进一步验证。

（完）
