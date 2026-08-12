---
title: Revisiting Spectral Representations in Generative Diffusion Models
title_zh: 重新审视生成式扩散模型中的谱表示
authors: "Yuehao Wang, Peihao Wang, Hanwen Jiang, Ziyi Yang, Qixing Huang, Zhangyang Wang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/875ad8df057fd76c87224c056f4ff9bace6c92f4.pdf"
tags: ["query:diff-video"]
score: 7.0
evidence: 研究扩散生成模型中的谱表示学习，与扩散模型理论直接相关
tldr: 扩散模型虽在各类生成任务上表现出色，但其隐藏层上的表示对齐为何能同时提升训练收敛与采样质量仍不清楚。本文从扰动核的共同视角，将自监督谱表示学习与扩散生成模型联系起来，揭示二者在加噪去噪过程中的内在一致性。该研究有助于从表示学习层面理解扩散模型的工作机制，并为改进扩散训练与采样提供理论支撑。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型表示对齐的机制尚不明确，缺乏统一理论解释。
method: 通过扰动核视角统一谱表示学习与扩散生成模型，分析其内在联系。
result: 揭示了谱表示与扩散模型共享机制，解释训练收敛与采样质量提升的原因。
conclusion: 为扩散模型的表示设计与训练策略提供了理论依据，可迁移至视频等生成任务。
---

## Abstract
Diffusion models have shown remarkable performance on diverse generation tasks. Recent work finds that imposing representation alignment on the hidden states of diffusion networks can both facilitate training convergence and enhance sampling quality, yet the mechanism driving this synergy remains insufficiently understood. In this paper, we investigate the connection between self-supervised spectral representation learning and diffusion generative models through a shared perspective on perturbation kernels. On the diffusion side, samples (e.g., images, videos) are produced by reversing a stochastic noise-injection process specified by Gaussian kernels; on the spectral representation side, spectral embeddings emerge from contrasting positive and negative relations induced by random perturbation kernels. Motivated by this, we propose a self-supervised spectral representation alignment method to facilitate diffusion model training. In addition, we clarify how joint spectral learning can benefit diffusion training from a geometric perspective. Furthermore, we find that the optimization of the spectral alignment objective is in an equivalent form of diffusion score distillation in the representation space. Building on these findings, we integrate a spectral regularizer into diffusion training objectives to improve the performance of diffusion models on multiple datasets. Experiments across images and 3D point clouds show consistent gains in generation quality.

---

## 论文详细总结（自动生成）

# 论文总结：重新审视生成式扩散模型中的谱表示

## 一、论文的核心问题与整体含义（研究动机与背景）

- **核心问题**：扩散模型在图像、视频等生成任务中表现出色，但近期研究发现——对扩散网络隐藏层施加表示对齐，既能促进训练收敛、又能提升采样质量——这一现象背后的协同机制长期缺乏理论解释。
- **研究动机**：作者希望从表示学习的层面，为扩散模型的加噪-去噪过程提供统一的数学与几何视角，进而解释"为何谱表示对齐能帮助扩散模型"。
- **整体含义**：若能将自监督谱表示学习与扩散生成模型在理论上统一起来，则可为扩散模型的架构设计、训练正则化策略以及跨模态（如图像、视频、3D点云）生成提供理论支撑和方法工具。

## 二、论文提出的方法论：核心思想、关键技术细节与算法流程

### 核心思想
- **共享视角——扰动核（Perturbation Kernels）**：扩散模型通过高斯核定义的随机噪声注入-逆向过程生成样本；谱表示学习通过随机扰动核构造正负样本对进行对比学习。二者共享相似的"扰动核"结构，因此存在内在一致性。
- 作者由此提出一种**自监督谱表示对齐方法（self-supervised spectral representation alignment）**，将其作为训练扩散模型的正则化手段。

### 关键技术细节
- **联合谱学习**：在扩散训练过程中，同步对隐藏层施加谱表示对齐约束。
- **几何角度解释**：从几何视角阐明了联合谱学习为何能加速扩散训练（例如有助于保持隐空间的结构稳定性）。
- **理论等价性发现**：谱对齐目标函数的优化，在表示空间中与扩散分数蒸馏（score distillation）具有等价形式，从而将对比学习与扩散模型的分数匹配目标联系起来。

