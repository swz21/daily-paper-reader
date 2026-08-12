---
title: "FaPS: A General and Fast Training Method for Diffusion Models"
title_zh: FaPS：一种通用且快速的扩散模型训练方法
authors: "Xianglu Wang, Bangxian Han, Hu Ding"
date: 2026-04-30
pdf: "https://openreview.net/pdf/6f22b71bd8f28503624821e9225d4d940eb8e76b.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 基于双重谱偏置的扩散模型通用快速训练方法
tldr: 扩散模型在图像生成上表现优异但训练耗时。本文通过谱偏置视角观察扩散学习动力学，发现双重谱偏置：迭代中低频先于高频被拟合，时间步上早期去噪重建低频。据此提出FaPS通用快速训练方法，优先高效学习低频结构并引导高频细化。实验表明FaPS在显著缩短训练时间的同时保持甚至提升生成质量。该工作为大规模扩散模型训练提供了成本降低的新思路。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型训练成本高，制约实际部署，缺少通用的加速训练策略。
method: 利用双重谱偏置现象，设计训练流程使模型先拟合低频再细化高频，以加速收敛。
result: 在图像生成任务上实现更快的训练收敛并保持SOTA质量。
conclusion: 为扩散模型的高效训练提供了一套基于学习动力学的通用方法。
---

## Abstract
Diffusion models have achieved state-of-the-art performance in image generation tasks. However, training powerful diffusion models remains time-consuming, which limits their practical deployment. In this paper, we revisit the learning dynamics of diffusion models through the lens of *spectral bias*, a phenomenon in which deep neural networks prioritize learning low-frequency modes. Through an empirical analysis of diffusion training, we observe that diffusion models exhibit a **dual** spectral bias. First, over training iterations, they fit low-frequency components earlier than high-frequency details. Second, along the diffusion timesteps, early denoising steps mainly reconstruct coarse low-frequency content, while high-frequency details emerge in later steps. Motivated by this observation, we propose Frequency-aware Patch Selection **(FaPS)**, a general and fast training method for diffusion models that can
be applied to both UNet and DiT backbones. 
Specifically, FaPS introduces a *frequency-aware gating* that adaptively selects image patches based on their frequency information and focuses computation only on the selected patches. Since the selection decisions are discrete and thus non-differentiable, we model the gating as a stochastic policy network and optimize it end-to-end using a policy gradient method. 
Our experiments demonstrate that FaPS achieves up to $\mathbf{3}\times$ faster training while maintaining comparable or superior generation quality, and improves the performance of diffusion models in limited-data settings.

---

## 论文详细总结（自动生成）

## 1. 论文的核心问题与整体含义

- **研究背景**：扩散模型（Diffusion Models）在图像生成任务上已达到最先进的性能，但其训练过程非常耗时，导致实际部署成本高昂，成为该技术大规模应用的主要瓶颈之一。
- **核心问题**：现有扩散模型训练缺乏通用、高效的加速策略，如何在显著缩短训练时间的同时保持甚至提升生成质量，是亟待解决的难题。
- **整体含义**：本文试图从神经网络学习动力学中的“谱偏置”（Spectral Bias）现象出发，揭示扩散模型训练过程中的内在规律，并据此提出一种通用的快速训练方法，从而降低扩散模型的训练成本，推动其实际应用。

## 2. 论文提出的方法论

- **核心思想**：基于对扩散模型训练过程的经验分析，作者发现了“双重谱偏置”现象：
  1. **迭代维度**：在训练迭代过程中，模型优先拟合图像的低频成分，之后才逐渐拟合高频细节。
  2. **时间步维度**：在扩散模型的去噪时间步中，早期的去噪步骤主要重建粗糙的低频内容，而高频细节在后期步骤才出现。
- **方法设计**：受这一观察启发，提出了 **频率感知补丁选择（Frequency-aware Patch Selection, FaPS）**，一种通用且快速的扩散模型训练方法。其关键技术细节包括：
  - **频率感知门控**：根据图像补丁的频率信息，自适应地选择图像补丁，并将计算资源仅集中在被选中的补丁上，从而减少不必要的计算开销。
  - **可微性处理**：由于补丁选择是离散操作，不可直接求导，作者将门控建模为随机策略网络，并使用**策略梯度方法**进行端到端优化，使选择过程能够与模型训练联合学习。
  - **通用性**：该方法可适用于 UNet 和 DiT（Diffusion Transformer）两种主流扩散模型骨干网络。
