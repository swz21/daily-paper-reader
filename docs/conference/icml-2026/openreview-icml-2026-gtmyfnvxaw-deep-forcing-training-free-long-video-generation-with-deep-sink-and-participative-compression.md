---
title: "Deep Forcing: Training-Free Long Video Generation with Deep Sink and Participative Compression"
title_zh: Deep Forcing：利用深层汇点与参与式压缩实现免训练长视频生成
authors: "Jung Yi, Wooseok Jang, Paul Hyunbin Cho, Jisu Nam, Heeji Yoon, Seungryong Kim"
date: 2026-04-30
pdf: "https://openreview.net/pdf/811a1d7b719fa60e8929da2a596f7bb823d9963d.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 针对自回归视频扩散模型的免训练扩展，实现稳定长视频生成
tldr: 自回归视频扩散模型在长视频生成中会出现视觉保真度下降与运动退化。本文提出Deep Forcing，一种免训练的扩展方法：Deep Sink保留滑动上下文窗口中的持久汇点并重对齐时间RoPE相位以维持全局上下文，Participative Compression通过重要性感知的KV缓存剪枝提高效率。实验表明该方法能显著提升长视频生成的稳定性，为免训练长时视频生成提供了实用方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 长视频生成中自回归扩散模型存在视觉误差累积和运动退化。
method: 提出Deep Sink保持全局上下文与Participative Compression做重要性感知KV剪枝，实现免训练稳定化。
result: 有效降低长程生成中的误差累积，提升视频保真度与运动一致性。
conclusion: 为自回归视频扩散模型提供了一种无需训练的即插即用长视频稳定生成方案。
---

## Abstract
Recent advances in autoregressive video diffusion have enabled real-time frame streaming, however, existing methods still suffer from visual error accumulation including visual fidelity and motion degradation over long-horizon. To address these challenges, we introduce Deep Forcing, a training-free extension of autoregressive video diffusion models that stabilizes long video generation through two complementary mechanisms. Deep Sink preserves approximately half of the sliding context window as persistent sink tokens and realigns their temporal RoPE phases to the current timeline, thereby maintaining global context during extended rollouts. Participative Compression performs importance-aware KV cache pruning, retaining only tokens that actively participate in recent attention while removing redundant or degraded history, effectively mitigating error accumulation under out-of-distribution lengths. Together, these components enable over 12× length extrapolation (e.g., 5s-trained → 60s+) without sacrificing inference speed, while improving visual fidelity and motion dynamics compared to prior methods. Our results demonstrate that Deep Forcing can achieve performance comparable to state-of-the-art training-based methods trained specifically for long video generation.

---

## 论文详细总结（自动生成）

# Deep Forcing 论文详细总结

## 1. 核心问题与整体含义（研究动机和背景）

- **研究背景**：近年来，自回归视频扩散模型（autoregressive video diffusion models）已实现实时帧流式生成，是视频生成领域的重要进展。
- **核心问题**：这类模型在长时程（long-horizon）视频生成中，面临两类严重的退化问题：
  - **视觉保真度下降**：误差随自回归步数逐步累积，导致画面质量恶化；
  - **运动退化**：长序列中运动动态趋于停滞、失真或重复。
- **深层原因**：模型在超出训练分布的长度（out-of-distribution lengths）下进行推理时，缺乏有效的全局上下文维持机制，同时历史token中冗余、退化的信息不断累积，进一步加剧误差传播。
- **整体含义**：该论文旨在解决"如何在不重新训练模型的前提下，让已有的自回归视频扩散模型稳定生成远超训练时长的视频"这一具有实际部署价值的问题。

## 2. 论文提出的方法论

本论文提出 **Deep Forcing**，一种**免训练（training-free）的即插即用扩展方法**，通过两个互补机制协同工作：

### 2.1 Deep Sink：深层汇点机制

- **核心思想**：在滑动上下文窗口（sliding context window）中，保留大约一半的token作为**持久汇点（persistent sink tokens）**，而不是全部丢弃。
- **关键操作**：
  - 从滑动窗口中筛选出约50%的token作为长期保留的汇点；
  - 将这些汇点token的**时间RoPE相位（temporal RoPE phase）重新对齐**到当前时间线（current timeline），使旧token仍能在新时间坐标系中正确表达时序关系。
- **作用**：在不增加上下文窗口大小的情况下，维持长期全局上下文信息，避免早期信息被完全遗忘。

### 2.2 Participative Compression：参与式压缩机制

- **核心思想**：对KV缓存（KV cache）进行**重要性感知剪枝（importance-aware pruning）**，仅保留在近期注意力中**活跃参与**的token。
- **关键操作**：
  - 评估历史token在最近注意力计算中的参与程度/重要性；
  - 剔除冗余的、或已退化（degraded）的历史token；
  - 保留对当前生成仍有贡献的关键信息。
