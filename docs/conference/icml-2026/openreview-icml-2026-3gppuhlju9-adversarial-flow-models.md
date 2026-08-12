---
title: Adversarial Flow Models
title_zh: 对抗流模型
authors: "Shanchuan Lin, Ceyuan Yang, Zhijie Lin, Hao Chen, Haoqi Fan"
date: 2026-04-30
pdf: "https://openreview.net/pdf/3cafbe72819c4f596d6afeeebd4ef27e1f23837c.pdf"
tags: ["query:diff-video"]
score: 7.0
evidence: 结合对抗训练与流模型的生成模型
tldr: 传统生成对抗网络学习噪声到数据的任意映射，训练不稳定；一致性方法需学习中间时间步导致误差累积。对抗流模型将对抗目标与流模型结合，鼓励生成器学习确定性的噪声到数据映射，同时支持单步与多步生成。实验在 ImageNet-256px 上以单步采样接近更高性能，显著提升训练稳定性并减少误差累积。该方法为高效生成提供新思路。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: GAN 训练不稳定且一致性方法需学习中间时间步，存在误差累积。
method: 用对抗目标训练流模型，学习确定性噪声到数据映射，支持单步/多步生成。
result: 在 ImageNet-256px 上单步生成接近更优性能，训练更稳定。
conclusion: 融合对抗与流模型，兼顾生成质量与采样效率。
---

## Abstract
We present adversarial flow models, a class of generative models that belongs to both the adversarial and flow families. Our method supports native one-step and multi-step generation and is trained with an adversarial objective. Unlike traditional GANs, in which the generator learns an arbitrary transport map between the noise and data distributions, our generator is encouraged to learn a deterministic noise-to-data mapping. This significantly stabilizes adversarial training. Unlike consistency-based methods, our model directly learns one-step or few-step generation without having to learn the intermediate timesteps of the probability flow for propagation. This preserves model capacity and avoids error accumulation. Under the same 1NFE setting on ImageNet-256px, our B/2 model approaches the performance of consistency-based XL/2 models, while our XL/2 model achieves a new best FID of 2.38. We additionally demonstrate end-to-end training of 56-layer and 112-layer models without any intermediate supervision, achieving FIDs of 2.08 and 1.94 with a single forward pass and surpassing the corresponding 28-layer 2NFE and 4NFE counterparts with equal compute and parameters.

---

## 论文详细总结（自动生成）

# 对抗流模型（Adversarial Flow Models）——论文总结

## 1. 论文的核心问题与整体含义

- **研究背景**：生成式模型领域长期存在两条技术路线——生成对抗网络（GAN）和基于概率流的扩散/流模型，二者各有优势与短板。
- **传统 GAN 的问题**：生成器学习的是噪声分布到数据分布之间的**任意传输映射**（arbitrary transport map），这种自由度过高，导致对抗训练过程不稳定，容易出现模式坍缩或震荡。
- **一致性方法（consistency-based methods）的问题**：虽然支持少步生成，但必须先学习概率流（probability flow）的**中间时间步**作为传播依据，这会消耗模型容量并导致**误差累积**。
- **整体含义**：本文提出一种同时属于**对抗家族**与**流模型家族**的生成模型——**对抗流模型（Adversarial Flow Models, AFM）**，通过对抗目标训练流模型，鼓励生成器学习**确定性**的噪声到数据映射，从而兼顾训练稳定性与采样效率。

## 2. 论文提出的方法论

- **核心思想**：
  - 将**判别器（对抗目标）** 与**流模型**的框架相结合，使生成器在对抗训练过程中被驱动去学习一个**确定性、可复现的噪声→数据映射**，而非任意传输映射。
  - 这种确定性映射的归纳偏置显著降低了对抗训练的难度和不稳定性。
- **关键技术点**：
  - **原生支持单步与多步生成**：模型可以直接完成一步（1-step）或少数几步（few-step）采样，无需像一致性模型那样预先学习中间时间步。
  - **无需中间监督**：通过端到端对抗训练直接优化最终生成结果，避免了误差沿概率流传播导致的累积问题。
  - **深度模型扩展**：支持 56 层乃至 112 层的深层网络端到端训练，且无需任何中层监督信号。
