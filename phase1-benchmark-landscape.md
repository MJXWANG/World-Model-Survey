# Phase 1: World Model & Video Generation Benchmark 全景分析

> 作者：AI Survey Assistant  
> 日期：2026-03-12  
> 数据来源：arXiv abstract 直接抓取 + Awesome-World-Models README  

---

## 一、Benchmark 分类框架

根据阅读所有论文 abstract 后，将现有 benchmark 分为 **5 大类**：

```
A. 通用视频生成质量评测（传统方向）
B. World Model 能力专项评测（新兴方向）
C. 具身/机器人世界模型评测
D. 自动驾驶世界模型评测  
E. 语言模型/VLM 作为世界模型评测
```

---

## 二、核心 Benchmark 详细分析表

### A. 通用视频生成质量（Traditional Baselines）

| Benchmark | 年份 | 测什么 | 核心指标 | 数据来源 | 评测协议 | 局限性 |
|-----------|------|--------|---------|---------|---------|--------|
| **FVD** (Fréchet Video Distance) | 2018 | 视频分布距离（感知质量） | FVD score | UCF-101等 | 开环统计距离 | 完全不测语义/物理/推理；只测分布相似度 |
| **FID** (Fréchet Inception Distance) | 2017 | 图像质量（逐帧） | FID score | ImageNet等 | 开环 | 帧间无时序，不测动态 |
| **VBench** | 2023 | 视频生成多维质量 | 16个子指标（运动流畅、背景一致等） | 真实视频 | 半自动 | 仍是感知质量，不测物理/因果推理 |

> ⚠️ **关键发现**：这类 benchmark 是"够漂亮"而非"够理解"——FVD 高的模型不代表它理解世界。

---

### B. World Model 能力专项评测（核心关注）

