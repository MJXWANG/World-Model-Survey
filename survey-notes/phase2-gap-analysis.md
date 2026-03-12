# Phase 2: Gap 分析 & 社区关注点

> 基于 Phase 1 的 15 篇 benchmark 论文 + 10 篇 survey/position 论文的 abstract 直接证据

---

## 一、核心 Gap 总结（有论文 evidence）

### Gap 1：视觉真实 ≠ 物理正确

**Evidence（直接引用 abstract）**：

> Physics Cognition Survey (2503.21765): *"generated content often violates the fundamental laws of physics, falling into the dilemma of **'visual realism but physical absurdity'**"*

> WorldModelBench (2502.20694): *"current benchmarks fail to rigorously evaluate these claims, **focusing only on general video quality**, ignoring important factors to world models such as **physics adherence**"*

**分析**：
- 现有 video generation model 在 FVD/FID 上得分高，但生成的视频里物体可以凭空消失、质量不守恒、运动方向违反重力
- 这是最被广泛认可的 gap，但解决方案仍在探索阶段
- WorldModelBench 的尝试（检测质量守恒违规）是迈出的第一步，但其判定标准仍较粗糙

**仍未解决的问题**：
- 如何 scalable 地自动检测物理违规（不依赖昂贵的人工标注）
- 更细粒度的物理定律违规检测（动量、能量、流体动力学等）

---

### Gap 2：开环生成质量 ≠ 闭环决策能力

**Evidence**：

> World-in-World (2510.18135): *"Progress on this question has been limited by **fragmented evaluation**: most existing benchmarks focus on **either** generation quality **or** downstream task performance"*

> ACT-Bench (2412.05337): *"current research primarily evaluates these models based on **visual realism or downstream task performance**, with **limited attention** to whether world models can correctly respond to **action inputs**"*

**分析**：
- 生成漂亮的视频 ≠ 能做出正确决策。一个模型可能生成 FVD 优秀的视频，但面对"如果向左转会发生什么"的问题完全失败
- 现有 benchmark 中，真正做到闭环评测的只有 **World-in-World** 一篇
- VBVR 在这方面是"准闭环"：视频推理任务要求模型在视频中执行推理，但仍非完全闭环 agent 评测

**仍未解决的问题**：
- 闭环评测的计算成本极高，难以大规模化
- Action space 的定义因领域而异，通用闭环 benchmark 尚不存在

---

### Gap 3：训练目标与任务目标错位

**Evidence**：

> RLVR-World (2505.13934): *"standard training objectives such as **maximum likelihood estimation (MLE) often misalign** with task-specific goals of world models, i.e., transition prediction metrics like accuracy or perceptual quality"*

**分析**：
- 几乎所有视频生成模型用 MSE/SSIM/感知损失训练，但 world model 的真正目标是"正确预测状态转移"
- 用 MLE 训练 → 模型学会生成"看起来合理"的视频，但不一定是"物理正确的"视频
- RLVR-World 提出用 RL + verifiable reward 替代 MLE，是方向之一

**仍未解决的问题**：
- Verifiable reward 的定义本身就很难（什么叫"正确的状态转移"？）
- RL 训练视频生成模型的不稳定性问题

---

### Gap 4：缺乏结构化推理能力（类儿童认知）

**Evidence**：

> WM-like-Child (2503.15168): *"While widely used in reinforcement learning, they **lack the structured, adaptive representations** that even young children intuitively develop. Advancing beyond pattern recognition requires dynamic, interpretable frameworks inspired by Piaget's cognitive development theory"*

> SVIB (2311.09064): *"Systematic compositionality, or the ability to adapt to novel situations by **creating a mental model of the world using reusable pieces of knowledge**, remains a **significant challenge**"*

> WM-ABench (2506.21876): *"Internal world models enable agents to **understand the world's state and predict transitions**, serving as the basis for **advanced deliberative reasoning**"*

**分析**：
- 现有 world model 本质上是"大型模式匹配器"，缺乏像儿童一样的因果推理、概念组合、反事实推理能力
- WM-like-Child 提出 6 个需要研究的方向：physics-informed、compositional、causal、interpretable、adaptive、safe
- VBVR 在一定程度上测了这种推理（Abstraction 维度），但测的仍是较浅的视觉推理

**仍未解决的问题**：
- 反事实推理（"如果X不发生，会怎样？"）几乎没有 benchmark 测
- 多步因果链推理（A→B→C→D）在视频 world model 中几乎未被评测

---

