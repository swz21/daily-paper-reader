---
title: Mode Seeking meets Mean Seeking for Fast Long Video Generation
title_zh: 模式寻求与均值寻求交融：快速长视频生成
authors: "Shengqu Cai, Weili Nie, Chao Liu, Julius Berner, Lvmin Zhang, Nanye Ma, Hansheng Chen, Maneesh Agrawala, Leonidas Guibas, Gordon Wetzstein, Arash Vahdat"
date: 2026-04-30
pdf: "https://openreview.net/pdf/9d3ad0e0c7d6bb9bb4e3876b3d409ade4ae2b05a.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 基于全局流匹配头与扩散Transformer的长视频生成
tldr: 短视频数据充足而长视频数据稀缺，长视频生成需要外推新事件和因果结构。该文提出模式寻求与均值寻求结合的训练范式，通过解耦扩散Transformer统一表征，用全局流匹配头分别建模局部保真与长期一致性。实验显示这一范式能利用短视频数据高效训练并生成连贯的长视频，推进了分钟级视频生成。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 长视频数据稀缺且超出短视频外推范围，难以同时保证局部保真与长期一致性。
method: 提出解耦扩散Transformer，用全局Flow Matching头与模式/均值寻求训练范式分别优化局部保真和长期连贯。
result: 实现快速长视频生成，缓解数据稀缺并提升长程一致性。
conclusion: 为分钟级视频生成提供新的训练范式。
---

## Abstract
Scaling video generation from seconds to minutes faces a critical bottleneck: while short-video data is abundant and high-fidelity, coherent long-form data is scarce and limited to narrow domains.
While multi-resolution image training works because higher resolution is largely an interpolation of the same underlying patch distribution, training across video lengths is fundamentally different: a longer video is an extrapolation that must invent new events and causal structure beyond the short-clip horizon.
To address this, we propose a training paradigm where Mode Seeking meets Mean Seeking, decoupling local fidelity from long-term coherence from a unified representation via a Decoupled Diffusion Transformer.
Our approach utilizes a global Flow Matching head trained via supervised learning on long videos to capture narrative structure, while simultaneously employing a local Distribution Matching head that aligns sliding windows to a frozen short-video teacher via a mode-seeking reverse-KL divergence.
This strategy enables the synthesis of minute-scale videos that learns long-range coherence and motions from limited long videos via supervised flow matching, while inheriting local realism by aligning every sliding-window segment of the student to a frozen short-video teacher.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机与背景）

- **核心问题**：将视频生成从秒级扩展到分钟级面临关键瓶颈——短视频数据丰富且保真度高，而长视频数据稀缺且仅覆盖狭窄领域，导致模型难以同时保证局部画面真实感与长期叙事连贯性。
- **背景洞察**：
  - 多分辨率图像训练之所以有效，是因为高分辨率本质上是对同一底层 patch 分布的内插；而不同长度视频的训练则完全不同——长视频超出短视频的“时间视野”，需要外推新事件与因果结构。
  - 因此，单纯用短视频数据训练无法直接生成连贯的长视频，而依赖长视频数据又受限于数据稀缺和领域狭窄。
- **整体意义**：该论文提出一种新的训练范式，旨在利用充足短视频数据的高保真性，同时从有限长视频中学习长期结构，实现分钟级高速长视频生成。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：将“模式寻求（Mode Seeking）”与“均值寻求（Mean Seeking）”相结合，从统一表示中解耦局部保真度和长期一致性。
- **关键架构**：解耦扩散 Transformer（Decoupled Diffusion Transformer）
  - 使用统一表征，但通过两个不同“头”分别处理不同时间尺度目标。
- **两个训练目标头**：
  - **全局流匹配头（Global Flow Matching head）**：通过监督学习在长视频上训练，用于捕捉叙事结构（长期一致性）。
  - **局部分布匹配头（Local Distribution Matching head）**：将滑动窗口与学生模型对齐到冻结的短视频教师模型，采用模式寻求的反向 KL 散度，用于继承局部真实感。
- **技术流程概述**：
  1. 从长视频数据中通过全局流匹配监督学习长期运动与叙事结构。
  2. 同时在每个滑动窗口片段上，将学生模型输出与冻结的短视频教师模型进行分布匹配（模式寻求），确保局部画面质量。
  3. 两者共享同一个解耦扩散 Transformer 的统一表征，分别优化不同目标，最终实现可并行、高效的分钟级视频生成。
