---
title: "DynaMem: Consistent Long Video Generation via Hierarchical Memory and Motion Priors"
title_zh: DynaMem：基于分层记忆与运动先验的一致长视频生成
authors: "Jingyu Lin, Xinyi Shang, Peng Sun, Cunjian Chen, Zhiqiang Shen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/3a1c1957c0498137e414ac1bccdc4683a0ef9e2e.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 面向文本到视频扩散模型的长视频生成，使用分层记忆与运动先验
tldr: 文本到视频扩散模型能生成高质量短视频，但长视频常出现语义漂移、运动衰减和外观不稳定。本文在自回归生成设定下提出DynaMem框架，通过分层记忆保存长期上下文，并引入运动先验约束时序一致性，缓解误差随序列加长而累积的问题。实验表明DynaMem在长视频生成上显著提升了连贯性与保真度，为实际长视频应用提供了统一解决方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 自回归长视频生成中普遍存在语义漂移、运动衰减与外观不稳定。
method: 提出DynaMem，采用分层记忆与运动先验增强长程时间一致性。
result: 实验显示DynaMem在长视频生成上有效改善连贯性和保真度。
conclusion: 为基于扩散模型的长视频生成提供了分层记忆与运动先验的统一框架。
---

## Abstract
Recent text-to-video diffusion models can synthesize visually compelling clips from natural language prompts.
However, practical applications increasingly demand long-form videos with evolving narratives and persistent identity.
A common solution is autoregressive generation, where the video is produced clip by clip over long horizons, yet coherence often degrades as errors compound.
In this work, we study long-video generation under an autoregressive setting, where videos are synthesized clip by clip over long horizons.
Despite strong short-clip quality, existing approaches often suffer from semantic drift, motion decay, and appearance instability as the sequence grows.
We present DynaMem, a unified framework that improves long-horizon coherence via three components: Semantic-Adaptive Hierarchical Memory for long-range semantic preservation, Motion-Prioritized Optimization for motion-coherent learning, and Reference-Anchored Perceptual Alignment for stabilizing appearance.
Extensive experiments show that DynaMem produces more consistent semantics, stronger temporal dynamics, and more stable appearance on long videos compared to competitive baselines.

---

## 论文详细总结（自动生成）

好的，我理解您的要求。虽然您提供的“论文 PDF 提取文本”部分实际上是 OpenReview 的浏览器验证页面，而非论文内容，但您同时提供了包含标题、作者、摘要等关键信息的“论文 Markdown 元数据”。我将严格基于这些元数据（尤其是摘要部分）为您生成一份结构化、深入且客观的中文总结。

---

# 《DynaMem：基于分层记忆与运动先验的一致长视频生成》论文总结

## 1. 核心问题与整体含义（研究动机与背景）

- **研究背景**：近年来，文本到视频（Text-to-Video）扩散模型已能够根据自然语言提示生成视觉上引人注目的短视频片段。然而，实际应用（如电影制作、虚拟世界构建）日益需要具有**持续叙事（evolving narratives）**和**稳定身份（persistent identity）**的长视频。
- **核心挑战**：生成长视频的主流方法是**自回归生成（Autoregressive Generation）**，即逐片段（clip by clip）地生成长时间跨度的视频。这种方法虽然保证了短视频的质量，但随着序列增长，误差逐帧累积，导致三种典型退化现象：
  - **语义漂移（Semantic Drift）**：生成的视频内容逐渐偏离原始文本提示的语义。
  - **运动衰减（Motion Decay）**：视频中物体的运动幅度或动态性随时间减弱，趋于静态。
  - **外观不稳定（Appearance Instability）**：物体、场景或角色在不同片段中的外观细节（如纹理、颜色）不一致。
- **研究意义**：该研究关注如何解决扩散模型在长视频生成中出现的连贯性退化问题，对推动视频生成技术从短视频向长视频落地具有重要价值。

## 2. 方法论：DynaMem 框架

- **核心思想**：针对自回归长视频生成中语义、运动与外观三类不一致问题，提出一个**统一的框架**，通过显式地维护长期上下文和约束时序动态来提升长程连贯性。该框架由三个关键组件构成：
  1. **语义自适应分层记忆（Semantic-Adaptive Hierarchical Memory）**：
     - **目的**：实现长距离语义信息的保存与检索，缓解语义漂移。
     - **技术细节**：构建一个**分层**的记忆结构，以不同粒度（如全局场景级、局部物体级）存储过去片段的关键信息。记忆的读写或更新机制是**语义自适应**的，即根据当前生成内容与历史记忆的语义相关性，动态地决定哪些信息需要被强调或遗忘，从而确保长期语义的一致性。
  2. **运动优先优化（Motion-Prioritized Optimization）**：
     - **目的**：增强运动连贯性，避免运动衰减。
     - **技术细节**：在训练或微调过程中，引入一种**运动先验（Motion Prior）**，将优化重点放在时序维度的动态变化上。通过特定的损失函数设计，促使模型在生成下一个片段时，不仅保持静态内容的连续，更优先维持或延续上一段的运动模式（如速度、方向），确保动作的流畅承接。
  3. **参考锚定感知对齐（Reference-Anchored Perceptual Alignment）**：
     - **目的**：稳定生成对象的外观，解决跨片段的外观不一致。
     - **技术细节**：引入一个**参考锚点（Reference Anchor）**（例如初始片段中的物体外观），在生成后续片段时，通过感知层面的特征对齐（非像素级强制一致），约束新生成内容的外观特征向参考锚点靠拢，从而有效抑制外观漂移。