### Gap 5：记忆与时空一致性缺失

**Evidence**：

> LoopNav (2505.22976): *"The ability to simulate the world in a spatially consistent manner is a **crucial requirement** for effective world models. Such a model enables high-quality visual generation, and also ensures **reliability for downstream tasks** such as simulation and planning"*

> Toward Stable WM (2503.08122): *"while these models excel in quality and diversity, we identify a critical gap: **maintaining consistency** of world content over extended generation sequences"*

> From Masks to Worlds (2510.20668): 描述世界模型演化路线最终落脚于 **"memory-augmented models"** 作为前沿方向

**分析**：
- 现有视频生成模型在短序列（<10秒）内表现好，但长时程（>30秒）时场景不一致、物体漂移
- 记忆机制是社区公认的下一个重要方向：Awesome-World-Models 专门设立了 "Memory in World Model" 章节
- LoopNav 是第一个专门测空间记忆一致性的 benchmark，但仍很初步

**仍未解决的问题**：
- 长时程记忆在视频 world model 中如何编码（implicit vs explicit memory）
- 记忆检索的效率与精度权衡

---

### Gap 6：2D 视频 → 3D/4D 世界理解的鸿沟

**Evidence**：

> 3D/4D World Modeling Survey (2509.07996): *"prior work largely emphasizes generative methods for **2D image and video data**, they **overlook** the rapidly growing body of work that leverages **native 3D and 4D representations** such as RGB-D imagery, occupancy grids, and LiDAR point clouds"*

> OmniWorld (2509.12201): *"the development of truly general 4D world models remains fundamental... jointly capture **spatial geometry and temporal dynamics**"*

**分析**：
- 绝大多数 benchmark（包括 VBVR）测的是 2D 视频推理
- 真实世界是 3D+时间=4D 的，纯视频理解是有信息损失的
- 社区开始向 4D occupancy、Gaussian Splatting、LiDAR 等 3D native 方向发展

**仍未解决的问题**：
- 3D world model 的 benchmark 几乎空白（OmniWorld 是数据集，不是评测框架）
- 2D video reasoning 是否能迁移到 3D 理解

---

### Gap 7：自监督预训练 ≠ 行动理解

**Evidence**：

> V-JEPA 2 (2506.09985): *"A major challenge for modern AI is to learn to **understand the world and learn to act largely by observation**... combines internet-scale video data with **a small amount of interaction data (robot trajectories)**"*

**分析**：
- 从互联网规模视频学到的知识（V-JEPA 2 的路线）是 passive observation
- 真正的 world model 需要理解 action→consequence 的关系，而这在纯观测数据中信号很弱
- VBVR 的推理任务某种程度上测了这个：模型需要理解"行动后的世界状态"

**仍未解决的问题**：
- 如何用最少的 interaction data 最大化 world model 的行动理解
- 行动理解的 generalizable benchmark 尚不存在

---

## 二、社区关注点热力图

基于以上分析，整理社区当前最关注的研究方向：

| 研究方向 | 关注热度 | 代表论文数 | 有无 Benchmark | VBVR 覆盖程度 |
|---------|---------|-----------|--------------|-------------|
| 物理合理性 | 🔥🔥🔥🔥 | 高（~20篇） | ✅ WorldModelBench | ⚠️ 部分 |
| 具身/机器人操控 | 🔥🔥🔥🔥 | 高（~30篇） | ✅ EWMBench, EVA | ❌ 无 |
| 自动驾驶场景生成 | 🔥🔥🔥🔥 | 最高（~40篇） | ✅ 多个 | ❌ 无 |
| 长时程一致性/记忆 | 🔥🔥🔥 | 中（~10篇） | ⚠️ LoopNav 初步 | ❌ 无 |
| 推理能力（视频） | 🔥🔥🔥 | 中（~8篇） | ✅ **VBVR核心** | ✅✅ 强 |
| 闭环评测 | 🔥🔥🔥 | 中（~5篇） | ⚠️ World-in-World | ⚠️ 准闭环 |
| 3D/4D 世界建模 | 🔥🔥🔥 | 中（~15篇） | ⚠️ 数据集为主 | ❌ 无 |
| 因果/反事实推理 | 🔥🔥 | 少（~3篇） | ❌ 几乎无 | ❌ 无 |
| Test-time scaling | 🔥🔥 | 少（~3篇） | ❌ 无 | ❌ 无 |
| 安全性 | 🔥🔥 | 少（~5篇） | ❌ 无 | ❌ 无 |
| 多智能体 WM | 🔥 | 少（~4篇） | ❌ 无 | ❌ 无 |