- **算法流程（文字说明）**：输入训练图像 → 将图像划分为多个补丁 → 计算各补丁的频率特征 → 频率感知门控（策略网络）根据频率信息决定哪些补丁被选中 → 仅在这些选中的补丁上进行扩散模型的训练损失计算与参数更新 → 通过策略梯度同时优化门控策略与生成模型。

## 3. 实验设计

- **任务与数据集**：根据摘要，实验主要围绕**图像生成**任务展开，但具体使用的数据集名称未在提供内容中明确给出。此外，还特别针对**有限数据设置**（limited-data settings）进行了验证。
- **基准与对比**：摘要未明确列出比较的具体基线方法，但提到 FaPS 在保持“相当或更好的生成质量”的同时实现加速，暗示其与标准扩散模型训练过程进行了对比，并且性能优于传统全量训练策略。
- **骨干网络覆盖**：实验同时覆盖了 UNet 和 DiT 两类主流架构，以验证方法的通用性。
- **评估指标**：摘要未明确提及 FID、IS 等具体指标，但通常图像生成任务会采用这些标准评估指标；此处无法确认详细指标设定。

## 4. 资源与算力

- **未明确说明**：在提供的摘要和元数据中，没有提及所使用的 GPU 型号（如 A100、H100 等）、GPU 数量、训练总时长等具体算力信息。
- 只能从结果中得知 FaPS 声称实现了最高 **3 倍**的训练加速，但对应的绝对资源消耗无法从当前信息中得知。

## 5. 实验数量与充分性

- **实验组数**：从提供内容来看，实验主要涵盖三个方面：
  1. 标准图像生成任务上的训练速度与生成质量对比；
  2. 不同骨干网络（UNet 和 DiT）上的通用性验证；
  3. 有限数据设置下的性能提升验证。
- **缺少细节**：由于提供内容仅为摘要和元数据，没有具体表格、消融实验、不同数据集上的结果对比等细节，因此无法对实验的全面性、公平性（如是否与同类加速方法比较、超参数设置、重复实验次数等）做出完整评估。
- **总体判断**：实验覆盖了核心场景与关键变量，但公开信息不足以判断其充分性。不过，该论文被 ICML 2026 接收，且 OpenReview 审稿评分为 9.0（满分通常为 10），说明在同行评审中可能获得了较高的认可。

## 6. 论文的主要结论与发现

- **重要发现**：扩散模型训练中存在“双重谱偏置”，这为理解扩散模型学习动态提供了新视角。
- **方法有效性**：FaPS 可实现高达 **3×** 的训练加速，同时保持或提升生成质量。
- **数据效率**：在有限数据场景下，FaPS 能提升扩散模型的性能，说明其具有较好的数据利用效率。
- **通用性**：方法适用于多种主流扩散模型架构，具有一定的通用性。
- **总体结论**：该研究为扩散模型的高效训练提供了一套基于学习动力学的通用方法，为大规模扩散模型训练降低成本提供了新思路。

## 7. 优点

- **动机明确，理论洞察有深度**：从“谱偏置”现象出发，将神经网络学习规律与扩散训练过程联系起来，方法具有理论依据，不是纯工程调参。
- **方法通用性强**：同时支持 UNet 和 DiT 两类主流架构，适用范围广，易被社区采用。
- **兼顾效率与质量**：通过只计算重要补丁来节省算力，同时不牺牲甚至提升了生成质量，在有限数据下也能获益。
- **可端到端学习**：利用策略梯度将不可微的离散选择建模为可优化过程，体现了较好的技术设计能力。

## 8. 不足与局限

- **信息完整性不足**：本次提供的材料仅为摘要和元数据，缺少实验设置细节、具体对比结果、消融研究等，无法全面评估方法优劣。
- **潜在应用局限**：摘要仅明确提及图像生成任务，虽然标签中含有 diff-video，但未在文本中体现视频生成的验证，其跨模态/跨任务泛化性尚未得知。
- **可能引入额外开销**：频率分析、补丁选择和策略网络本身会带来额外计算开销，实际净加速取决于这些成本的权衡，该方面在摘要中未充分展开。
- **策略梯度训练稳定性**：离散选择的随机策略网络可能面临高方差、收敛不稳定等问题，摘要中未讨论应对措施。
- **未与其他加速方法对比**：如知识蒸馏、结构剪枝、渐进训练等已有扩散加速技术未被提及，方法的相对优势有待进一步验证。

---

（完）
