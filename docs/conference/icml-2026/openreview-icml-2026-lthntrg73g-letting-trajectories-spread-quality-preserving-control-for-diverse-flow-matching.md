---
title: "Letting Trajectories Spread: Quality-Preserving Control for Diverse Flow Matching"
title_zh: 让轨迹扩散：保持质量的多样流匹配控制
authors: "Jingxuan Wu, Zhenglin Wan, Xingrui Yu, Yuzhe YANG, Bo An, Ivor Tsang, Yang You"
date: 2026-04-30
pdf: "https://openreview.net/pdf/562b99845fdde18686c40228d1db79402bd18533.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 针对流匹配多样性生成的免训练推理时控制
tldr: 基于流的文本到图像模型受限于确定性轨迹，在有限采样预算下难以兼顾多样性和质量。本文提出一种免训练的推理时控制机制，通过特征空间中的横向扩散目标促使轨迹彼此分离，同时加入与质量方向正交的时间调度随机扰动。实验显示该方法在保真度不下降的前提下显著提升生成多样性。该工作为流模型多样性控制提供了一种即插即用的新工具。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 流模型的确定性轨迹在有限采样下难以探索多样模式，现有多样性改进方法要么重训练要么损失保真度。
method: 设计与质量方向几何解耦的引导，联合特征空间横向扩散目标与时间调度随机扰动，实现训练时免费的多样性控制。
result: 在图像生成任务上，该方法在保持质量的同时显著提升样本多样性。
conclusion: 为流匹配生成模型提供了无需重训练的多样性增强新机制。
---

## Abstract
Flow-based text-to-image models follow deterministic trajectories, making it costly to explore diverse modes under limited sampling budgets. Existing approaches to improving diversity often rely on retraining or degrade image fidelity. To address this limitation, we present a training-free, inference-time control mechanism that makes the flow itself diversity-aware. Our core insight is to encourage diversity through guidance that is geometrically decoupled from the model’s quality-seeking direction. Our method simultaneously encourages lateral spread among trajectories via a feature-space objective and reintroduces uncertainty through a time-scheduled stochastic perturbation. Crucially, this perturbation is projected to be orthogonal to the generation flow, a geometric constraint that allows it to boost variation without degrading image details or prompt fidelity. Theoretically, we show that this design monotonically increases a volume surrogate while approximately preserving the marginal distribution, providing a principled explanation for the robustness of generation quality. Empirically, across multiple text-to-image settings under fixed sampling budgets, our method consistently improves diversity metrics such as the Vendi Score and Brisque over strong baselines, while upholding image quality and alignment.

---

## 论文详细总结（自动生成）

由于 PDF 原页为 OpenReview 的浏览器验证页面，无法提取正文全文，以下总结完全基于题目元数据与摘要信息编写。

# 论文详细总结：让轨迹扩散：保持质量的多样流匹配控制

## 1. 论文的核心问题与整体含义

- 背景：基于流的文本到图像生成模型（如 Flow Matching 类模型）依赖确定性轨迹进行采样。在有限的采样预算下，这些轨迹难以充分探索数据分布中的多样模式，导致生成样本同质化。
- 问题：现有多样性改进方法面临两难——要么需要重新训练模型（成本高、不灵活），要么在提升多样性时牺牲图像保真度或文本对齐。
- 核心研究问题：如何在不重训练、不降低质量的前提下，让流匹配模型在推理阶段获得更强的生成多样性？
- 整体含义：该工作提出了一种训练免费（training-free）的推理时控制机制，使流匹配生成过程本身具备“多样性感知”能力，为流模型的多模态生成控制提供了一个通用、即插即用的新工具。

## 2. 论文提出的方法论