---

## 三、社区期望的理想 World Model

综合所有 survey/position 论文，社区期望的 world model 具备：

### 能力层面（从 abstract 直接提炼）

```
1. 物理正确性     → 遵守质量守恒、重力、碰撞等物理定律
                    Evidence: Physics Cognition Survey, WorldModelBench

2. 因果推理       → 理解 action→consequence 的因果链
                    Evidence: WM-like-Child (Piaget认知发展理论), WM for Embodied AI Survey

3. 反事实推理     → "如果X不发生会怎样？"
                    Evidence: WM for EmbodiedAI: "counterfactual rollouts"

4. 组合泛化       → 用已知概念组合理解新场景
                    Evidence: SVIB, WM-like-Child

5. 长时程一致性   → 跨时间步保持场景/物体的一致性
                    Evidence: LoopNav, Toward Stable WM, From Masks to Worlds

6. 行动可控性     → 对动作指令的精确响应
                    Evidence: ACT-Bench, V-JEPA 2, RLVR-World

7. 闭环决策支持   → 直接支持 agent 决策，而非仅生成视频
                    Evidence: World-in-World, Benchmarking WM Learning

8. 多模态理解     → 不仅限于视频，整合 3D、语言、action
                    Evidence: 3D/4D WM Survey, OmniWorld
```

### 评测层面（社区期望的 benchmark 特性）

```
1. 程序化/可扩展  → 不依赖昂贵人工标注（VBVR 已实现）
2. 多维度覆盖     → 不只测一个维度（WorldScore 在尝试）
3. 有 Ground Truth → 明确的正确答案（VBVR 已实现）
4. 闭环设计       → 测 agent 决策而非仅视频质量（仍是空白）
5. 物理 GT        → 用物理仿真引擎提供精确 GT（几乎无人做）
```

---

## 四、现有工作的根本局限（为什么不足）

### 局限 1：评测哲学错误

大多数 benchmark 测的是"好看不好看"（感知质量），而不是"懂不懂"（world understanding）。这是根本性的哲学错位。

> 类比：测一个学生是否理解物理，却只检查他的字写得好不好看。

### 局限 2：数据生成困境

- 真实视频：有物理真实性，但标注成本极高
- 合成视频：标注方便，但 domain gap 大、视觉质量差
- **VBVR 的程序化数据引擎是一个重要突破**：兼顾可扩展性和标注精确性

### 局限 3：评测维度割裂

- 每个 benchmark 只测一个维度（物理/语义/空间/行动）
- 没有一个 benchmark 能全面反映 world model 的综合能力
- WorldScore 是第一个尝试统一的，但仍然以几何/场景生成为主

### 局限 4：人类基准线缺失

- 大多数 benchmark 没有人类表现的基准线
- VBVR 提供了人类 97.4% vs. 最佳模型 68.5% 的明确对比，清晰展示了 gap

### 局限 5：脱离实际应用

- Benchmark 设计者考虑评测，但没考虑"为什么要有 world model"
- 真正的目的是支持 planning、simulation、decision making
- 现有 benchmark 与下游任务的关系往往很弱

---

## 五、VBVR 的独特贡献与剩余空白

### VBVR 已解决的：
- ✅ 程序化数据生成（可扩展，无人工标注成本）
- ✅ 明确的 Ground Truth
- ✅ 人类基准线（97.4%）
- ✅ 多维度推理评测（5大类：Knowledge/Abstraction/Spatiality/Transformation/Perception）
- ✅ 统一 Leaderboard（揭示巨大gap）

### VBVR 仍有空白（即 VBVR-2.0 的机会）：
- ❌ **因果链推理**（A→B→C 多步推理）
- ❌ **反事实推理**（"如果不这样做会怎样"）
- ❌ **记忆与长时程一致性测试**
- ❌ **物理定律违规检测**（超越几何/运动，深入力学）
- ❌ **3D 空间理解**（从 2D 视频推理扩展到 3D）
- ❌ **行动→后果的精确评测**（现有维度测的是静态推理）
- ❌ **真正闭环评测**（VBVR 仍是 open-loop 预测正确答案）
- ❌ **具身/操控场景**（机器人抓取、推拉等需要物理理解的场景）
- ❌ **开放词汇泛化**（VBVR 任务类别固定，无法测 OOD 泛化）

---

*下一步 → [Phase 3: VBVR-2.0 方向提案](./phase3-vbvr2-proposal.md)*
