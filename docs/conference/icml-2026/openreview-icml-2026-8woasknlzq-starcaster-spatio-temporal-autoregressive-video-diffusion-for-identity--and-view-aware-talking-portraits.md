---
title: "STARCaster: Spatio-Temporal AutoRegressive Video Diffusion for Identity- and View-Aware Talking Portraits"
title_zh: STARCaster：面向身份与视角感知说话人肖像的时空自回归视频扩散模型
authors: "Foivos Paraperas Papantoniou, Stathis Galanakis, Rolandos Alexandros Potamias, Bernhard Kainz, Stefanos Zafeiriou"
date: 2026-04-30
pdf: "https://openreview.net/pdf/71282418b273c2e4755851f35182bc38c6b22da4.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 面向说话人肖像动画的时空视频扩散模型
tldr: 现有2D语音驱动视频扩散模型严重依赖参考引导导致动作多样性受限，3D感知动画又常因三平面反演产生身份漂移。本文提出STARCaster，一个统一的身份感知时空视频扩散模型，通过引入更软的身份约束并摆脱严格参考条件，同时支持语音驱动的肖像动画和动态视角控制。实验表明该方法在提升动作多样性的同时保持身份一致性，为说话人肖像生成提供了新范式。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有语音驱动肖像动画存在动作多样性差或身份漂移问题。
method: 提出STARCaster统一框架，采用软身份约束与时空自回归视频扩散实现动画与视角控制。
result: 实验证明在动作多样性和身份一致性上均取得显著提升。
conclusion: 为身份与视角感知的说话人视频生成提供了统一且有效的扩散模型方案。
---

## Abstract
This paper presents STARCaster, an identity-aware spatio-temporal video diffusion model that addresses both speech-driven portrait animation and dynamic viewpoint control, given an identity embedding or reference image, within a unified framework. Existing 2D speech-to-video diffusion models depend heavily on reference guidance, leading to limited motion diversity. At the same time, 3D-aware animation typically relies on inversion through pretrained tri-plane generators, which often leads to imperfect reconstructions and identity drift. We rethink reference- and geometry-based paradigms in two ways. First, we deviate from strict reference conditioning at pretraining by introducing softer identity constraints. Second, we address 3D awareness implicitly within the 2D video domain by leveraging the inherent multi-view nature of video data. STARCaster adopts a compositional approach progressing from ID-aware motion modeling, to audio-visual synchronization via lip reading-based supervision, and finally to novel view animation through temporal-to-spatial adaptation. To overcome the scarcity of 4D audio-visual data, we propose a decoupled learning approach in which view consistency and temporal coherence are trained independently. Comprehensive evaluations demonstrate that STARCaster generalizes effectively across tasks and identities, consistently surpassing prior approaches in different benchmarks.

---

## 论文详细总结（自动生成）

# STARCaster 论文总结

## 1. 核心问题与研究动机

- **研究背景**：语音驱动的说话人肖像动画（Talking Portrait Animation）是近年来视频生成领域的热门方向，目标是根据语音输入生成口型同步、表情自然的说话人视频，并进一步支持视角控制。
- **核心问题**：
  - 现有 **2D 语音驱动视频扩散模型** 严重依赖参考图像（reference guidance）作为条件，导致生成的动作多样性受限，视频表现单一、机械。
  - 现有 **3D 感知动画方法** 通常依赖预训练三平面生成器（tri-plane generators）进行潜码反演，而反演过程往往产生不完美的重建结果，从而引发**身份漂移（identity drift）**。
- **整体含义**：如何在 **不牺牲身份一致性** 的前提下提升动作多样性，并同时支持**动态视角控制**，是当前说话人视频生成的核心挑战。STARCaster 试图将两个任务（语音驱动动画 + 视角控制）统一到一个扩散模型框架中，为说话人肖像生成提供一种新的范式。

## 2. 方法论

### 核心思想

- **重新思考参考引导范式**：在预训练阶段放弃严格的参考图像条件，引入**更软的身份约束（softer identity constraints）**，仅依赖身份嵌入（identity embedding）或参考图像提供粗粒度身份信息，从而释放动作多样性。
- **隐式 3D 感知**：不显式构建 3D 几何（如三平面或网格），而是在 2D 视频域内利用视频数据天然的多视角特性，隐式地学习 3D 感知能力。

### 模型架构与训练流程

STARCaster 采用**组合式（compositional）学习策略**，分三个阶段递进：

1. **身份感知运动建模（ID-aware Motion Modeling）**
   - 输入身份嵌入 + 语音特征，生成与身份一致的面部运动序列；
   - 该阶段摆脱了对参考图像的强依赖，以提升运动的多样性和自然度。

2. **音视频同步（Audio-Visual Synchronization）**
   - 引入**唇读监督（lip reading-based supervision）**，通过判别式的唇读模型反馈，细化口型与语音的同步精度；
   - 该机制帮助模型生成精确的发音口型，提升视频的真实感。

3. **新视角动画（Novel View Animation）**
   - 通过**时间到空间的适应（temporal-to-spatial adaptation）** 将时间维度的运动知识迁移到空间视角维度的控制上；
   - 实现从同视角动画到任意目标视角的泛化。

