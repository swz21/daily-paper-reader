---
title: "Quant VideoGen: Auto-Regressive Long Video Generation via 2-Bit KV-Cache Quantization"
title_zh: Quant VideoGen：通过2比特KV缓存量化实现自回归长视频生成
authors: "Haocheng Xi, Shuo Yang, Yilong Zhao, Muyang Li, Han Cai, Xingyang Li, Yujun Lin, Zhuoyang Zhang, Jintao Zhang, Xiuyu Li, Zhiying Xu, Jun Wu, Chenfeng Xu, Ion Stoica, Song Han, Kurt Keutzer"
date: 2026-04-30
pdf: "https://openreview.net/pdf/4d8cbd9ba2bf9986c926e4d5303604e8beb2ccbc.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 基于自回归视频扩散的长视频生成
tldr: 自回归视频扩散模型在长视频生成中面临KV缓存内存瓶颈，限制部署和长时一致性。作者提出Quant VideoGen，一种无需训练的KV缓存量化框架，利用视频时空冗余将KV缓存压缩至2比特，缓解显存压力并提升长视频生成的部署效率。实验表明该方法在保持生成质量的同时显著降低内存占用，促进了自回归视频扩散模型在通用硬件上的应用。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 自回归视频扩散模型的KV缓存随生成历史增长，占用大量GPU内存，制约部署并损害长时一致性。
method: 提出Quant VideoGen，利用视频时空冗余对KV缓存进行2比特训练后量化，减少内存占用。
result: 在不影响生成质量的情况下大幅降低KV缓存内存，提升长视频生成的可用性。
conclusion: 为自回归视频扩散模型提供了系统级优化方案，推动了长视频生成的实用化部署。
---

## Abstract
Despite rapid progress in auto-regressive video diffusion, we identify an emerging system–algorithm bottleneck that limits both deployability and generation quality: KV-cache memory. In auto-regressive video generation models, the KV-cache grows with generation history and quickly dominates GPU memory (often ≥30 GB), preventing deployment on widely available hardware. More critically, memory-bounded KV budgets constrain the effective working memory, directly degrading long-horizon consistency in identity, layout, and motion.
To address this challenge, we present Quant VideoGen (QVG), a training-free KV-cache quantization framework for auto-regressive video diffusion models. QVG exploits video’s inherent spatiotemporal redundancy via Semantic-Aware Smoothing, producing low-magnitude, quantization-friendly residuals. Building on this, QVG introduces Progressive Residual Quantization, a coarse-to-fine multi-stage scheme that further reduces quantization error while enabling a smooth quality–memory trade-off.
Across LongCat-Video, HY-WorldPlay, and Self-Forcing, QVG establishes a new Pareto frontier between quality and memory efficiency, reducing KV memory by up to 7.0× with less than 4% end-to-end latency overhead, while delivering significantly better generation quality than existing baselines.

---

## 论文详细总结（自动生成）

## 论文详细中文总结

### 1. 核心问题与整体含义（研究动机与背景）
- **问题定义**：自回归视频扩散模型在长视频生成方面取得显著进展，但存在一个新兴的“系统-算法”双重瓶颈——KV缓存（Key-Value Cache）内存。
- **具体表现**：
  - 在自回归视频生成中，KV缓存随生成历史长度线性增长，很快占据GPU主导内存（通常 ≥30 GB），导致模型难以部署在普通商用硬件上。
  - 内存受限的KV预算会压缩模型的有效“工作记忆”，直接损害长视频生成中**身份、布局、运动**等维度上的长时序一致性。
- **整体含义**：KV缓存问题同时制约了系统的可部署性与生成质量，是长视频生成走向实际应用的关键障碍，亟需系统级优化方案。

---

### 2. 方法论（核心思想与关键实现）
- **总体方案**：提出 **Quant VideoGen (QVG)**，一个**无需训练（Training-free）**的KV缓存量化框架，专门针对自回归视频扩散模型设计。
- **核心技术一：语义感知平滑（Semantic-Aware Smoothing）**
  - 核心思想：利用视频数据固有的**时空冗余**（spatiotemporal redundancy）。
  - 实现原理：通过平滑操作将原始键值信息转化为**低幅度、量化友好**的残差表示，降低量化的难度与误差。