- **公式/算法**（文字描述）：文中未给出具体数学公式，但理论上是联合优化两个损失项：全局流匹配损失（均值寻求）与局部反向 KL 散度损失（模式寻求），并可能按时间尺度对扩散 transformer 内部模块进行解耦（如不同注意力层或分支）。

## 3. 实验设计（从现有文本提取）

- **数据集**：仅提及“长视频数据”和“短视频数据”，未列出具体数据集名称（如 UCF-101、Kinetics 等）。
- **Benchmark**：未见明确的基准测试说明。
- **对比方法**：未在给定文本中列出具体对比模型，仅从标题与摘要推断可能对比了纯短视频训练、纯长视频监督、以及单一目标（均值或模式）训练范式。
- **说明**：论文 PDF 提取内容仅包含摘要，实验章节（数据集、评估指标、对比方法）未在提供文本中呈现，无法给出更具体细节。

## 4. 资源与算力

- **明确信息**：给定文本（仅摘要）中**未提及**任何 GPU 型号、数量、训练时长、显存开销等算力细节。
- **已知推断**：根据标题“快速长视频生成”及方法设计（解耦训练、冻结教师、并行化可能的全局/局部头），推测其训练效率优于直接训练长视频模型，但无从验证具体资源消耗。
- **结论**：资源与算力信息缺失，需查阅完整论文才能补充。

## 5. 实验数量与充分性

- **可见实验信息**：仅摘要中提及实验结果表明该范式“能够利用短视频数据高效训练并生成连贯的长视频”，没有给出具体实验组数、消融列表或定量指标。
- **充分性评估**：
  - 从摘要层面无法判断实验充分性。
  - 论文被 ICML 2026 接收且分数 9.0，通常意味着实验较为扎实，但本分仅依据摘要文本，无法客观评估对比公平性、消融完整性等。
  - 潜在实验需求：至少应包括不同视频长度（如 30s/1min/5min）、不同数据稀缺程度、长短视频比例消融、两个损失头的权重消融、与 SOTA 长视频生成方法的定量/定性对比、用户研究等。

## 6. 主要结论与发现

- 分钟级长视频生成可以通过“模式寻求 + 均值寻求”的联合训练范式实现，无需依赖大量长视频数据。
- 解耦扩散 Transformer 能将局部保真和长期一致性解耦到不同训练目标中，从而同时从有限长视频中学习叙事结构、从短视频教师中继承局部真实性。
- 该方法能够生成具有长程连贯性和运动一致性的分钟级视频，同时保持局部画面的高真实感。

## 7. 优点

- **问题建模准确**：明确指出“长视频是外推而不仅是内插”，从本质区别出发设计方法，而非简单照搬图像/短视频经验。
- **巧妙的数据利用**：结合有限长视频的监督信号与充足短视频的分布知识，在数据稀缺条件下实现长期连贯性。
- **训练高效**：利用冻结的短视频教师与滑动窗口匹配，避免直接生成大量长视频数据；两个头共享统一表征，支持快速训练。
- **理论可解释**：均值寻求（对应全局流匹配）与模式寻求（对应局部反向 KL）分别对应不同时间尺度目标，逻辑清晰。

## 8. 不足与局限

- **实验信息不透明**：根据现有文本，未披露数据集细节、对比方法、定量指标，无法从摘要层面验证其泛化能力。
- **数据偏差风险**：长视频数据本身“局限于狭窄领域”（摘要承认），即使方法缓解数据稀缺，训练出的模型可能仍偏向长视频数据所在领域。
- **模式寻求的稳定性**：反向 KL 散度在模式寻求时容易导致模式崩溃（只生成少数高概率样本），论文未在摘要中说明如何避免局部多样性下降。
- **应用限制**：方法假设存在一个可用的冻结短视频教师模型，若短视频教师本身质量不足或与长视频域差异较大，匹配效果可能受限。
- **评估缺失**：未见对“长程一致性”的量化评估方式（如叙事连贯性指标、因果合理性指标）的说明，可能存在主观评估偏差。

（完）
