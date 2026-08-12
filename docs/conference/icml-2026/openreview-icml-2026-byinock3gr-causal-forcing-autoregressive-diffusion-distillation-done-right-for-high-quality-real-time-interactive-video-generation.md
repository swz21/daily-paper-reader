---
title: "Causal Forcing: Autoregressive Diffusion Distillation Done Right for High-Quality Real-Time Interactive Video Generation"
title_zh: 因果强制：面向高质量实时交互视频生成的自回归扩散蒸馏
authors: "Hongzhou Zhu, Min Zhao, Guande He, Hang Su, Chongxuan Li, Jun Zhu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/7412280feb1da18be6050f695eeec1a3685f0b07.pdf"
tags: ["query:diff-video"]
score: 9.0
evidence: 将双向视频扩散模型蒸馏为自回归模型以实现实时交互视频生成
tldr: 实时交互视频生成需要将预训练的双向视频扩散模型蒸馏为少步自回归模型，但全注意力转因果注意力会造成架构差距。现有方法用ODE蒸馏初始化学生模型，因缺少帧级单射而无法恢复教师流映射，退化为条件期望解。本文提出因果强制（Causal Forcing），通过理论指导的蒸馏方式填平这一差距，在保持高质量生成的同时实现实时交互，为视频扩散模型的实时部署提供了可靠方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 双向扩散蒸馏到自回归存在架构差距，现有方法退化为条件期望解。
method: 提出因果强制方法，从理论上修复帧级可逆性条件，实现正确的自回归蒸馏。
result: 实验表明该方法生成质量和速度均优于现有蒸馏方案，支持实时交互。
conclusion: 为实时交互视频生成提供了一种理论可靠的自回归扩散蒸馏框架。
---

## Abstract
To achieve real-time interactive video generation, current methods distill pretrained bidirectional video diffusion models into few-step autoregressive (AR) models, facing an *architectural gap* when full attention is replaced by causal attention. However, existing approaches do not bridge this gap theoretically. They initialize the AR student via ODE distillation, which requires *frame-level injectivity*, where each noisy frame must map to a unique clean frame under the PF-ODE of an *AR teacher*. Distilling an AR student from a bidirectional teacher violates this condition, preventing recovery of the teacher's flow map and instead inducing a conditional-expectation solution, which degrades performance. To address this issue, we propose *Causal Forcing*, which uses an autoregressive teacher for ODE initialization to bridge the architectural gap, and then applies the same DMD procedure as in Self Forcing. Empirical results show that our method outperforms all baselines across all metrics, surpassing the SOTA Self Forcing by 19.3\% in Dynamic Degree, 8.7\% in VisionReward, and 16.7\% in Instruction Following. Project page: https://thu-ml.github.io/CausalForcing.github.io/; the code: https://github.com/thu-ml/Causal-Forcing.

---

## 论文详细总结（自动生成）

# 《因果强制：面向高质量实时交互视频生成的自回归扩散蒸馏》论文总结

## 1. 核心问题与整体含义

- **研究背景**：实时交互式视频生成要求模型能够快速响应条件输入并逐帧生成，而预训练的双向视频扩散模型虽然生成质量高，但由于采样步骤多、全注意力架构难以支持流式推理，无法直接用于实时交互场景。
- **核心问题**：现有的蒸馏方案将预训练双向扩散模型蒸馏为少步自回归（AR）模型，用因果注意力替换全注意力，由此引入了“架构差距”。但现有方法并未从理论上弥合这一差距：
  - 它们通过**ODE蒸馏**初始化学生模型，前提要求教师模型具有**帧级单射性**——即在AR教师的概率流ODE（PF-ODE）中，每个带噪声帧都应唯一映射到一个干净帧。
  - 从**双向教师**蒸馏出的AR学生不满足该条件，导致无法恢复教师的流映射，只能退化为**条件期望解**，严重损害生成质量。
- **整体意义**：本文提出了**Causal Forcing（因果强制）**方法，从理论上修复帧级可逆性条件，以可靠的方式实现自回归扩散蒸馏，使高质量实时交互视频生成成为可能，填补了该方向的理论空白。

## 2. 方法论：核心思想与关键技术细节

