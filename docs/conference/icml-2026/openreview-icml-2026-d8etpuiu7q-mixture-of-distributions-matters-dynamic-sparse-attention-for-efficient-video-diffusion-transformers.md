---
title: "Mixture of Distributions Matters: Dynamic Sparse Attention for Efficient Video Diffusion Transformers"
title_zh: 分布混合至关重要：面向高效视频扩散Transformer的动态稀疏注意力
authors: "Yuxi Liu, Yipeng Hu, Zekun Zhang, Kunze Jiang, Kun Yuan"
date: 2026-04-30
pdf: "https://openreview.net/pdf/93c5eb555432210e060fd09b84ab065a7219bba5.pdf"
tags: ["query:diff-video"]
score: 8.0
evidence: 面向高效视频扩散Transformer的动态稀疏注意力
tldr: 视频扩散Transformer因自注意力二次复杂度难以部署，现有稀疏注意力要么模式过于静态，要么动态采样代价高。本文提出MOD-DiT，用混合分布刻画注意力模式的动态变化，在避免昂贵采样的同时得到准确的动态稀疏掩码。该方法在降低计算开销的同时保持生成质量，为长视频DiT的高效推理提供了实用方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 视频DiT自注意力二次复杂度阻碍实际部署，现有稀疏注意力或模式静态或采样开销高。
method: 提出MOD-DiT，基于分布混合建模注意力模式，实现无需昂贵采样的动态稀疏注意力。
result: 在保证视频生成质量的同时显著降低注意力计算成本。
conclusion: 为长序列视频扩散模型提供高效且准确的稀疏注意力方案。
---

## Abstract
While Diffusion Transformers (DiTs) have achieved notable progress in video generation, this long-sequence generation task remains constrained by the quadratic complexity inherent to self-attention mechanisms, creating significant barriers to practical deployment. Although sparse attention methods attempt to address this challenge, existing approaches either rely on oversimplified static patterns or require computationally expensive sampling operations to achieve dynamic sparsity, resulting in inaccurate pattern predictions and degraded generation quality. To overcome these limitations, we propose a \underline{\textbf{M}}ixture-\underline{\textbf{O}}f-\underline{\textbf{D}}istribution \textbf{DiT} (\textbf{MOD-DiT}), a novel sampling-free dynamic attention framework that accurately models evolving attention patterns through a two-stage process. First, MOD-DiT leverages prior information from early denoising steps and adopts a {distributed mixing approach} to model an efficient linear approximation model, which is then used to predict mask patterns for a specific denoising interval. Second, an online block masking strategy dynamically applies these predicted masks while maintaining historical sparsity information, eliminating the need for repetitive sampling operations. Extensive evaluations demonstrate consistent acceleration and quality improvements across multiple benchmarks and model architectures, validating MOD-DiT's effectiveness for efficient, high-quality video generation while overcoming the computational limitations of traditional sparse attention approaches.

---

## 论文详细总结（自动生成）

# 中文论文总结：MOD-DiT

## 1. 论文的核心问题与整体含义（研究动机与背景）

- **核心问题**：视频扩散 Transformer（DiT）虽在视频生成上表现优异，但自注意力机制的**二次计算复杂度**（quadratic complexity）使其在处理长序列视频生成任务时计算开销巨大，严重阻碍了实际部署。
- **现有方法的不足**：
  - 现有稀疏注意力方法要么依赖**过度简化的静态模式**（static patterns），无法适应注意力随生成过程动态变化的特性，导致模式预测不准确；
  - 要么需要通过**计算昂贵的采样操作**来获得动态稀疏性，额外开销大，生成质量也受影响。
- **研究目标**：提出一种既能准确建模注意力动态变化、又无需高昂采样代价的高效稀疏注意力框架，在降低计算成本的同时保持视频生成质量。

## 2. 论文提出的方法论

- **整体框架**：提出 **MOD-DiT（Mixture-of-Distribution DiT）**，一种**无采样（sampling-free）的动态稀疏注意力机制**。
- **核心思想**：利用**分布混合（distributed mixing / mixture-of-distribution）** 方式建模注意力模式的演变过程，在避免重复采样操作的前提下，精确预测动态稀疏掩码。
- **两阶段流程（算法思路，文字描述）**：
  1. **第一阶段：掩码模式预测**
     - 利用**早期去噪步骤**（early denoising steps）中获得的先验信息；
     - 采用**分布式混合方法**构建一个高效的**线性近似模型**；
     - 用该模型预测某个特定去噪区间（denoising interval）内的注意力掩码模式。
  2. **第二阶段：在线块掩码策略（online block masking strategy）**
     - 动态应用第一阶段预测出的掩码；
     - 同时**维护历史稀疏性信息**（maintaining historical sparsity information）；
     - 从而避免了生成过程中反复进行采样操作，显著降低计算开销。
