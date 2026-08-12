---
title: "SpeedVFI: One-step Diffusion for Efficient Video Frame Interpolation"
title_zh: SpeedVFI：高效视频帧插值的单步扩散方法
authors: "Ganggui Ding, Xiaogang Xu, Hao Chen, Chunhua Shen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/b3a60e2acb30ede9ae0f90d8997a046925385a9e.pdf"
tags: ["query:diff-video"]
score: 7.0
evidence: 面向视频帧插值的任务专属单步扩散方法
tldr: 视频扩散模型在帧插值中能应对大运动和遮挡，却因两两推理冗余和多步去噪而速度缓慢。SpeedVFI将生成式帧插值转化为统一的序列插补任务，并设计任务专属的单步扩散公式，在一次前向中插值整个序列，同时蒸馏生成轨迹为单步去噪。该方案在保留扩散模型稳健性的同时显著降低推理开销，为高效生成式视频插值提供了新思路。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 视频扩散模型在帧插值中对大运动和遮挡稳健，但多步迭代去噪与两两推理冗余导致效率低。
method: 将生成式VFI重构为统一序列插补，提出任务专属单步扩散公式，通过单次前向插值整段视频并蒸馏去噪轨迹。
result: 在保持鲁棒性的同时大幅提升视频帧插值速度。
conclusion: 说明单步扩散可有效弥合生成式VFI与学习式方法之间的效率差距。
---

## Abstract
Generative video diffusion models have shown strong robustness to large motion and occlusions for video frame interpolation (VFI). However, their inference efficiency lags significantly behind learning-based methods due to the structural redundancy of pairwise inference and the procedural latency of multi-step iterative denoising. To address these limitations, we propose SpeedVFI, a task-specific one-step diffusion formulation that recasts generative VFI as unified sequence interpolation. SpeedVFI achieves dual efficiency improvements by interpolating the entire video sequence in a single forward pass to eliminate pairwise overhead, and by distilling the generation trajectory into a one-step denoising process to bypass iterative latency. To make this formulation effective for VFI, we introduce temporal RoPE alignment for temporally consistent conditioning and noise-centric partial attention to reduce computational overhead while preserving global context. Extensive experiments demonstrate that SpeedVFI accelerates diffusion-based VFI by orders of magnitude while maintaining competitive quantitative and visual quality.

---

## 论文详细总结（自动生成）

## 论文总结：SpeedVFI——高效视频帧插值的单步扩散方法

> **说明**：本次提供的“论文PDF提取文本”实为 OpenReview 的浏览器验证页面，并非论文原文。以下总结严格基于元数据中提供的标题、作者、摘要、动机、方法、结果等字段信息撰写。对于元数据中未明确披露的内容（如实验数据集、算力等），将明确指出“未在给定信息中说明”。

---

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究背景**：视频帧插值（Video Frame Interpolation, VFI）旨在两帧之间生成中间帧，是视频处理领域的基础任务。近年来，生成式视频扩散模型因其强大的生成能力，在大运动、遮挡等复杂场景下表现出显著优于传统学习式方法的鲁棒性。
- **核心问题**：扩散模型用于 VFI 时存在两大结构性效率瓶颈——(1) **两两推理冗余**：现有方法通常对每一对相邻帧单独进行插值，导致整个视频序列的推理过程大量重复；(2) **多步迭代去噪延迟**：扩散模型的生成过程依赖多步迭代去噪，推理速度远慢于单次前向传播的学习式方法。
- **整体含义**：尽管扩散模型在 VFI 质量上具备优势，但其低效的推理过程使其难以实用化。论文旨在弥合生成式 VFI 与学习式方法之间的效率鸿沟，同时保留扩散模型的鲁棒性。
- **动机总结**：将生成式 VFI 从“慢而稳”转变为“快而稳”，使其在保持高质量的同时达到实用级别的推理速度。

---

### 2. 论文提出的方法论

- **核心思想**：将生成式 VFI 重新定义为**统一的序列插补（unified sequence interpolation）**任务，并为此设计**任务专属的单步扩散（one-step diffusion）公式**，从而在架构层面同时消除两两推理冗余和多步去噪延迟。

- **双维度效率提升机制**：
  1. **单次前向插值整个序列**：不再对每一对帧单独插值，而是通过一次前向传播完成整段视频序列的插补，从根本上消除 pairwise inference 的重复计算。
  2. **蒸馏去噪轨迹为单步**：将原本需要多步迭代的去噪过程蒸馏为一个单步去噪操作，从而绕过迭代延迟。

- **关键技术细节**：
  - **时间 RoPE 对齐（Temporal RoPE Alignment）**：用于保证时序条件的一致性，确保扩散模型在处理长序列时能维持时间维度的连贯性。
  - **噪声中心部分注意力（Noise-centric Partial Attention）**：通过仅对部分噪声区域进行注意力计算来降低计算开销，同时保持全局上下文信息的完整性，兼顾效率与质量。

