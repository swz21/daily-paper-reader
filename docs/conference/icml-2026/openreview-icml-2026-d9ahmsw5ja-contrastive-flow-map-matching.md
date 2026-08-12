---
title: Contrastive Flow Map Matching
title_zh: 对比流图匹配
authors: "Junyu Zhang, Daochang Liu, Younghyun Kim, Jong Hwan Ko, Shichao Zhang, Chang Xu, Eunbyung Park"
date: 2026-04-30
pdf: "https://openreview.net/pdf/632cc7656b10c54f9bd7f61c0822fc0c44ec0925.pdf"
tags: ["query:diff-video"]
score: 7.0
evidence: 对比流图匹配用于扩散式生成训练
tldr: 流图匹配（FMM）支持一步和少步采样，但训练转移与模型流图之间的不匹配限制了性能。本文提出对比流图匹配（CFMM），通过反向KL的联合分解指导，设计平均速度回归与采样对齐的InfoNCE对比损失。该训练插件显著提升少步扩散式生成的质量，且可作为预训练模型的即插即用模块。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 流图匹配的训练过程与实际采样不一致，影响少步生成性能。
method: 基于联合KL分解，用平均速度回归和InfoNCE对比损失对齐训练与采样。
result: 作为训练插件提升了少步采样的生成质量。
conclusion: CFMM为扩散式生成提供了一种高效的训练对齐框架。
---

## Abstract
Flow map matching (FMM) enables one- and few-step sampling for diffusion-style generation, yet its performance is often hindered by the mismatch between ground-truth training transitions and model-induced flow maps.
We propose **Contrastive Flow Map Matching (CFMM)**, a principled framework that explicitly aligns FMM training with practical sampling.
Our approach is motivated by a joint-KL decomposition on the reverse KL divergence, which decomposes the distributional gap into a marginal mismatch over intermediate states and a conditional mismatch in endpoint reconstruction.
This analysis motivates two complementary objectives: average-velocity regression for marginal alignment and a sampling-aligned InfoNCE contrastive loss for conditional refinement.
CFMM is a training-only plug-in for pre-trained FMMs, incurs no inference-time overhead, and supports training FMMs from scratch.
Experiments on CIFAR-10, ImageNet, and LSUN across multiple FMM baselines demonstrate consistent improvements in fidelity and perceptual quality with only modest additional training cost.

---

## 论文详细总结（自动生成）

# 论文总结：对比流图匹配（Contrastive Flow Map Matching, CFMM）

## 1. 核心问题与整体含义（研究动机与背景）

- **研究背景**：流图匹配（Flow Map Matching, FMM）是一种支持一步和少步采样的扩散式生成训练方法，旨在提高生成效率。
- **核心问题**：FMM 的训练过程与推理采样过程之间存在**不一致性**——训练时使用真实（ground-truth）转移路径，而采样时使用的是模型自身生成的流图（model-induced flow maps）。这种“训练-采样”不匹配严重限制了少步生成的质量。
- **整体含义**：本文旨在通过一种原则性的训练框架，显式地将 FMM 的训练目标与实际的采样过程对齐，从而在保持少步采样高效性的同时，提升生成质量。

## 2. 提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：基于**反向 KL 散度的联合分解**，将真实分布与模型分布之间的差异分解为两部分：
  - **边缘分布不匹配**：中间状态上的边缘分布差异；
  - **条件分布不匹配**：端点重构（条件生成）上的差异。
- **技术细节**：
  - 针对边缘对齐，提出**平均速度回归（average-velocity regression）** 目标；
  - 针对条件精炼，提出**采样对齐的 InfoNCE 对比损失（sampling-aligned InfoNCE contrastive loss）**。
- **形式化动机**：通过联合 KL 分解，分别指导两个互补目标的优化，实现训练与采样的一致性。
- **算法流程（文字说明）**：
  1. 预训练或随机初始化一个 FMM 模型；
  2. 计算平均速度回归损失，使模型中间状态的速度场对齐真实边缘分布；
  3. 计算采样对齐的 InfoNCE 对比损失，强化模型在端点重构时的条件匹配；
  4. 联合优化上述两个目标，更新模型参数；
  5. 推理阶段与标准 FMM 完全一致，无额外开销。
- **关键性质**：CFMM 是一个**仅训练阶段**的插件（training-only plug-in），可直接用于预训练 FMM 模型，也支持从头训练（from scratch）。

## 3. 实验设计

- **数据集**：CIFAR-10、ImageNet、LSUN。
- **基准**：多个 FMM 基线（文中未列出具体基线名称）。
- **对比方法**：文中仅提及“multiple FMM baselines”，未具体指明是哪些方法（如 Rectified Flow、Flow Matching 等），但可推测为常见的流匹配类方法。
- **任务场景**：少步扩散式生成（one-step / few-step sampling）的图像生成。

## 4. 资源与算力

- **文中未明确说明**任何关于 GPU 型号、数量、训练时长或计算资源的信息。
- 仅提到“modest additional training cost”（适度的额外训练成本），但未给出量化数值。

## 5. 实验数量与充分性

- **实验数量**：摘要中仅说明在三个数据集、多个 FMM 基线上进行了实验，并报告了“一致改进”。但**未提供具体实验数量、数值结果、消融实验或统计显著性分析**。
- **充分性评价**：
  - 由于本文提供的是论文摘要/元数据，而非完整正文，无法判断实验的详细设计。
  - 从摘要看，覆盖了三个常用图像生成数据集，覆盖面尚可，但缺少对具体指标的展示、与 SOTA 的对比、以及关键模块的消融研究。
  - 因此，**实验充分性无法充分评估**，需要完整论文佐证。

## 6. 主要结论与发现

- CFMM 能显著提升少步扩散式生成的质量，且适用于多种 FMM 基线。
- 对比流图匹配作为一种训练对齐框架，可有效缓解训练-采样不一致的问题。
- 该方法不增加推理成本，可作为预训练模型的即插即用模块，也能支持从零训练。

## 7. 优点

- **理论驱动**：从反向 KL 的联合分解出发，为训练目标提供了清晰的动机与可解释性。
- **即插即用**：仅训练阶段生效，无需改变推理流程，易于集成到现有 FMM 模型中。
- **低成本——高效率**：只需“适度的额外训练成本”，却能带来一致的生成质量提升。
- **适用范围广**：在多数据集、多 FMM 基线上均有效，且支持从头训练。

## 8. 不足与局限

- **信息不全**：由于可见内容有限，无法获知具体数值结果、与基线差异的显著性、以及消融实验细节。
- **实验覆盖偏差风险**：仅提到三个数据集，未涉及高分辨率、视频、文本或跨模态生成等场景，泛化性验证不足。
- **对比公平性**：未列出具体基线名称与实现细节，难以评估对比是否公平。
- **资源信息缺失**：未报告训练所需的算力，影响了可复现性和实际应用的成本评估。
- **潜在局限**：CFMM 的理论框架基于反向 KL 分解，可能对特定分布假设敏感，但文中未讨论其失败情况或适用范围边界。

（完）
