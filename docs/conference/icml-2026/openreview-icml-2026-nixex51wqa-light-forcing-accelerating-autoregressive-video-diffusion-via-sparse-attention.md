---
title: "Light Forcing: Accelerating Autoregressive Video Diffusion via Sparse Attention"
title_zh: Light Forcing：通过稀疏注意力加速自回归视频扩散
authors: "Chengtao Lv, Yumeng Shi, Yushi Huang, Ruihao Gong, Shen Ren, Wenya Wang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/c904c5c39446f5830c99ecb19444635fc200a5cc.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 面向自回归视频扩散模型的稀疏注意力加速
tldr: 自回归视频生成模型受注意力二次复杂度制约，而现有稀疏注意力方法直接应用会导致性能退化。本文提出Light Forcing，首个面向自回归视频扩散模型的稀疏注意力方案，通过Chunk-Aware Growth机制定量估计贡献并充分利用过去的历史上下文信息。实验表明该方法能够在不牺牲视频质量的前提下显著加速生成，推动了高效视频扩散部署。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 自回归视频扩散模型的注意力二次复杂度成为部署瓶颈，现有稀疏注意力不适用于自回归生成。
method: 提出Chunk-Aware Growth机制，结合历史上下文信息设计面向自回归视频模型的稀疏注意力。
result: 在保持视频质量的同时显著降低了注意力计算开销。
conclusion: Light Forcing是首个针对自回归视频扩散模型的高效稀疏注意力方案。
---

