---
title: When Do Diffusion Models Learn to Generate Multiple Objects?
title_zh: 扩散模型何时才能学会生成多个物体？
authors: "Yujin Jeong, Arnas Uselis, Iro Laina, Seong Joon Oh, Anna Rohrbach"
date: 2026-04-30
pdf: "https://openreview.net/pdf/1de5f071bc9ab4a651f9643a40996b7f43e94181.pdf"
tags: ["query:diff-video"]
score: 8.0
evidence: 通过受控数据集框架分析扩散模型多物体生成的数据层面局限
tldr: 文本到图像扩散模型在多物体生成中不够可靠，多数研究聚焦模型本身而忽视数据影响。本文设计受控数据集框架mosaic，分离概念泛化和组合泛化两种场景，系统研究数据不平衡和组合缺失对生成的影响。结果表明数据分布是导致多物体生成失败的重要原因。该发现为构建更好的训练数据和评估基准提供了依据。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型在多物体生成中的失败原因尚不明确，需要厘清数据本身的作用。
method: 引入受控数据集mosaic，分别考察概念泛化和组合泛化两种数据机制。
result: 实验揭示数据不平衡和组合缺失是多物体生成失败的关键因素。
conclusion: 从数据层面解释扩散模型局限，为改进训练数据和方法提供指导。
---

## Abstract
Text-to-image diffusion models achieve impressive visual fidelity, yet they remain unreliable in multi-object generation.
Despite extensive empirical evidence of these failures, the underlying causes remain unclear.
We begin by asking how much of this limitation arises from the data itself.
To disentangle data effects, we consider two regimes across different dataset sizes:
(1) concept generalization, where each individual concept is observed during training under potentially imbalanced data distributions, and
(2) compositional generalization, where specific combinations of concepts are systematically held out.
To study these regimes, we introduce mosaic  (Multi-Object Spatial relations, AttrIbution, Counting), a controlled framework for dataset generation.
By training diffusion models on mosaic, we find that scene complexity plays a dominant role rather than concept imbalance, and that counting is uniquely difficult to learn in low-data regimes.
Moreover, compositional generalization collapses as more concept combinations are held out during training.
These findings highlight fundamental limitations of diffusion models and motivate stronger inductive biases and data design for robust multi-object compositional generation.

---

## 论文详细总结（自动生成）

# 论文总结：When Do Diffusion Models Learn to Generate Multiple Objects?

## 1. 核心问题与整体含义
- **研究背景**：文本到图像扩散模型虽然能生成高视觉保真度的图像，但在多物体生成任务中仍不可靠，例如物体遗漏、属性绑定错误、计数混乱等。
- **核心问题**：以往研究大多从模型架构、训练策略或损失函数角度解释和缓解这类失败，却很少关注**训练数据本身**所带来的影响。本文提出的根本问题是：**多物体生成能力的局限，在多大程度上源于数据？**
- **整体含义**：通过系统性操控数据分布来隔离数据效应，本文揭示数据中的不平衡、场景复杂度和组合缺失是模型失败的重要诱因，为构建更合理的训练数据与评估基准提供了依据，也强调了引入更强归纳偏置的必要性。

## 2. 方法论
- **核心思想**：不修改模型，而是**设计一个受控的数据生成框架**，在可调节的数据条件下训练扩散模型，从而观察并归因数据层面的影响。
- **关键技术细节**：
  - 引入受控数据集 **mosaic**（Multi-Object Spatial relations, AttrIbution, Counting），覆盖多物体场景中的三类核心挑战：空间关系、属性归因、计数。
  - 区分两种独立的泛化机制：
    1. **概念泛化（concept generalization）**：训练数据中每个概念（如物体类别）都出现过，但各概念的样本数量可能不平衡，考察分布不平衡的影响。
    2. **组合泛化（compositional generalization）**：训练时系统性保留（hold out）某些概念组合，考察组合缺失对生成的影响。
  - 在不同数据集规模（low-data 到 larger-data）下分别训练扩散模型，比较生成质量随数据条件变化的趋势。
