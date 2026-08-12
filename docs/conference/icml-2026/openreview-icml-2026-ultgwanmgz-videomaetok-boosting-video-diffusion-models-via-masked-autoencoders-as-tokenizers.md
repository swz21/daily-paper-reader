---
title: "VideoMAETok: Boosting Video Diffusion Models via Masked Autoencoders as Tokenizers"
title_zh: VideoMAETok：通过掩码自编码器作为分词器提升视频扩散模型
authors: "Zhan Tong, Tinne Tuytelaars"
date: 2026-04-30
pdf: "https://openreview.net/pdf/8a632e778a71d490118ca8eeeef667dcb6c0a087.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 通过掩码自编码器训练视频分词器以提升潜在视频扩散
tldr: 现有视频tokenizer以重建为目标，与扩散模型的去噪训练目标不匹配，导致重建指标（如rFVD）不能很好反映生成质量（gFVD）。本文提出VideoMAETok，一类基于ViT的视频tokenizer，通过掩码自编码器将其显式训练为损坏-逆反模型，以对齐潜在视频扩散的训练过程。该方法有效提升了潜在视频扩散模型的生成质量。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 视频分词器的重建目标与扩散模型去噪目标不匹配，影响下游生成质量。
method: 将视频tokenizer训练为掩码自编码器形式的损坏-逆反模型，对齐扩散训练。
result: 在潜在视频扩散模型上提升了生成质量指标。
conclusion: VideoMAETok为扩散模型提供了一种更匹配的视频分词器训练范式。
---

## Abstract
Latent diffusion models have become the dominant paradigm for video generation, making the video tokenizer a critical role. While most existing tokenizers are trained primarily for reconstruction, diffusion models are optimized to denoise heavily corrupted latents, which creates a mismatch between tokenizer training objectives and downstream generative learning. As a result, reconstruction metrics (e.g., rFVD) can be a poor proxy for generation quality (gFVD), and overly prioritizing reconstruction may even hinder diffusion training. We propose VideoMAETok, a simple family of ViT-based video tokenizers trained explicitly as corruption-inversion models for latent video diffusion. VideoMAETok builds on masked autoencoders: we (i) apply high-ratio token masking and encode only visible spatiotemporal tokens for efficiency, and (ii) corrupt latent tokens with interpolative Gaussian noise to better match the denoising nature of diffusion generators. Training under such corruption encourages latents that remain informative and well-conditioned for downstream denoising. Extensive experiments show that VideoMAETok consistently improves generation quality when paired with off-the-shelf diffusion models (SiT and LightningDiT), achieving state-of-the-art gFVD on Kinetics-600 and UCF-101 while remaining compute-efficient. Code is available at https://github.com/yztongzhan/VideoMAETok.

---

## 论文详细总结（自动生成）

# VideoMAETok：通过掩码自编码器作为分词器提升视频扩散模型

## 1. 核心问题与整体含义（研究动机与背景）

- **研究背景**：潜在扩散模型（Latent Diffusion Models）已成为视频生成的主流范式。该类模型首先将视频压缩到潜空间，再由扩散模型在潜空间中去噪生成，因此**视频分词器（Video Tokenizer）**在其中扮演着至关重要的角色。
- **核心问题**：现有视频分词器主要以**重建（Reconstruction）**为目标进行训练，但扩散模型的本质是**去噪（Denoising）**——它需要处理的是被严重破坏的潜变量。这两者的训练目标存在根本性错配（Mismatch）。
- **关键观察**：由于训练目标不一致，**重建指标（如 rFVD）无法很好地反映生成质量（如 gFVD）**；过于追求重建性能甚至可能**阻碍扩散模型的训练**。
- **总体含义**：该论文提出，与其让分词器孤立地优化重建，不如将其训练为与扩散模型去噪过程相匹配的“损坏-逆反”（Corruption-Inversion）模型，从而使分词器学习到的潜空间对下游扩散生成更友好。这项工作为视频分词器提供了一种新的训练范式，具有方法论层面的启发意义。

## 2. 方法论：核心思想、关键技术细节

### 2.1 核心思想

- 将视频分词器显式训练为**损坏-逆反模型**，使它的编码-解码过程对齐扩散生成器的去噪训练过程。
- 具体做法是借助**掩码自编码器（Masked Autoencoder, MAE）**这一自监督框架来训练基于 ViT 的视频分词器。

### 2.2 关键技术细节

- **基于 ViT 的分词器架构**：VideoMAETok 采用 Vision Transformer（ViT）作为分词器的基础结构。
- **高比例 token 掩码（High-Ratio Token Masking）**：对视频时空 token 施加高比例的随机掩码，仅对可见的时空 token 进行编码，从而显著降低计算开销，提升训练效率。
- **插值高斯噪声破坏（Interpolative Gaussian Noise Corruption）**：在训练过程中，对潜 token 施加带有插值性质的高斯噪声进行破坏，以更好地匹配扩散生成器在实际训练中的去噪特性。
- **训练目标对齐**：通过对损坏后的 token 进行重建/逆反，分词器学到的潜空间能够保持信息丰富且对后续去噪过程条件良好（well-conditioned），从而弥合分词器训练与扩散模型训练之间的目标差距。

