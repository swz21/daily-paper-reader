---
title: "Mean Flow Distillation: Robust and Stable Distillation for Flow Matching Models"
title_zh: 均值流蒸馏：面向流匹配模型的鲁棒稳定蒸馏方法
authors: "An Zhao, Shengyuan Zhang, Zhongjian Sun, Yixiang Zhou, Zejian Li, Ling Yang, Tianrun Chen, Lingyun Sun"
date: 2026-04-30
pdf: "https://openreview.net/pdf/8a2564e88694d94f48baf55728284cc4a5c72cb3.pdf"
tags: ["query:diff-video"]
score: 8.0
evidence: 专门面向流匹配模型提出的新蒸馏框架
tldr: 流匹配模型虽然生成能力强，但依赖ODE迭代采样，计算开销大。现有蒸馏方法多借用扩散模型思路，未利用流的内在几何结构，导致训练不稳定与生成质量下降。本文提出均值流蒸馏（MFD），从理论上证明其相当于时间低通滤波器，可有效抑制高频噪声，显著提升蒸馏的鲁棒性与稳定性，并保持较好的生成质量，为流匹配模型的高效采样提供了专用方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 流匹配模型采样开销大，现有蒸馏方法不稳定且质量退化。
method: 提出均值流蒸馏（MFD），利用流结构的时序低通滤波特性进行鲁棒蒸馏。
result: 理论分析与实验表明MFD抑制高频噪声，提升训练稳定性和生成质量。
conclusion: 为流匹配模型提供了高效且稳定的蒸馏框架，可推广至视频等生成任务。
---

## Abstract
Flow Matching models have demonstrated strong performance across a wide range of generative tasks.
However, their reliance on ODE-based iterative sampling incurs substantial computational overhead, which limits their applicability in real-time scenes. 
While distillation is a promising solution, existing approaches largely borrow from diffusion-based score matching, often failing to exploit the intrinsic geometric structure of flows and suffering from training instability, high variance, and degraded generation quality.
In this paper, we propose Mean Flow Distillation (MFD), a novel distillation framework tailored for flow matching models.
We theoretically demonstrate that MFD acts as a temporal low-pass filter, effectively suppressing the high-frequency optimization noise inherent in variational score distillation (VSD) while ensuring global trajectory consistency. We further prove the Mean Flow Matching Theorem, establishing that matching expected average velocities is sufficient for strict distribution alignment. Empirically, on challenging high-dimensional manifolds including 4D occupancy forecasting and text-to-image generation, MFD achieves state-of-the-art performance, enabling high-fidelity single-step generation.

---

## 论文详细总结（自动生成）

## 论文详细中文总结

### 1. 核心问题与整体含义（研究动机与背景）

- **研究对象**：流匹配（Flow Matching, FM）模型，这是一类在图像、视频、4D 占用预测等领域表现优异的生成模型。
- **核心痛点**：流匹配模型依赖 ODE 迭代采样，推理阶段计算开销巨大，严重制约了其在实时场景中的部署。
- **现有方案之不足**：现有蒸馏方法多直接借用扩散模型中的分数匹配 / 变分分数蒸馏（VSD）思路，**没有充分利用流模型自身的内在几何结构**，导致训练不稳定、方差高、生成质量退化。
- **本文目标**：提出一种**专门为流匹配模型设计的蒸馏框架**——**均值流蒸馏（Mean Flow Distillation, MFD）**，在保持生成质量的同时实现高保真单步生成。

### 2. 方法论：均值流蒸馏（MFD）

- **核心思想**：利用流模型的时间结构特性，将蒸馏目标从“逐点速度匹配”改为“**期望平均速度匹配**”，从而抑制优化过程中的高频噪声。
- **关键理论贡献**：
  - **时间低通滤波解释**：作者从理论上证明，MFD 本质上等价于一个**时间维度的低通滤波器**，可以有效滤除变分分数蒸馏（VSD）中固有的高频优化噪声。
  - **全局轨迹一致性**：MFD 不仅关注单步速度匹配，还保证了整条生成轨迹的全局一致性。
  - **均值流匹配定理（Mean Flow Matching Theorem）**：作者证明，**只要匹配期望平均速度，就足以实现严格的分布对齐**，无需对每个时间步进行精确匹配。
