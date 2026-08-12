---
title: "FAST-AR: Fast Autoregressive Video Diffusion and World Models with Temporal Cache Compression and Sparse Attention"
title_zh: 快速自回归视频扩散与世界模型：基于时间缓存压缩和稀疏注意力
authors: "Dvir Samuel, Issar Tzachor, Matan Levy, Michael Green, Gal Chechik, Rami Ben-Ari"
date: 2026-04-30
pdf: "https://openreview.net/pdf/f93d6a0d6126da027b65cdcf692c09d9839ed00c.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 自回归视频扩散模型，通过缓存压缩与稀疏注意力加速长视频生成
tldr: 自回归视频扩散模型在生成过程中KV缓存不断增长，导致推理延迟和显存开销上升，限制了长时间上下文和一致性。作者识别出缓存键重复、语义查询缓慢演变等三类冗余，提出FAST-AR，通过时间缓存压缩与稀疏注意力降低计算量。实验表明该方法在保持长时视频一致性的同时显著提升推理速度。该工作为长视频流式生成与视频世界模型的实际部署提供了高效方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 自回归视频扩散的注意力层在推理时KV缓存增长，造成高延迟和显存压力，制约长时间上下文与一致性。
method: 识别三类注意力冗余，设计时间缓存压缩和稀疏注意力机制，在不改变生成质量的前提下加速自回归视频扩散。
result: 在多个视频生成基准上，FAST-AR在保持长期一致性的同时显著降低推理延迟和显存占用。
conclusion: 为长视频流式生成与视频世界模型提供了高效、可扩展的推理加速方案。
---

## Abstract
Autoregressive video diffusion models enable streaming generation, opening the door to long-form synthesis, video world models, and interactive neural game engines. However, their core attention layers become a major bottleneck at inference time: as generation progresses, the KV cache grows, causing both increasing latency and escalating GPU memory, which in turn restricts usable temporal context and harms long-range consistency. In this work, we study redundancy in autoregressive video diffusion and identify three persistent sources: near-duplicate cached keys across frames, slowly evolving (largely semantic) queries/keys that make many attention computations redundant, and cross-attention over long prompts where only a small subset of tokens matters per frame.
Building on these observations, we propose a unified, training-free attention framework (FAST-AR) for FAST-AutoRegressive diffusion, consisting of three components: TempCache compresses the KV cache via temporal correspondence to bound cache growth; AnnCA accelerates cross-attention by selecting frame-relevant prompt tokens using fast approximate nearest neighbor (ANN) matching; and AnnSA sparsifies self-attention by restricting each query to semantically matched keys, also using a lightweight ANN. Together, these modules reduce attention, compute, and memory and are compatible with existing autoregressive diffusion backbones and world models. Experiments demonstrate up to x5--x10 end-to-end speedups while preserving near-identical visual quality and, crucially, maintaining stable throughput and nearly constant peak GPU memory usage over long rollouts, where prior methods progressively slow down and suffer from increasing memory usage.

---

## 论文详细总结（自动生成）

## FAST-AR 论文总结

### 1. 核心问题与整体含义（研究动机与背景）

- **研究对象**：自回归视频扩散模型（Autoregressive Video Diffusion Models）。这类模型支持流式视频生成，是实现长视频合成、视频世界模型（world models）和交互式神经游戏引擎的关键技术路径。
- **核心痛点**：在推理过程中，随着生成帧数增加，注意力层中的 KV 缓存（KV cache）不断增长，导致：
  - **推理延迟持续上升**，生成速度逐渐变慢；
  - **GPU 显存占用不断攀升**，限制可用的时间上下文长度；
  - **长程一致性受损**，模型难以在长时间跨度上保持语义和视觉的一致性。
- **整体意义**：KV 缓存膨胀是自回归视频扩散模型走向长视频实际部署的主要计算瓶颈。解决这一问题对于实时流式生成、可扩展视频世界模型和高保真交互式生成具有重要意义。

### 2. 方法论

#### 核心思想

研究者首先识别出自回归视频扩散模型注意力计算中的**三类持续性冗余**，在此基础上提出一个**统一、无需训练（training-free）**的注意力加速框架 FAST-AR，从 KV 缓存压缩和注意力稀疏化两个角度同时降低计算与存储开销。

#### 三类注意力冗余

1. **跨帧近似重复的缓存键（near-duplicate cached keys）**：相邻帧之间的键向量高度相似，导致 KV 缓存中存在大量冗余信息。
2. **缓慢演化的语义查询/键（slowly evolving semantic queries/keys）**：查询和键随时间的演化是渐进式的，许多注意力计算在连续帧之间重复进行，结果几乎相同。
3. **长提示词跨注意力的稀疏性**：在长文本提示下，每帧实际只依赖少量与当前帧相关的 token，而非全部提示 token。

#### 三个技术组件