- 核心思想：通过一种与模型“质量追求方向”几何解耦的引导机制来鼓励轨迹发散，从而在保持生成质量的同时提升多样性。
- 关键技术细节：
  - 特征空间横向扩散目标：在特征空间中设定目标，促使不同采样轨迹相互分离（lateral spread），避免轨迹坍缩到相同的模式。
  - 时间调度的随机扰动：按照时间表向采样过程注入随机扰动，重新引入生成过程中的不确定性。
  - 正交投影约束：上述随机扰动被投影到与生成流正交的方向上。该几何约束确保扰动只增加轨迹间的分散度，而不影响沿流方向的生成细节，从而保护图像清晰度和提示一致性。
- 理论分析：论文证明该设计能单调增加一个“体积代理”（volume surrogate）指标，同时近似保持边际分布不变。这为“多样性提升不损害生成质量”提供了原理性解释。

## 3. 实验设计

- 使用场景：多个文本到图像生成设置，且采样预算固定。
- 评估指标：
  - 多样性：使用 Vendi Score、Brisque 等指标评估；
  - 质量与对齐：保持图像质量与提示对齐程度不下降（具体指标如 FID、CLIP Score 等在摘要中未列明）。
- 对比方法：与“强基线”（strong baselines）进行对比，但摘要中未列出具体基线方法名称。
- 数据集：摘要未明确列出所用数据集名称（如 MS-COCO、Flickr30K 等未提及）。

## 4. 资源与算力

- 文本中未说明使用的 GPU 型号、数量、训练时长或推理时间开销。
- 由于该方法属于训练免费（training-free）的推理时控制，推测不需要额外训练成本，但论文未提供推理阶段的额外计算量对比数据。

## 5. 实验数量与充分性

- 实验覆盖度：摘要提到在“多个文本到图像设置”下进行验证，说明有一定跨场景实验；但具体实验数量（如几个数据集、几组对比、几组消融）未列出。
- 消融研究：摘要未明确提及消融实验细节，但从方法论包含多个组件（横向扩散、时间扰动、正交投影）来看，合理的消融应包含各组件的单独与组合影响，但无法从摘要确认。
- 客观性与公平性：实验同时考察了多样性、质量与对齐三类指标，并声称在固定采样预算下与强基线对比，方向上是合理的；但因缺少基线与数据集的具体信息，无法在摘要层面完成公平性评估。
- 总体评价：方法有理论支撑且实验维度较全面（多样性+质量+对齐），但可验证性受限于摘要信息不完整。

## 6. 论文的主要结论与发现

- 在固定采样预算下，所提出的方法能在不损害图像质量和对齐的前提下显著提升生成多样性（Vendi Score、Brisque 等指标改善）。
- 理论分析表明，正交化的随机扰动与横向扩散目标的组合，能够在扩大生成分布覆盖范围的同时近似维持原分布，是一种可解释的多样性增强机制。
- 该工作为流匹配生成模型提供了一种无需重训练的多样性控制新途径。

## 7. 优点

- 训练免费、即插即用：无需修改模型参数，可直接用于已训练好的流模型。
- 几何解耦的巧妙设计：将“多样性方向”与“质量方向”正交化，从几何层面规避了多样性与质量的根本冲突。
- 有理论保障：单调体积代理递增 + 边际分布近似保持，为该方法的稳定性提供了数学依据。
- 通用性强：面向流匹配框架提出，原理上不依赖于特定文本到图像架构，具备扩展到其他流模型任务的潜力。

## 8. 不足与局限

- 实验细节缺失：数据集、基线方法、具体多样性/质量数值均未在摘要中给出，难以评估效果的实际规模与统计显著性。
- 模态覆盖有限：摘要仅涉及文本到图像生成，未包含视频、音频、3D 等其他流模型应用场景。
- 推理开销未知：虽然不需要训练，但特征空间引导与正交投影可能引入额外推理计算，论文未给出开销对比。
- 理论代理指标与实际感知质量之间未证明严格等价：体积代理上升未必意味着用户可见的多样性改善。
- 对复杂长文本提示、分布外（OOD）提示的多样性表现未在摘要中讨论，真实场景鲁棒性未知。

（完）
