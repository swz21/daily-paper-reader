---
title: Principled RL for Flow Matching Emerges from the Chunk-level Policy Optimization
title_zh: 块级策略优化中涌现的流匹配原则性强化学习
authors: "Yifu Luo, Haoyuan Sun, Xinhao Hu, Penghui Du, Keyu Fan, Bo Li, SiNan Du, Xu Wan, Zhiyu Chen, Bo Xia, Yongzhe Chang, Kai Wu, Kun Gai, Tiantian Zhang, Xueqian Wang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/67e0c3bd4b5943cfc2a0ba700aa097167dd8ab68.pdf"
tags: ["query:diff-video"]
score: 8.0
evidence: 块级策略优化用于流匹配后训练
tldr: 流匹配文生图模型用GRPO后训练时，逐时间步的优势归因不准确。本文提出组块级策略优化GCPO，把相邻时间步聚合成块，并把策略优化从步级转移至块级，从而缓解归因噪声。实验表明GCPO在标准文生图基准上超过现有方法，为流匹配模型的后训练提供了更稳健的RL范式。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 基于GRPO的流匹配后训练存在优势归因不准确的局限。
method: 提出组块级策略优化(GCPO)，将连续时间步聚合为块并将优化从步级转向块级。
result: 在标准文生图基准上取得更优性能。
conclusion: 说明块级RL是流匹配后训练更原则性的范式。
---

## Abstract
Recent Progress in post-training flow matching for text-to-image (T2I) generation with Group Relative Policy Optimization (GRPO) has demonstrated strong potential. However, it is hindered by a critical limitation: inaccurate advantage attribution. In this work, we argue that aggregating consecutive timesteps into a coherent 'chunk' and shifting the policy optimization paradigm from GRPO's step level to the chunk level can effectively mitigate the negative impact of this issue. Building on this insight, we propose Group Chunking Policy Optimization (GCPO), the first chunk-level reinforcement learning approach for post-training flow matching. Extensive experiments demonstrate that GCPO achieves superior performance on both standard T2I benchmarks and preference alignment, with up to $43$% additional gains over GRPO, highlighting the promise of chunk-level policy optimization.

---

## 论文详细总结（自动生成）

# 论文总结：块级策略优化中涌现的流匹配原则性强化学习

## 1. 核心问题与整体含义
- **背景**：流匹配（Flow Matching）模型在文本到图像（T2I）生成中的后训练正在探索使用 GRPO（Group Relative Policy Optimization），并展现出较强潜力。
- **关键问题**：GRPO 在流匹配后训练中存在关键局限——**优势归因不准确**（inaccurate advantage attribution），即逐时间步的奖励/优势估计无法准确反映真正的生成质量贡献。
- **核心论点**：将连续的相邻时间步聚合成一个“块”（chunk），并把策略优化从 GRPO 的**步级（step-level）** 转移到**块级（chunk-level）**，可以有效缓解该问题带来的负面影响。
- **整体含义**：本文提出第一个块级策略优化方法 GCPO，为流匹配模型的强化学习后训练提供了一套更稳健、更原则性的范式。

## 2. 方法论
- **核心思想**：将流匹配的时间轴划分为多个由连续时间步组成的“块”，在块级别进行优势估计与策略更新，而不是像 GRPO 那样逐时间步独立处理。
- **关键技术细节**：
  - 用块级聚合替代逐时间步归因，降低噪声对策略梯度的影响；
  - 在块内计算优势函数（具体公式未在提供内容中给出）；
  - 将策略优化的目标从单步密度的提升转为块整体的生成质量改进。
- **算法流程（文字描述）**：
  1. 将生成过程的离散时间步按相邻关系划分为多个块（chunk）；
  2. 对每个块计算该块的奖励或优势汇总；
  3. 使用块级优势信号更新策略网络；
  4. 反复迭代直至收敛。
- **注意**：论文摘要中未给出块大小选择、具体损失函数、超参数设置等技术细节。

## 3. 实验设计
- **数据集/场景**：使用了“标准 T2I 基准”（standard T2I benchmarks）以及“偏好对齐”（preference alignment）任务；但具体数据集名称（如 COCO、DrawBench 等）未在提供内容中列出。
- **Benchmark**：标准文本到图像生成评估基准。
- **对比方法**：主要与 GRPO 及“现有方法”对比。
- **关键结果**：GCPO 在标准基准和偏好对齐上均优于对比方法；相对 GRPO 最多可获得 **43%** 的额外提升。

## 4. 资源与算力
- 提供内容中**未提及**任何算力信息（如 GPU 型号、数量、训练时长、显存消耗等）。
- 因此无法据此评估该方法的计算成本或实际训练开销。

## 5. 实验数量与充分性
- 摘要称进行了“广泛实验”（Extensive experiments），但未给出具体实验组数、数据集数量或消融实验细节。
- 已展示的实验覆盖了标准基准和偏好对齐，并给出了与 GRPO 的量化对比，但：
  - 未说明是否有消融研究（如块大小的影响）；
  - 未报告多次重复实验的方差或统计显著性检验；
  - 未展示与其他 RL 后训练方法（如 DPO 等）的对比。
- 因此，仅凭现有信息，实验的客观性和公平性无法完全判断，但主要结论有明显证据支撑。

## 6. 主要结论与发现
- GCPO 在标准文本到图像生成基准和偏好对齐任务上取得了**更优性能**。
- 相比 GRPO，最高可带来 **43% 的额外性能提升**。
- 结论：块级策略优化是流匹配后训练中一种**更原则性的强化学习范式**，值得作为未来 RL 后训练设计的默认方向。

## 7. 优点
- **方法创新**：首次将 RL 后训练从步级提升到块级，直接针对 GRPO 优势归因不准的痛点。
- **概念清晰**：将时间步聚合为块的思路简单且可推广，理论上能有效抑制采样噪声。
- **效果显著**：在相对成熟的 GRPO 基础上仍能获得最高 43% 的增益，说明改进方向有实际价值。

## 8. 不足与局限
- **技术细节缺失**：摘要未提供块级优势估计的具体公式、块划分策略、超参数选择等关键内容。
- **实验信息不完整**：未列出具体数据集、消融实验、重复实验次数及统计显著性检验。
- **算力未说明**：无法判断该方法的实际训练成本和可复现性。
- **对比范围有限**：主要与 GRPO 对比，缺少与其它后训练范式（如直接偏好优化、其他 RL 算法）的系统比较。
- **偏差风险**：仅凭摘要数据，难以排除结果受评测集选择、随机种子或实现细节影响的可能。

（完）
