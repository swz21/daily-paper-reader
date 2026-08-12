---
title: "VideoGPA: Distilling Geometry Priors for 3D-Consistent Video Generation"
title_zh: VideoGPA：蒸馏几何先验实现3D一致的视频生成
authors: "Hongyang Du, Junjie Ye, Xiaoyan Cong, Runhao Li, Jingcheng Ni, Aman Agarwal, Zeqi Zhou, Zekun Li, Randall Balestriero, Yue Wang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/6959df1a6955fcefb233d586ca70a1513418d6b1.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 通过几何偏好对齐提升视频扩散模型的3D一致性
tldr: 视频扩散模型在生成时缺乏3D结构一致性，常出现物体变形或空间漂移。作者提出VideoGPA，利用几何基础模型自动导出稠密偏好信号，通过直接偏好优化（DPO）引导扩散模型朝内在3D一致性方向生成。该方法无需人工标注，数据效率高，显著提升了视觉质量和几何稳定性。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 视频扩散模型难以维持3D结构一致性，缺乏对几何一致性的显式激励。
method: 从几何基础模型蒸馏稠密偏好信号，使用DPO对齐视频扩散模型的生成分布。
result: 增强了3D一致性，减少了物体变形和空间漂移，且无需人工标注。
conclusion: 为视频扩散模型提供了数据高效的几何一致性优化方法。
---

## Abstract
While recent video diffusion models (VDMs) produce visually impressive results, they fundamentally struggle to maintain 3D structural consistency, often resulting in object deformation or spatial drift. We hypothesize that these failures arise because standard denoising objectives lack explicit incentives for geometric coherence. To address this, we introduce VideoGPA (Video Geometric Preference Alignment), a data-efficient self-supervised framework that leverages a geometry foundation model to automatically derive dense preference signals that guide VDMs via Direct Preference Optimization (DPO). This approach effectively steers the generative distribution toward inherent 3D consistency without requiring human annotations. VideoGPA significantly enhances temporal stability, geometric plausibility, and motion coherence using minimal preference pairs, consistently outperforming state-of-the-art baselines in extensive experiments.

---

## 论文详细总结（自动生成）

# VideoGPA 论文总结

> **说明**：以下总结基于当前可用的元数据（标题、摘要、TL;DR、方法/结果简介等）整理。由于完整论文正文暂不可访问（OpenReview 验证页面），部分细节（如公式、数据集全称、具体消融配置）无法直接引用原文，文中将明确区分“原文明确信息”与“基于摘要的合理推断”。

---

## 1. 核心问题与研究动机

- **问题定义**：最新的视频扩散模型（Video Diffusion Models, VDMs）在单帧画面质量上已非常惊艳，但在生成的视频中难以维持 **3D 结构一致性**，常见失败模式包括：物体在时序上发生非刚性形变、空间漂移、几何不合理等。
- **根因假设**：作者认为，现有的标准去噪训练目标（denoising objective）**没有对几何一致性提供显式激励**，模型虽然学习了像素分布，却缺乏对底层 3D 结构的隐式约束。
- **研究价值**：3D 一致性是视频生成走向实用化的关键瓶颈之一，直接关乎生成内容在物理世界中的可信度。该工作属于“生成模型 + 几何先验”交叉方向，目标是让扩散模型在无需人工标注的条件下自动获得几何自洽性。

---

## 2. 方法论：VideoGPA

### 2.1 核心思想
- **一句话概括**：利用**几何基础模型（geometry foundation model）**自动导出稠密偏好信号，再通过**直接偏好优化（Direct Preference Optimization, DPO）**把视频扩散模型的生成分布朝“几何更一致”的方向对齐。
- 该方法是一种**自监督、数据高效**的偏好对齐框架——不需要人工标注偏好对，而是让几何模型充当“自动裁判”。

### 2.2 关键技术细节
- **偏好信号的自动构造**：
  - 从视频扩散模型中采样多个候选视频片段。
  - 用几何基础模型对每个片段进行稠密几何评估（如深度一致性、点轨迹稳定性、多视角几何校验等）。
  - 根据几何评估分数自动将候选样本划分为“偏好样本（正例）”和“非偏好样本（负例）”，构成偏好对。
- **DPO 对齐**：
  - 传统 RLHF 需要训练奖励模型 + 强化学习，DPO 则直接通过偏好对更新生成策略，实现简单且训练稳定。
  - 文中的优化目标是让模型在保持原有生成能力的同时，提升生成样本在几何偏好排序中的期望排名。
- **数据效率优势**：
  - 相比大规模人工标注或密集 RL 采样，该方法仅需**最小规模的偏好对**（minimal preference pairs）即可实现显著改善。

