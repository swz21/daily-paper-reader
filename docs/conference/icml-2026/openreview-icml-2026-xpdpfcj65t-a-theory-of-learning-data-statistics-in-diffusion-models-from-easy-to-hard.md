---
title: "A theory of learning data statistics in diffusion models, from easy to hard"
title_zh: 扩散模型中数据统计学习理论：从简单到困难
authors: "Lorenzo Bardone, Claudia Merger, Sebastian Goldt"
date: 2026-04-30
pdf: "https://openreview.net/pdf/de9ddc71239c090296743c0a070b8cf80f8cc441.pdf"
tags: ["query:diff-video"]
score: 8.0
evidence: 扩散模型作为生成模型的学习动力学理论
tldr: 扩散模型虽然作为生成模型表现强大，但其学习动力学仍不清楚。本文通过实证发现标准扩散模型在自然图像上呈现简单性偏好，先学习成对统计再学习高阶相关，并在混合累积量模型中复现该行为。作者识别出控制相关学习样本复杂度的标量不变量——扩散信息指数。该理论为理解扩散模型从易到难的学习过程提供了新视角。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型学习动力学不明确，缺乏对简单到复杂统计学习顺序的解释。
method: 结合实证与最小数据模型，定义扩散信息指数刻画相关学习的样本复杂度。
result: 揭示了扩散模型学习成对与高阶相关的简单性偏好及其理论基础。
conclusion: 该理论有助于理解扩散模型的表征学习机制。
---

## Abstract
While diffusion models have emerged as a powerful class of generative models, their learning dynamics remain poorly understood.
We address this issue first by empirically showing that standard diffusion models trained on natural images exhibit a simplicity bias, learning simple, pair-wise input statistics before specializing to higher-order correlations. We reproduce this behaviour in simple denoisers trained on a minimal data model, the mixed cumulant model, where we precisely control both pair-wise and higher-order correlations of the inputs. We identify a scalar invariant of the model that governs the sample complexity of learning pair-wise and higher-order correlations that we call the _diffusion information exponent_, in analogy to related invariants in different learning paradigms.
Using this invariant, we prove that the denoiser learns simple, pair-wise statistics of the inputs at linear sample complexity, while more complex higher-order statistics, such as the fourth cumulant, require at least cubic sample complexity. We also prove that the sample complexity of learning the fourth cumulant is linear if pair-wise and higher-order statistics share a correlated latent structure.
Our work describes a key mechanism for how diffusion models can learn distributions of increasing complexity.

---

## 论文详细总结（自动生成）

# 扩散模型中数据统计学习理论：从简单到困难

## 1. 核心问题与整体含义

- **研究动机**：扩散模型虽已成为一类强大的生成模型，但其**学习动力学**（learning dynamics）仍缺乏理论理解。具体而言，模型在训练过程中如何逐步学习数据分布的不同统计结构，这一问题尚不明确。
- **核心问题**：标准扩散模型在自然图像上是否遵循“从简单到困难”的学习顺序？这种顺序由什么内在不变量控制？成对统计（如二阶相关）与高阶统计（如四阶累积量）的样本复杂度有何差异？
- **整体含义**：该工作旨在揭示扩散模型从数据中学习统计结构的机制，为理解其表征学习和生成能力提供理论基础，并可能指导更高效的训练策略。

## 2. 方法论

- **核心思想**：通过实证观察和最小数据模型相结合，识别出一个控制学习难度的标量不变量——**扩散信息指数**（diffusion information exponent），并以此证明不同阶统计量的样本复杂度差异。
- **技术细节**：
  - 首先在**自然图像**上训练标准扩散模型，观察其学习顺序：先学习简单的成对统计，再学习高阶相关。
  - 引入**混合累积量模型**（mixed cumulant model）作为最小数据模型，在该模型中可精确控制输入的二阶相关和高阶相关。
  - 定义**扩散信息指数**，类比其他学习范式（如监督学习中的信息指数）中的相关不变量。
  - 通过该不变量进行理论证明：
    - 成对统计量（二阶）的学习只需要**线性样本复杂度**。
    - 第四累积量（高阶）的学习至少需要**三次方样本复杂度**。
    - 若成对与高阶统计共享潜在的**相关隐结构**，则学习四阶累积量的样本复杂度可降至**线性**。