## Abstract
Advanced autoregressive (AR) video generation models have improved visual fidelity and interactivity, but the quadratic complexity of attention remains a primary bottleneck for efficient deployment. While existing sparse attention solutions have shown promise on bidirectional models, we identify that applying these solutions to AR models leads to considerable performance degradation for two reasons: isolated consideration of chunk generation and insufficient utilization of past informative context. Motivated by these observations, we propose \textsc{Light Forcing}, the \textit{first} sparse attention solution tailored for AR video generation models. It incorporates a \textit{Chunk-Aware Growth} mechanism to quantitatively estimate the contribution of each chunk, which determines their sparsity allocation. This progressive sparsity increase strategy enables the current chunk to inherit prior knowledge in earlier chunks during generation. Additionally, we introduce a \textit{Hierarchical Sparse Attention} to capture informative historical and local context in a coarse-to-fine manner. Such two-level mask selection strategy (i.e., frame and block level) can adaptively handle diverse attention patterns. Extensive experiments demonstrate that our method outperforms existing sparse attention in quality (e.g., 84.5 on VBench) and efficiency (e.g., $1.2{\sim}1.3\times$ end-to-end speedup).     Combined with other efficient solutions, \textsc{Light Forcing} further achieves a $2.0{\sim}3.0\times$ end-to-end speedup across diverse GPUs (e.g., 27.4\,FPS on RTX 5090 and 33.9\,FPS on H100). Code is released via this \href{https://github.com/chengtao-lv/LightForcing}{link}.

---

## 论文详细总结（自动生成）

# 论文总结：Light Forcing——通过稀疏注意力加速自回归视频扩散

## 1. 核心问题与整体含义（研究动机与背景）

- 自回归（AR）视频生成模型近年来在视觉保真度和交互性上取得了显著进步，但其注意力机制的 **二次复杂度（quadratic complexity）** 成为高效部署的主要瓶颈。
- 已有稀疏注意力方法在双向（bidirectional）模型上表现良好，但直接迁移到自回归视频生成模型上会导致明显的性能退化。
- 论文识别出两个关键原因：
  1. **孤立看待 chunk 生成**：现有方法没有考虑自回归生成中不同 chunk 之间的依赖关系；
  2. **历史上下文利用不足**：过去已生成的信息（informative context）没有被充分利用。
- 基于此，论文提出 **Light Forcing**，这是**首个专门为自回归视频扩散模型设计的稀疏注意力方案**，旨在不牺牲视频质量的前提下显著加速生成，推动高效视频扩散的实际部署。

## 2. 方法论：核心思想、关键技术细节

- **总体思路**：通过稀疏注意力减少计算量，同时针对自回归视频生成的特点设计掩码策略，使模型能够继承历史信息并保持生成质量。
- **Chunk-Aware Growth 机制**：
  - 定量估计每个 chunk 对最终生成的贡献，并据此分配不同的稀疏度。
  - 采用 **渐进式稀疏度增长策略**，即越早的 chunk 分配越高（或越低，视具体实现）的稀疏度，使当前 chunk 能够继承早期 chunk 中已有的先验知识。
  - 这样避免了将每个 chunk 孤立看待的问题，使稀疏分配与自回归生成过程对齐。
- **Hierarchical Sparse Attention（层次化稀疏注意力）**：
  - 以 **从粗到细（coarse-to-fine）** 的方式捕获信息丰富的历史上下文与局部上下文。
  - 采用 **两级掩码选择策略：帧级（frame level）和块级（block level）**，从而自适应地处理多样化的注意力模式。
- 整体算法流程（文字描述）：
  1. 对输入视频序列按 chunk 划分；
  2. 通过 Chunk-Aware Growth 计算每个 chunk 的重要性/贡献，确定其稀疏度分配；
  3. 在注意力计算时，使用层次化稀疏注意力：先在帧级筛选重要帧，再在块级筛选重要 token 块；
  4. 生成当前 chunk 时，利用被保留的历史上下文和局部上下文进行自回归预测，逐步生成整个视频。

## 3. 实验设计：数据集、Benchmark、对比方法

- **Benchmark**：论文报告了 **VBench** 上的质量评估结果（84.5 分），VBench 是视频生成常用的综合评测基准。
- **效率评估**：报告了端到端加速比，以及在不同 GPU 上的生成速度（FPS）。
- **对比方法**：主要与 **现有稀疏注意力方法** 进行对比，强调了它们在自回归模型上的性能退化，而 Light Forcing 在质量和效率上均优于它们。
- **额外组合实验**：Light Forcing 还与其他高效方案（如蒸馏、量化等，摘要未指明具体技术）结合，评估组合加速效果。
- **硬件场景**：提到 RTX 5090 和 H100 等不同 GPU 上的端到端速度。

## 4. 资源与算力

- 论文摘要和元数据中 **未明确说明** 所使用的 GPU 数量、训练时长、模型参数量等具体算力资源。
- 仅能从效率结果推断：
  - 单独使用 Light Forcing 可获得 **1.2×~1.3× 端到端加速**；
  - 配合其他高效解决方案后，在多种 GPU 上获得 **2.0×~3.0× 端到端加速**；
  - 例如 RTX 5090 上达到 27.4 FPS，H100 上达到 33.9 FPS。
- 若需要复现训练/推理成本，应查阅论文原文或代码仓库获取更详尽信息。

## 5. 实验数量与充分性

- 从摘要可见至少包含以下几组实验：
  - **质量评估**：VBench 分数对比；
  - **效率评估**：单独 Light Forcing 的加速比；
  - **组合加速实验**：与其他高效方案联合的加速效果；
  - **多硬件验证**：RTX 5090、H100 等不同 GPU 上的 FPS。
- 但从当前提供的信息来看，**未明确列出**：
  - 不同数据集上的生成质量细节；
  - 具体消融实验（如去掉 Chunk-Aware Growth 或 Hierarchical Sparse Attention 的对比）；
  - 与更多基线（如 Full Attention、其他稀疏注意力变体）的完整对比表。
- 因此，实验设计看起来覆盖了核心论点和应用场景，但**充分性有限**——摘要层面的信息不足以全面评估其公平性和统计显著性。需要阅读论文全文或附录中的详细实验表格才能做出更严格判断。

## 6. 主要结论与发现

- 现有稀疏注意力方法不适用于自回归视频扩散模型，原因是孤立处理 chunk 且未充分利用历史上下文。
- Light Forcing 通过 Chunk-Aware Growth 和 Hierarchical Sparse Attention，在不牺牲视频质量的前提下显著降低了注意力计算开销。
- 实验表明，Light Forcing 在质量（VBench 84.5）和效率（1.2×~1.3× 加速）上都优于现有稀疏注意力方法。
- 与其他高效解决方案结合后，可实现 2.0×~3.0× 的端到端加速，支持实时或近实时视频生成（RTX 5090 上 27.4 FPS，H100 上 33.9 FPS），证明了实际部署潜力。
- 论文声称 Light Forcing 是**首个针对自回归视频扩散模型的高效稀疏注意力方案**，并已开源代码。

## 7. 优点

- **问题定位准确**：揭示了稀疏注意力从双向模型迁移到自回归模型时的两大失败原因，具有明确的分析价值。
- **方法设计有针对性**：Chunk-Aware Growth 与自回归生成过程紧密结合，不是简单套用现成稀疏注意力，而是从视频生成特性出发设计。
- **稀疏分配可定量**：基于贡献估计分配稀疏度，比固定掩码或启发式方法更具合理性和灵活性。
- **层次化掩码策略**：帧级 + 块级两级选择，兼顾计算效率与信息保留，能适应不同的注意力模式。
- **实验证据覆盖实际部署**：报告了端到端加速和不同 GPU 上的 FPS，并验证了与其他加速方案的组合效果，实用性强。
- **开源**：提供代码链接，利于复现和后续研究。

## 8. 不足与局限

- **信息不完整**：当前摘要提供的内容未包含具体数据集、基线数量、消融实验细节、训练配置等，难以全面评估实验的严谨性和可复现性。
- **加速比有限**：单独使用 Light Forcing 仅获得 1.2×~1.3× 加速，说明稀疏注意力单独带来的收益相对有限，实际需依赖组合方案才能达到显著加速。
- **可能存在的偏差风险**：VBench 分数单一，未展示多样性、文本对齐、运动质量等分项指标，无法判断是否在某些维度上质量有所下降。
- **应用范围**：方法是针对自回归视频扩散模型设计的，可能不适用于双向扩散模型或其他视频生成架构；此外，对超长视频、高分辨率场景的稀疏模式鲁棒性未知。
- **理论保证不足**：Chunk-Aware Growth 中的“贡献估计”具体方式未在摘要中说明，缺乏理论或经验上的误差分析。
- **算力信息缺失**：未报告训练成本、模型规模、GPU 数量等，不利于衡量方法的训练开销。

（完）
