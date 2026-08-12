---
title: "Flash-VAED: Plug-and-Play VAE Decoders for Efficient Video Generation"
title_zh: Flash-VAED：用于高效视频生成的即插即用VAE解码器
authors: "Lunjie Zhu, Yushi Huang, Xingtong Ge, Yufei Xue, Zhening Liu, Yumeng Zhang, Zehong Lin, Jun Zhang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/640e4965eaee9770a919857c4c7495253d5313c8.pdf"
tags: ["query:diff-video"]
score: 8.0
evidence: 面向潜空间扩散视频生成的VAE解码器加速
tldr: 潜在扩散模型可生成高质量视频，但随着扩散Transformer变快，VAE解码器成为延迟瓶颈。文章提出Flash-VAED，用独立感知通道剪枝缓解通道冗余，并用阶段式主运算优化处理因果3D卷积的高成本，构建即插即用解码器。它保持与原始潜分布对齐，显著降低解码耗时，可通用加速视频生成流程。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 潜空间扩散模型实现高质量视频合成，但推理成本高，且VAE解码器成为新的延迟瓶颈。
method: 提出通用VAE解码器加速框架，包括独立感知通道剪枝和阶段式主运算优化，并构建Flash-VAED解码器。
result: 在保持与原始潜分布对齐和质量的同时显著降低VAE解码时延。
conclusion: 证明通过剪枝和算子优化可大规模加速视频生成中的VAE解码环节。
---

## Abstract
Latent diffusion models have enabled high-quality video synthesis, yet their inference remains costly and time-consuming. As diffusion transformers become increasingly efficient, the latency bottleneck inevitably shifts to VAE decoders. To reduce their latency while maintaining quality, we propose a universal acceleration framework for VAE decoders that preserves full alignment with the original latent distribution. Specifically, we propose (1) an *independence-aware channel pruning* method to effectively mitigate severe channel redundancy, and (2) a *stage-wise dominant operator optimization* strategy to address the high inference cost of the widely used causal 3D convolutions in VAE decoders. Based on these innovations, we construct a **Flash-VAED** family. Moreover, we design a *three-phase dynamic distillation* framework that efficiently transfers the capabilities of the original VAE decoder to Flash-VAED. Extensive experiments on Wan and LTX-Video VAE decoders demonstrate that our method outperforms baselines in both quality and speed, achieving approximately a **6$\times$ speedup** while maintaining the reconstruction performance up to **96.9%**. Notably, Flash-VAED accelerates the end-to-end generation pipeline by up to **36%** with negligible quality drops on VBench-2.0. Our code is available at https://github.com/Aoko955/Flash-VAED.

---

## 论文详细总结（自动生成）

# Flash-VAED：用于高效视频生成的即插即用VAE解码器 — 论文总结

## 1. 核心问题与整体含义

- **研究动机**：潜空间扩散模型（Latent Diffusion Models）已成为高质量视频合成的主流方法，但其推理成本高、耗时长。随着扩散Transformer（Diffusion Transformers）的推理效率不断提升，**VAE解码器逐渐取代扩散模型本身，成为视频生成管线中的新延迟瓶颈**。
- **核心问题**：如何在保持视频重建质量的前提下，显著降低VAE解码器的推理延迟，从而加速整个端到端视频生成流程。
- **整体含义**：论文提出一种**通用的、即插即用**的VAE解码器加速框架——Flash-VAED，在不改变原始潜空间分布对齐的情况下，大幅降低解码耗时，可泛化应用于多种视频生成系统。

## 2. 方法论

- **核心思想**：Flash-VAED 从两个角度解决VAE解码器的效率问题：
  1. **通道冗余严重**：通过剪枝方法移除冗余通道。
  2. **因果3D卷积计算成本高**：对高频使用的算子进行优化。
- **关键技术细节**：
  - **独立感知通道剪枝（Independence-Aware Channel Pruning）**：有效缓解VAE解码器中严重的通道冗余，剪枝过程保持通道间的独立性感知，以最小化信息损失。
  - **阶段式主运算优化（Stage-wise Dominant Operator Optimization）**：针对VAE解码器中广泛使用的因果3D卷积，按阶段识别并优化主导算子，降低其推理开销。
  - **三阶段动态蒸馏（Three-Phase Dynamic Distillation）**：设计了一个三阶段的动态蒸馏框架，用于将原始VAE解码器的能力高效迁移至Flash-VAED，确保剪枝和算子优化后的解码器仍与原始潜空间分布保持对齐。