- **关键优势**：无需昂贵采样即可获得动态稀疏性，兼顾了模式准确性（优于静态模式）与计算效率（优于采样式动态方法）。

## 3. 实验设计

- **Benchmark 与数据集**：文中摘要仅提及在**多个基准（multiple benchmarks）** 和**多种模型架构（multiple model architectures）** 上进行了评估，但**未列出具体的数据集名称**（如 UCF-101、Kinetics、MSR-VTT 等均未提及）。
- **对比方法**：文中未逐一列出具体的基线方法，但从问题定位推断，对比对象应涵盖**静态稀疏注意力方法**与**动态采样式稀疏注意力方法**（具体名称未给出）。
- **评估指标**：文中未明确列出具体指标（如 FVD、IS、PSNR、FLOPs 等），仅以“consistent acceleration and quality improvements”概括性地说明效果。
- **评测维度**：同时涉及**加速效果**（计算效率）与**生成质量**两个方面。

> ⚠️ 由于该论文可获取的文本仅包含摘要部分，**“实验设计”的完整细节（具体数据集、评估指标、对比方法列表）在现有文本中并未披露**，上述内容均基于摘要可确认的信息进行概括。

## 4. 资源与算力

- **文中未提及任何具体算力信息**，包括：
  - GPU 型号（如 A100、H100 等）；
  - GPU 数量；
  - 训练时长 / 推理加速的具体数值。
- 因此，**无法评估该方法的计算开销是否具有实际部署优势**，只能依据作者自述“显著降低注意力计算成本”作为参考。

## 5. 实验数量与充分性

- **实验数量**：摘要仅笼统提到“广泛的评估”（Extensive evaluations），**未提供实验组数、消融实验数量、各 benchmark 的详细结果表**。
- **未披露的关键实验信息**：
  - 是否做了消融实验（如验证线性近似模型精度、掩码预测阶段的影响等）；
  - 是否在不同视频长度、不同分辨率下测试；
  - 与基线方法的定量对比数值。
- **充分性判断**：
  - 从现有文本看，实验规模与严谨性**无法客观评估**；
  - 作者声称的“多个基准和模型架构”仅是定性描述，缺少可验证的定量证据；
  - 鉴于该论文已被 ICML 2026 接收且评分 8.0，推测完整论文中应包含较系统的实验，但在当前提取文本范围内无法确认。

## 6. 论文的主要结论与发现

- **主要结论**：MOD-DiT 在**多个基准和多种模型架构上**都实现了**一致的加速效果与质量提升**。
- **具体发现**：
  - 通过分布式混合建模可以准确刻画注意力模式的**动态演变**；
  - 结合在线块掩码策略，可在**避免重复采样**的条件下保持历史稀疏信息；
  - 证明该方案能有效解决传统稀疏注意力方法在**模式准确性**与**计算开销**之间的权衡问题。
- **整体定位**：为长序列视频扩散模型提供了一种**既高效又准确**的稀疏注意力方案，推进了 DiT 在实际视频生成场景中的可用性。

## 7. 优点

- **方法创新性强**：将注意力模式的演变建模为“分布混合”问题，视角新颖，从理论上绕开了采样式动态稀疏的高昂代价。
- **无采样动态注意力**：同时克服了静态模式“不准确”和动态采样“高成本”两个痛点，属于方法论上的有效折中。
- **利用先验信息**：借助早期去噪步的先验，避免对每个时间步都重新计算注意力模式，设计上符合扩散模型的迭代特性。
- **在线块掩码策略**：通过维护历史稀疏性信息，兼顾了局部动态变化与长期结构稳定性，设计合理。
- **验证范围较广**：在多个基准与模型架构上验证，说明方法具有一定泛化能力（以作者自述为准）。

## 8. 不足与局限

- **信息完整度不足**：当前可获取文本仅有摘要，**缺乏实验细节、方法公式、算法伪代码及定量结果**，难以全面评估方法的实际效果与可靠性。
- **实验细节缺失**：
  - 未给出具体数据集、评估指标、基线方法的明确清单；
  - 未提供加速比、FLOPs 降低幅度、生成质量指标数值等硬性数据；
  - 消融实验情况完全未知。
- **算力信息缺失**：未报告训练/推理所需的 GPU 资源与时间成本，实际部署门槛不明。
- **潜在应用限制**：
  - 方法依赖“早期去噪步骤的先验信息”，对不同的去噪调度器或步数较少的推理场景，其适用性有待验证；
  - “块掩码”（block masking）策略可能会牺牲细粒度的 token 级稀疏性，在部分高质量生成要求下可能出现精度损失；
  - 当前验证范围虽声称“多基准”，但未披露是否包含极端长视频或高分辨率场景。
- **客观性风险**：目前所有结论都建立在作者自述层面，缺少同行可核查的实验证据，存在一定的**宣称与证据不匹配**风险。

---

（完）
