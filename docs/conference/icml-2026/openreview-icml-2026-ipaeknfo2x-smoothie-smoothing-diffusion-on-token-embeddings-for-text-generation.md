---
title: "Smoothie: Smoothing Diffusion on Token Embeddings for Text Generation"
title_zh: 平滑扩散：面向文本生成的词元嵌入平滑方法
authors: "Alexander Shabalin, Viacheslav Meshchaninov, Dmitry Vetrov"
date: 2026-04-30
pdf: "https://openreview.net/pdf/343053e34ffc16fb9006bcd1602bf3c2987533b4.pdf"
tags: ["query:diff-video"]
score: 4.0
evidence: 面向文本生成的扩散模型，非视频；仅匹配广义扩散生成模型需求
tldr: 扩散模型在文本生成中面临离散性挑战。现有方法要么在连续潜空间加高斯扩散但解码困难，要么在类别单纯形空间操作但忽略词元语义关系。本文提出Smoothie，通过对词元嵌入按语义相似度逐步平滑，在保持自然解码的同时实现渐进式信息移除，兼顾两类方法的优点，为离散文本上的扩散生成提供了一种新途径。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 文本的离散性使扩散模型难以直接应用，现有连续或离散方法各有缺陷。
method: 提出Smoothie，在词元嵌入上按语义相似度进行渐进式平滑，实现连续与离散表示的融合。
result: 实验表明Smoothie在文本生成上表现优异，既保留语义结构又能自然解码。
conclusion: 平滑词元嵌入方法为离散数据的扩散生成提供了更优的建模与解码途径。
---

## Abstract
Diffusion models have achieved state-of-the-art performance in generating images, audio, and video, but their adaptation to text remains challenging due to its discrete nature. Prior approaches either apply Gaussian diffusion in continuous latent spaces, which inherits semantic structure but struggles with token decoding, or operate in categorical simplex space, which respect discreteness but disregard semantic relation between tokens. In this paper, we propose Smoothing Diffusion on Token Embeddings (Smoothie), a novel diffusion method that combines the strengths of both approaches by progressively smoothing token embeddings based on semantic similarity. This technique enables gradual information removal while maintaining a natural decoding process. Experimental results on several sequence-to-sequence and unconditional generation tasks demonstrate that Smoothie outperforms existing diffusion-based models in generation quality. Furthermore, ablation studies show that our proposed diffusion space yields better performance than both the standard embedding space and the categorical simplex.

---

## 论文详细总结（自动生成）

# 论文总结：Smoothie —— 面向文本生成的词元嵌入平滑扩散方法

## 1. 核心问题与整体含义（研究动机与背景）
- **问题背景**：扩散模型在图像、音频、视频等连续模态上已取得顶尖表现，但文本是离散的（由有限词元组成），直接应用扩散模型存在根本性困难。
- **现有方法的不足**：
  - 连续潜空间高斯扩散方法：能够继承语义结构，但**解码为具体词元困难**；
  - 类别单纯形空间方法：尊重离散性，但**忽略词元之间的语义关系**。
- **核心问题**：能否设计一种扩散空间，既保留连续表示的语义丰富性，又能自然、高质量地解码回离散文本？
- **整体含义**：本文提出一种新的扩散生成范式，通过在词元嵌入上逐步“平滑”来实现渐进式噪声注入，为离散文本的扩散建模提供了一种兼顾两类方法优点的新思路。

## 2. 提出的方法论：Smoothie
- **核心思想**：不是直接在连续潜变量上添加高斯噪声，也不在类别单纯形上操作，而是**基于词元之间的语义相似度，对词元嵌入进行逐步平滑**。
- **关键机制**：
  - 通过语义相似度定义平滑操作，使“噪声”过程不是随机的，而是沿着语义结构逐渐模糊词元信息；
  - 这一过程实现了**渐进式信息移除**，即前向过程逐渐损失细节，保留高层语义；
  - 反向过程（生成）从平滑状态逐步恢复原始嵌入，由于平滑后的表示仍然位于嵌入空间，**解码过程与传统嵌入解码一致，自然且直接**。