| 组件 | 全称 | 功能 |
|------|------|------|
| **TempCache** | Temporal Cache Compression | 利用帧间的时间对应关系（temporal correspondence）压缩 KV 缓存，限制缓存增长规模 |
| **AnnCA** | Approximate Nearest Neighbor Cross-Attention | 通过快速近似最近邻（ANN）匹配，为每帧选取相关的提示词 token，加速跨注意力 |
| **AnnSA** | Approximate Nearest Neighbor Self-Attention | 通过轻量级 ANN，将每个查询只限制在与语义匹配的键上进行注意力计算，实现自注意力稀疏化 |

#### 流程说明

- 在推理的每一时间步，TempCache 先对历史 KV 缓存进行时间维度的压缩，利用帧间对应关系合并冗余键值；然后 AnnCA 对跨注意力部分的提示词 token 进行近似最近邻筛选，仅保留与当前帧语义相关的 token；AnnSA 对自注意力部分施加相似约束，每个 query 仅对语义匹配的 key 做注意力计算。
- 三个模块协同工作，共同降低注意力计算量和显存占用，且无需修改模型权重，可即插即用，兼容现有自回归扩散骨干网络和视频世界模型。

### 3. 实验设计

- **基准/场景**：在多个视频生成基准上测试了长视频流式生成、视频世界模型等场景，具体数据集名称在摘要中未逐一说明。
- **对比方法**：与现有自回归视频扩散方法对比，尤其对比了先前方法在长序列生成中逐渐减速、显存不断上升的问题。
- **评测内容**：
  - 端到端推理速度（speedup）
  - 生成视觉质量（visual quality）
  - 长程一致性（long-range consistency）
  - 吞吐量稳定性（stable throughput）
  - 推理过程中峰值显存变化

### 4. 资源与算力

- 论文摘要中**未提供**具体算力资源配置信息，例如 GPU 型号、数量、训练或评测时长等均未提及。
- 需要指出：由于该方法是 **training-free** 的，核心贡献在推理阶段的加速，因此训练算力需求不是主要关注点。但推理阶段的硬件环境（GPU 型号、批大小、序列长度等）和端到端耗时细节在摘要中同样未披露，需查阅论文正文获取。

### 5. 实验数量与充分性

- 从摘要信息看，实验覆盖了多个基准和场景，并验证了三个组件协同工作的有效性，但**具体实验组数（如各数据集上的独立结果、逐模块消融等）未在摘要中列出**。
- **充分性分析**：
  - **优点**：验证了加速倍数（5–10 倍）、视觉质量保持和长序列显存稳定性，这些都是实际部署最关心的指标。
  - **潜在不足**：缺乏对各个组件独立贡献的量化消融信息（摘要层面未披露）；不同视频分辨率、不同长度、不同模型规模的覆盖情况未知；与已训练的高效注意力方法（如基于学习的稀疏注意力）的对比也未在摘要中体现。
  - 总体而言，从摘要可初步判断实验设计的方向是正确的，但结论的一般性（generalization）仍需正文更全面的实验支撑。

### 6. 主要结论与发现

- FAST-AR 在保持接近原始模型的视觉质量（near-identical visual quality）的同时，实现 **最多 5–10 倍的端到端推理加速**。
- 在长序列生成过程中，FAST-AR 维持**稳定吞吐量**和**近乎恒定的峰值显存占用**，而先前方法在相同条件下会逐渐变慢、显存持续增加。
- 该方法不改变模型权重、无需额外训练，可直接集成到现有自回归扩散模型和世界模型中，为长视频流式生成提供了高效、可扩展的推理加速方案。

### 7. 优点

- **问题定位精准**：从注意力冗余的多个来源入手，系统性地分析了长视频推理效率瓶颈的根因。
- **统一解决方案**：将缓存压缩与稀疏注意力整合成一个框架，三模块各司其职又相互配合，覆盖面广。
- **无需训练（training-free）**：不修改模型参数，部署成本低，易于集成到已有系统中。
- **显存可预测性**：在长序列推理中保持近似恒定的 GPU 内存，对实际部署至关重要。
- **加速效果显著**：5–10 倍端到端加速，且视觉质量损失很小，实用价值高。
- **兼容性**：声称兼容现有自回归扩散骨干和世界模型，具有较好的通用性。

### 8. 不足与局限

- **实验细节披露不足**：摘要中未给出具体数据集列表、基线方法细节、模型规模、分辨率设置，难以全面评估实验覆盖广度。
- **算力信息缺失**：未说明推理所用 GPU 型号与配置，加速比的可复现性缺乏硬件语境。
- **消融不透明**：三大组件各自的独立贡献、组合增益的量化结果未在摘要中呈现，尚无法判断各组件的重要性分配。
- **适用范围边界**：方法对视觉质量的影响在极端长序列、复杂运动场景或小模型上的表现未知；training-free 的近似最近邻方法在提示词规模极大或帧间变化剧烈时的退化风险也需要进一步分析。
- **潜在风险**：近似最近邻匹配可能引入误差累积，在特别长的时间跨度上是否会影响长期一致性，仍需更长序列实验验证。

（完）