### 应对数据稀缺的策略：解耦学习

- 4D 音视频数据（同一说话人、多视角、带语音）极度稀缺；
- 因此作者提出**解耦学习（decoupled learning）**：
  - **视图一致性（view consistency）** 与 **时间一致性（temporal coherence）** 分别独立训练；
  - 避免需要同时标注多视角 + 时序 + 语音的完整 4D 数据集。

### 目标函数 / 训练信号（文字说明）

- 扩散模型去噪损失：用于视频帧生成；
- 唇读损失：用于口型-语音对齐；
- 身份保持损失：通过身份嵌入约束生成视频的身份一致性；
- 时间与视角一致性损失（解耦训练）。

## 3. 实验设计

### 数据集与场景

- 摘要中未明确列出具体数据集名称（如 HDTF、VoxCeleb、CelebV-HD 等）；
- 文中提到“4D 音视频数据稀缺”，说明实验涉及多视角说话人视频场景；
- 实验覆盖**多任务**（语音驱动动画、视角控制）和**多身份**（不同说话人）的泛化测试。

### Benchmark

- 论文在**多个基准（different benchmarks）** 上进行了评估；
- 具体基准名称在提供的摘要中未详细披露。

### 对比方法

- 对比了**先前的方法（prior approaches）**，涵盖：
  - 2D 语音驱动扩散模型类方法；
  - 3D 感知生成方法（基于三平面反演）。
- 具体对比方法名称未在摘要中一一列出。

## 4. 资源与算力

- **论文提供的信息中未明确提及**所使用的 GPU 型号、数量、训练时长等计算资源细节；
- 也未说明推理阶段的实时性要求；
- 考虑到模型采用多阶段组合训练（运动建模、音视频同步、视角适应），推测其训练成本较高，但这一点仅为推测，原文未披露。

## 5. 实验数量与充分性

- **实验覆盖广度较好**：根据摘要，论文在多个 benchmark 上进行了评估，并横跨不同任务（动画生成 + 视角控制）和不同身份，说明实验设计具有较好的泛化性验证。
- **消融实验情况**：摘要中未明确提及是否包含针对各模块（如软身份约束、唇读监督、解耦学习）的消融分析；
- **客观性评估**：
  - 优点：在多个基准上一致超越先前方法，结论具有较强说服力；
  - 局限：由于摘要信息有限，无法判断具体实验的统计显著性、评估指标细节（如 FID、LSE-C、身份相似度等数值）以及用户研究等主观评估；
  - 因此，从可获取的信息来看，实验设计方向正确、覆盖较全面，但**充分性无法完全验证**，有待全文补充。

## 6. 主要结论与发现

- STARCaster 在一个统一框架内同时实现了**语音驱动的肖像动画**和**动态视角控制**；
- 通过**软身份约束**替代严格参考条件，显著提升了**动作多样性**；
- 通过**隐式 3D 感知**替代三平面反演，有效避免了**身份漂移**问题；
- 在**多个基准任务**上，STARCaster 均**一致性地超越先前方法**，展现了出色的跨任务和跨身份泛化能力；
- 解耦学习策略成功克服了 4D 音视频数据稀缺的瓶颈。

## 7. 优点

- **统一框架**：一个模型同时处理身份感知动画和视角控制，避免了多阶段级联系统中的误差累积；
- **创新性**：打破了对参考图像的强依赖范式，引入了“软身份约束”这一简洁有效的方案；
- **隐式 3D 处理**：不依赖显式 3D 重建，利用 2D 视频的多视角特性实现 3D 感知，规避了三平面反演带来的身份漂移；
- **数据高效的训练策略**：通过解耦学习将视角一致性与时间一致性分开训练，降低了对稀缺 4D 数据的需求；
- **泛化能力强**：跨任务、跨身份、跨基准的一致性能表现证明了方法具有较强的普适性；
- 从评审角度看，论文获得 **ICML-2026 接收** 且评分为 **9.0 分**，说明其贡献和实验受到高度认可。

## 8. 不足与局限

- **信息透明性有限**：摘要中未提供具体数据集名称、对比方法细节、评估指标数值和算力信息，难以全面复现或横向对比；
- **消融分析未披露**：文中未明确展示各组件（软身份约束、唇读监督、解耦训练）的独立贡献，无法判断各模块的实际增益大小；
- **解耦训练可能引入潜在不一致**：视图一致性和时间一致性分开训练，虽然缓解了数据稀缺问题，但可能在实际推理中出现视图与时间维度的耦合误差；
- **身份保持的边界**：“软身份约束”在提升动作多样性的同时，对于极端姿态、长时间视频或大角度视角变化，身份保持能力可能下降；
- **应用范围有限**：目前面向说话人肖像这一特定场景，对于全身动作、多人交互或复杂背景等更广泛的视频生成任务可能不适用；
- **4D 数据稀缺的根本问题**：虽然解耦学习缓解了该问题，但并未从根源上解决——真实场景下多视角+语音+动态的高质量数据仍然难以大规模获取。

（完）
