---
title: Alignment-Guided Score Matching for Text-to-Image Alignment in Diffusion Models
title_zh: 扩散模型中用于文本-图像对齐的对齐引导分数匹配
authors: "Jaa-Yeon Lee, Yeobin Hong, Taesung Kwon, Jong Chul Ye"
date: 2026-04-30
pdf: "https://openreview.net/pdf/5a6e627431cae8602c3d2c2dace58838fcd15006.pdf"
tags: ["query:diff-video"]
score: 6.0
evidence: 扩散模型中的文本-图像对齐分数匹配
tldr: 扩散模型能生成逼真图像但常存在文本-图像不对齐问题，而后训练方法依赖外部奖励信号。本文提出对齐引导分数匹配，在扩散过程内部直接优化文本-图像对齐，无需外部奖励。结合对比学习改进软文本标记，缓解了过度计数与重复等问题，提升了生成对齐效果。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型文本-图像对齐不足，现有后训练方法依赖奖励质量且不介入扩散过程。
method: 在扩散过程中引入对齐引导的分数匹配，结合对比学习优化软文本标记。
result: 缓解了过度计数与重复问题，提升了文本-图像对齐性能。
conclusion: 该方法提供了一种无需外部奖励的对齐改进方案。
---

## Abstract
Diffusion models generate highly realistic images but often struggle with precise text–image alignment. While recent post-training methods improve alignment using external rewards or human preference signals, their performance heavily depends on reward quality and does not directly address alignment within the diffusion process itself.
Recent reward-free approaches such as SoftREPA demonstrate that optimizing soft text tokens via contrastive learning can effectively improve text-image representation alignment, outperforming standard parameter-efficient fine-tuning baselines. However, the contrastive formulation can excessively penalize negative pairs, which manifests as characteristic failure cases such as over-counting and
repetition.
To address this issue, we propose a lightweight, reward-free post-training method that refines soft tokens by integrating contrastive alignment guidance directly into the score-matching objective of diffusion models. By assigning alignment directions at the score level, our approach mitigates these limitations and yields more coherent and semantically faithful generations.
Experiments show that our method matches SoftREPA while substantially improving its failure cases, achieving over 35\% improvement in counting accuracy on the GenEval benchmark. Our method is seamlessly applicable to existing diffusion backbones (SD1.5, SDXL, and SD3), and is complementary to existing RL-based diffusion post-training methods.

---

## 论文详细总结（自动生成）

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **问题背景**：扩散模型虽然能够生成高度逼真的图像，但在精确的文本–图像对齐（text–image alignment）上仍存在明显不足，例如生成结果与提示词中的物体数量、空间关系、属性描述等不一致。
- **现有方法的局限**：近年来的后训练（post-training）方法通过外部奖励模型或人类偏好信号来优化扩散模型，但其性能高度依赖奖励信号的质量，并且没有在扩散过程内部直接解决对齐问题。
- **无奖励方法的不足**：最近的无奖励方法（如 SoftREPA）通过对比学习优化软文本标记（soft text tokens），虽然能有效提升文本–图像表示对齐，且优于标准的参数高效微调基线，但对比学习目标会对负样本对进行过度惩罚，导致一些特有的失败模式，如**过度计数（over-counting）**和**重复（repetition）**。
- **本文目标**：提出一种轻量级、无奖励的后训练方法，在扩散模型的分数匹配目标中直接引入对齐引导，从而改善对齐质量并修复上述失败模式。

## 2. 论文提出的方法论

- **核心思想**：不依赖外部奖励信号，而是将对比学习的对齐引导直接整合到扩散模型的**分数匹配目标（score-matching objective）**中，在**分数层级（score level）**上赋予对齐方向，从而指导生成过程。
- **技术路线**：
  - 采用软文本标记（soft text tokens）作为可优化的参数，延续 SoftREPA 的思路。
  - 将对比学习得到的对齐方向嵌入到分数匹配过程中，使模型在去噪采样时逐步修正文本–图像对齐。
  - 通过这种“对齐引导”机制，缓解对比学习对负样本对的过度惩罚，避免过度计数和重复等问题。
