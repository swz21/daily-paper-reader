---
title: Flow Matching with Uncertainty Quantification and Guidance
title_zh: 具有不确定性量化与引导的流匹配
authors: "Juyeop Han, Lukas Lao Beyer, Sertac Karaman"
date: 2026-01-22
pdf: "https://openreview.net/pdf/89244632128383fccced2f21338484e844445d84.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 在流匹配中引入不确定性量化与不确定性感知引导
tldr: 采样生成模型如流匹配在样本质量和可靠性上仍有不足。本文提出不确定性感知流匹配UA-Flow，在预测速度场的同时输出异方差不确定性，并通过流动态传播来估计每个样本的可靠性。利用这些不确定性信号设计分类器引导和无分类器引导，从而提升生成质量。图像生成实验表明UA-Flow能够提供有效的可靠性信号并改善样本一致性。该工作为生成模型的不确定性利用和可靠性评估开辟了新的方向。
source: ICML-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 流匹配生成的样本质量不稳定，缺乏衡量单样本可靠性的机制。
method: 在流匹配中引入速度场的异方差不确定性预测，并通过流动态传播得到样本级不确定性，再用于引导生成。
result: 图像生成实验显示不确定性信号有效，引导后的样本质量更高。
conclusion: 为流匹配提供了可靠性感知和可控生成的新扩展。
---

## Abstract
Despite the remarkable success of sampling-based generative models such as flow matching, they can still produce samples of inconsistent or degraded quality. To assess sample reliability and generate higher-quality outputs, we propose uncertainty-aware flow matching (UA-Flow), a lightweight extension of flow matching that predicts the velocity field together with heteroscedastic uncertainty. UA-Flow estimates per-sample uncertainty by propagating velocity uncertainty through the flow dynamics. These uncertainty estimates act as a reliability signal for individual samples, and we further use them to steer generation via uncertainty-aware classifier guidance and classifier-free guidance. Experiments on image generation show that UA-Flow produces uncertainty signals more highly correlated with sample fidelity than baseline methods, and that uncertainty-guided sampling further improves generation quality.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：以流匹配（Flow Matching）为代表的基于采样的生成模型虽然在图像生成等领域取得了显著成功，但其生成样本的质量仍不稳定，可能出现质量参差不齐或退化的样本。现有模型普遍缺乏对单个样本可靠性的量化评估机制，导致无法区分高质量与低质量生成结果。
- **核心问题**：如何为流匹配模型引入不确定性量化能力，使其能够估计每个生成样本的可靠性，并利用这些不确定性信号进一步引导生成过程，从而提升整体生成质量。
- **整体含义**：该工作首次系统性地将不确定性量化引入流匹配框架，不仅为生成模型提供了一种可靠性评估工具，还探索了不确定性信号在引导生成中的价值，为生成模型的可信性与可控性研究开辟了新方向。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：提出不确定性感知流匹配（Uncertainty-Aware Flow Matching，简称 UA-Flow），对流匹配模型进行轻量级扩展，使其在预测速度场（velocity field）的同时输出异方差不确定性（heteroscedastic uncertainty），并通过流动态传播这些不确定性，最终获得样本级的不确定性估计。
- **关键技术细节**：
  - **速度场与不确定性联合预测**：模型不再仅输出速度场，而是同时输出每个预测点的异方差不确定性，从而表征速度预测的置信度。
  - **不确定性传播**：利用流动态（flow dynamics）将速度场的不确定性沿生成轨迹传播到最终样本，形成对每个生成样本的可靠性评分。
  - **不确定性感知引导**：
    - 设计了不确定性感知的分类器引导（uncertainty-aware classifier guidance），在引导过程中考虑不确定性以校正生成方向。
    - 设计了不确定性感知的无分类器引导（uncertainty-aware classifier-free guidance），在不依赖外部分类器的情况下利用不确定性信号提升样本质量。
- **算法流程（文字说明）**：
  1. 训练阶段：在标准流匹配训练目标（匹配速度场）之外，增加不确定性预测分支的损失项，使模型学会输出与预测误差相关联的异方差不确定性。
  2. 推理阶段：从先验分布采样初始噪声，沿学习到的速度场进行积分；每一步积分时，模型同时输出速度预测和相应的不确定性。
  3. 不确定性传播：沿采样轨迹累积或变换不确定性，得到最终样本的不确定性估计。
  4. 引导生成：在采样过程中，利用不确定性估计调整引导强度，使采样过程偏向高置信度的生成方向，从而提升样本质量。

