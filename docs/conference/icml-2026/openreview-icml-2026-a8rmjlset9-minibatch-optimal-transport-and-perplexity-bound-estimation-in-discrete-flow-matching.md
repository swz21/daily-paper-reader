---
title: Minibatch Optimal Transport and Perplexity Bound Estimation in Discrete Flow Matching
title_zh: 离散流匹配中的小批量最优传输与困惑度界估计
authors: "Etrit Haxholli, Yeti Z. Gurbuz, Oğul Can, Eli Waxman"
date: 2026-04-30
pdf: "https://openreview.net/pdf/2894f62a416bc956ecf7f75bd865edda54717e59.pdf"
tags: ["query:diff-video"]
score: 7.0
evidence: 离散流匹配结合最优传输以减少状态转移
tldr: 离散流匹配在建模类别数据上表现优异，但无法直接应用整流策略来减少状态转移。该文提出动态最优传输式最小化目标，推导凸插值下离散流的 Kantorovich 形式，并利用小批量策略优化。实验表明可将状态转移次数从 1024 降至 32 而不损失生成困惑度，显著提升离散流匹配的效率。为类别数据生成提供更高效的流匹配方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 离散流匹配因路径随机性无法使用整流，状态转移次数多。
method: 提出动态最优传输目标并推导 Kantorovich 小批量优化。
result: 状态转移减少高达32倍，生成困惑度不受影响。
conclusion: 显著提升离散流匹配的效率，适用于类别数据生成。
---

## Abstract
Discrete flow matching, a recent framework for modeling categorical data, has shown competitive performance with autoregressive models. However, unlike continuous flow matching, the rectification strategy cannot be applied due to the stochasticity of discrete paths, necessitating alternative methods to minimize state transitions. We propose a dynamic-optimal-transport-like minimization objective and derive its Kantorovich formulation for discrete flows with convex interpolants, where transport cost depends solely on inter-state dissimilarity and can be optimized via minibatch strategies. We show that such methods can reduce the number of transitions up to 32 times (1024 to 32) to reach the same generative perplexity without compromising diversity. Additionally, path nondeterminism in discrete flows precludes an instantaneous change-of-variables analogue, preventing precise probability estimation available to continuous flows. We therefore propose two upper bounds on perplexity, enabling principled training, evaluation and model comparison. Finally, we introduce Multimask Flows which outperform masked flows in generative perplexity without compromising diversity, particularly when utilizing minibatch Optimal Transport.

---

## 论文详细总结（自动生成）

# 离散流匹配中的小批量最优传输与困惑度界估计

## 1. 核心问题与研究动机

- **研究背景**：离散流匹配（Discrete Flow Matching）是一种近期提出的用于建模类别数据（categorical data）的生成框架，在性能上可与自回归模型相竞争。
- **核心问题**：与连续流匹配不同，离散流匹配无法直接应用整流（rectification）策略来减少状态转移次数。原因在于离散路径具有**随机性（stochasticity）**，导致状态转移次数过多，生成效率低。
- **研究意义**：本文旨在为离散流匹配提出替代性的高效优化方法，在减少状态转移的同时保持生成质量，从而提升离散类别数据生成的整体效率。

## 2. 方法论

- **核心思想**：提出一种类似动态最优传输（Dynamic Optimal Transport）的最小化目标，用于优化离散流匹配中的状态转移成本。
- **关键推导**：
  - 为带有**凸插值（convex interpolants）**的离散流推导出对应的 **Kantorovich 形式**。
  - 在该形式下，传输成本仅依赖于状态间的**不相似度（inter-state dissimilarity）**，从而简化了优化问题。
- **优化策略**：采用**小批量策略（minibatch strategies）**对最优传输目标进行优化，使方法在计算上可行。
- **困惑度上界**：
  - 由于离散路径的非确定性（path nondeterminism），无法像连续流那样使用瞬时变量变换（instantaneous change-of-variables）来精确估计概率。
  - 因此，作者提出两种**困惑度上界（upper bounds on perplexity）**，用于支持有原则的训练、评估与模型比较。
- **新模型**：提出 **Multimask Flows**，在生成困惑度上优于掩码流（masked flows），且不牺牲多样性；特别是结合小批量最优传输时效果更佳。

## 3. 实验设计

- **效果验证**：实验表明，所提方法可以达到与 baseline 相同的生成困惑度，同时将状态转移次数从 **1024 次降至 32 次**，约为 **32 倍减少**。
- **模型对比**：将 Multimask Flows 与掩码流（masked flows）进行对比，关注生成困惑度与多样性两个指标。
- **数据集与基准**：
  - 在提供的论文内容中，**未明确列出具体使用哪些数据集**以及 benchmark 的详细信息。
  - 仅能从摘要推断实验涉及类别数据生成任务，但具体领域（如文本、图像离散表示等）尚不明确。

## 4. 资源与算力

- 在提供的文本内容中，**未涉及任何关于 GPU 型号、数量、训练时长、显存占用等算力资源的信息**。
- 因此无法评估该方法的计算成本与可复现性相关的资源需求。

## 5. 实验数量与充分性

- **实验组数**：从摘要中可以看出至少包含以下实验类型：
  - 状态转移次数对生成困惑度的影响实验（1024 → 32）；
  - Multimask Flows 与 masked flows 的生成性能对比；
  - 是否使用小批量最优传输的对比（意味着包含一定的消融分析）。
- **充分性评估**：
  - 就摘要提供的信息而言，实验结论明确地支持“减少状态转移不损失困惑度”这一核心主张；
  - 但由于缺少**数据集数量、任务多样性、baseline 范围、统计显著性与重复次数**等信息，无法判断实验覆盖是否足够全面；
  - 若模型只在一类任务上验证，则其泛化能力仍存在不确定性。

## 6. 主要结论与发现

- 离散流匹配结合动态最优传输式目标与小批量优化，可以**显著减少状态转移次数（最高 32 倍）**，同时保持生成困惑度不下降、不牺牲多样性。
- 提出了离散流下的 Kantorovich 形式，为传输成本的优化提供了理论依据。
- 提出了两个困惑度上界，弥补了离散流无法精确估计概率的不足，为训练与评估提供了可行方案。
- 提出 Multimask Flows，相比掩码流在生成困惑度上更优，且与最优传输策略兼容良好。

## 7. 优点

- **问题定位明确**：针对离散流匹配中“不能用整流、转移过多”这一核心痛点展开研究。
- **理论贡献清晰**：将动态最优传输思想扩展到离散流场景，并给出 Kantorovich 形式下的可优化目标，具有较强的理论价值。
- **方法实用性强**：小批量优化策略使方法在真实规模下可用，不是仅限于理论。
- **评价角度完整**：同时考虑了质量（perplexity）、多样性（diversity）与效率（transition count），避免单一指标带来的偏差。
- **弥补空白**：提出的困惑度上界为离散流模型提供了此前缺失的评估工具。

## 8. 不足与局限

- **信息不完整**：当前提供的论文内容仅有摘要，无法获得完整的实验设置、数据划分、超参数选择等细节。
- **数据集覆盖未知**：实验在哪些具体数据集上进行不清楚，无法判断是否只适用于特定类型的类别数据。
- **对比范围有限**：摘要中仅提及与 masked flows 比较，未提及与自回归模型、其他离散扩散模型或连续流匹配方法的详细对比。
- **计算资源信息缺失**：没有报告训练成本，难以评估该方法相对于 baseline 的实际效率优势。
- **通用性待验证**：小批量最优传输的性能受 batch size 影响，且最优传输在小批量上仅近似求解，其在大规模生成任务中的稳定性尚未在文中呈现。

（完）