- **方法特点**：
  - 无需外部奖励或人类偏好信号；
  - 轻量级，属于后训练方法，可应用于现有扩散骨干网络；
  - 与现有的基于强化学习（RL）的扩散模型后训练方法互补。

## 3. 实验设计

- **基准数据集**：使用了 **GenEval** 基准进行文本–图像对齐评估，重点报告了计数准确率。
- **对比方法**：
  - 与 **SoftREPA** 对比，验证本方法在保持对齐性能的同时改进其失败案例；
  - 与标准参数高效微调基线对比（隐含）；
  - 与基于 RL 的后训练方法进行互补性测试。
- **模型适用性**：在 **SD1.5、SDXL、SD3** 多个扩散骨干网络上进行了实验，验证方法的通用性。
- **主要衡量指标**：文本–图像对齐能力（含计数准确率）、生成连贯性和语义保真度。

## 4. 资源与算力

- 摘要和元数据中**未明确说明**使用的 GPU 型号、数量、训练时长等算力信息。
- 仅能推断该方法为“轻量级”后训练，参数开销较小，但具体资源需求未知。

## 5. 实验数量与充分性

- 已提及的实验覆盖：至少包括 GenEval 基准上的计数准确率测试，以及 SD1.5、SDXL、SD3 三种骨干网络上的适配性验证，还可能包含与 SoftREPA 和 RL 方法的对比。
- 由于仅提供摘要，**无法获知具体的消融实验数量、统计显著性检验或更多下游数据集上的结果**。因此从现有信息看，实验能初步证明方法的有效性和通用性，但在全面性上（如更多基准、多样场景、鲁棒性分析）尚无法充分评估。
- 公平性方面：与 SoftREPA 的直接对比采用了相同任务和基准，但未详细说明训练设置是否完全一致。

## 6. 论文的主要结论与发现

- 提出的对齐引导分数匹配方法能够达到与 SoftREPA 相当的整体文本–图像对齐性能。
- 在 SoftREPA 的失败案例（如过度计数和重复生成）上取得了显著改善：在 GenEval 基准上**计数准确率提升了超过 35%**。
- 生成结果更加连贯、语义更加忠实。
- 方法可无缝应用于现有主流扩散模型（SD1.5、SDXL、SD3），并且能够与基于 RL 的扩散模型后训练方法互补。

## 7. 优点

- **无需外部奖励**：避免了奖励模型质量对训练效果的影响，降低了依赖。
- **直指扩散过程内部**：将对齐信号注入分数匹配目标，比单纯优化表示更直接地引导生成。
- **轻量且通用**：作为后训练方法，开销小，且跨版本（SD1.5、SDXL、SD3）适用。
- **互补性强**：可与 RL 后训练方法结合，具有扩展空间。
- **解决具体问题**：针对对比学习的负样本过度惩罚缺陷，提出了有理论依据的改进，并在计数任务上获得大幅提升。

## 8. 不足与局限

- **信息不完整**：提供的文本仅为摘要，缺乏方法细节、公式、伪代码和完整实验结果，难以充分验证技术实现。
- **实验覆盖有限**：仅提及 GenEval 基准和三个 SD 骨干，未涉及更多文本–图像对齐基准（如 T2I-CompBench、DSG-1K 等），也未报告多样场景的定性结果。
- **评估指标单一**：突出计数准确率，但对复杂语义属性（颜色、空间关系、风格等）的对齐效果未在摘要中说明。
- **算力与训练成本未公开**：无法评估实际部署门槛。
- **潜在偏差风险**：软标记优化可能对特定提示分布敏感，未见对不同提示分布或长提示的鲁棒性分析。
- **对比实验的公平性细节不足**：未说明与 SoftREPA 和 RL 方法的统一训练超参、迭代次数等，可能影响结论的客观性。

（完）
