---
title: One-step Latent-free Image Generation with Pixel Mean Flows
title_zh: 基于像素均值流的单步无潜空间图像生成
authors: "Yiyang Lu, Susie Lu, Qiao Sun, Hanhong Zhao, Zhicheng Jiang, Xianbang Wang, Tianhong Li, Zhengyang Geng, Kaiming He"
date: 2026-04-30
pdf: "https://openreview.net/pdf/45dc467e5976acda40a089f6f336a7d495c400ed.pdf"
tags: ["query:diff-video"]
score: 7.0
evidence: 像素均值流实现单步流式图像生成
tldr: 当前扩散/流模型多采用多步采样和潜空间表示，本文提出像素均值流pMF，把网络输出空间与损失空间分开，网络在图像流形上预测x，损失用均值流速度空间定义。借助两者间的简单变换，pMF在像素域实现单步图像生成，兼顾生成质量与速度。该工作为无潜空间单步生成提供了新路径。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散/流模型通常需多步采样且在潜空间进行，限制效率与透明度。
method: 提出pixel MeanFlow (pMF)，将网络输出空间与损失空间分离，网络预测低维图像流形上的x，损失在均值流速度空间定义。
result: 实现无潜空间的单步图像生成，性能优于以往方法。
conclusion: 表明输出空间与损失空间解耦可兼顾单步采样与像素级生成。
---

## Abstract
Modern diffusion/flow-based models for image generation typically exhibit two core characteristics: (i) using multi-step sampling, and (ii) operating in a latent space. Recent advances have made encouraging progress on each aspect individually, paving the way toward one-step diffusion/flow without latents. In this work, we take a further step towards this goal and propose "pixel MeanFlow" (pMF). Our core guideline is to formulate the network output space and the loss space separately. The network target is designed to be on a presumed low-dimensional image manifold (i.e., x-prediction), while the loss is defined via MeanFlow in the velocity space. We introduce a simple transformation between the image manifold and the average velocity field. In experiments, pMF achieves strong results for one-step latent-free generation on ImageNet at 256$\times$256 resolution (2.22 FID) and 512$\times$512 resolution (2.48 FID), filling a key missing piece in this regime. We hope that our study will further advance the boundaries of diffusion/flow-based generative models.

---

## 论文详细总结（自动生成）

根据提供的论文信息，以下是对该论文的详细中文总结：

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **背景**：现代基于扩散/流的图像生成模型通常具备两大核心特征：(i) 采用多步采样，以及 (ii) 在潜空间（Latent Space）中操作。多步采样导致推理速度慢，潜空间操作则降低了生成过程的透明度。
- **研究动机**：尽管近期研究在这两个方向上分别取得了进展，但尚无工作能在**不依赖潜空间**的前提下实现**单步生成**。本文旨在填补这一关键空白，探索一种既无需潜空间表示又能完成单步采样的图像生成新范式。
- **整体含义**：通过重新设计网络输出空间与损失空间的关系，论文挑战了“单步采样必须依赖潜空间”的固有假设，为高效、透明的生成模型提供了新的可能性。

## 2. 论文提出的方法论

- **核心思想**：提出 **Pixel MeanFlow（pMF）**，核心准则是**将网络输出空间与损失空间分离开来**。
  - **网络输出空间**：网络目标被设定在假设的低维图像流形上，即采用 **x-prediction**（直接预测图像本身）。
  - **损失空间**：损失函数通过 **MeanFlow** 在**速度空间（velocity space）** 中定义。
- **关键技术细节**：论文引入了图像流形与平均速度场之间的**简单变换**，从而建立起像素空间网络输出与速度空间损失之间的桥梁。这种解耦设计使得模型能够在像素域直接进行单步生成，同时利用速度空间损失进行有效优化。

## 3. 实验设计

- **数据集与场景**：实验在 **ImageNet** 数据集上进行，覆盖两个标准分辨率场景：
  - 256×256 分辨率
  - 512×512 分辨率
- **Benchmark**：以 **FID（Fréchet Inception Distance）** 作为主要评估指标。
- **对比方法**：摘要中未列出具体的基线方法名称，但表述为 pMF 在“one-step latent-free generation”领域中取得了显著成果，暗示其与现有的单步生成或潜空间生成方法进行了对比。由于原文信息有限，具体对比方法集（如与其他流匹配模型、蒸馏扩散模型或 GAN 的对比）尚不明确。

## 4. 资源与算力

- **未明确说明**：提供的原文（摘要与元数据）中**未提及**任何关于训练算力的细节，包括 GPU 型号、GPU 数量、训练时长（天数/小时数）等信息。

## 5. 实验数量与充分性

- **实验数量**：从现有文本看，论文报告了在 ImageNet 上两个分辨率（256² 和 512²）的实验结果，分别取得 2.22 FID 和 2.48 FID。
- **充分性评估**：
  - **客观性**：FID 是业界标准的生成质量评估指标，结果可信度较高。
  - **公平性**：由于摘要未列出详细对比方法、消融实验或训练细节，**无法完全判断实验对比的公平性**。
  - **覆盖度**：论文仅展示了标准分辨率下的 FID 指标，未提及如 MS-COCO、LSUN 等其他数据集上的泛化验证，也未显示关于“输出空间与损失空间解耦”这一核心设计的具体消融研究。因此，从可获取的信息来看，实验覆盖范围**相对有限**，验证深度可能不足。

## 6. 论文的主要结论与发现

- **核心结论**：pMF 在 ImageNet 上实现了**无潜空间的单步图像生成**，并在 256×256（2.22 FID）和 512×512（2.48 FID）分辨率下取得了强竞争力的结果，填补了这一领域的关键空白。
- **关键发现**：证明了**将网络输出空间与损失空间解耦**是可行的——既可以保持单步采样的高效性，又能实现像素级（latent-free）的生成质量。这一设计范式为未来无潜空间生成模型的研究提供了新的思路。

## 7. 优点

- **创新性强**：首次将“输出空间”与“损失空间”分离作为核心准则，打破了传统流模型必须绑定两者或在潜空间操作的限制。
- **实用价值高**：同时实现“单步采样”和“无需潜空间”，在保持生成质量的同时大幅简化了采样流程，潜在推理速度优于多步潜空间方法。
- **结果明确**：在 ImageNet 标准基准上报告了清晰的 FID 成绩，验证了方法的有效性。

## 8. 不足与局限

- **实验细节缺失**：摘要中未提供对比基线的具体列表、消融实验（如验证 x-prediction 与速度空间损失解耦的贡献度）、以及不同模型规模（参数量）下的性能表现。
- **评估范围有限**：仅在 ImageNet 上验证，缺乏在文本到图像（Text-to-Image）或更大尺寸（如 1024×1024）等更广泛、更具挑战性场景下的测试。
- **算力信息未披露**：缺少训练成本描述，导致无法评估该方法的实际部署门槛。
- **理论假设有待检验**：论文提到“presumed low-dimensional image manifold”，该假设的合理性及其在不同数据分布下的适用性尚未在摘要中论证。

（完）