- **技术细节与公式/算法流程**：原论文摘要未提供具体的数学公式或算法伪代码；从描述推断其流程大致为：
  1. 定义词元嵌入的语义相似度度量（如余弦相似度）；
  2. 前向过程按相似度逐步将当前词元嵌入向其他词元方向平均/插值，形成连续过渡；
  3. 反向过程训练神经网络逐步逆转该平滑过程；
  4. 最终从纯“平滑”状态或噪声状态采样，经反向去平滑恢复出目标文本的嵌入序列，再通过最近邻/softmax解码输出词元。
- **与现有方法的区别**：该扩散空间既保留了嵌入空间的语义结构，又避免了常见连续扩散中解码歧义问题；同时因为平滑过程与语义相关，比单纯形空间更能捕捉词间关系。

## 3. 实验设计
- **任务场景**：摘要明确提到“several sequence-to-sequence and unconditional generation tasks”，即涉及**序列到序列**（如翻译、摘要等）和**无条件生成**（如纯文本生成）两类任务。
- **Benchmark / 数据集**：原文摘要**未列出**具体数据集名称（如 WMT、CNN/DailyMail、PTB 等），无法得知。
- **对比方法**：摘要仅笼统地表示“existing diffusion-based models”，即与已有的基于扩散的生成模型比较，但**未列出具体基线**（如 Diffusion-LM、SSD-LM、DiffuSeq 等）。
- **消融实验**：摘要提到做了 “ablation studies”，对比了所提扩散空间与两种替代空间：
  - 标准嵌入空间（standard embedding space）；
  - 类别单纯形（categorical simplex）。
  结果显示所提空间优于两者。

## 4. 资源与算力
- **论文中未明确说明**：提供的文本中没有提及使用的 GPU 型号、数量、训练时长、Batch Size 等算力信息。
- 需要指出：由于信息缺失，无法评估其训练成本或可复现性所需的硬件条件。

## 5. 实验数量与充分性
- **实验数量**：从摘要仅能确认有主实验（多个任务）和消融实验，但**具体实验组数未知**。
- **充分性评估**：
  - 从覆盖范围看，同时涉及序列到序列和无条件生成，覆盖面较广；
  - 有消融验证扩散空间设计的有效性，增加了可信度；
  - 但由于论文正文未提供，无法判断数据集规模、评价指标、多组随机种子、统计显著性检验等细节，因此**无法全面评估实验是否足够充分、客观、公平**。
  - 建议以正式论文全文为准进行完整评估。

## 6. 主要结论与发现
- 提出 SMOOTHIE 方法，通过在词元嵌入上按语义相似度逐步平滑，成功结合了连续扩散和离散扩散的优势。
- 在多个序列到序列和无条件生成任务中，Smoothie 的生成质量**优于现有基于扩散的模型**。
- 消融实验表明，所提出的**平滑扩散空间**比标准嵌入空间和类别单纯形空间都更优，证实了该设计的关键作用。
- 总体结论：平滑词元嵌入为离散数据上的扩散生成提供了一种更理想的建模与解码途径。

## 7. 优点
- **方法新颖性**：从语义相似度角度设计扩散过程，不同于简单高斯噪声或单纯形操作，具有较好的理论动机。
- **融合优势**：同时保留语义结构（连续方法的优点）和自然解码（离散方法的优点），实现了两者平衡。
- **解码简单**：因为操作始终在词元嵌入空间，解码逻辑与普通语言模型一致，避免了复杂的两阶段生成。
- **普适性**：可适用于序列到序列和无条件生成等多种文本生成场景。
- **有消融验证**：通过消融实验确认了扩散空间设计的决定性作用，增强了方法的可解释性。

## 8. 不足与局限
- **信息缺失**：当前提供的论文摘要过于简洁，缺少方法具体公式、网络架构、训练细节、超参数设置等关键内容，难以深入评估。
- **实验覆盖未知**：未列出具体数据集和基线，无法判断实验是否具有代表性；也未见多语言、长文本、大规模数据集等更全面的验证。
- **潜在偏差风险**：基于语义相似度的平滑可能过度依赖嵌入质量；对于抽象词、稀有词或语义相近但含义不同的词元，平滑过程可能引入混淆。
- **应用限制**：生成质量虽报道更优，但文本扩散模型通常推理较慢；文中未讨论效率问题，实际部署可能受限。
- **复现性**：没有提供代码或详细实验配置，暂时无法复现。

---

（完）
