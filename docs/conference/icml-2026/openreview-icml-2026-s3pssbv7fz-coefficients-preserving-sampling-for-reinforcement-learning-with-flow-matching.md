---
title: Coefficients-Preserving Sampling for Reinforcement Learning with Flow Matching
title_zh: 保持系数的采样：流匹配中的强化学习
authors: "Feng Wang, Zihao Yu"
date: 2026-01-22
pdf: "https://openreview.net/pdf/f5d1a826dbd2a7a6db72c9f1b1be78563b4001da.pdf"
tags: ["query:diff-video"]
score: 8.0
evidence: 用于图像和视频生成的流匹配强化学习采样
tldr: 在线强化学习用于改进扩散和流匹配模型的图像视频生成时，常需引入随机性，但SDE采样会带来明显噪声伪影，损害奖励学习。本文通过理论分析追溯噪声源于采样中注入的过量随机性，并提出保持系数的采样方法，在保证探索的同时减少伪影，从而提升RL的奖励学习效果和生成质量。
source: ICML-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 在线RL应用在流匹配模型上时，SDE采样引入的噪声会阻碍奖励学习。
method: 提出系数保持采样策略，在不破坏探索的前提下减少采样随机性带来的伪影。
result: 降低了噪声伪影，改善了奖励学习过程，提升了生成质量。
conclusion: 为流匹配模型上的在线RL采样提供了理论指导与实用方案。
---

## Abstract
Reinforcement Learning (RL) has recently emerged as a powerful technique for improving image and video generation in Diffusion and Flow Matching models, specifically for enhancing output quality and alignment with prompts. A critical step for applying online RL methods on Flow Matching is the introduction of stochasticity into the deterministic framework, commonly realized by Stochastic Differential Equation (SDE). Our investigation reveals a significant drawback to this approach: SDE-based sampling introduces pronounced noise artifacts in the generated images, which we found to be detrimental to the reward learning process. A rigorous theoretical analysis traces the origin of this noise to an excess of stochasticity injected during inference. To address this, we draw inspiration from Denoising Diffusion Implicit Models (DDIM) to reformulate the sampling process. Our proposed method, Coefficients-Preserving Sampling (CPS), eliminates these noise artifacts. This leads to more accurate reward modeling, ultimately enabling faster and more stable convergence for reinforcement learning-based optimizers like Flow-GRPO and Dance-GRPO.

---

## 论文详细总结（自动生成）

# 《保持系数的采样：流匹配中的强化学习》论文总结

## 1. 论文的核心问题与整体含义（研究动机与背景）

- **背景**：强化学习（RL）已被证明是提升扩散模型（Diffusion）和流匹配（Flow Matching）模型在图像与视频生成质量、以及与提示词对齐程度上的有效技术。
- **核心问题**：将在线 RL 应用于流匹配模型时，需要向原本确定性的生成框架（如 ODE 采样）中引入随机性，以支持 RL 的探索。然而，当前实现这一随机性的主流方式——基于随机微分方程（SDE）的采样——会引入明显的噪声伪影。
- **本文发现**：理论分析表明，这种噪声源于**推断过程中注入的随机性过量**，且这些噪声伪影对奖励学习过程产生了明显的损害，会导致奖励建模不准确、优化不稳定。
- **整体含义**：本文指出了一个在流匹配模型上进行在线 RL 时容易被忽视但十分关键的问题——即采样随机性并非越大越好，过度随机化会破坏生成质量与奖励学习的平衡，从而限制了 RL 对生成模型的整体提升空间。

## 2. 论文提出的方法论

- **核心思想**：借鉴 Denoising Diffusion Implicit Models（DDIM）的思想，重新设计流匹配模型在 RL 在线采样中的随机性注入方式，在不破坏 RL 探索需求的前提下，**保留生成过程中的关键系数**，从而消除 SDE 采样带来的噪声伪影。
- **方法名称**：Coefficients-Preserving Sampling（CPS，保持系数的采样）。
- **关键技术细节**：
  - 现有 SDE 采样在每一步推断中注入了过多的随机噪声，这被视为噪声伪影的主要来源。
  - CPS 通过保留采样过程中的决定性系数（如 denoise 时确定性路径上的关键系数），在保留足够探索随机性的同时，大幅压缩不必要的随机扰动，使生成过程更接近确定性流匹配的干净输出。
  - 该方法不依赖特定 RL 优化器，可与 Flow-GRPO、Dance-GRPO 等基于策略优化的 RL 算法兼容。