### 算法流程（文字说明）
1. 前向加噪：按扩散过程用高斯扰动核对样本逐步加噪。
2. 反向去噪训练：以去噪网络估计分数函数。
3. 在去噪网络的隐藏状态上附加一个谱表示对齐正则项。
4. 联合优化去噪目标与谱对齐目标，利用二者的等价性平衡损失。
5. 训练完成后，采用标准采样过程生成新样本。

### 表述公式要点
- 谱对齐目标与扩散分数蒸馏在表示空间中形式等价。
- 最终训练目标为扩散生成损失 + 谱正则化项。

## 三、实验设计：数据集、场景与对比方法

- **数据集与场景**：
  - 图像生成任务
  - 3D 点云生成任务
  - （论文摘要中未列出具体数据集名称，如 CIFAR、ImageNet、ShapeNet 等，需查看全文确认）
- **Benchmark**：论文未在摘要中说明具体基准；但声称在多个数据集上获得一致的生成质量提升。
- **对比方法**：论文摘要未列出具体基线模型或对照方法（需查阅全文获取细节）。

## 四、资源与算力

- 论文的摘要和元数据中**未提及任何具体算力信息**（如 GPU 型号、数量、训练时长、显存占用等）。
- 若要了解训练成本，需要查阅论文全文的实验设置部分。

## 五、实验数量与充分性

- 从摘要来看，实验覆盖了两个模态：**图像** 和 **3D 点云**，在多个数据集上报告了一致的生成质量提升。
- **充分性评价**：
  - 优点方面：跨模态（2D 图像 + 3D 点云）验证了方法的普适性。
  - 不足方面：摘要中未报告消融实验、未说明与具体基线方法的量化对比指标（如 FID、Coverage 等），也未展示采样效率、训练稳定性等附加分析。
  - 总体而言：实验在广度上有一定覆盖，但摘要层面的信息不足以判断其完整公平性，需要结合论文正文评估。

## 六、论文的主要结论与发现

1. 谱表示学习与扩散生成模型共享"扰动核"机制，二者在数学形式上存在内在一致性。
2. 自监督谱表示对齐可同时促进扩散模型的训练收敛和采样质量。
3. 从几何视角看，联合谱学习有助于扩散模型更好维持隐空间结构。
4. 谱对齐目标的优化与表示空间中的扩散分数蒸馏等价，揭示了两者的深度联系。
5. 将谱正则化器集成到扩散训练目标中，可在图像与3D点云上带来一致的生成质量提升。

## 七、优点

- **理论贡献突出**：首次从扰动核的统一视角连接两个看似独立的研究分支（自监督谱表示学习与扩散生成模型）。
- **方法论创新**：提出的谱表示对齐方法具有自监督性质，可天然嵌入扩散训练流程。
- **理论-实践结合紧密**：不仅提出方法，还从几何角度和分数蒸馏等价性两个层次论证机理，解释性较强。
- **跨模态验证**：在图像与3D点云两种不同类型的生成任务上都获得增益，说明方法具有一定的通用性，并推断可迁移至视频等生成任务。

## 八、不足与局限

- **实验信息不完整**：摘要未给出具体数据集名称、基线方法、评估指标、消融实验等，难以判断实验的严谨程度和可比性。
- **算力信息缺失**：未提供训练成本相关信息，不利于后续研究者复现和评估实际应用价值。
- **应用边界不明**：虽然作者推测可迁移到视频生成，但论文实验未覆盖视频；对于更高维、更复杂模态的适用性仍是开放问题。
- **理论假设的局限**：谱表示对齐与分数蒸馏的等价性分析可能基于特定扰动核假设，是否对任意噪声调度和网络结构都成立，需要看全文中假设条件的限定范围。
- **训练开销问题**：引入额外正则化器可能增加训练计算负担，论文未对这一潜在权衡进行说明或优化。
- **正文内容缺失**：当前仅能获取论文摘要和元数据，无法对更多具体细节进行全面审查。

---

（完）