- **算法流程（文字说明）**：使用 mosaic 生成器按预设规则合成图像 → 构造不同不平衡程度的数据集或剔除特定组合 → 在每种配置上训练扩散模型 → 评估多物体生成性能（如空间关系正确率、属性绑定准确率、计数结果），并分析各数据因素与性能的因果关系。

## 3. 实验设计
- **数据集/场景**：全部实验基于 mosaic 框架生成的**受控合成数据集**，专门针对多物体空间关系、属性归因和计数设计场景。
- **Benchmark**：mosaic 本身既作为训练数据来源，也作为评估生成质量的基准，用来衡量模型在各类多物体生成任务上的表现。
- **对比方法**：本文**没有提出新模型或对比其他生成方法**，而是对比同一扩散模型在不同数据配置（概念不平衡程度、组合保留比例、数据规模）下的性能差异。这类“数据条件对照实验”直接检验数据因素的作用。

## 4. 资源与算力
- 摘要和元数据中**未明确提及**使用的 GPU 型号、数量、训练时长或总计算量。
- 从论文性质看，属于对扩散模型的数据分析研究，必然涉及多次不同数据配置下的训练，但具体算力开销没有在提供的信息中给出。

## 5. 实验数量与充分性
- 根据摘要，主要实验包括三组核心发现对应的实验：
  1. 比较**场景复杂度**与**概念不平衡**在多物体生成中的相对作用；
  2. 低数据 regime 下**计数**任务的学习困难程度；
  3. 训练时被保留的概念组合数量如何影响**组合泛化**能力。
- **充分性评价**：实验设计具有系统性——通过受控框架隔离变量、在不同数据规模和不同泛化机制下进行测试，能够形成较清晰的因果结论。但由于提供的资料只有摘要和元数据，无法确认具体实验次数、消融设计的完整性和评估指标的细节；因此不能完全评判其公平性，只能说在受控数据范围内具有较高内部效度。

## 6. 主要结论与发现
- **场景复杂度比概念不平衡影响更大**：即使概念分布不均衡，只要场景复杂度增加，多物体生成失败率也会显著上升；概念不平衡并非首要瓶颈。
- **计数是低数据 regime 下的特有难点**：在训练数据较少时，计数任务尤其难以学会，说明计数能力可能更依赖充足的数据覆盖。
- **组合泛化随保留组合增加而崩溃**：训练时若系统性缺失更多概念组合，模型在未见组合上的生成性能急剧下降，说明组合覆盖不足是组合泛化失败的关键数据原因。
- 总体来看，**数据分布是导致多物体生成失败的重要原因**，仅靠扩大数据量不一定是解决组合泛化问题的有效途径。

## 7. 优点
- **因果归因能力**：通过完全可控的数据集生成，将概念泛化和组合泛化分离开来，比直接在真实数据上观察更能建立因果关系。
- **数据集框架设计**：mosaic 同时覆盖空间关系、属性归因和计数这三大多物体生成难点，可作为后续研究与评估基准复用的工具。
- **结论具有指导意义**：不仅解释了失败原因，还指出了数据设计的可能方向（例如注意场景复杂度覆盖、避免组合缺失），对社区改进训练数据和方法论有实际价值。

## 8. 不足与局限
- **依赖合成数据**：mosaic 是受控合成数据集，与真实世界图像分布存在差距，结论是否完全迁移到自然图像上仍需验证。
- **未考虑模型端影响**：研究有意将模型视为固定，只改变数据，因而无法回答模型架构或训练方法能否弥补数据缺陷。
- **泛化范围有限**：只在扩散模型上进行实验，结论不一定适用于其他生成模型（如 GAN、自回归模型）。
- **信息缺失**：提供的资料中没有详细的超参数、评估指标、训练配置和算力信息，无法复现或评估实验的绝对充分性；组合保留的具体比例、数据规模的具体取值也未给出。

（完）
