---
title: Unlocking the Duality between Flow and Field Matching
title_zh: 解锁流匹配与场匹配之间的对偶性
authors: "Daniil Shlenskii, Alexander Varlamov, Nazar Buzun, Alexander Korotin"
date: 2026-01-19
pdf: "https://openreview.net/pdf/59422b2930966bc78c725b1ddc254c96f4b38d29.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 建立条件流匹配与前向交互场匹配之间的理论等价性
tldr: 条件流匹配统一了扩散和流匹配等生成范式，而交互场匹配源自物理学启发的增广空间。两者起点看似不同，本文却证明自然子类前向交互场匹配与条件流匹配等价，并构造了两者间的显式双射。这一结果揭示了这些生成框架在本体论上的一致性，为理解不同生成范式提供了统一视角。该理论发现对生成模型研究和框架选择具有重要指导意义。
source: ICML-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 条件流匹配和交互场匹配两种生成框架表面对象不同，其内在关系不明。
method: 聚焦前向交互场匹配子类，通过构造映射证明其与条件流匹配存在双射等价。
result: 从数学上建立了两个框架的等价关系。
conclusion: 加深了对流匹配与场匹配统一性的理解，为生成模型理论提供了新洞见。
---

## Abstract
Conditional Flow Matching (CFM) unifies conventional generative paradigms such as diffusion models and flow matching. Interaction Field Matching (IFM) is a newer framework that generalizes Electrostatic Field Matching (EFM) rooted in Poisson Flow Generative Models (PFGM). While both frameworks define generative dynamics, they start from different objects: CFM specifies a conditional probability path in data space, whereas IFM specifies a physics-inspired interaction field in an augmented data space.
This raises a basic question: **are CFM and IFM genuinely different, or are they two descriptions of the same underlying dynamics?** We show that they coincide for a natural subclass of IFM that we call forward-only IFM. Specifically, we construct a bijection between CFM and forward-only IFM. We further show that general IFM is strictly more expressive: it includes EFM and other interaction fields that cannot be realized within the standard CFM formulation. Finally, we highlight how this duality can benefit both frameworks: it provides a probabilistic interpretation of forward-only IFM and yield novel, IFM-driven techniques for CFM.

---

## 论文详细总结（自动生成）

# 中文总结

## 1. 核心问题与整体含义

- **研究动机**：条件流匹配（CFM）与交互场匹配（IFM）是两种新兴的生成模型框架。CFM 统一了扩散模型和流匹配等传统生成范式，而 IFM 则推广了基于静电场的匹配方法（EFM），根植于泊松流生成模型（PFGM）。两者都定义了生成动力学，但出发点不同：CFM 在数据空间中指定条件概率路径，IFM 在增广数据空间中指定物理启发的交互场。这引发了一个基本问题：**这两个框架本质上是否相同，还是对同一底层动力学的两种不同描述？**
- **整体含义**：论文通过证明二者在特定子类上的等价性，揭示了不同生成范式在数学本体上的一致性，为理解生成模型提供了统一视角，并推动两个框架之间的理论和技术互通。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：聚焦 IFM 的一个自然子类——**前向交互场匹配（forward-only IFM）**，证明其与标准 CFM 之间存在一一对应关系。
- **关键技术细节**：
  - 构造了 CFM 与前向 IFM 之间的**显式双射**，说明任意一个 CFM 条件概率路径都可对应到一个前向 IFM 交互场，反之亦然。
  - 证明**一般 IFM 严格强于 CFM**：一般 IFM 包含了 EFM 以及其他无法在标准 CFM 框架内实现的交互场，因此具有更强的表达能力。
  - 基于该对偶性，为前向 IFM 提供了**概率解释**，并提出了由 IFM 启发的 CFM 新技术。
- **算法流程**：论文未给出具体算法伪代码，主要通过数学构造和等价性证明实现理论建立。

## 3. 实验设计

- **数据集/场景**：从摘要中未提及任何具体数据集或应用场景。
- **Benchmark**：未说明使用的基准测试。
- **对比方法**：主要对比 CFM、前向 IFM、一般 IFM（含 EFM）之间的理论关系，而非实验性能对比。

## 4. 资源与算力

- 摘要及可见文本中**未提及任何算力信息**，包括 GPU 型号、数量、训练时长等。全文以理论分析为主，可能不涉及大规模训练实验。

## 5. 实验数量与充分性

- 从摘要来看，论文**没有报告任何数值实验**，属于纯理论贡献。
- 因此无法评估实验充分性、客观性或公平性；其结论主要依赖数学证明而非实证验证。

## 6. 主要结论与发现

- **核心发现**：CFM 与前向 IFM 是等价的，可通过显式双射互相转化。
- **表达能力差异**：一般 IFM 比标准 CFM 更具表达力，能够覆盖 EFM 及更多无法由 CFM 实现的交互场。
- **理论价值**：该对偶性为前向 IFM 提供了概率解释，并为 CFM 带来了新的 IFM 驱动技术，展示了两个框架之间的互利关系。

## 7. 优点

- **理论贡献清晰**：直接回答了两种框架之间“是否本质不同”的悬而未决的问题。
- **构造性证明**：通过显式双射而非仅存在性论证，增强了结论的可用性和说服力。
- **统一视角**：为生成模型领域提供了新的理论统合，加深了对流匹配与场匹配关系的理解。
- **互惠启示**：同时惠及 CFM 和 IFM 两个方向，具有前瞻性。

## 8. 不足与局限

- **缺乏实验验证**：未提供任何实证结果，对于实际训练效果、数值稳定性或性能影响未有说明。
- **仅限理论子类**：等价性局限于“前向 IFM”子类，一般 IFM 严格更强，因此实际应用中的选择仍需具体分析。
- **应用限制不明**：未讨论这些理论结果在真实生成任务（如图像、视频、语音等）中的可行性和收益。
- **可读性门槛**：依赖较强数学背景，对非专业读者可能不够友好。
- **结论适用范围**：标准 CFM 之外的变体是否同样适用于该双射，文中未提及。

（完）