- **最终产物**：基于上述创新构建了Flash-VAED解码器系列，可即插即用地替换现有VAE解码器。

## 3. 实验设计

- **评测目标**：在两个公开视频VAE解码器上验证效果：
  - **Wan VAE解码器**
  - **LTX-Video VAE解码器**
- **对比方法**：与基准方法（baselines）在**质量和速度**两个维度上对比。
- **评价指标**：
  - 重建性能（质量指标，如重建保真度，文中以百分比形式给出）。
  - 解码速度（加速倍数）。
  - 端到端生成管线的整体加速效果，使用 **VBench-2.0** 评估质量下降情况。
- **主要结果**：
  - 相比基线，Flash-VAED在质量和速度上均表现更优。
  - 实现约 **6倍解码速度提升**，同时保持 **96.9%** 的重建性能。
  - 在VBench-2.0上，端到端生成管线加速最高达 **36%**，且质量下降可忽略不计。

## 4. 资源与算力

- **原文未明确说明**：论文提供的摘要和元数据中**没有提及具体GPU型号、数量、训练时长、显存占用等算力资源信息**。
- 因此，无法从现有材料中总结训练或推理的硬件配置细节。

## 5. 实验数量与充分性

- **实验数量**：从摘要中可知至少包含：
  - 两个不同VAE解码器（Wan、LTX-Video）上的验证实验。
  - 与基线方法的对比实验。
  - 端到端生成管线的加速评估（VBench-2.0）。
  - 具体的性能量化（6倍加速、96.9%重建保持率、36%端到端加速）。
- **充分性评估**：
  - 覆盖了两个主流视频VAE架构，具有一定代表性。
  - 不过现有提取信息**缺少对消融实验、不同剪枝比例、蒸馏阶段细节、更多数据集/任务（如图文生成、长视频等）的详细描述**。
  - 由于信息有限，无法充分判断实验是否覆盖了所有关键变量，以及是否对所有基线的超参数进行了公平调优。从摘要表述看，方法在一致条件下优于基线，但具体公平性细节需查阅全文。

## 6. 主要结论与发现

- Flash-VAED作为一种通用加速框架，可显著降低视频生成中VAE解码器的延迟。
- 通过**独立感知通道剪枝**和**阶段式主运算优化**，在保持与原始潜空间分布对齐的同时，实现约6倍解码加速。
- 该加速能有效传递至整个视频生成流程，端到端提速最高36%，且重建质量和视频生成质量（VBench-2.0）仅有可忽略的下降。
- 证明剪枝与算子优化是可行的VAE解码器加速路径，且即插即用的特性使其易于集成到现有视频生成系统中。

## 7. 优点

- **即插即用**：Flash-VAED可直接替换现有VAE解码器，无需修改扩散模型本身，实用性强。
- **通用性**：框架不针对单一VAE架构，在Wan和LTX-Video两种不同解码器上均验证有效。
- **性能优异**：6倍解码加速和96.9%重建保持率是很有竞争力的结果。
- **端到端收益显著**：不仅改进单模块速度，还带来36%的整体流程加速，且质量损失可忽略。
- **方法论清晰**：将剪枝与算子优化结合，并设计三阶段动态蒸馏来保持分布对齐，思路完整。

## 8. 不足与局限

- **信息不完整**：现有材料中缺少对方法细节（如剪枝比例、蒸馏各阶段具体操作）的完整描述，难以完全复现。
- **算力资源未披露**：没有给出GPU规格、训练时间等，影响对可复现性的判断。
- **实验覆盖有限**：仅涉及两个VAE解码器，未展示在更多视频生成模型、不同分辨率/帧率/长视频场景下的表现。
- **消融实验缺失**：未明确报告各创新组件（剪枝、算子优化、三阶段蒸馏）的独立贡献，无法判断每个模块的具体必要性。
- **潜在偏差风险**：由于对比基线和超参数设置的细节未完全给出，公平性需全文审阅；另外加速收益可能与具体硬件上的算子库优化有关，通用性仍需更广泛验证。
- **质量指标单一**：重建性能用“96.9%”概括，未说明具体指标（如PSNR、SSIM、LPIPS或FVD），可能限制与其他方法的直接比较。

（完）