#### B1. WorldModelBench (Feb 2025)
- **arXiv**: [2502.20694](https://arxiv.org/abs/2502.20694)
- **核心声明**（原文）：*"current benchmarks fail to rigorously evaluate these claims, focusing only on general video quality, ignoring important factors to world models such as physics adherence"*
- **测什么**：
  - Dimension 1: **Instruction-Following**（指令跟随）
  - Dimension 2: **Physics-Adherence**（物理遵从）—— 如质量守恒、物体大小合理性
- **数据**：67K 众包人工标注，覆盖 14 个前沿模型
- **评测协议**：基于人工偏好训练自动 judge 模型（2B参数，比 GPT-4o 高 8.6% 准确率）
- **应用场景**：机器人、自动驾驶（application-driven domains）
- **局限性（论文自述）**：
  - 仅覆盖 2 个维度（指令+物理），未测**推理链**、**长时程一致性**
  - 评测协议依赖人工标注，成本高

---

#### B2. WorldSimBench (Oct 2024)
- **arXiv**: [2410.18072](https://arxiv.org/abs/2410.18072)
- **核心声明**（原文）：*"the lack of categorization based on inherent characteristics continues to hinder the progress of predictive model development"*
- **测什么**：将预测模型按"内在特性"分类评测（首次提出分层评测体系）
- **创新点**：提出按模型类型（开放/封闭、像素/潜在空间）分类评测框架
- **局限性**：分类框架本身仍偏向**外观质量**，推理能力未深度覆盖

---

#### B3. WorldScore (Apr 2025)
- **arXiv**: [2504.00983](https://arxiv.org/abs/2504.00983)
- **核心声明**（原文）：*"the first unified benchmark for world generation... decompose world generation into a sequence of next-scene generation tasks with explicit camera trajectory-based layout specifications"*
- **测什么**：
  - 将世界生成分解为**连续 next-scene 生成任务**
  - 以 **camera trajectory**（相机轨迹）为 layout 规范
  - 统一评测：3D 场景生成、4D 场景生成、视频生成
- **数据**：3,000 个精选测试样本
- **重要性**：第一个**统一**覆盖 3D/4D/Video 的 benchmark
- **局限性**：场景生成≠推理；camera trajectory 是几何控制，不测语义推理

---

#### B4. World-in-World (Oct 2025)
- **arXiv**: [2510.18135](https://arxiv.org/abs/2510.18135)
- **核心声明**（原文）：*"Progress on this question has been limited by fragmented evaluation: most existing benchmarks focus on either generation quality or downstream task performance"*
- **测什么**：**闭环评测**—— 世界模型 + 具身 agent 的决策能力
- **重要性**：强调 open-loop quality ≠ closed-loop decision capability
- **局限性**：实现复杂，评测场景受限

---

#### B5. WorldPrediction (Jun 2025)
- **arXiv**: [2506.04363](https://arxiv.org/abs/2506.04363)
- **核心声明**（原文）：*"It is not clear how current AI models, especially generative models, are able to learn such world models and carry out procedural planning in diverse environments"*
- **测什么**：
  - 高层**世界建模**（world modeling）
  - **长时程过程规划**（long-horizon procedural planning）
  - 视频形式的 benchmark（而非语言问答）
- **局限性**：过程规划仍偏向高层抽象，不涉及底层物理交互

---

#### B6. Toward Stable World Models (Mar 2025)
- **arXiv**: [2503.08122](https://arxiv.org/abs/2503.08122)
- **测什么**：**World Stability**（内容一致性保持）—— 生成式世界模型在持续生成中能否保持一致
- **发现**：扩散模型在生成沉浸式环境时，内容稳定性仍是主要缺陷
- **局限性**：只测稳定性（consistency），不测推理/因果/控制

---

#### B7. Benchmarking World-Model Learning (Oct 2025)
- **arXiv**: [2510.19788](https://arxiv.org/abs/2510.19788)
- **核心声明**（原文）：*"Model-learning agents should gather information to learn world models that support many downstream tasks... Current methods for learning and evaluating world models diverge from this goal"*
- **测什么**：
  - 预测未观测状态
  - 近/远期行动后果估计
  - 行动序列规划
  - 动态变化检测
- **重要性**：第一个从 **model-based RL** 角度系统 benchmark WM 学习
- **局限性**：偏 RL 框架，不适用于大规模视频生成模型

---

### C. 具身AI / 机器人世界模型评测

#### C1. EWMBench (May 2025)
- **arXiv**: [2505.09694](https://arxiv.org/abs/2505.09694)
- **核心声明**（原文）：*"text-to-video diffusion models have evolved into embodied world models (EWMs) capable of generating physically plausible scenes from language"*
- **测什么**（3个维度）：
  1. **Scene Quality**（场景质量）
  2. **Motion Quality**（运动质量）
  3. **Semantic Quality**（语义质量）
- **数据**：具身场景（机器人操作等）
- **局限性**：三个维度都是**生成质量**，不测智能推理；"physically plausible"的判定仍主观

---

#### C2. EVA: Embodied World Model (Oct 2024)
- **arXiv**: [2410.15461](https://arxiv.org/abs/2410.15461)
- **核心声明**（原文）：*"existing models often lack robust understanding, limiting their ability to perform multi-step predictions or handle Out-of-Distribution scenarios"*
- **测什么**：未来视频预期（Future Video Anticipation）—— 具身场景下的多步预测
- **发现**：现有模型对 OOD 场景泛化差
- **局限性**：预测视频 ≠ 理解世界；OOD 测试集规模有限

---

#### C3. LoopNav / Toward Memory-Aided World Models (May 2025)
- **arXiv**: [2505.22976](https://arxiv.org/abs/2505.22976)
- **核心声明**（原文）：*"The ability to simulate the world in a spatially consistent manner is a crucial requirement for effective world models"*
- **测什么**：**空间一致性**（Spatial Consistency）—— 以循环导航任务为载体
- **重要性**：首次将**记忆**显式纳入 world model 评测
- **局限性**：空间一致性≠因果推理；任务场景偏简单

---

#### C4. OmniWorld: 4D World Modeling Dataset (Sep 2025)
- **arXiv**: [2509.12201](https://arxiv.org/abs/2509.12201)
- **核心声明**（原文）：*"the development of truly general 4D world models remains fundamental... jointly capture spatial geometry and temporal dynamics"*
- **测什么**：4D 世界建模（空间几何 + 时间动态）
- **数据**：多领域、多模态数据集
- **局限性**：数据集贡献为主，评测框架较弱

---

### D. 自动驾驶世界模型评测

#### D1. ACT-Bench (Dec 2024)
- **arXiv**: [2412.05337](https://arxiv.org/abs/2412.05337)
- **核心声明**（原文）：*"current research primarily evaluates these models based on visual realism or downstream task performance, with limited attention to whether world models can correctly respond to action inputs"*
- **测什么**：**行动可控性**（Action Controllability）—— 世界模型是否能对动作输入正确响应
- **重要性**：指出现有评测忽略了"动作→结果"的对应关系
- **局限性**：仅限自动驾驶场景；动作空间定义局限

---

#### D2. SimWorld (Mar 2025)
- **arXiv**: [2503.13952](https://arxiv.org/abs/2503.13952)
- **测什么**：模拟器条件下的场景生成（Simulator-Conditioned Scene Generation）
- **目标**：解决自动驾驶数据稀缺问题——用 world model 生成数据
- **局限性**：evaluation 服务于 data augmentation，不是 general WM 能力评测

---

#### D3. DrivingGen (Jan 2026, ICLR 2026)
- **arXiv**: [2601.01528](https://arxiv.org/abs/2601.01528)
- **核心声明**（原文）：*"despite fast-growing research activity, the field lacks a comprehensive evaluation framework"*
- **测什么**（4个维度）：
  1. **Visual Realism**（视觉真实性）
  2. **Trajectory Plausibility**（轨迹合理性）
  3. **Temporal Coherence**（时间连贯性）
  4. **Controllability**（可控性）
- **局限性**：自动驾驶专用，不 general

---

### E. 语言模型/VLM 作为世界模型评测

#### E1. WM-ABench (Jun 2025)
- **arXiv**: [2506.21876](https://arxiv.org/abs/2506.21876)
- **核心声明**（原文）：*"Internal world models enable agents to understand the world's state and predict transitions, serving as the basis for advanced deliberative reasoning"*
- **测什么**：VLM 的内部世界模型能力（原子级评测）
  - 世界状态理解
  - 状态转移预测
- **模型**：OpenAI o3, GPT-4o, Gemini 等
- **局限性**：偏向静态理解，未测时序视频推理

---

#### E2. UNIVERSE (Jun 2025)
- **arXiv**: [2506.17967](https://arxiv.org/abs/2506.17967)
- **核心声明**（原文）：*"evaluating world model rollouts remains a fundamental challenge, requiring fine-grained, temporally grounded assessment of action alignment and semantic consistency"*
- **测什么**：用 VLM 作为自动评测器来评估 world model 的 rollout
- **重要性**：解决 world model 评测的 **自动化** 问题
- **局限性**：评测器本身可靠性依赖大模型能力

---

#### E3. Evaluating Implicit World Model in LLMs (Jun 2024)
- **arXiv**: [2406.03689](https://arxiv.org/abs/2406.03689)
- **测什么**：LLM 中隐式世界模型的恢复能力
- **框架**：用确定性有限自动机（DFA）形式化 world model 概念
- **局限性**：高度形式化，与实际视频生成/具身 AI 距离较远

---

#### E4. Imagine the Unseen World / SVIB (Nov 2023)
- **arXiv**: [2311.09064](https://arxiv.org/abs/2311.09064)
- **测什么**：**系统性泛化**（Systematic Compositionality）
  - 用已知知识的"碎片"构建新场景的能力
- **创新**：首个测试 systematic visual imagination 的 benchmark
- **局限性**：测试动态含义（dynamical implications），但场景较受限

---

## 三、VBVR 的定位

基于以上所有 benchmark 的分析，VBVR ([video-reason.com](https://video-reason.com)) 的独特定位是：

| 维度 | VBVR 的做法 | 其他 benchmark 的做法 |
|------|------------|-------------------|
| **任务形式** | 程序化生成视频推理任务 | 静态图像/文本QA 或开环生成质量 |
| **评测对象** | 视频生成模型的**推理能力** | 生成质量 or VLM 理解能力 |
| **Leaderboard** | Human 97.4% vs. 最佳模型 68.5%（巨大gap） | 无统一人类基准线 |
| **任务维度** | Knowledge / Abstraction / Spatiality / Transformation / Perception | 物理合理性 / 视觉质量 / 语义一致性 |
| **数据生成** | 完全程序化（可无限扩展） | 人工标注或真实视频（成本高） |
| **闭环程度** | 视频 = 推理载体（quasi-闭环） | 大多数为开环 |

> **VBVR 的核心假设**：*视频推理是下一个基础智能范式*，这意味着 world model 的能力应该体现在**能否在视频媒介中正确推理**，而非仅仅生成好看的视频。

---

## 四、调研到的 Survey 论文（背景框架）

| 论文 | arXiv | 核心观点 |
|------|-------|---------|
| **Is Sora a World Simulator?** | 2405.03520 | Sora 展示了物理法则的初步理解，但离真正 world simulator 还很远 |
| **Understanding World or Predicting Future?** | 2411.14499 | World model 有两派：理解现在（understanding）vs 预测未来（prediction），需要统一 |

---

## 五、数据汇总：现有 Benchmark 覆盖维度对比

| Benchmark | 视觉质量 | 物理合理性 | 指令跟随 | 时序一致性 | 空间记忆 | 推理能力 | 行动可控 | 闭环评测 |
|-----------|---------|-----------|---------|-----------|---------|---------|---------|---------|
| FVD/FID | ✅ | ❌ | ❌ | ⚠️ | ❌ | ❌ | ❌ | ❌ |
| WorldModelBench | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ |
| WorldSimBench | ✅ | ⚠️ | ❌ | ⚠️ | ❌ | ❌ | ❌ | ❌ |
| WorldScore | ✅ | ❌ | ✅ | ✅ | ❌ | ❌ | ✅ | ❌ |
| EWMBench | ✅ | ⚠️ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| WM-ABench | ❌ | ⚠️ | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| WorldPrediction | ❌ | ❌ | ✅ | ⚠️ | ❌ | ✅ | ❌ | ❌ |
| EVA | ✅ | ❌ | ✅ | ✅ | ❌ | ⚠️ | ❌ | ❌ |
| ACT-Bench | ✅ | ❌ | ✅ | ✅ | ❌ | ❌ | ✅ | ❌ |
| LoopNav | ✅ | ❌ | ❌ | ✅ | ✅ | ❌ | ❌ | ❌ |
| World-in-World | ✅ | ⚠️ | ✅ | ✅ | ❌ | ⚠️ | ✅ | ✅ |
| DrivingGen | ✅ | ⚠️ | ✅ | ✅ | ❌ | ❌ | ✅ | ❌ |
| Benchmarking-WML | ❌ | ⚠️ | ✅ | ⚠️ | ❌ | ✅ | ✅ | ⚠️ |
| SVIB | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ | ❌ | ❌ |
| **VBVR** | ⚠️ | ⚠️ | ✅ | ✅ | ❌ | **✅✅** | ⚠️ | ⚠️ |

**图例**：✅ 强覆盖 | ⚠️ 部分覆盖 | ❌ 未覆盖

> **最大的空白（❌集中的列）**：
> 1. **空间记忆**（Spatial Memory）：只有 LoopNav 一篇
> 2. **推理能力**（Reasoning）：只有 VBVR、WM-ABench、WorldPrediction、Benchmarking-WML
> 3. **闭环评测**（Closed-loop）：只有 World-in-World 一篇做到真正闭环

---

*下一步 → [Phase 2: Gap 分析 & 社区关注点](./phase2-gap-analysis.md)*