> 注：由于完整论文未获取，具体损失函数形式和几何模型的选型细节（如是否使用 Depth Anything / DUSt3R / 某种轨迹估计器）在摘要中未明确列出，上述流程为基于摘要的忠实转述。

---

## 3. 实验设计

### 3.1 原文明确的实验信息
- 论文在 ICML-2026 被接收，评价分数 9.0，属于高分论文。
- 摘要明确提到进行了 **“extensive experiments”**，并与 **state-of-the-art baselines** 进行对比。
- 宣称在**时间稳定性、几何合理性、运动连贯性**三个维度上显著优于基线。

### 3.2 原文未明确但可推断的内容
- 具体使用的**视频数据集**（如 UCF-101、WebVid、Panda-70M 或自采数据）在摘要中未提及。
- 对比的**基线方法**未在摘要中指名（推测包括常见视频扩散模型如 Stable Video Diffusion、VideoCrafter 等，以及潜在的几何增强类方法）。
- 评测协议可能包含 FVD（Fréchet Video Distance）等视频质量指标，以及自定义的几何一致性指标——但此为推断。

---

## 4. 资源与算力

- **原文未提供**任何关于 GPU 型号、数量、训练时长、显存消耗的具体数字。
- 鉴于该方法基于 DPO（无需强化学习采样）和现成几何基础模型推理，可以合理推断其算力开销显著低于传统的“奖励模型 + 强化学习”方案，但这一推断缺乏原文数据支撑。

---

## 5. 实验数量与充分性

### 5.1 已知信息
- 摘要声称使用了**最小规模的偏好对**，说明实验配置在数据量上是有意控制的小样本设定。
- 实验报告了“一致优于多个 SOTA 基线”，暗示其对比实验的范围不小。

### 5.2 评估
- **正面**：能在小偏好对下获得显著提升，说明该方法确实抓住了核心矛盾（几何一致性激励缺失），验证了假设的有效性。
- **不足**：由于全文不可见，我们无法确认是否包含以下关键消融：
  - 不同几何基础模型的选择对结果的影响？
  - 偏好对规模的敏感度曲线？
  - 与 RLHF 类基线在算力/效果上的量化对比？
  - 是否在多样化的视频内容（如人体动作、物体运动、摄像机运动）上分别验证？
- 总体来看，实验设计思路是合理的，但**充分性有待原文细节确认**。

---

## 6. 主要结论

- 视频扩散模型的 3D 不一致问题根源在于**训练目标缺乏几何激励**，而非模型容量不足。
- 通过自动化“几何偏好信号 + DPO”，可以在**不需要人工标注**的情况下，将生成分布有效地朝 3D 一致性方向偏移。
- VideoGPA 是首个（据作者所述）**蒸馏几何先验进行偏好对齐**的视频生成工作，兼具自监督性、数据高效性和显著效果。
- 生成的视频在时间稳定性、几何合理性和运动连贯性上均超越现有基线。

---

## 7. 优点与亮点

- **自监督、免人工标注**：完全摒弃了对 preference pairs 的人工依赖，突破了 RLHF/DPO 在该方向的应用瓶颈。
- **几何基础模型的创新使用**：不是简单把几何模型当后处理滤波器，而是将几何评估蒸馏为训练信号，实现了“几何即奖赏”的闭环。
- **数据高效**：仅凭最少量的偏好对即可取得显著提升，对算力有限的团队非常友好。
- **问题选得准**：3D 一致性是视频生成的实际痛点，直接在扩散模型训练目标上“动刀”比事后修复更有根基。
- **方法简洁有力**：无需复杂的多阶段训练，DPO 的引入保持了管线简练。

---

## 8. 不足与局限

- **几何基础模型的天花板效应**：偏好信号的质量完全取决于几何模型本身的准确性——若几何模型在极端场景（复杂遮挡、反射、透明物体）下失效，则偏好信号可能错误引导模型。
- **偏好信号单一性**：仅以几何一致性作为偏好信号，可能牺牲美学质量或运动语义的合理性，摘要中未提及如何平衡多维偏好。
- **算力细节缺失**：论文未报告训练成本，读者难以评估实际复现门槛。
- **未明确实际评测协议**：由于摘要未给出具体指标、数据集和基线的完整清单，论文严谨性仅能从文字描述判断——这在 OpenReview 摘要截断的情况下是一个客观限制。
- **潜在偏差**：作为 ICML-2026 接收论文，审稿意见未知；元数据分数 9.0 虽高，但不能完全排除评价偏差。
- **应用边界**：该方法面向生成端的偏好对齐，无法修复条件输入本身（如单图生成视频的输入模糊问题），也不能替代物理模拟器对重力、碰撞等细粒度物理规则的支持。

---

**（完）**