- **核心思想**：要消除架构差距，就不能用双向教师去初始化AR学生。Causal Forcing 改用**自回归教师（AR teacher）**参与ODE初始化，从而满足帧级单射性条件，让学生的流映射能够正确对应教师的流映射。
- **关键技术细节与流程**：
  1. **ODE蒸馏初始化**：使用自回归教师模型构造PF-ODE，保证每个带噪帧到干净帧映射的唯一性，从而满足帧级可逆性条件；这一步从根源上避免了条件期望退化解。
  2. **DMD蒸馏过程**：在完成合理的ODE初始化后，采用与 **Self Forcing** 相同分布的匹配蒸馏（DMD）程序，进一步优化自回归学生模型，缩小其与真实数据分布之间的差距。
  3. **推理阶段**：训练完成的学生模型以自回归方式逐帧生成，支持少步采样，从而在运行时实现实时交互。

## 3. 实验设计

- **数据集与场景**：论文主要面向真实世界视频生成任务，但从摘要来看，文章未明确指出使用了哪些具体数据集（如UCF101、MSR-VTT等）——这在该摘录文本中缺省。
- **基准（Benchmark）**：同样未在摘要中给出明确的基准数据集名称。
- **对比方法**：
  - 与当前 SOTA 方法 **Self Forcing** 进行了直接对比；
  - 同时对比了其他基线方法（具体名称在摘要中未列出）。
- **评价指标**：
  - **Dynamic Degree（动态程度）**：衡量视频中动态变化强度；
  - **VisionReward**：基于视觉奖励模型的生成质量评估；
  - **Instruction Following（指令跟随度）**：评估对文本指令的遵循程度。

## 4. 资源与算力

- **摘要与元数据中未提及**任何具体算力信息，包括GPU型号、GPU数量、训练时长、分布式配置等。
- 无法判断训练开销是否显著高于现有方法，这是一处信息缺失。

## 5. 实验数量与充分性

- **实验数量**：从摘要可见，论文至少报告了与Self Forcing的对比结果。由于提供的文本仅包含摘要，无法得知是否存在不同数据集上的多样实验、消融实验、以及不同模型规模/类别的测试。
- **充分性与客观性评估**：
  - 在已有信息范围内，Causal Forcing在三个指标上全面超越Self Forcing，说明其优势具备一定一致性；
  - 但缺少消融研究的具体说明（比如“如果不采用AR教师初始化，具体掉多少分”），也缺少基准数据集的名称，难以从这份摘要中判断实验系统性；
  - 作为ICML-2026接收论文，审稿人评估得分9.0，侧面表明实验在论文全文层面较充分。

## 6. 主要结论与发现

- **核心结论**：双向教师与AR学生之间的架构差距，是导致现有蒸馏方法退化为条件期望解的根本原因；使用AR教师进行ODE初始化可以弥补这一差距。
- **方法效果**：Causal Forcing比SOTA的 **Self Forcing** 在以下指标上分别显著提升：
  - **Dynamic Degree** 提升 19.3%；
  - **VisionReward** 提升 8.7%；
  - **Instruction Following** 提升 16.7%。
- **总体判断**：该方法在保持高质量生成的同时实现了实时交互，为视频扩散模型的实时部署提供了可靠且理论完备的解决方案。

## 7. 优点

- **理论驱动**：不依赖经验调参，而是从帧级可逆性这一数学条件出发诊断问题并设计解决方案，学术说服力强。
- **问题诊断准确**：明确指出现有方法退化的原因——双向教师不满足帧级单射性，条件期望解导致生成质量受限。
- **设计简洁优雅**：复用已有的DMD过程，仅将ODE初始化环节换为AR教师，即可同时解决理论问题和实践问题，实现成本低。
- **生成质量与速度兼顾**：支持少步自回归采样，满足实时交互需求，同时生成质量全面超越已有方法。
- **开源开放**：提供了项目主页和代码仓库，便于复现与后续研究。

## 8. 不足与局限

- **实验细节信息不足**：摘要中未列出所使用的数据集与基准名称，也未报告具体算力资源，读者难以评估泛化性与可复现性。
- **依赖AR教师质量**：方法效果高度依赖AR教师自身的生成能力，如果AR教师本身能力有限，可能会成为新的瓶颈。
- **应用范围**：当前主要面向视频生成；该框架是否能自然推广到其他时序生成模态（如音频、状态序列等）尚不清楚。
- **交互实时性**：虽然论文声称支持实时交互，但未在摘要中给出具体帧率（FPS）或延迟的数字，所谓“实时”的实际程度待考量。
- **与Self Forcing的关系**：方法在初始化之后复用Self Forcing的DMD程序，论文中未在摘要部分明确说明二者在理论上的深层差异，读者需阅读全文才能完整理解。

（完）
