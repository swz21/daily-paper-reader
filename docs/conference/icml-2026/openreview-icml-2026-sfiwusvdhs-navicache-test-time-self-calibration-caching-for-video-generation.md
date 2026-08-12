---
title: "NaviCache: Test-Time Self-Calibration Caching for Video Generation"
title_zh: NaviCache：面向视频生成的测试时自校准缓存加速方法
authors: "Zheqi Lv, Zhibo Zhu, Jinke Wang, Qi Tian, Shengyu Zhang, Zhengyu Chen, Chengxi Zang, Zhou Zhao, Fei Wu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/1b587248d62360f498e7576e2fee3e343f63e844.pdf"
tags: ["query:diff-video"]
score: 8.0
evidence: 面向视频扩散模型的测试时自校准缓存，直接用于视频生成加速
tldr: 视频扩散模型计算成本极高，现有离线校准加速存在依赖校准数据、耗时且易漂移的问题，而免校准方法易受观测噪声影响。本文提出NaviCache，将特征演化重述为惯性导航系统问题，实现即插即用的测试时自校准缓存，在避免离线校准的同时利用扩散轨迹的内在动量，显著降低视频生成开销并提升稳定性，为视频扩散模型的高效部署提供了实用方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 视频扩散模型计算代价高，现有加速方法存在校准依赖或噪声敏感问题。
method: 提出NaviCache，将特征演化视为惯性导航问题，实现测试时自校准缓存加速。
result: 实验显示NaviCache能有效降低视频生成计算成本，并保持生成质量与稳定性。
conclusion: 为视频扩散模型提供了一种无需离线校准、即插即用的高效加速方法。
---

## Abstract
Video Diffusion Models (VDMs) is constrained by immense computational costs. While offline calibration-based acceleration suffers from calibration data dependency, prohibitive calibration duration, and susceptibility to distribution shifts, offline calibration-free methods eliminate these hurdles. However, since they rely on instantaneous zero-order approximations where the mapping between input and output differences varies in real-time, they are susceptible to observational noise and ignore the intrinsic momentum within the diffusion trajectory. In this paper, we propose NaviCache, a plug-and-play test-time self-calibration method re-conceptualizing feature evolution as an Inertial Navigation System (INS) problem. NaviCache bridges the fundamental domain gap and the non-stationary nature of diffusion by modeling the relative coupling between input and output variations. We introduce a dual-state estimation architecture that adaptively tracks the feature change ratio and its latent drift, initialized via a specialized Initial Alignment phase. By integrating a time-dependent noise schedule with an uncertainty-aware Measurement Update mechanism, NaviCache provides a theoretically grounded mechanism for error-bounded block skipping. 
Extensive experiments on the HunyuanVideo, Wan, and Open-Sora series demonstrate that NaviCache exhibits more accurate error judgment for block skipping and achieves outstanding comprehensive performance.

---

## 论文详细总结（自动生成）

# NaviCache：面向视频生成的测试时自校准缓存加速方法——论文总结

## 1. 核心问题与整体含义

- 视频扩散模型（VDMs）在生成高质量视频时计算成本极其高昂，严重制约了实际部署与应用。
- 现有的加速方法主要分为两类：
  - **离线校准方法**：依赖校准数据、校准耗时长，且容易因分布偏移导致性能下降。
  - **免校准方法**：虽然避免了离线校准，但依赖输入–输出差异之间的即时零阶近似，这种映射实时变化，因此容易受观测噪声影响，并且忽略了扩散轨迹本身固有的动量。
- 本文认为，视频扩散加速需要同时解决两个关键挑战：**基础领域差距（domain gap）** 与**扩散过程的非平稳性**。
- 整体意义：提出一种无需离线校准、即插即用的测试时自校准缓存方法，在降低视频生成开销的同时保持生成质量与稳定性，为视频扩散模型的高效部署提供了实用方案。

## 2. 方法论