- **核心技术二：渐进残差量化（Progressive Residual Quantization）**
  - 核心思想：采用**从粗到细（coarse-to-fine）**的多阶段量化策略。
  - 实现效果：在多个量化阶段之间逐步逼近原始分布，进一步降低累积量化误差。
  - 附加优势：支持**平滑的“质量-内存”权衡**控制，用户可以根据硬件条件灵活调节压缩率。
- **算法流程概述（文字化描述）**：
  1. 对KV缓存进行语义感知平滑，生成低幅度残差；
  2. 对残差执行多阶段渐进式量化，由粗粒度到细粒度逐级修正；
  3. 在量化精度与内存占用之间形成可调节的权衡曲线，完成2比特（2-bit）级别的极低比特量化。

---

### 3. 实验设计
- **基准与场景**：
  - 在三个基准上开展验证：**LongCat-Video**、**HY-WorldPlay**，以及**Self-Forcing**。
  - 覆盖不同视频生成任务/模型场景，用于验证方法的普适性。
- **对比方法**：
  - 与现有的基线方法（baselines）进行生成质量对比，强调QVG在相同或更低内存条件下具有更好的生成质量。
- **评估指标**：
  - KV缓存内存缩减倍数（最高达 **7.0×**）。
  - 端到端延迟开销（< 4%）。
  - 生成质量对比（优于现有基线）。

---

### 4. 资源与算力
- **明确信息缺失**：论文摘要及当前提供的元数据中**未明确说明**所使用的GPU型号、数量、训练时长或推理硬件配置。
- **推断信息**：作为“无需训练”的量化框架，其核心优势在于**推理阶段内存压缩**，通常不需要大量额外算力进行训练；但具体实验硬件环境仍需原文正文补充。

---

### 5. 实验数量与充分性
- **实验覆盖**：在3个不同基准/数据集上进行了评估，覆盖了多种视频生成设定。
- **指标维度**：同时关注**内存效率（KV缩减）**、**时延开销**和**生成质量（用户/自动指标）**，构成较完整的评估体系。
- **充分性评估**：
  - 优点：多基准验证 + 多个评估维度，初步具备客观性与对比性；尤其是Pareto前沿的建立，说明了方法在不同内存阈值下都能保持最优或接近最优的质量。
  - 不足：当前摘要文本未展示**详细的消融实验**（如对语义感知平滑/渐进量化的单独贡献分析），也未报告具体数值表格。原文可能有更详尽的实验，但就提供的信息而言，可获取的实验细节有限。

---

### 6. 主要结论与发现
- QVG成功在**生成质量与KV缓存内存**之间建立了一条新的 **Pareto 前沿（Pareto frontier）**，即在不同内存预算下均能取得较现有方法更好的质量表现。
- 在保持生成质量的前提下，实现了**最高 7.0× 的KV内存压缩**，同时带来**不超过 4%** 的端到端延迟开销。
- 相比现有基线，QVG在相同或更高压缩率下的生成质量显著更优，证明了训练后量化在视频扩散模型上的可行性和优越性。

---

### 7. 优点与亮点
- **无需训练**：部署友好，直接应用到现成的自回归视频扩散模型，无需重新训练或微调。
- **利用视频先验**：创新性地将视频的**时空冗余**引入量化设计，而非笼统地对KV进行统一处理，方法更具针对性和有效性。
- **渐进式量化机制**：多阶段、粗到细的量化设计既降低误差，又提供了**可调节的权衡开关**，灵活适配不同显存资源的硬件环境。
- **系统性优化视角**：从“系统-算法”协同的角度解决KV缓存瓶颈，兼顾部署成本与视频质量，实用价值高。
- **Pareto前沿表现**：能同时在多个指标（内存、质量、延迟）上占据优势，实验边界的构建具有说服力。

---

### 8. 不足与局限
- **实验细节有限**：摘要中未报告具体量化比特配置、不同压缩率下的详细指标，以及每个数据集的量化对比表格，影响完全复现和深入评估。
- **消融信息缺失**：未单独展示语义感知平滑与渐进量化各自贡献度的消融实验（至少在提供材料中未体现）。
- **硬件资源披露不足**：未说明实验所用GPU型号、数量、批量大小、推理时间等关键配置，无法判断其普适性与代价。
- **适用域边界**：验证范围主要为自回归视频扩散模型；对非自回归模型、图像扩散模型或其他Transformer结构视频模型的迁移能力尚未在摘要中体现。
- **长视频长度上限**：未明确说明在极端长视频（如数分钟或更长）下KV压缩对内容一致性的维持效果，存在评估范围覆盖不足的风险。

---

（完）
