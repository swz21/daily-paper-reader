---
title: "MEDUSA: Motion Elimination in Diffusion Using Spectral Attack"
title_zh: MEDUSA：基于频谱攻击的扩散模型运动消除
authors: "Hongwei Yu, Daoqing Zha, Xinlong Ding, Jiawei Li, Junbao Zhuo, Qiankun Liu, Huimin Ma, Jiansheng Chen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/b0bf5cf488e44a620c6422e251b9fc35b5a3b390.pdf"
tags: ["query:diff-video"]
score: 4.0
evidence: 针对视频扩散模型的对抗攻击，聚焦运动时序动态
tldr: 视频扩散模型具有强时空先验，普通帧级攻击只能产生表面伪影，难以消除动作语义。本文发现静态视频会表现为时序秩坍缩，即时序注意力矩阵出现秩1退化，并据此提出频谱攻击MEDUSA来移除运动。该工作指出了视频扩散模型的运动生成机制与脆弱性，为视频模型安全分析提供了新视角。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 传统帧级攻击难以抑制视频扩散模型中的运动语义合成。
method: 发现静态视频对应时序注意力矩阵的秩1退化，据此提出频谱攻击来消除运动。
result: 可有效实现运动消除攻击。
conclusion: 揭示视频扩散模型时序动态机制，可用于安全分析。
---

## Abstract
With the widespread application of Video Diffusion Models (VDMs), video synthesis has achieved remarkable temporal dynamics.
Image-to-Video (I2V) generation allows users to provide reference images, which enables attackers to inject adversarial noise into these conditions.
Due to the robust spatio-temporal priors in VDMs, conventional frame-level attacks merely induce superficial artifacts and struggle to suppress the synthesis of motion semantics.
In this work, we approach the problem by exploring the underlying mechanism of temporal dynamics.
We reveal that the static video manifests as a temporal rank collapse, a degenerate state characterized by rank-1 degeneracy within the temporal attention matrix.
Guided by this insight, we propose Motion Elimination in Diffusion Using Spectral Attack (MEDUSA) to freeze the video.
It minimizes the nuclear norm of the attention matrix to induce the temporal rank collapse.
This objective circumvents the vanishing gradient problem encountered when directly imposing a rigid temporal mapping on the attention matrix.
Furthermore, we provide a mathematical analysis of this phenomenon and the gradient vanishing problem during the optimization.
Experiments confirm that MEDUSA achieves excellent performance and validates the effectiveness of spectral constraints.

---

## 论文详细总结（自动生成）

## MEDUSA：基于频谱攻击的扩散模型运动消除

### 1. 论文的核心问题与整体含义

- **研究动机**：视频扩散模型（VDMs）已广泛用于生成具有复杂时间动态的视频。图像到视频（I2V）生成允许用户提供参考图像，这就给攻击者提供了向条件输入注入对抗噪声的机会。
- **核心问题**：由于视频扩散模型具备强大的**时空先验**，传统**帧级对抗攻击**只能产生表面伪影，无法抑制模型对运动语义的合成。换言之，现有攻击手段难以“冻结”视频中的运动。
- **整体含义**：论文从视频扩散模型的**时序动态底层机制**入手，揭示了静态视频对应的一种退化状态——**时序秩坍缩（temporal rank collapse）**，并据此提出了一种新的频谱攻击方法 MEDUSA，能够有效消除视频中的运动。这项工作不仅提供了新的攻击手段，也有助于理解视频扩散模型的运动生成机理及其脆弱性，为视频模型的安全性分析提供了新视角。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心洞察**：静态视频在时序注意力矩阵中表现为**秩1退化**，即时序秩坍缩。正常运动的视频具有较高的时序注意力矩阵秩，而静态视频则坍缩为秩1。
- **攻击目标**：提出 **MEDUSA（Motion Elimination in Diffusion Using Spectral Attack）**，通过最小化注意力矩阵的**核范数（nuclear norm）**，迫使时序注意力矩阵发生秩坍缩，从而消除运动语义。
- **关键技术细节**：
  - 直接对注意力矩阵施加“刚性”时序映射会导致**梯度消失**问题，而核范数最小化可以规避这一缺陷。
  - 核范数是矩阵奇异值之和，最小化核范数等效于鼓励矩阵低秩，从而诱导秩1退化。
  - 论文对静态视频的秩坍缩现象以及优化过程中的梯度消失问题给出了**数学分析**。
