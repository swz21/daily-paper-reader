---
title: Self-Supervised Flow Matching for Scalable Multi-Modal Synthesis
title_zh: 面向可扩展多模态合成的自监督流匹配
authors: "Hila Chefer, Patrick Esser, Dominik Lorenz, Dustin Podell, Vikash Raja, Vinh Tong, Antonio Torralba, Robin Rombach"
date: 2026-04-30
pdf: "https://openreview.net/pdf/df95dabc5dd945dcd8c644feef48b515d66fc98a.pdf"
tags: ["query:diff-video"]
score: 8.0
evidence: 自监督流匹配，将表征学习融入生成框架
tldr: 扩散与流模型常依赖外部模型获取语义表征，训练目标与表征学习不一致且扩展性不佳。Self-Flow 提出自监督流匹配范式，通过双时间步调度在词元间施加异质噪声，迫使模型从被破坏的输入中推断缺失信息，从而在生成框架内学习语义表征。该方法无需外部模型，显著改善收敛速度与生成质量，且具备良好的扩展行为。为多模态合成提供了更自洽的生成学习方式。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散/流模型依赖外部语义模型，目标错配且扩展性差。
method: 设计双时间步调度，以异质噪声水平创造信息不对称，实现自监督表征学习。
result: 无需外部模型即改善收敛与生成质量，扩展行为更优。
conclusion: 将表征学习无缝集成到流匹配中，提升多模态合成性能。
---

## Abstract
Strong semantic representations improve the convergence and generation quality of diffusion and flow models. Existing approaches largely rely on external models, which require separate training, operate on misaligned objectives, and exhibit unexpected scaling behavior. We argue that this dependence arises from the model's training objective, which poses a denoising task with little incentive to learn semantic representations. We introduce *Self-Flow*: a self-supervised flow matching paradigm that integrates representation learning within the generative framework. Our key mechanism, *Dual-Timestep Scheduling*, applies heterogeneous noise levels across tokens, creating an information asymmetry that forces the model to infer missing information from corrupted inputs. This drives learning strong representations alongside generative capabilities without external supervision. Our method generalizes across modalities and enables multi-modal training while following expected scaling laws, achieving superior image, video, and audio generation.

---

## 论文详细总结（自动生成）

# 《Self-Supervised Flow Matching for Scalable Multi-Modal Synthesis》论文总结

## 1. 核心问题与整体含义（研究动机与背景）

- **问题背景**：扩散模型与流模型（Flow Matching）在生成质量与收敛速度上强烈依赖“强大的语义表征”。但现有方法通常借助外部预训练模型（如 CLIP、文本编码器等）来获取语义信息。
- **核心痛点**：
  - 外部模型需要单独训练，训练成本高；
  - 外部模型的训练目标与生成模型的去噪目标不一致，存在“目标错配”（misaligned objectives）；
  - 外部模型在规模扩展时表现出不可预测的缩放行为（unexpected scaling behavior），限制了生成模型的扩展性。
- **根本原因**：作者认为，生成模型自身的训练目标是一个“去噪任务”，本身几乎没有激励去学习语义表征，因此才不得不依赖外部模型。
- **整体含义**：该论文试图将“表征学习”直接嵌入生成框架内部，使模型在学会生成的同时自发学习语义表征，从而摆脱对外部语义模型的依赖，并提升多模态生成的规模扩展性。

## 2. 方法论：Self-Flow 与双时间步调度

- **核心思想**：提出一种自监督流匹配范式 **Self-Flow**，把表征学习集成到生成式训练目标中，不依赖任何外部监督信号。
- **关键技术机制：双时间步调度（Dual-Timestep Scheduling）**
  - 对输入序列中的不同“词元/令牌”（tokens）施加**异质性噪声水平**；
  - 不同的 token 被破坏程度不同，从而在输入内部形成**信息不对称**；
  - 模型必须从“被破坏更严重”的 token 中推断“被破坏较轻”或缺失的信息，这种推理压力迫使模型学习到更强的语义表征。
