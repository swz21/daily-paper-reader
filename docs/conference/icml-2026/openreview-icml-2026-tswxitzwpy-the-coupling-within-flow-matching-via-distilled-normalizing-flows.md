---
title: "The Coupling Within: Flow Matching via Distilled Normalizing Flows"
title_zh: 内部耦合：通过蒸馏归一化流实现流匹配
authors: "David Berthelot, Tianrong Chen, Jiatao Gu, marco cuturi, Laurent Dinh, Bhavik Chandna, Michal Klein, Joshua M. Susskind, Shuangfei Zhai"
date: 2026-01-23
pdf: "https://openreview.net/pdf/c1aa103efd3cfd32d6e835b3398849cc5f79bd3d.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 提出从预训练归一化流中蒸馏耦合来训练流匹配模型
tldr: 流匹配训练中耦合测度的选择至关重要，但计算自适应耦合往往代价高。本文提出范式转变：不直接计算自适应耦合，而是从一个预训练的双射模型蒸馏耦合，将噪声空间和数据空间可靠联系。实验表明该蒸馏耦合策略在多种生成任务上提升了流模型的训练与推理性能。该工作为流匹配的耦合设计提供了新的高效思路。
source: ICML-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 流匹配的训练质量依赖于噪声-数据对的耦合选择，而自适应耦合计算成本高。
method: 从预训练的双射归一化流中蒸馏出耦合，替代直接计算自适应耦合，用于定义流匹配回归损失。
result: 在多个生成基准上，蒸馏耦合策略改善了流模型的训练效果和推理灵活性。
conclusion: 为流匹配训练提供了一种高效且可迁移的耦合构造新范式。
---

## Abstract
Flow models have rapidly become the go-to method for training and deploying large-scale generators, owing their success to inference-time flexibility via adjustable integration steps. A crucial ingredient in flow training is the choice of coupling measure for sampling noise/data pairs that define the flow matching (FM) regression loss. While FM training defaults usually to independent coupling, recent works show that adaptive couplings informed by noise/data distributions (e.g., via optimal transport, OT) improve both model training and inference. We radicalize this insight by shifting the paradigm: rather than computing adaptive couplings directly, we use distilled couplings from a different, pretrained model capable of placing noise and data spaces in bijection—a property intrinsic to normalizing flows (NF) through their maximum likelihood and invertibility requirements. Leveraging recent advances in NF image generation via auto-regressive
(AR) blocks, we propose Normalized Flow Matching (NFM), a new method that distills the quasi-deterministic coupling of pretrained NF models to train student flow models. These students achieve the best of both worlds: significantly outperforming flow models trained with independent or even OT couplings, while also improving on the teacher AR-NF model.

---

## 论文详细总结（自动生成）

# 论文总结：内部耦合：通过蒸馏归一化流实现流匹配

## 1. 核心问题与整体含义

- **研究动机**：流模型（Flow Models）已成为大规模生成模型的主流工具，其核心优势在于推理时可以通过调整积分步数灵活控制生成质量与成本。
- **关键问题**：在流匹配（Flow Matching）训练中，噪声–数据对的“耦合测度”选择至关重要——它直接定义了 FM 回归损失，影响模型训练效果和推理表现。
- **现有局限**：常见的独立耦合虽然简单，但并非最优；近年工作表明，基于最优传输（OT）等的自适应耦合能改善训练，但直接计算自适应耦合通常代价高昂。
- **论文立场**：本文提出一种范式转变——与其直接计算自适应耦合，不如从另一个预训练的、能将噪声与数据空间建立双射的模型中“蒸馏”出耦合。由于归一化流（NF）天然具备最大似然训练与可逆性，是这种教师模型的理想选择。

## 2. 方法论：核心思想、关键技术细节与流程

- **核心思想**：利用预训练的、基于自回归（AR）模块构造的归一化流模型作为教师，将教师模型的准确定性耦合（quasi-deterministic coupling）蒸馏给学生流模型，从而避免昂贵的耦合计算。
- **方法名称**：Normalized Flow Matching（NFM）。
- **关键技术**：
  - 教师 NF 通过可逆映射将噪声空间与数据空间形成一一对应，因此可以直接从教师模型获取高质量噪声–数据配对。
  - 学生模型使用这些蒸馏出的配对来定义并优化 FM 回归损失，而不是独立采样或计算 OT 耦合。
