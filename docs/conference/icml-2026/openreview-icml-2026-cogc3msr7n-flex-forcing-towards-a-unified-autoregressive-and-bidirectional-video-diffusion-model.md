---
title: "Flex-Forcing: Towards a Unified Autoregressive and Bidirectional Video Diffusion Model"
title_zh: Flex-Forcing：统一自回归与双向的视频扩散模型
authors: "Xinyin Ma, Julius Berner, Chao Liu, Arash Vahdat, Weili Nie, Xinchao Wang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/fb09bc662aa5fe48966115b38afe6de5901f4cc7.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 面向视频生成的双向与自回归统一扩散模型
tldr: 现有视频扩散模型往往只能采用双向或自回归一种推理方式，双向生成质量高但速度慢，自回归生成快但长程一致性不足。为此，Flex-Forcing 提出一种在时间轴与去噪步骤上联合定义的灵活分块机制，允许同一视频扩散模型在双向与自回归两种模式下训练和推理。该方法兼顾全局语义连贯与流式高效生成，为视频扩散模型提供统一的生成框架。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有视频扩散模型推理模式固定，双向与自回归各有局限，缺乏统一框架。
method: 提出灵活分块机制，联合时间轴与去噪步骤，训练统一的双向/自回归视频扩散模型。
result: 在保持全局连贯的同时显著提升视频生成效率，支持灵活的流式生成。
conclusion: 统一双向与自回归范式，为视频扩散模型提供新的推理和训练框架。
---

## Abstract
Recent progress in large-scale generative models has substantially advanced video generation, yet existing methods remain constrained by a rigid inference paradigm. Bidirectional diffusion models excel at global coherence and visual fidelity but suffer from slow inference, while autoregressive models offer efficient and streaming generation at the cost of long-range consistency and exposure bias. We introduce Flex-Forcing, a unified training and inference framework that enables a video diffusion model to seamlessly operate under both bidirectional and autoregressive generation regimes. The core idea is a flexible chunking mechanism jointly defined over the temporal axis and denoising steps. This design allows the model to (1) perform flexible chunking according to different device budgets, (2) perform bidirectional inference across chunks for global structure planning, while generating frames autoregressively within each chunk for efficient and fine-grained synthesis, and (3) perform any-order, any-timestep autoregressive generation without the strict causal constraint. Extensive experiments on multiple video generation benchmarks demonstrate that Flex-Forcing achieves consistently better video quality, long-video stability than strong baselines with a rigid inference schedule, while offering faster inference.

---

## 论文详细总结（自动生成）

# Flex-Forcing：统一自回归与双向的视频扩散模型——论文总结

## 1. 核心问题与整体含义

- **研究背景**：近年来大规模生成模型推动了视频生成的快速发展，但现有视频扩散模型在推理范式上存在刚性约束，无法同时兼顾质量与效率。
- **现有方法的矛盾**：
  - **双向扩散模型**：擅长全局一致性（global coherence）和视觉保真度，但推理速度慢。
  - **自回归模型**：支持高效、流式生成，但长程一致性差，且存在暴露偏差（exposure bias）。
- **核心问题**：能否在**同一个模型**中统一双向与自回归两种生成范式，从而兼顾全局语义规划与高效的流式生成？
- **整体意义**：Flex-Forcing 提出了一种统一的训练与推理框架，打破了视频扩散模型只能固定采用一种推理模式的限制，为视频生成提供了一条新的技术路径。

## 2. 方法论

- **核心思想**：在时间轴（temporal axis）与去噪步骤（denoising steps）上**联合定义**一个灵活的分块机制（flexible chunking mechanism），使得同一个视频扩散模型可以同时支持双向和自回归两种模式。
- **关键技术细节**：
  1. **灵活的块划分**：根据设备预算（device budget）动态调整分块的大小与边界；
  2. **跨块双向推理**：在不同块之间执行双向推理，用于全局结构规划；
  3. **块内自回归生成**：在每个块内部，以自回归方式逐帧生成，以便高效、细粒度的合成；
  4. **任意顺序、任意时间步的自回归生成**：模型不再受严格因果约束（strict causal constraint）的限制，可以在去噪过程中的任意时间步、以任意顺序生成帧。
- **公式/算法流程**：原文摘要中未提供具体的数学公式与算法伪代码。可以理解为：将视频序列沿时间轴切分为多个块，每个块内部执行自回归式帧预测，块之间采用双向注意力进行全局协调；同时结合去噪时间步的分块设计，使训练和推理过程统一在同一个模型框架下完成。

## 3. 实验设计

- **基准（Benchmark）**：在**多个视频生成基准**（multiple video generation benchmarks）上进行了实验评估。摘要未列出具体数据集名称。
- **对比方法**：与采用**刚性推理调度（rigid inference schedule）的强基线模型**进行了对比。摘要未点名具体基线模型。
- **评估维度**：
  - 视频质量；
  - 长视频稳定性；
  - 推理速度。

## 4. 资源与算力

- **原文未明确说明**使用的 GPU 型号、数量、训练时长等算力资源信息。
- 由于本文为 ICML-2026 接收论文，完整版可能在正文或附录中有所披露，但基于当前提供的摘要文本，无法获取具体算力配置。

## 5. 实验数量与充分性

- **实验数量**：摘要仅笼统提到“多个视频生成基准”上的实验，未提供具体实验组数和消融实验细节。
- **充分性评估**：
  - **优点**：实验覆盖了视频质量、长视频稳定性、推理速度三个关键维度，对比对象为刚性推理调度的强基线，方向合理。
  - **不足**：由于缺乏具体的数据集名称、基线方法名称以及消融实验细节，**无法从摘要层面判断实验的全面性与公平性**。完整的实验设计、统计显著性和消融分析需要在全文正文中进一步确认。

## 6. 主要结论与发现

- Flex-Forcing 在**视频质量**和**长视频稳定性**上持续优于采用刚性推理调度的强基线模型；
- 同时实现了**更快的推理速度**；
- 统一的训练与推理框架能够在双向生成与自回归生成之间无缝切换，兼具全局结构与流式高效的优点。

## 7. 优点

- **范式统一性**：首次在同一个视频扩散模型中统一双向与自回归两种生成范式，打破现有方法的刚性推理限制。
- **灵活性与适配性**：支持根据设备预算灵活调整分块策略，适应不同推理场景。
- **全局与局部兼顾**：跨块双向推理保证全局语义一致，块内自回归生成提升效率并支持细粒度合成。
- **摆脱严格因果约束**：支持任意顺序、任意去噪时间步的自回归生成，在理论上具有更强的表述能力和生成自由度。

## 8. 不足与局限

- **信息不完整**：提供的摘要文本信息有限，无法获取数据集名称、基线细节、消融实验等关键实验信息，难以充分评估方法的实际效果和泛化能力。
- **算力资源不明**：正文未披露 GPU 型号与数量、训练时长等关键资源信息，不利于复现和成本评估。
- **实验充分性待验证**：目前只能确认“多个基准”上的评估，具体实验的覆盖面（如不同分辨率、不同视频长度、不同场景类别）和消融研究的深度尚不明确。
- **潜在偏差风险**：摘要中“一致更优”的表述缺乏具体量化指标支撑（如 FVD、IS 等客观指标），可能存在主观报告偏差。
- **应用限制**：虽然提出任意顺序生成，但实际推理效率与质量在不同分块配置下的平衡关系仍有待深入研究。

（完）