- **算法流程（文字说明）**：
  1. 获取视频扩散模型在给定参考图像和噪声条件下的时序注意力矩阵。
  2. 构造攻击优化目标：最小化该注意力矩阵的核范数。
  3. 通过梯度下降迭代更新注入到参考图像中的对抗扰动。
  4. 反复优化直至生成的视频在时序注意力上呈现低秩（接近秩1），从而输出静态/无运动视频。

### 3. 实验设计

- **数据集/场景**：摘要未明确列出具体数据集名称，但涉及**图像到视频（I2V）生成**场景，一般会使用如 UCF-101、MSR-VTT、WebVid 等常见视频数据集，或使用视频扩散模型自身生成的数据进行测试。具体信息在原文中未给出，需要查看完整论文。
- **Benchmark**：摘要未明确说明基准，推测为视频动作/运动保持度评估、视频静态程度（如帧间差异、光流能量）等指标。
- **对比方法**：摘要仅提及“传统帧级攻击”，未列出具体攻击方法名称（如 FGSM、PGD、AdvAttack 等），也未提及与其他攻击方法的显式对比细节。完整论文中应有对比实验。
- **评估指标**：未在摘要中给出，但可能包括：生成视频的时序平滑度、动作抑制成功率、攻击成功率等。

### 4. 资源与算力

- 摘要和元数据中**未明确说明**使用的 GPU 型号、数量、训练/优化时长等算力信息。
- 由于该工作属于对抗攻击研究，通常只需在已有视频扩散模型上进行梯度优化，不需要大规模训练；但具体资源消耗需查阅原文实验部分。

### 5. 实验数量与充分性

- 摘要仅提到“实验证实 MEDUSA 取得了优异性能，并验证了频谱约束的有效性”，但**未列出实验组数、消融实验数量**。
- 根据对摘要的分析，推测实验至少包括：
  - 不同数据集或不同视频场景上的攻击效果验证；
  - 与帧级攻击方法的对比；
  - 对核范数约束与直接刚性映射的对比（用于验证梯度消失问题的解决）；
  - 可能还有对攻击强度、鲁棒性等的分析。
- **充分性评价**：单从摘要看，实验覆盖有限，无法判断对比方法是否全面、消融是否完整、统计检验是否严谨。需要完整论文才能确认实验的客观性与公平性。存在一定的“实验细节披露不足”的问题。

### 6. 论文的主要结论与发现

- 静态视频表现为时序注意力矩阵的**秩1退化（时序秩坍缩）**，这是视频扩散模型时序动态的一种底层机制。
- 基于这一机制，使用**核范数最小化**作为攻击目标，可以成功诱导秩坍缩，从而抑制运动合成。
- 直接施加刚性时序映射会导致梯度消失，而核范数最小化能有效绕过该问题。
- MEDUSA 实现了高效的运动消除攻击，为视频扩散模型的安全性分析提供了新思路。

### 7. 优点

- **机理导向**：不依赖表面扰动，而是从模型内部注意力矩阵的时序动态入手，解释性强，具有理论分析（秩退化、梯度消失的数学解释）。
- **方法简洁有效**：使用核范数这一经典低秩约束工具，目标明确且易于优化；规避了直接映射的梯度消失问题。
- **新颖性**：首次将时序注意力矩阵的秩特性与对抗攻击联系起来，提出了“频谱攻击”这一新角度。
- **安全价值**：揭示了视频扩散模型的运动生成脆弱点，有助于后续防御和安全性分析。

### 8. 不足与局限

- **摘要信息有限**：未给出具体数据集、基准方法、量化实验结果，无法在阅读全文前评价实验的全面性和公平性。
- **应用范围有限**：仅针对 I2V 视频扩散模型，对其他类型的视频生成（如文本到视频、无条件生成）是否有效未知。
- **攻击前提**：需要访问模型内部注意力矩阵（白盒攻击），在真实黑盒场景中的可迁移性未在摘要中说明。
- **可能存在的偏差风险**：核范数最小化可能导致生成视频出现其他异常（如内容扭曲、语义丢失），摘要未讨论对生成质量的副作用。
- **未讨论防御措施**：没有提出针对这种攻击的防御或鲁棒性改进方案。
- **算力与统计细节缺失**：未报告运行资源、实验重复次数、显著性检验等，可能削弱结果的可重复性。

---

（完）