- **技术流程（文字说明）**：
  1. 定义流匹配模型的速度场，并建立蒸馏目标；
  2. 将原始逐点速度匹配目标替换为对平均速度场的匹配；
  3. 通过时间低通滤波机制，在训练过程中自然抑制高频扰动；
  4. 通过均值流匹配定理保证分布对齐的充分性；
  5. 在蒸馏训练中实现稳定收敛，最终支持单步采样。

### 3. 实验设计

- **应用场景 / 数据集**：
  - **4D 占用预测**：高维流形上的预测任务，考验模型对复杂时空结构的建模能力；
  - **文本到图像生成**：常见的生成模型评测场景，用于验证生成质量与保真度。
- **Benchmark**：未在摘要中明确说明具体基准数据集名称（如 MS-COCO、FID 评分等），也未列出具体基线方法名称。
- **对比方法**：根据摘要推断，至少与基于 VSD 的现有蒸馏方法进行了对比。
- **结果**：在以上两个任务上均取得 **state-of-the-art（SOTA）** 效果，支持**高保真单步生成**。

### 4. 资源与算力

- 论文提供的摘要和元数据中**未明确说明**具体使用的 GPU 型号、数量、训练时长等信息。
- 仅能推断：涉及 4D 占用预测与文本到图像生成，这两类任务通常需要多卡 GPU 集群级资源，但**具体算力配置无法从现有信息中确认**。

### 5. 实验数量与充分性

- **实验组数**：从摘要可见至少两类任务的实验（4D 占用预测、文本到图像），加上理论分析，共包含实验与理论两部分。
- **消融实验**：摘要与元数据中**未提及消融实验的存在**（如对低通滤波强度、平均窗口大小等的敏感性分析）。
- **充分性与客观性评估**：
  - **优点**：选择了高维、有挑战性的真实应用场景，覆盖面较广；
  - **不足**：未披露基线设置、评估指标细节、多次重复实验的方差等，客观上削弱了对结果可靠性的完整判断；
  - 需要阅读全文才能确认实验的完备性和公平性。

### 6. 主要结论与发现

- MFD 作为一种**面向流匹配模型的专用蒸馏框架**，在理论与实验两个层面均有效。
- 理论层面：
  - MFD 相当于时间低通滤波器，能有效抑制 VSD 训练中的高频噪声；
  - 均值流匹配定理保证了“匹配平均速度即可对齐分布”这一结论的严格性。
- 实验层面：
  - 在 4D 占用预测和文本到图像生成任务上取得 SOTA；
  - 实现了**高保真单步生成**，显著降低采样开销。
- **总体结论**：MFD 为流匹配模型提供了一种高效、稳定、可推广的蒸馏方案。

### 7. 优点

- **方法原创性强**：不再简单借用扩散模型的蒸馏思路，而是深入利用了流模型自身的几何与时间结构。
- **理论支撑扎实**：不仅有实验验证，还提供了低通滤波解释和均值流匹配定理等理论保证，逻辑自洽。
- **针对性强**：直击现有蒸馏方法训练不稳定、方差高的痛点，从机制层面入手解决。
- **应用价值高**：单步生成能力对实时部署意义重大，且方法可推广至视频等更复杂的生成任务。

### 8. 不足与局限

- **实验细节披露不充分**：具体数据集、评估指标、对比基线、超参数设置等尚未完全公开，外部难以完整复现。
- **算力与训练成本未说明**：缺少对资源消耗的量化评估，实际部署门槛未知。
- **消融实验缺失**：从现有信息看，未系统地检验各组件（如时间窗口选择、滤波强度）的贡献。
- **推广性尚需验证**：虽然目标转向视频等任务，但实际实验未覆盖视频生成，跨任务泛化能力仍需证据。
- **潜在偏差风险**：理论证明在理想条件下成立，但实际中均值速度匹配在复杂多模态分布上的严谨有效性仍有待更深入验证。

（完）