- **算法流程（文字描述）**：
  1. 从噪声分布采样输入。
  2. 生成器（流模型）将噪声映射为数据，该映射被训练为确定性函数。
  3. 判别器对生成样本与真实数据做对抗判别。
  4. 生成器在对抗损失的驱动下不断逼近真实数据分布，同时保持映射的确定性。
  5. 推理时可选择单步直接生成，或多步迭代细化生成。

## 3. 实验设计

- **数据集**：ImageNet-256px，即 256×256 分辨率的 ImageNet 类别条件生成任务。
- **Benchmark 指标**：FID（Fréchet Inception Distance），生成质量的标准评估指标。
- **对比方法**：
  - **一致性模型**（consistency-based models），尤其是不同参数规模的版本（如 XL/2 规模）。
  - **不同深度/步数的自对比**：28 层模型在 2NFE 与 4NFE 设置下的表现，作为 56 层与 112 层模型的对照组。
- **关键结果对比**：
  - 在相同的单步（1NFE）采样设置下，本文 B/2 规模的模型，其性能已接近一致性方法中 XL/2 规模模型的水平。
  - 本文 XL/2 模型在 ImageNet-256px 上取得 **FID = 2.38** 的新最佳结果。
  - 56 层模型单步生成 FID = 2.08；112 层模型单步生成 FID = 1.94，均超过同等计算量与参数量的 28 层 2NFE/4NFE 对照模型。

## 4. 资源与算力

- **文中未明确说明具体算力信息**，包括 GPU 型号、数量、训练时长、批量大小等细节均未在摘要与元数据中给出。
- 仅可推测该实验规模较大（ImageNet-256px 上的 112 层端到端训练），但无法量化其计算消耗。

## 5. 实验数量与充分性

- **已进行的实验类型**：
  - 不同模型规模对比（B/2 vs. XL/2）；
  - 不同网络深度对比（28 层 vs. 56 层 vs. 112 层）；
  - 不同采样步数对比（1NFE vs. 2NFE vs. 4NFE）。
- **充分性评价**：
  - **优点**：包含模型规模、深度、采样步数三个维度的对照，并执行了等参数量/等算力的公平对比，能在一定程度上说明方法扩展性。
  - **不足**：目前可见信息仅覆盖 ImageNet-256px **单一数据集**，缺少 CIFAR、LSUN、文本到图像等其他常见基准的验证；也未见关于损失函数各组分（如对抗损失与流匹配损失的比例）、判别器设计等的系统消融分析。

## 6. 论文的主要结论与发现

- 对抗流模型可以同时获得 GAN 的高采样效率与流模型的训练稳定性。
- 学习**确定性映射**是稳定对抗训练的关键因素，相比任意传输映射能显著降低训练难度。
- 直接学习单步生成（不经过中间时间步传播）有助于保留模型容量、避免误差累积，因此单步生成性能即可接近甚至超越一致性模型的少步生成。
- 深度模型可以端到端训练且无需中间监督，深度增加能持续带来 FID 改善（112 层 > 56 层 > 28 层同等步数），且仅需单次前向传播即可获得 SOTA 结果。

## 7. 优点

- **方法创新性强**：将对抗训练与流模型有机融合，别于传统 GAN 和一致性模型的已有路径。
- **训练稳定性改善**：确定性映射降低了对抗训练的方差，使深层模型端到端训练成为可能。
- **采样效率突出**：原生支持单步生成，在 1NFE 条件下超越现有同规模方法，推理成本大幅下降。
- **实验对比公平**：在评估 56/112 层模型时，刻意与等计算/等参数的 28 层多步模型对比，增强了结论说服力。

## 8. 不足与局限

- **实验覆盖有限**：目前结果集中于 ImageNet-256px，尚不确定方法在其他数据集（如低分辨率 CIFAR、高分辨率自然图像）和任务（如 text-to-image、视频生成）上的泛化能力。
- **算力信息缺失**：未报告训练资源，影响其他研究者评估可复现性与实际落地成本。
- **缺少消融与鲁棒性分析**：未展示判别器设计、损失权重、流模型具体参数化方式等消融实验；也未分析单步生成在真实应用中的鲁棒性与失败案例。
- **理论分析不足**：对“确定性映射为何能稳定对抗训练”缺乏形式化的理论解释，主要依赖经验验证。

（完）