- **核心思想**：将扩散过程中的特征演化重新概念化为**惯性导航系统（Inertial Navigation System, INS）问题**，通过建模输入与输出变化之间的相对耦合关系，来适应扩散轨迹的非平稳性。
- **关键技术细节**：
  - **双状态估计架构（dual-state estimation architecture）**：自适应跟踪特征变化比例（feature change ratio）及其潜在漂移（latent drift）。
  - **初始对准阶段（Initial Alignment）**：用于初始化估计器，为后续自校准建立可靠起点。
  - **时间相关噪声调度（time-dependent noise schedule）**：结合扩散过程不同时间步的噪声特性，调整估计策略。
  - **不确定性感知的测量更新机制（uncertainty-aware Measurement Update）**：在更新状态时考虑观测不确定性，抑制噪声影响。
  - **误差有界跳块（error-bounded block skipping）**：基于上述机制提供理论支撑，在保证误差有界的前提下跳过部分计算块，从而实现加速。
- 算法流程的文字概括：首先通过初始对准阶段估计初始状态；然后在每一步扩散中，利用双状态估计器跟踪特征变化比例和漂移；结合时间相关噪声调度与不确定性感知更新，判断当前块是否可以跳过，从而减少不必要的计算。

## 3. 实验设计

- **使用的模型/数据集**：论文在 **HunyuanVideo、Wan 和 Open-Sora 系列** 上进行实验。
- **Benchmark**：主要评估对象是视频生成任务中的**跳块判断准确性**和**综合生成性能**。
- **对比方法**：摘要中未明确列出具体对比方法，但推测应与离线校准方法和免校准基线进行了比较。
- **评估指标**：未在摘要中详细说明，可能包括生成质量（如 FVD/IS）和计算开销（如 FLOPs/延迟）等常见指标。

## 4. 资源与算力

- 摘要和给定元数据中**未明确说明**使用的 GPU 型号、数量、训练/推理时长等信息。
- 因此无法从现有材料中总结具体算力配置；若需完整了解资源开销，需要查阅论文完整实验章节。

## 5. 实验数量与充分性

- 摘要仅概述了在三个模型系列上的实验，表明方法具有一定的**跨架构泛化性**。
- 但提供的信息不足以判断：
  - 是否包含详细的消融实验（如每个组件的贡献）；
  - 是否覆盖不同视频分辨率、时长、内容类型；
  - 是否与多种主流加速方法进行公平比较（相同硬件、相同采样步数等）。
- 结论：从摘要层面看，实验初步支持方法有效性，但**充分性和公平性有待完整论文中的实验细节进一步验证**。

## 6. 主要结论与发现

- NaviCache 在跳块任务的误差判断上更为准确，能更好地决定哪些计算块可以跳过。
- 在 HunyuanVideo、Wan 和 Open-Sora 系列上，NaviCache 取得了**优秀的综合性能**，证明其能有效降低视频生成计算成本并保持生成质量。
- 验证了将扩散轨迹特征演化建模为惯性导航问题这一思路的有效性，填补了离线校准与免校准方法之间的空白。

## 7. 优点

- **无需离线校准**：避免了校准数据依赖和长时间校准过程，即插即用。
- **理论驱动**：基于惯性导航系统的成熟理论，为误差有界跳块提供了理论支撑。
- **噪声鲁棒**：通过不确定性感知更新机制，显著降低观测噪声影响。
- **考虑内在动量**：利用扩散轨迹的时序连续性，而非简单瞬时近似，更贴合扩散过程的实际动态。
- **跨模型泛化**：在多个视频扩散模型上验证，表明方法具有较好的通用性。

## 8. 不足与局限

- **实验信息不完整**：当前摘要未给出具体数据集细节、指标数值和基线对比数据，难以定量评估优势幅度。
- **资源与算力未披露**：缺少关于 GPU 类型、数量、时间开销的说明，不利于复现和成本评估。
- **消融与敏感性分析缺失**：未明确是否对各组件（如初始对准、噪声调度、不确定性更新）进行独立消融，也未见对超参数（如误差阈值）的敏感性分析。
- **应用范围限制**：主要面向视频扩散模型的块跳过加速，是否适用于其他生成模型（如图像扩散、多模态生成）尚不明确。
- **偏差风险**：仅在三个模型系列上测试，可能受特定架构设计影响；与规模更大、更分散的基准比较结果仍需确认。

（完）
