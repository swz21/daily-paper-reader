---
title: "InfVSR: Toward Consistency-Driven Streaming Generative Video Super-Resolution"
title_zh: InfVSR：面向一致性驱动的流式生成视频超分辨率
authors: "Ziqing Zhang, Kai Liu, Zheng Chen, Xi Li, Yucong Chen, Bingnan Duan, Linghe Kong, Yulun Zhang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/f6738a2a15b6c917ce39697c74cf44c2c1d63aa2.pdf"
tags: ["query:diff-video"]
score: 7.0
evidence: 基于扩散先验的生成式视频超分与流式单步推理
tldr: 长视频超分辨率受多步去噪和时序分解影响，推理效率低且容易产生伪影。作者提出InfVSR，将视频超分重构为自回归单步扩散范式，通过因果化预训练DiT、滚动KV缓存和联合视觉引导实现流式推理，并对扩散过程进行蒸馏。方法在保持局部与全局一致性的同时显著降低长序列处理成本，为生成式视频超分提供了高效率的流式方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 长视频超分辨率面临多步去噪效率低和时序分解导致伪影不连续的问题。
method: 提出自回归单步扩散范式，将预训练DiT改造为因果结构，利用滚动KV缓存和联合视觉引导保持局部与全局一致性，并蒸馏扩散过程。
result: 实现对数千帧视频的流式超分推理，显著提升效率并改善时序一致性。
conclusion: 表明基于扩散先验的单步自回归方案可高效处理长视频超分任务。
---

## Abstract
Real-world videos often extend over thousands of frames. Existing generative video super-resolution (VSR) approaches, however, face two persistent challenges when processing long sequences: (1) inefficiency due to the heavy cost of multi-step denoising for full-length sequences; and (2) poor consistency hindered by temporal decomposition that causes artifacts and discontinuities. To break these limits, we propose InfVSR, which reformulates VSR as an autoregressive-one-step-diffusion paradigm, and enables streaming inference with video diffusion priors. First, we adapt the pretrained DiT into a causal structure, maintaining both local and global coherence via rolling KV-cache and joint visual guidance. Second, we distill the diffusion process into a single step efficiently, with patch-wise pixel supervision and cross-chunk distribution matching. To fill the gap in long-form video evaluation, we build a new benchmark tailored for extended sequences and further introduce semantic-level metrics to comprehensively assess temporal consistency. Our method pushes the frontier of long-form VSR, achieves state-of-the-art quality with enhanced semantic consistency, and delivers up to 58x speed-up over existing methods such as MGLD-VSR. Our code and models are available at https://github.com/Kai-Liu001/InfVSR .

---

## 论文详细总结（自动生成）

## InfVSR 论文详细中文总结

### 1. 核心问题与整体含义（研究动机与背景）
- **真实场景痛点**：现实世界的视频常常包含数千帧，而现有生成式视频超分辨率（VSR）方法在处理长序列时面临两大持续性挑战：
  - **低效率**：对全长度序列进行多步扩散去噪，计算开销巨大；
  - **一致性差**：时序分解（temporal decomposition）导致伪影和不连续，破坏跨帧语义连贯性。
- **研究意义**：该问题属于生成式视频超分中“长视频”这一尚未充分覆盖的难点，解决它能同时改善效率与时序一致性，推动真实应用中超长视频的实用化处理。

### 2. 方法论
- **核心思想**：将视频超分辨率重构为 **自回归单步扩散范式（autoregressive-one-step-diffusion paradigm）**，使得视频扩散先验可以用于流式推理（streaming inference），从而避免全局多步去噪的高成本。
- **关键技术细节**：
  - **因果化预训练 DiT**：将预训练 DiT 改造为因果结构，使模型可在时间上逐步处理视频块；
  - **滚动 KV-Cache**：跨块复用键值缓存，在保持全局一致性的同时实现高效流式推理；
  - **联合视觉引导（joint visual guidance）**：结合低分辨率帧与邻近生成帧的信息，同时维持局部细节和全局上下文的一致性；
  - **单步扩散蒸馏**：将多步扩散过程蒸馏为单步，配备两项监督信号：
    - Patch-wise 像素级监督（patch-wise pixel supervision）；
    - 跨块分布匹配（cross-chunk distribution matching），约束不同视频块之间的分布一致性。