- **算法流程**（文字说明）：标准 RL 策略探索阶段先生成图像/视频样本 → 但将原本采用 SDE 的采样路径替换为 CPS 采样路径 → 在保持探索必要随机性的同时，减少每步噪声注入 → 生成的样本在保持多样性的前提下避开噪声伪影 → 奖励模型对 CPS 生成的样本进行更准确的打分 → 策略梯度更新，奖励学习逐步收敛。

## 3. 实验设计

- **场景**：图像生成与视频生成两大类任务。
- **优化器**：Flow-GRPO 和 Dance-GRPO 两种在线强化学习优化器。
- **数据集/Benchmark**：论文摘要中**未明确列出**具体使用了哪些生成基准数据集（如 MS-COCO、UCF-101 等）。
- **对比方法**：论文摘要中提到了与标准 SDE 采样方式作为对比基准，但**未列出其他具体替代采样方法**。
- **评估指标**：摘要中提到生成质量与奖励建模准确性等，但**未提供具体的量化评估指标**。

## 4. 资源与算力

- 论文在提供的摘要和元数据中**未提及**使用的 GPU 型号、数量、训练时长、采样成本等算力相关信息。
- 因此，关于资源与算力部分，**本文提供的信息不足以给出具体说明**。

## 5. 实验数量与充分性

- 从摘要和元数据来看，作者进行了图像生成和视频生成两类场景下的实验，并测试了两种 RL 优化器（Flow-GRPO、Dance-GRPO），但**实验组数、消融实验设置、不同方法的复现细节均未提供**。
- 该论文的来源标记为 ICML-2026-Rejected-Public，这一方面说明摘要所呈现的实验证据可能不够充分，导致审稿人未认可其完整贡献；另一方面也表明**实验的完整性与充分性，从当前可得信息来看尚无法客观评估**。
- 从现有摘要看，作者声称实验结果表明 CPS 能实现"更快、更稳定的收敛"，但**缺少奖励曲线定量数据与对比表的支撑**。

## 6. 论文的主要结论与发现

- SDE 采样是导致流匹配模型 RL 训练中噪声伪影的根本原因，且这些伪影会直接损害奖励学习。
- 通过系数保持采样（CPS），可以消除这类噪声伪影，使奖励建模更加准确。
- 在 Flow-GRPO 和 Dance-GRPO 等 RL 优化器下，CPS 能够带来**更快、更稳定**的收敛，并提升最终生成质量。
- 总体结论：CPS 为流匹配模型上的在线 RL 采样提供了理论分析基础与实用方案，平衡了探索与生成质量之间的矛盾。

## 7. 优点

- **理论扎实**：通过理论分析追溯了噪声伪影与过量随机性注入之间的因果联系，而非仅凭经验做启发式改进。
- **方法简洁、通用性好**：CPS 不依赖特定 RL 算法结构，可适用于多种在线 RL 优化器。
- **方向具有普遍意义**：指出了 SDE 采样在生成模型 RL 优化中的一个系统性缺陷，对后续扩散/流匹配模型 RL 采样设计具有方法论层面的借鉴价值。
- **动机明确**：将 DDIM 的思想迁移到流匹配 RL 采样中，做到了"旧工具、新场景"，具有一定的设计巧思。

## 8. 不足与局限

- **实验信息严重不足**：具体数据集、基准、对比方法、定量结果、消融实验在摘要中未交代，难以评估方法的实际表现幅度。
- **被拒稿结果**：来自 ICML-2026-Rejected-Public，说明当前版本的方法或其验证存在审稿人认为不够充分的环节。
- **缺少泛化性讨论**：没有讨论 CPS 在真实世界大规模生成任务（如长视频生成、文生图评测集）中的表现，以及它对探索多样性的影响是否有边界条件。
- **与纯确定性采样（ODE）的关系未说明**：CPS 仍然需要保留部分随机性以支持 RL 探索，但论文摘要未明确说明探索性与采样质量的权衡边界在哪里。
- **算力与训练成本缺失**：论文没有提及 CPS 相比 SDE 采样在计算开销上的差异，这限制了实际部署中对其效益比的判断。

（完）