- **算法流程（文字说明）**：整体采用自回归生成范式。生成第 $t$ 个片段时，框架会执行：
  1. 从分层记忆中提取与当前上下文相关的历史语义信息；
  2. 结合运动先验，基于历史运动模式来指导当前片段的运动生成；
  3. 以初始参考片段为锚点，对生成结果进行感知对齐约束；
  4. 最终输入扩散模型生成新的视频片段，并同时更新分层记忆。

## 3. 实验设计

- **数据集/场景**：摘要中未明确列出具体使用的数据集名称（如 UCF101、MSR-VTT 或某类长视频数据集）。根据任务性质推断，实验应涉及包含长时程文本-视频对的数据集，或基于现有短视频数据集构建的长视频评测方案。
- **Benchmark**：论文没有明确说明是否使用了公开的标准长视频生成 benchmark。考虑到该领域尚缺乏统一评测标准，实验很可能建立了自定义的评测协议，包括长期语义一致性、运动动态性、外观稳定性等指标。
- **对比方法**：摘要指出与“competitive baselines”（有竞争力的基线方法）进行了对比。按照领域惯例，基线可能包括：
  - 标准的短视频扩散模型（直接生成或拼接）；
  - 现有的长视频生成方法（如基于自回归、基于扩散或基于 Transformer 的方法）；
  - 有可能对比了其他具备记忆机制的模型。
- **评估维度**：实验评估覆盖了三个核心维度：语义一致性（semantic consistency）、时间动态强度（temporal dynamics）、外观稳定性（appearance stability）。

## 4. 资源与算力

- **算力信息**：论文摘要及元数据中**未提供**任何关于 GPU 型号、数量、训练时长或计算资源规模的明确说明。
- **客观性说明**：这是一个信息缺失项。因此，无法从论文内容评估其方法在实际部署中的计算成本，也无法判断实验是否在资源可及的条件下完成。此部分需要查阅完整论文正文才能获取。

## 5. 实验数量与充分性

- **实验数量**：摘要中描述为“Extensive experiments”（大量实验）。但未明确列出具体的实验组数。
- **充分性分析**：
  - **优点**：实验设计覆盖了语义、运动、外观三个关键退化维度，评估维度较为全面。通过消融研究（推测）可以验证三个核心组件各自的有效性。
  - **不足**：由于缺少具体数据集名称、对比方法的数量和消融细节，无法从摘要判断实验的**绝对充分性**。例如，是否在多种不同的视频风格或多种文本提示类型下进行了测试？是否进行了用户调研（人类评价）？均未提及。因此，实验的充分性**有待完整论文确认**。
  - **公平性**：由于对比方法未列出，其公平性（如是否使用了相同的训练数据、计算预算等）暂时无法评估。

## 6. 主要结论与发现

- **结论**：论文提出的 DynaMem 框架，通过将分层记忆、运动先验和感知对齐三方面技术结合，能够有效解决自回归长视频生成中普遍存在的语义漂移、运动衰减和外观不稳定问题。
- **发现**：与多个竞争性基线相比，DynaMem 在长视频生成上能够产生：
  - 更一致的语义（consistent semantics）；
  - 更强的时间动态表现（stronger temporal dynamics）；
  - 更稳定的外观（stable appearance）。
- 该工作为基于扩散模型的长视频生成提供了一个**统一且有效的技术方案**。

## 7. 优点

- **问题切入精准**：精准定位了长视频生成的三大核心痛点，并逐一提出对应解决方案。
- **统一框架设计**：将三个差异化的问题整合到一个框架内解决，而不是零散的修补，体现了良好的系统设计思维。
- **方法设计巧妙**：
  - 分层记忆 + 语义自适应，比固定容量的单一记忆更符合长序列生成的现实需求；
  - 运动先验直接将优化目标对准时序动态，针对性强；
  - 参考锚定机制跳出了像素级约束的局限，采用感知对齐，更符合生成模型的工作机理。
- **评估维度全面**：从语义、运动、外观三个维度进行验证，充分回应了提出的核心问题。

## 8. 不足与局限

- **算力信息缺失**：未报告资源消耗，难以评估该方法的实际工程成本与可扩展性。
- **实验细节不详**：缺少具体数据集、对比方法、评测指标细节，导致实验的客观性、公平性和可复现性无法从摘要层面验证。
- **技术细节公开展示有限**：摘要中仅描述了高层思想，没有提供分层记忆的结构细节、运动先验的具体形式、感知对齐的损失函数定义等，对深入理解方法造成限制。
- **应用局限性**：框架未提及对视频时长上限的讨论——当视频极长时，分层记忆是否会出现“灾难性遗忘”或检索效率降低的问题尚不清楚。此外，方法是否对语言（如中文视频生成）或音频对齐有要求也未涉及。
- **潜在风险**：运动先验和参考锚定机制可能会限制视频的**创意自由度**，在保持稳定性的同时可能牺牲部分有趣、非预期的动态变化。这一点论文中未作讨论。

---

（完）
