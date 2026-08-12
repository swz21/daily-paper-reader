---
title: "TFTF: Training-Free Targeted Flow for Conditional Sampling"
title_zh: TFTF：用于条件采样的免训练目标流
authors: "Qianqian Qu, Jun S. Liu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/91ec281bcd81a9a963d115664ebdcffc131e8bc1.pdf"
tags: ["query:diff-video"]
score: 8.0
evidence: 面向流匹配模型的免训练条件采样方法
tldr: 针对流匹配模型的条件采样问题，TFTF 提出基于重要性采样的免训练方案。为解决高维重要性采样权重退化，引入序贯蒙特卡洛重采样，并在中间阶段用带噪声强度可调的随机流替换确定性流，促进样本沿不同轨迹分离。理论保证渐近精确，实验在 MNIST 等条件采样任务上显著优于现有方法。该框架无需额外训练即可增强流匹配模型的条件生成能力。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 流匹配模型条件采样依赖额外训练或简单重要性采样，存在权重退化。
method: 结合重要性采样与序贯蒙特卡洛重采样，并引入可调噪声的随机流实现免训练条件采样。
result: 在 MNIST 等条件采样任务上显著超越现有方法，且具备渐近精确性。
conclusion: 为流匹配模型提供了一种无需训练的通用条件采样新框架。
---

## Abstract
We propose a training-free conditional sampling method for flow matching models based on importance sampling. Because a naïve application of importance sampling suffers from weight degeneracy in high-dimensional settings, we modify and incorporate a resampling technique in sequential Monte Carlo (SMC) during intermediate stages of the generation process. To encourage the generated samples to diverge along distinct trajectories, we derive a stochastic flow with adjustable noise strength to replace the deterministic flow at the intermediate stage. Our framework requires no additional training, while providing theoretical guarantees of asymptotic accuracy. Experimentally, our method significantly outperforms existing approaches on conditional sampling tasks for MNIST and CIFAR-10. We further demonstrate the applicability of our approach in higher-dimensional, multimodal settings through text-to-image generation experiments on CelebA-HQ.

---

## 论文详细总结（自动生成）

# TFTF: 免训练目标流用于条件采样

## 1. 论文的核心问题与整体含义

- **研究动机**：流匹配模型（Flow Matching）在生成任务中表现出色，但进行条件采样（如根据类别标签、文本描述生成图像）时，现有方法通常依赖额外训练（如分类器引导）或朴素的重要性采样。
- **核心问题**：朴素重要性采样在高维空间中容易产生 **权重退化**，导致条件采样失效。如何在不额外训练的前提下，高效、稳定地对预训练流匹配模型进行条件采样，是一个关键挑战。
- **整体含义**：TFTF 提出一种**免训练（training-free）**的条件采样框架，结合重要性采样与序贯蒙特卡洛（SMC）重采样，并引入带可调噪声的随机流，使得现有预训练流匹配模型可以直接用于条件生成，而无需修改模型参数。

## 2. 方法论

- **核心思想**：将条件采样视为对条件分布的重要性采样问题，通过在生成过程的中间阶段引入重采样和随机扰动，解决权重退化问题，同时保持生成轨迹的多样性。
- **关键技术细节**：
  - **重要性采样**：利用预训练流匹配模型的无条件分布作为提案分布，对条件分布进行加权采样。
  - **序贯蒙特卡洛（SMC）重采样**：在生成过程的中间阶段，对样本进行重采样，以缓解高维重要性采样导致的权重集中（即权重退化）。
  - **带可调噪声强度的随机流**：将中间阶段的确定性流替换为随机流，通过调节噪声强度促使样本沿不同轨迹分离，增强多样性，同时保证前后阶段的一致性。
- **理论保障**：该方法提供**渐近精确性**的理论保证，即当样本数量趋于无穷时，加权样本能够准确逼近目标条件分布。
- **算法流程（文字说明）**：
  1. 从预训练流匹配模型的无条件分布中采样初始粒子。
  2. 沿着流模型的 ODE/SDE 推进到中间阶段。
  3. 在中间阶段引入重要性权重，并根据权重进行 SMC 重采样（筛选高权重粒子）。
  4. 使用带可调噪声的随机流继续推进粒子，使其在轨迹间分离。
  5. 持续迭代上述过程，直到生成阶段结束，得到符合条件分布的样本。

## 3. 实验设计

- **数据集/场景**：
  - **MNIST**：条件数字生成（如按类别标签采样）。
  - **CIFAR-10**：条件类别生成（10 类图像）。
  - **CelebA-HQ**：高维多模态场景下的文本到图像生成。
- **Benchmark**：与**现有条件采样方法**进行对比，但摘要中未具体列出对比方法的名称，仅表示 TFTF **显著优于现有方法**。
- **评价维度**：定性生成效果（或定量指标，如 FID、分类准确率等，摘要未明确写明具体指标）。

## 4. 资源与算力

- **论文摘要中未明确提及** GPU 型号、数量或训练时长等算力信息。
- 由于该方法强调“免训练”，理论上只需预训练模型权重，推理阶段需要额外计算用于重采样和噪声流推进，但具体算力开销未在摘要中量化。

## 5. 实验数量与充分性

- **实验数量**：包含 **三个数据集/场景**（MNIST、CIFAR-10、CelebA-HQ），覆盖了低维到高维、单模态到多模态的条件生成。
- **消融实验**：摘要未明确提及是否包含消融研究（如噪声强度调节、重采样策略的影响等）。
- **充分性评估**：
  - 优点：覆盖了不同维度（MNIST 低维、CIFAR-10 适中、CelebA-HQ 高维）和不同条件类型（类别、文本），具有一定代表性。
  - 不足：摘要信息有限，缺少与具体 baseline 的数值对比、统计显著性分析、以及消融实验的详细说明，因此实验的完整性和可复现性需要依赖论文全文。

## 6. 主要结论

- TFTF 为流匹配模型提供了一种**无需额外训练**的通用条件采样框架。
- 通过重要性采样 + SMC 重采样 + 可调噪声随机流，有效解决了高维权重退化问题，并提升了条件生成质量。
- 在 MNIST、CIFAR-10、CelebA-HQ 上的实验结果表明，该方法显著优于现有条件采样方法，具有理论上的渐近精确性。

## 7. 优点

- **免训练**：不修改预训练模型，即插即用，降低计算资源需求。
- **理论保证**：具备渐近精确性，方法论可靠。
- **通用性**：适用于不同维度和不同条件类型（类别、文本）的流匹配模型。
- **解决实际问题**：针对重要性采样的权重退化问题提出了有效的 SMC 重采样策略。
- **随机流设计**：可调噪声强度增强了生成轨迹的多样性，兼顾探索与稳定性。

## 8. 不足与局限

- **实验细节缺失**：摘要中未列出对比方法的名称、具体指标、误差棒等，难以完全评估其公平性。
- **缺乏消融分析**：未说明噪声强度、重采样频率等关键超参数的影响，无法判断各模块的必要性。
- **高维应用有限**：虽然涉及 CelebA-HQ 文本到图像生成，但尚未覆盖更大规模或更复杂的数据集（如 ImageNet、MS-COCO 等）。
- **计算开销**：免训练虽然省去训练成本，但 SMC 重采样和随机流推进可能带来额外的推理时计算，摘要未报告具体开销。
- **理论假设**：渐近精确性依赖样本规模，实际应用中的有限样本可能影响性能，摘要未讨论收敛速率。
- **适用范围**：当前验证集中于图像域，未涉及文本、音频等其他模态的流匹配模型。

（完）