- **算法流程概括**：输入视频帧序列 → 通过时间 RoPE 对齐进行条件编码 → 采用噪声中心部分注意力进行单步去噪 → 一次性输出整个插值后的视频序列。整个过程为一次前向传播，无迭代。

---

### 3. 实验设计

- **数据集 / Benchmark**：元数据中**未具体说明**使用了哪些数据集或 benchmark。但鉴于论文发表于 ICML 2026 且聚焦 VFI 任务，通常可预期会使用主流 VFI 基准（如 Vimeo90K、UCF101、DAVIS 等），以及面向大运动/遮挡的挑战性数据集。此点需以原文为准。
- **对比方法**：元数据未列出具体对比的基线方法。根据摘要推断，对比对象应涵盖：(1) 传统学习式 VFI 方法（如 RIFE、IFRNet 等）；(2) 现有基于扩散模型的 VFI 方法（如 DiffusionVFI 等）。
- **评估维度**：实验报告了**定量指标**（如 PSNR、SSIM 等）和**视觉质量**两方面的结果，同时对比了推理速度的提升幅度。

---

### 4. 资源与算力

- **未在给定信息中明确说明**。元数据中未提及 GPU 型号、数量、训练时长、参数量或推理延迟的具体数值等资源信息。
- 摘要中仅提到“加速了数个数量级（orders of magnitude）”，但未给出具体硬件环境和时间开销数据。

---

### 5. 实验数量与充分性

- **实验规模**：从摘要可知进行了“大量实验（Extensive experiments）”，但元数据未给出具体实验组数。
- **关于充分性的评估**：由于缺少详细信息，无法从现有材料判断实验是否充分。合理的推测是：
  - 若能覆盖多种分辨率的视频数据集、多种运动幅度场景（小运动/大运动/遮挡）、多种基线方法、以及消融实验（验证时间 RoPE 对齐和噪声中心部分注意力的各自贡献），则实验是充分的。
  - **客观性与公平性**：论文声明在保持竞争性定量和视觉质量的同时显著加速，这一结论的公平性取决于是否使用了相同的硬件环境、相同的评估协议，以及是否报告了合理的方差或置信区间——这些细节目前无法验证。

---

### 6. 论文的主要结论与发现

- **核心结论**：SpeedVFI 通过任务专属的单步扩散公式，将扩散模型在 VFI 中的推理速度提升了数个数量级，同时保持了与现有扩散方法相当的定量指标和视觉质量。
- **关键发现**：
  - 生成式 VFI 的“慢”并非不可避免，通过任务重构（序列插补）和模型设计（单步扩散 + 蒸馏），可以同时获得鲁棒性和高效率。
  - 时间 RoPE 对齐和噪声中心部分注意力这两个组件是使单步扩散在 VFI 中有效工作的关键。
  - 该研究证明了单步扩散可以有效地弥合生成式 VFI 与学习式方法之间的效率差距，为高效生成式视频插值提供了新方向。

---

### 7. 优点

- **问题定位精准**：直击扩散模型用于 VFI 的两大核心痛点（两两冗余与多步迭代），切中要害。
- **方法论设计巧妙**：将 VFI 重构为统一序列插补任务，从任务定义层面消除冗余；单步扩散配合轨迹蒸馏，是效率优化的根本性突破。
- **技术组件互补性强**：时间 RoPE 对齐解决时序一致性问题，噪声中心部分注意力在降低计算量的同时保留全局上下文，两者配合使单步扩散在 VFI 上可行。
- **效率提升显著**：实现“数个数量级”的加速，实用性价值高。
- **通用性潜力**：该框架不仅适用于 VFI，其“任务专属单步扩散”的范式可能推广到其他视频生成任务。

---

### 8. 不足与局限

- **实验细节缺失**：元数据中未披露具体数据集、基线方法、评估指标数值，难以独立评估方法的实际效果和可复现性。
- **算力信息缺失**：未提供训练/推理所需的 GPU 资源、参数量、运行时间等关键工程信息，影响对其实用性的完整评估。
- **单步扩散的潜在质量上限**：将多步去噪蒸馏为单步，理论上可能牺牲部分极端复杂场景（如大遮挡、剧烈运动）下的生成质量。摘要声明“保持竞争性质量”，但未给出与多步扩散方法的逐项对比细节。
- **部分注意力机制的局限**：噪声中心部分注意力虽然降低开销，但在需要全局信息高度交互的场景中可能存在信息损失。
- **无法验证的公平性**：缺少硬件环境、评估协议等细节，无法确认与基线方法对比的公平性。
- **应用限制**：该方法是任务专属设计（VFI），推广到其他任务（如视频修复、超分）可能需要重新设计，通用性边界未讨论。

---

（完）