- **公式或算法流程**：论文摘要中未给出具体公式。结合流匹配框架，可理解为：在训练时对每个 token 使用不同的时间步（timestep）来控制噪声强度，而标准流匹配通常对所有 token 使用同一时间步。该设计在不改变生成目标的前提下，引入了自监督的语义推理任务。
- **多模态扩展**：该方法不依赖特定模态假设，可推广到图像、视频、音频等多种模态，并支持多模态联合训练。

## 3. 实验设计：数据集 / 场景 / Benchmark / 对比方法

- 摘要中仅提到方法在**图像、视频、音频生成**上取得了更强的效果（superior generation quality），并未具体说明：
  - 使用了哪些具体数据集（如 ImageNet、UCF101、Kinetics、AudioSet 等未提及）；
  - 对比了哪些基线方法（如 Stable Diffusion、DiT、Latent Flow Matching 等未列出）；
  - 具体 benchmark 与评估指标（FID、FVD、IS、CLIP Score 等未给出）。
- 因此，从当前所给文本只能确认实验场景覆盖了**图像、视频、音频三种模态**，但无法核验具体实验设置。

## 4. 资源与算力

- 论文摘要和元数据中**没有明确说明**使用的 GPU 型号、数量、训练时长、总算力等资源信息。
- 需要指出：若想评估方法的可复现性与实际训练成本，还需查阅论文正文或附录中的实验配置。

## 5. 实验数量与充分性

- 从可见内容看，实验结论主要为“在图像、视频、音频生成上取得更优效果”，但缺乏具体实验数量、消融实验、扩展性曲线等细节。
- 由于论文已被 ICML 2026 接收（元数据中标明），估计正文实验较为完整，但当前文本不足以判断：
  - 是否包含充分的消融实验（如双时间步调度的必要性、不同噪声调度选择等）；
  - 是否与足够多的强基线对比；
  - 是否在公平条件下（相同算力、相同数据、相同训练步数）进行比较。
- **总体评价**：从摘要来看，实验覆盖模态较广，但公开信息有限，无法充分验证实验的客观性与公平性。

## 6. 主要结论与发现

- **Self-Flow 能够在不依赖外部模型的情况下，将表征学习无缝集成到流匹配生成框架中。**
- **显著改善收敛速度**：相比依赖外部语义模型的传统方法，Self-Flow 的生成模型收敛更快。
- **提升生成质量**：在图像、视频、音频生成上均优于现有方法。
- **遵循预期缩放规律（expected scaling laws）**：与外部模型可能带来的不稳定缩放行为不同，Self-Flow 展现出更好的扩展性，更适合大规模多模态训练。

## 7. 优点与亮点

- **自监督、无外部依赖**：无需单独训练外部语义模型，简化了训练流程，避免了目标错配问题。
- **方法简洁且机制巧妙**：双时间步调度只需在训练时对不同 token 使用不同噪声强度，即可在生成目标内部引入表征学习信号，改动成本低、概念清晰。
- **模态通用性强**：不依赖特定模态的特定编码器，可统一应用于图像、视频、音频等模态，支持多模态联合训练。
- **强调扩展性**：论文明确指出方法符合预期缩放规律，这在当今大规模生成模型训练中非常重要。
- **理论动机明确**：从“去噪目标缺乏表征学习激励”这一角度出发，给出了为什么现有方法依赖外部模型的深层原因，并直接对症下药。

## 8. 不足与局限

- **细节缺失**：当前可获取内容仅为摘要，缺少算法伪代码、公式推导、网络架构、训练细节等关键信息。
- **实验信息不足**：未列出具体数据集、基线方法、评估指标、消融实验等，无法独立验证其宣称的优越性。
- **潜在偏差风险**：摘要声称“优于图像、视频、音频生成”，但未说明是否在相同算力、相同数据规模下进行公平比较；也未说明是否存在对特定数据集有利的设置。
- **应用限制**：双时间步调度在不同 token 间引入异质噪声，可能对不同模态或不同序列长度产生敏感影响，正文中需要讨论鲁棒性与超参数选择；摘要未涉及这些边界情况。
- **可复现性**：由于缺少算力与实验配置信息，研究者在复现时会面临一定困难。

（完）