- **算法流程（文字描述）**：
  1. 预训练一个 AR 归一化流作为教师（teacher），该模型可完成噪声与数据的双向映射。
  2. 从教师模型中采样或推导出噪声–数据的耦合对。
  3. 用这些耦合对构造流匹配回归目标。
  4. 训练学生流模型（student），使其在训练中吸收教师的耦合信息。
- **说明**：提供的材料中并没有给出具体数学公式；上述流程是基于摘要的概括性描述。

## 3. 实验设计

- **任务场景**：图像生成（摘要明确提到利用 AR-NF 进行图像生成）；论文标签还包含 “query:diff-video”，暗示可能涉及视频相关任务，但摘要未明确说明。
- **Benchmark**：原文仅说“多个生成基准”，未列出具体数据集名称（如 CIFAR、ImageNet 等）。
- **对比方法**：
  - 使用独立耦合训练的流模型；
  - 使用最优传输（OT）耦合训练的流模型；
  - 教师 AR-NF 模型本身。
- **主要评测维度**：训练效果、推理性能（包括推理灵活性）。

## 4. 资源与算力

- 提供的论文摘要与元数据中**完全没有**提及使用的 GPU 型号、数量、训练时长、能耗等算力信息。
- 因此无法评估其训练成本或可复现性。

## 5. 实验数量与充分性

- 从现有材料只能看到概括性结论：在“多个生成基准”上，NFM 训练的流模型优于独立耦合和 OT 耦合，并且还超过了教师 AR-NF。
- **不足**：没有给出具体实验次数、数据集详情、消融实验设置、超参数选择或统计显著性检验。
- **评价**：基于当前提供的摘要信息，无法判断实验是否充分、客观、公平；必须阅读全文才能评估。

## 6. 主要结论与发现

- 从预训练归一化流中蒸馏耦合，是一种高效且可迁移的耦合构造新范式。
- 该方法训练出的学生流模型：
  - 显著优于用独立耦合训练的流模型；
  - 也优于用 OT 耦合训练的流模型；
  - 同时在推理性能上超过教师 AR-NF 模型。
- 学生模型获得了“两全其美”的效果：训练性能好，同时保留了流模型推理时可通过调整积分步数进行灵活控制的优点。

## 7. 优点

- **范式创新**：首次（或突出地）提出“蒸馏耦合”而不是“计算耦合”，打开了流匹配训练的新思路。
- **利用 NF 内在属性**：结合归一化流的双射性与可逆性，自然获得准确定性耦合，降低自适应耦合的计算开销。
- **可迁移性**：预训练的 NF 教师可以复用，无需针对每次训练重新求解耦合（如 OT）。
- **性能提升**：在多个生成基准上超越独立/OT 耦合，且学生模型在推理上还强于教师，说明蒸馏后的耦合信息确实有效。
- **推理灵活性**：保留了流模型原有优势，不牺牲部署时的灵活性。

## 8. 不足与局限

- **实验信息不透明**：提供的材料未给出具体数据集、量化结果、消融实验及训练细节，无法验证结论的稳健性。
- **依赖教师模型质量**：蒸馏耦合的效果高度依赖于预训练 NF 的质量，若教师模型本身较弱，则蒸馏出的耦合可能受限。
- **预训练成本未讨论**：虽然蒸馏避免了耦合计算，但训练教师 AR-NF 本身可能代价不菲；文中未说明这部分开销。
- **适用性存疑**：摘要聚焦图像生成 AR-NF，其他模态（如文本、音频、视频）是否同样适用未得到证据支持。
- **公平性难以判断**：缺乏与 OT 耦合的计算成本对比，无法确认总体效率优势；是否有意避开某些更强的基线也不得而知。
- **发表状态**：该论文标记为 “ICML-2026-Rejected-Public”，说明在评审中可能存在未被满足的关切，虽然其评分较高（score: 9.0），但最终未获录用。

（完）