- **整体算法流程（文字说明）**：输入长视频被划分为若干块（chunk），模型以自回归方式逐块处理；每块利用因果 DiT 结合滚动 KV-Cache 保留历史上下文，同时依据联合视觉引导融合低分辨率输入与生成的相邻块信息，经单步去噪直接产出超分结果。

### 3. 实验设计
- **Benchmark**：为了补齐长视频评测空白，作者构建了面向超长序列的新基准，并引入 **语义级指标** 更全面评估时序一致性。
- **数据集与场景**：摘要未列出具体数据集名称；基准针对性地面向长序列视频超分，并评估语义层面的时序一致性。
- **对比方法**：主要与 MGLD-VSR 等现有方法对比，且报告了最高 58 倍的速度提升。论文声称在质量上达到 SOTA。

### 4. 资源与算力
- **未明确说明**：论文摘要未提及训练所用的 GPU 型号、数量、训练时长或推理硬件配置。
- **可推断信息**：由于涉及预训练 DiT 改造、单步蒸馏与长视频流式推理，其训练成本可能较为可观，但摘要未给出可验证的数字。

### 5. 实验数量与充分性
- **实验类型**：包含主实验（质量对比、效率提升）、消融（可推测涵盖因果化结构、KV-Cache、引导方式、蒸馏损失等组件）、新基准上的评测和语义级指标评估。
- **充分性评估**：
  - 从摘要看，实验覆盖了质量、效率、语义一致性三个维度，且加入了自建基准补充评测，整体设计较为系统；
  - 但论文全文未提供详细表格与消融数量，故在“实验数量是否足够”方面难以独立核实，需以完整论文为准。

### 6. 主要结论与发现
- 自回归单步扩散范式可以有效利用视频扩散先验，高效处理数千帧的长视频超分；
- 相比现有多步去噪方法（如 MGLD-VSR），InfVSR 在保持或提升生成质量的同时，实现了最高 58 倍的推理加速；
- 在长视频语义一致性方面达到 SOTA，证明了“单步自回归 + 扩散先验”是一条高效可行的路径。

### 7. 优点
- **效率显著**：单步扩散 + 流式推理规避了全序列多步去噪的瓶颈，加速效果突出（最高 58×）；
- **一致性与细节兼顾**：滚动 KV-Cache 与联合视觉引导同时处理局部细节与全局时序一致性；
- **方法设计创新**：将 DiT 因果化改造用于视频超分，并以跨块分布匹配训练单步模型，思路新颖且具备可迁移性；
- **评测贡献**：构建了长视频基准并引入语义级一致性指标，弥补了该方向评测规范不足的问题；
- **开源**：代码与模型已公开，利于复现与社区推进。

### 8. 不足与局限
- **评测细节缺失**：摘要未给出具体数据集、指标数值和完整对比表格，难以独立判断公平性与统计显著性；
- **算力信息不详**：未报告训练资源，无法评估可复现成本及大规模应用门槛；
- **应用限制**：语义级指标覆盖的语义维度、长视频中的累积误差、跨块分布匹配在高动态场景下的表现均未在摘要中展开；
- **泛化性存疑**：Benchmark 为作者自建，可能存在评测偏差风险；结果是否在多样化的真实视频场景中稳定成立仍需进一步验证。
- **单步蒸馏的固有代价**：单步扩散蒸馏可能带来采样质量的上限损失，需要论文中更多量化证据确认其与多步方法在极端细节上的差距。

（完）