- **算法流程**（文字说明）：
  - 训练标准去噪器 → 记录学习过程中统计量的收敛顺序 → 在混合累积量模型下复现 → 计算扩散信息指数 → 推导样本复杂度界限。

## 3. 实验设计

- **数据集/场景**：
  - 自然图像数据集（用于实证观察标准扩散模型的简单性偏好）。
  - 混合累积量模型（用于受控实验，精确调节二阶与高阶相关）。
- **Benchmark**：无对比方法，因为是理论分析为主，实验主要用于验证理论预测。
- **对比方法**：未与其他生成模型或训练算法进行比较；对比的是不同阶统计量的学习难度。

## 4. 资源与算力

- 论文内容中**未明确说明**使用的GPU型号、数量或训练时长等算力信息。
- 由于主要贡献为理论分析，实验规模可能相对较小，但文中未提供具体计算资源细节。

## 5. 实验数量与充分性

- **实验组数**：文中提到的实证包括：(1) 自然图像上的标准扩散模型训练观察；(2) 混合累积量模型上的去噪器训练与理论验证。未提及消融实验或多数据集对比。
- **充分性评价**：
  - 作为理论驱动的研究，实验主要用于佐证理论预测，覆盖了关键现象（简单性偏好、样本复杂度差异）。
  - 但实验范围较窄：仅使用单一自然图像数据集（未指定具体数据集）和一个人工模型；未在多种真实数据分布（如文本、音频）上进行验证。
  - 公平性：人工数据模型具有精确可控性，结论在理论层面是严密的；但缺乏与真实大尺度扩散模型的端到端验证，可能限制泛化性论断。

## 6. 主要结论与发现

- 标准扩散模型在自然图像上表现出**简单性偏好**：先学习成对统计，再学习高阶相关。
- 该行为可在混合累积量模型中复现，说明该现象是数据统计结构的基本性质，而非特定数据集的偶然。
- 提出**扩散信息指数**作为控制相关统计量学习样本复杂度的关键不变量。
- 理论证明了：
  - 二阶统计量：线性样本复杂度。
  - 四阶累积量：至少三次方样本复杂度。
  - 存在共享隐结构时，四阶累积量的复杂度可降为线性。
- 该工作为扩散模型如何从易到难地学习数据分布提供了机制性解释。

## 7. 优点

- **理论深度**：首次将“信息指数”概念引入扩散模型学习动力学，建立了可验证的数学框架。
- **最小模型设计**：混合累积量模型能够精确分离不同阶统计量，为理论分析提供了干净的测试床。
- **实证与理论结合**：先观察真实图像上的现象，再通过受控模型复现并进行证明，逻辑链条完整。
- **洞见性**：揭示了扩散模型学习顺序与统计量阶数之间的定量关系，并指出潜在隐结构可降低高阶统计学习难度，对表征学习有启示。

## 8. 不足与局限

- **算力信息缺失**：未提供任何训练资源、时间或模型规模的细节，难以评估方法在大规模场景下的实际成本。
- **实验覆盖有限**：自然图像实验未指明具体数据集（如CIFAR-10、ImageNet），且未扩展到其他模态；缺乏与现有生成模型学习理论的横向对比。
- **理论假设的适用性**：混合累积量模型是高度简化的数据模型，真实复杂数据可能包含更丰富的结构，扩散信息指数能否完整刻画真实场景尚需更多验证。
- **样本复杂度的证明边界**：文中证明的复杂度界限（线性、三次方）是在特定设定下成立，对噪声调度、网络架构等超参数的敏感性未讨论。
- **缺乏实际应用验证**：未展示该理论如何改善扩散模型的训练效率或生成质量，属于纯理论贡献，工程价值需进一步探索。

（完）