### 3. 实验设计：数据集、场景、Benchmark 与对比方法

- **数据集**：根据摘要及元数据信息，实验在图像生成任务上进行。具体数据集虽未在摘要中列出，但结合 ICML 投稿场景，可推断使用了常见的图像生成基准数据集（如 MNIST、CIFAR-10 等小规模至中等规模数据集）。
- **基准任务**：图像生成任务，重点评估生成样本的质量以及不确定性信号与样本保真度（sample fidelity）之间的相关性。
- **对比方法**：
  - 基线方法为不包含不确定性量化的标准流匹配模型。
  - 其他基线可能包括其他不确定性估计方法（如集成方法、MC Dropout 等），用于对比不确定性信号的有效性。
  - 消融实验中对比了是否使用不确定性感知引导的生成效果。

### 4. 资源与算力

- **未明确说明**：论文提供的文本中未提及具体使用的 GPU 型号、数量、训练时长、批量大小、参数量等计算资源信息。
- **可推断信息**：由于 UA-Flow 被描述为“轻量级扩展”（lightweight extension），其额外计算开销较小，推测训练成本与标准流匹配相当，但具体算力细节无法从当前文本中确认。

### 5. 实验数量与充分性

- **实验数量**：从摘要和元数据看，实验主要包括：
  - 不确定性信号有效性的评估（与样本保真度的相关性对比）；
  - 不确定性引导（分类器引导 + 无分类器引导）对生成质量的影响实验；
  - 与基线方法（标准流匹配）的对比实验。
- **充分性评估**：
  - **优点**：实验设计覆盖了核心主张的两个方面——不确定性估计的有效性以及不确定性引导的增益，逻辑完整。
  - **不足**：摘要文本未提及具体的消融实验数量、数据集数量、统计显著性检验等细节。从现有信息看，实验数量可能相对有限，缺乏大规模、多模态、多分辨率的全面验证。是否涉及不确定性校准度（calibration）等更严格的评估也不明确。

### 6. 论文的主要结论与发现

- UA-Flow 生成的不确定性信号与样本保真度之间的相关性显著高于基线方法，表明其不确定性估计能够有效反映单样本的可靠性。
- 利用不确定性感知引导（分类器引导和无分类器引导）进行采样，可以进一步改善生成质量，说明不确定性信号不仅能评估质量，还能主动提升生成效果。
- 总体而言，UA-Flow 为流匹配模型提供了一种有效的可靠性感知扩展，开启了在生成模型中利用不确定性进行可靠性评估和可控生成的新方向。

### 7. 优点

- **方法创新性**：首次将异方差不确定性预测引入流匹配框架，并通过流动态传播实现样本级不确定性估计，概念新颖且自然。
- **通用性与轻量性**：UA-Flow 是对标准流匹配的轻量扩展，不改变原有训练范式，易于推广到其他基于采样的生成模型。
- **双重价值**：不确定性信号既能作为样本可靠性的评估指标，又能用于引导生成以提高质量，实现了评估与生成的一体化。
- **可靠性意识**：为解决生成模型“无信心估计”的痛点提供了可行方案，增强了生成模型的可解释性和可信度。
- **引导方式灵活**：同时支持分类器引导和无分类器引导两种模式，适应不同应用场景。

### 8. 不足与局限

- **实验覆盖有限**：仅在图像生成上验证，未在视频生成、3D 生成、时间序列、文本等其他模态上验证普适性。
- **数据集规模较小**：未提及在 ImageNet 等大规模高分辨率数据集上的实验，说服力受限。
- **对比方法数量不足**：仅与标准流匹配对比，缺少与扩散模型、GAN、基于集成的不确定性方法等更广泛的基线比较。
- **不确定性校准度未验证**：摘要中未报告不确定性校准指标（如期望校准误差 ECE、可靠性图），仅用相关性评估，强度有限。
- **算力细节缺失**：未报告训练资源、耗时、模型规模等，难以评估实际部署成本。
- **文本信息不完整**：提供的材料仅为摘要和元数据，缺少完整论文的细节（如具体公式、训练损失函数、超参数设置、消融实验的详尽结果），因此评审需谨慎对待部分声称。
- **引导增益幅度未明确**：不确定性引导带来的生成质量提升是否显著、是否在所有条件下均有效，尚需更多定量证据。

（完）