### 2.3 算法流程（文字说明）

1. 从输入视频中提取时空 patch 并转换为 token；
2. 以高比例随机掩码去除大部分 token，仅保留可见 token；
3. 对（部分或全部）潜 token 施加插值高斯噪声进行损坏；
4. 以掩码自编码器方式训练分词器，要求其从可见 token 和损坏的潜表示中重建原始视频信息；
5. 训练完成后，将分词器与现成的潜在扩散模型（如 SiT、LightningDiT）配对用于视频生成。

## 3. 实验设计

- **数据集**：
  - **Kinetics-600**（K-600）：大规模视频动作识别数据集，用作视频生成评测；
  - **UCF-101**：经典行为识别数据集，常用于视频生成 benchmark。
- **评测指标**：
  - **gFVD（生成视频 Frechet Video Distance）**：核心指标，衡量生成质量；
  - **rFVD（重建视频 FVD）**：用于对比重建与生成之间的差异。
- **对比方法**：
  - 配对的扩散模型：**SiT** 和 **LightningDiT**（均为现成、开箱即用的扩散模型）；
  - 与现有视频分词器方案进行对比，验证其生成质量的提升。

## 4. 资源与算力

- 在用户提供的材料（摘要与元数据）中，**没有明确给出**具体的 GPU 型号、使用数量或训练时长等算力信息。
- 论文仅在摘要中提及该方法“保持计算高效（compute-efficient）”且“只编码可见 token 以提升效率”，但并未量化具体的资源消耗。

## 5. 实验数量与充分性

- **实验数量**：
  - 论文报告了在 **2 个数据集**（Kinetics-600、UCF-101）上的生成质量评测；
  - 在 **2 种扩散模型**（SiT、LightningDiT）上进行了配对实验；
  - 从摘要看，还涉及与现有方法的对比。
- **充分性评估**：
  - **优点**：跨数据集、跨扩散模型架构的验证增强了一定的泛化说服力；以 gFVD 是否提升作为核心评判，直接回应了论文提出的“rFVD 不代表 gFVD”这一动机。
  - **不足**：由于材料有限，无法确认是否包含详尽消融（如掩码比例、噪声强度、ViT 规模等）；对比的分词器基线数量未见具体列表；缺乏用户研究或额外指标（如 CLIP 分数、文本-视频对齐）的补充证据。总体来看，公开信息下的实验描述偏向精简，其“充分性”有待全文细节进一步佐证。

## 6. 主要结论与发现

- **结论 1**：通过掩码自编码器训练的 VideoMAETok 分词器能够**显著提升潜在视频扩散模型的生成质量**。
- **结论 2**：在 Kinetics-600 和 UCF-101 上，结合 SiT 和 LightningDiT 时，VideoMAETok 取得了**当时最优（SOTA）的 gFVD**。
- **结论 3**：高比例掩码策略使训练过程对计算资源友好，**在保持高效的同时不牺牲生成性能**。
- **核心发现**：分词器的训练目标应与下游扩散模型的去噪目标对齐，而不是只顾重建精度；“为扩散而训练的分词器”比“为重建而训练的分词器”更有利于潜在视频扩散模型。

## 7. 优点

- **思路简洁且直击痛点**：直接指出了 tokenizer 训练目标与扩散模型去噪目标之间的错配问题，并以 MAE 框架给出一种简洁优雅的解决方案。
- **训练与生成目标对齐**：将分词器显式训练为“损坏-逆反”模型，使其学到的潜空间与扩散模型的数据分布更一致，这是方法论上的重要亮点。
- **计算高效**：通过高比例 mask 只编码可见 token，显著减少计算量，利于扩展到大规模视频数据。
- **即插即用**：与现成扩散模型（SiT、LightningDiT）可直接配对使用，无需改动扩散模型本身。
- **开源**：提供了代码仓库，便于复现和后续研究。

## 8. 不足与局限

- **算力信息缺失**：论文提供的材料中未报告 GPU 型号、数量或训练时长，难以评估其实际训练成本与可复现门槛。
- **实验覆盖有限**：只报告了 2 个数据集和 2 种扩散模型的组合，对更广泛的扩散架构（如 DiT、U-Net 类）和更多视频场景（如文本到视频、高分辨率/长视频）的适用性尚不明确。
- **指标单一性风险**：以 gFVD 为核心指标，虽然直接，但 FVD 类指标本身对评估指标的敏感性可能引入偏差，缺少多样化的评测维度（如语义一致性、时间一致性）。
- **掩码与噪声的引入方式可能造成信息损失**：高比例掩码和噪声破坏虽然提高了效率和生成质量，但潜空间中可能丢失高频细节，对重建任务的性能可能有潜在影响，论文未展开讨论这一权衡。
- **潜在偏差**：论文属于预印本/接收版本摘要，公开文本有限，无法核实消融实验的完整性和对比基线的公平性；此外，模型在特定数据集上的 SOTA 表现是否能在真实世界多样化的视频分布上持续成立仍有待验证。

（完）