- **作用**：减轻历史错误信息的累积效应，防止模型在超长生成中被自身的退化输出污染。

### 2.3 算法流程（文字说明）

1. 使用预训练的自回归视频扩散模型进行逐帧/逐段自回归生成；
2. 在每个滑动窗口内，通过 Deep Sink 保留部分持久汇点，并重对齐其时间RoPE相位；
3. 通过 Participative Compression 对KV缓存进行重要性剪枝，压缩历史上下文；
4. 重复上述过程直至生成完整长视频，全程无需微调或额外训练。

## 3. 实验设计

由于所提供的文本仅为摘要和元数据，实验细节有限，但可获得以下信息：

- **长度外推能力**：以 **5秒训练的视频生成模型** 为基础，成功扩展到 **60秒以上** 的长视频生成，即超过 **12倍长度外推（12× length extrapolation）**。
- **对比方法**：
  - 与已有的免训练长视频生成方法进行对比（文中称"prior methods"）；
  - 与**基于训练的SOTA长视频生成方法**（training-based state-of-the-art）进行对比。
- **评测维度**：视觉保真度（visual fidelity）与运动动态（motion dynamics）。
- **具体数据集名称、benchmark榜单、FLOPs等细粒度实验信息在给定内容中未明确列出**（需查阅全文获取）。

## 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量、训练时长等算力信息。
- 值得注意的是，由于该方法属于**免训练方法**，**不需要额外的训练资源**；推理阶段的计算开销与剪枝策略有关，文中声称"不牺牲推理速度（without sacrificing inference speed）"。
- 具体的显存占用、推理耗时等量化指标在给定摘要中未报告。

## 5. 实验数量与充分性

- **从摘要可确认的实验组**：
  - 长度外推实验（5s → 60s+，12倍以上）；
  - 与免训练现有方法的对比；
  - 与基于训练SOTA方法的对比；
  - 视觉保真度与运动动态两个维度的评测。
- **消融实验**：摘要中未明确提及是否对 Deep Sink 和 Participative Compression 两个组件分别进行了消融分析，但从方法论的设计来看，该类工作通常会包含组件消融，具体需查阅全文确认。
- **总体评价**：摘要所示实验结果展示了明确的有效性，尤其是与训练方法的对比增强了说服力；但实验覆盖的**场景多样性、数据集广度、用户研究等尚未在摘要中体现**，需全文验证其充分性。

## 6. 主要结论与发现

- **Deep Forcing 实现了超过12倍的长度外推**（如5秒训练扩展到60秒以上），且不牺牲推理速度。
- **在视觉保真度和运动动态方面均优于先前的免训练方法**。
- **性能可与专门为长视频生成而训练的SOTA方法相媲美**，这意味着免训练方案有望替代部分需要昂贵训练成本的方案。
- 核心洞察是：**通过"保留关键全局上下文（Deep Sink）"+"剔除退化历史（Participative Compression）"的组合，可以有效抑制自回归长程生成中的误差累积**。

## 7. 优点

- **免训练、即插即用**：无需改造训练流程或重新训练模型，可直接应用于现有自回归视频扩散模型，部署成本极低。
- **效率优势**：在提升生成稳定性的同时不牺牲推理速度，KV缓存剪枝还可能在长序列中带来效率收益。
- **机制设计巧妙**：
  - Deep Sink通过RoPE相位重对齐，解决了滑动窗口中旧token时间语义错位的问题；
  - Participative Compression以重要性为导向，而非简单丢弃旧token，兼顾信息保留与噪声抑制。
- **性能上限高**：达到了与训练专用长视频模型相当的水平，直接验证了免训练路线的可行性。
- **问题针对性强**：直接针对自回归视频扩散模型在OOD长度下的两大痛点（保真度降低与运动退化）发力。

## 8. 不足与局限

- **实验信息透明度**：摘要中未提供数据集名称、具体评测协议、定量数值（如FVD/CLIP-T等指标）及与基线方法的精确差距，难以完全独立评估其效果。
- **算力与资源信息缺失**：未报告推理时间复杂度、GPU型号、显存占用等，对实际部署参考价值受限。
- **应用范围**：方法针对自回归视频扩散模型设计，**是否适用于其他视频生成范式（如DiT并行生成、GAN-based方法）未在摘要中说明**。
- **潜在偏差风险**：Deep Sink中"保留窗口的一半"及Participative Compression中重要性阈值等超参数，可能存在对特定模型/数据集的调优需求；其在不同分辨率、不同视频风格下的泛化能力有待更多验证。
- **极端长序列问题**：虽然达成12倍外推（60s），但对于更长的生成（如分钟级以上）是否仍能保持稳定，尚缺少证据。
- **没有用户研究或定性对比图**：摘要仅提到定量"视觉保真度和运动动态"改善，缺乏真实观感与用户评测层面的证据。

（完）
