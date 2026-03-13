# 论文精读笔记：World Model Benchmark Survey
## 面向 VBVR-2.0 的系统性文献解读

> 更新日期：2026-03-12  
> 格式：每篇 → 核心主张 / 方法设计 / 关键发现 / 我们的 Insight

---

# 第一组：定义问题——什么是 World Model？

> **读这组的目的**：搞清楚社区对 world model 的定义分歧，以及 VBVR 站在哪个位置。

---

## Paper 1｜Understanding World or Predicting Future?
**A Comprehensive Survey of World Models**  
arXiv: [2411.14499](https://arxiv.org/abs/2411.14499) | Nov 2024

### 核心主张
这篇 survey 提出 world model 领域存在一个根本性的**哲学分裂**：

> *"world models are regarded as tools for either **understanding the present** or **predicting the future**"*

一派认为 world model = 理解当前世界状态（感知派，偏 VLM/LLM）；另一派认为 world model = 预测未来状态（生成派，偏 video diffusion）。两个社区用同一个词，说的是两件不同的事。

### 方法/框架
提供了一个统一的文献综述框架，把 world model 按"理解 vs 预测"两个维度分类整理，覆盖 LLM、视频生成、RL、具身AI 等多个子领域。

### 关键发现
- "理解派"的 world model 关注：因果推理、状态表示、物理知识
- "预测派"的 world model 关注：视频连贯性、未来帧预测、动作条件生成
- **两者之间的桥梁几乎不存在**：很少有工作同时做到"理解当前"和"预测未来"

### 🔑 我们的 Insight
VBVR-1.0 是"预测派"的评测工具（测模型能否生成正确的下一帧/视频）。但 VBVR-2.0 应该**跨越这条鸿沟**：用视频生成作为载体，但测试的是"理解派"的能力（因果推理、反事实推理）。这正是 VBVR 区别于其他纯生成质量 benchmark 的机会。

---

## Paper 2｜Is Sora a World Simulator?
**A Comprehensive Survey on General World Models and Beyond**  
arXiv: [2405.03520](https://arxiv.org/abs/2405.03520) | May 2024

### 核心主张
> *"General world models represent a crucial pathway toward achieving AGI... Recently, Sora model has attained significant attention due to its remarkable simulation capabilities, which exhibits an **incipient comprehension of physical laws**"*

这篇是 Sora 发布后第一篇综合 survey，核心问题是：Sora 是否真的是世界模拟器？答案是：**有潜力，但还远没到**。

### 方法/框架
从五个维度评估 Sora 和类 Sora 模型是否具备 world simulator 能力：
1. 物理法则遵守
2. 长时程一致性
3. 可控性与交互性
4. 泛化到 OOD 场景
5. 对下游任务的支持

### 关键发现
- Sora 展示了"初步的物理法则理解"（incipient comprehension），但在复杂物理场景中仍频繁出错
- 长时程一致性是最大短板（超过几秒场景就开始漂移）
- 当时没有任何 benchmark 能系统评测这些能力

### 🔑 我们的 Insight
这篇 2024 年 5 月的论文指出"没有好的 benchmark"，而 VBVR 正是在 2025 年填补了这个空白。但它指出的五个维度中，VBVR-1.0 只覆盖了部分（可控性、部分物理），**长时程一致性、OOD 泛化、因果推理**仍未覆盖。这为 VBVR-2.0 提供了明确的路线图。

---

# 第二组：现有 Benchmark 在测什么？

> **读这组的目的**：搞清楚每个 benchmark 的设计哲学、测了什么、没测什么。

---

## Paper 3｜WorldModelBench
**Judging Video Generation Models As World Models**  
arXiv: [2502.20694](https://arxiv.org/abs/2502.20694) | Feb 2025

### 核心主张
> *"current benchmarks fail to rigorously evaluate these claims, focusing only on **general video quality**, ignoring important factors to world models such as **physics adherence**"*

WorldModelBench 是第一个明确把"世界模型能力"和"视频生成质量"分开评测的 benchmark。

### 方法设计
两个评测维度：
- **Instruction-Following**（指令跟随）：模型是否按照文字描述生成正确内容
- **Physics-Adherence**（物理遵从）：生成内容是否违反物理规律，重点检测**质量守恒定律**（物体大小不合理变化）

数据：众包 **67K 人工标注**，覆盖 **14 个前沿模型**。  
评测器：用人工标注 fine-tune 了一个 **2B 参数自动 judge**，比 GPT-4o 高 8.6% 准确率。

### 关键发现
- 所有测试的 14 个模型在物理遵从维度上表现都很差
- 视频生成质量高（FVD 好）的模型，物理遵从不一定好——两者几乎不相关
- 用 judge reward 做 post-training 能明显提升物理合理性

### 🔑 我们的 Insight
这篇是目前最接近 VBVR 精神的 benchmark——明确区分"好看"和"正确"。但它只测了**一个物理定律**（质量守恒），且评测维度只有 2 个。VBVR 的程序化 GT 比 WorldModelBench 的众包标注更精确、更可扩展。**VBVR-2.0 可以把物理层次做深**（动量守恒、能量守恒、热传导等），这是直接可比的竞争维度。

---

## Paper 4｜WorldSimBench
**Towards Video Generation Models as World Simulators**  
arXiv: [2410.18072](https://arxiv.org/abs/2410.18072) | Oct 2024

### 核心主张
> *"the lack of **categorization based on inherent characteristics** continues to hinder the progress of predictive model development"*

WorldSimBench 的核心贡献不是新的评测指标，而是提出了一套**分类框架**——把视频预测模型按照"内在特性"分类后再分别评测。

### 方法设计
提出模型分类维度：
- 开放式 vs 封闭式世界假设
- 像素空间 vs 潜在空间
- 有动作条件 vs 无动作条件

在此框架下，对不同类型的模型用匹配的指标评测（不能用同一把尺子量所有模型）。

### 关键发现
- 不同类型模型的"最优评测指标"是不同的，混用指标会产生误导性结论
- 现有 benchmark 普遍存在"类型不分"的问题

### 🔑 我们的 Insight
WorldSimBench 提醒我们：VBVR-2.0 的评测设计需要考虑**被测模型的类型**。Image-to-Video 模型和 Text-to-Video 模型对同一任务的输入形式不同，应该有对应的 task 设计。VBVR-1.0 的 I2V 框架（first frame → video → answer）本身是一个好的选择，因为它统一了输入形式。

---

## Paper 5｜WorldScore
**A Unified Evaluation Benchmark for World Generation**  
arXiv: [2504.00983](https://arxiv.org/abs/2504.00983) | Apr 2025

### 核心主张
> *"the **first unified benchmark** for world generation... decompose world generation into a sequence of **next-scene generation tasks** with explicit **camera trajectory-based layout specifications**"*

WorldScore 的野心是统一评测 3D 场景生成、4D 场景生成、视频生成——用同一个框架。

### 方法设计
- 把世界生成分解为连续的 **next-scene 生成**任务
- 每个任务有明确的 **camera trajectory** 规范（几何层面的 layout 约束）
- 数据集：**3,000** 个精选测试样本，覆盖室内、室外、动态场景

评测指标：几何一致性、外观质量、时序连贯性

### 关键发现
- 当前最好的视频生成模型在"严格按 camera trajectory 生成"任务上表现不稳定
- 3D 方法和视频方法之间存在系统性的 tradeoff：3D 方法几何更准，视频方法外观更真实

### 🔑 我们的 Insight
WorldScore 证明了**统一框架是可行的**（3D/4D/Video 放在一起比），这对 VBVR-2.0 有启发：VBVR-2.0 可以考虑把不同难度层次（几何推理、物理推理、因果推理）放在同一个统一 leaderboard 里，让社区看到模型在不同能力维度的表现。但 WorldScore 本质上还是在测**生成质量**（camera trajectory 的 geometric compliance），不是推理能力。

---

## Paper 6｜EWMBench
**Evaluating Scene, Motion, and Semantic Quality in Embodied World Models**  
arXiv: [2505.09694](https://arxiv.org/abs/2505.09694) | May 2025

### 核心主张
> *"text-to-video diffusion models have evolved into **embodied world models (EWMs)** capable of generating physically plausible scenes from language"*

EWMBench 是第一个专门针对**具身世界模型**（Embodied World Models）的 benchmark，评测对象是用语言条件驱动的视频生成模型在机器人/具身场景下的表现。

### 方法设计
三个评测维度：
1. **Scene Quality**（场景质量）：室内场景的视觉真实性、布局合理性
2. **Motion Quality**（运动质量）：物体运动的流畅性、物理合理性
3. **Semantic Quality**（语义质量）：语言描述与生成内容的语义一致性

针对具身场景（机器人操作台、厨房、桌面等）专门构建测试集。

### 关键发现
- 在具身场景中，视频生成模型的 **motion quality 最差**（物体运动经常不合理）
- semantic quality 相对较好（能大致理解"拿起杯子"这类语言指令）
- scene quality 介于两者之间

### 🔑 我们的 Insight
EWMBench 告诉我们：具身场景中，"运动"是最难的。VBVR-2.0 如果加入具身动作推理任务（"执行这个动作后，场景会怎么变？"），运动相关的因果推理会是高价值且高难度的方向。同时，EWMBench 只测生成质量，不测推理——这正是 VBVR 可以差异化的地方。

---

## Paper 7｜WorldPrediction
**A Benchmark for High-level World Modeling and Long-horizon Procedural Planning**  
arXiv: [2506.04363](https://arxiv.org/abs/2506.04363) | Jun 2025

### 核心主张
> *"Humans are known to have an internal 'world model' that enables us to carry out **action planning based on world states**... It is not clear how current AI models... are able to carry out **procedural planning** in diverse environments"*

WorldPrediction 是最接近 VBVR 精神的竞品——用**视频形式**测试模型的世界建模和长时程规划能力。

### 方法设计
- 视频形式的 benchmark（不是语言 QA）
- 测两种能力：
  - **World Modeling**：给定当前状态，预测哪些行动会导致什么结果
  - **Procedural Planning**：给定目标，规划多步行动序列
- 场景：日常生活（厨房、工具使用等）

### 关键发现
- 所有测试模型（包括 GPT-4o、Gemini）在长时程规划任务上表现很差
- 单步预测相对可以，但 3 步以上规划准确率急剧下降
- 视频形式的规划比纯语言形式更难（缺乏大规模视频规划训练数据）

### 🔑 我们的 Insight
WorldPrediction 和 VBVR 有非常高的相似度（都用视频测推理），但有两个关键差异：① WorldPrediction 的 GT 依赖人工标注（不程序化，难以扩展）；② WorldPrediction 测的是"规划"而不是"因果推理"（给目标→找路径 vs 给事件→理解原因）。**VBVR-2.0 可以在因果+反事实维度上补充 WorldPrediction 的空白**，两者形成互补而非竞争关系。

---

## Paper 8｜World-in-World
**World Models in a Closed-Loop World**  
arXiv: [2510.18135](https://arxiv.org/abs/2510.18135) | Oct 2025

### 核心主张
> *"Progress on this question has been limited by **fragmented evaluation**: most existing benchmarks focus on **either** generation quality **or** downstream task performance, but not both in an integrated manner"*

World-in-World 是第一个真正意义上的**闭环评测框架**——把世界模型和 agent 决策放在同一个评测循环里。

### 方法设计
- 闭环：世界模型生成未来状态 → agent 根据预测状态做决策 → 执行动作 → 再次预测
- 评测的是整个"感知-预测-决策"循环的综合表现
- 不单独评测生成质量，而是评测"预测准确性对最终决策成功率的贡献"

### 关键发现
- 生成质量高（FVD 好）的模型，在闭环决策任务中**不一定表现好**
- 某些生成质量一般但"预测方向准确"的模型在闭环任务中反而更好
- 闭环评测揭示了开环 benchmark 的系统性误导

### 🔑 我们的 Insight
这是目前最有前瞻性的评测框架，但实现极其复杂。VBVR 的"准闭环"设计（first frame → video → 推理问题）是一个好的折中：不需要真实 agent 执行，但通过推理问题测试模型是否"内部理解"了世界状态。**VBVR-2.0 可以增加 action-conditional 推理任务**，在不做真闭环的前提下，逼近闭环评测的核心能力。

---

## Paper 9｜ACT-Bench
**Towards Action Controllable World Models for Autonomous Driving**  
arXiv: [2412.05337](https://arxiv.org/abs/2412.05337) | Dec 2024

### 核心主张
> *"current research primarily evaluates these models based on **visual realism** or downstream task performance, with **limited attention** to whether world models can **correctly respond to action inputs**"*

ACT-Bench 发现了一个被大家忽视的核心问题：自动驾驶 world model 的评测从来不测"动作→结果"的对应关系，只测生成的视频好不好看。

### 方法设计
- 专门针对**动作可控性**设计测试集
- 给定一个驾驶动作（左转、右转、加速、刹车），评测生成视频是否正确反映了该动作的物理后果
- 评测指标：动作执行前后场景变化的一致性

### 关键发现
- 大多数自动驾驶 world model 对"左转指令"和"右转指令"生成的视频几乎一样——模型根本没有理解动作语义
- 视觉质量分（FVD）高的模型，动作可控性分普遍低
- 动作可控性与视觉质量之间几乎零相关

### 🔑 我们的 Insight
这个发现震撼：模型生成高质量视频，但动作指令对它来说形同虚设。这直接支持了 VBVR-2.0 的一个方向——**动作后果推理**（给定动作，预测正确后果）。在 VBVR 的程序化框架下，这个测试可以做得更精细、更通用（不限于驾驶场景）。

---

# 第三组：具体的 Gap——物理、记忆、因果

> **读这组的目的**：找到有论文 evidence 支撑的具体 gap，评估 VBVR-2.0 能填哪些。

---

## Paper 10｜Physics Cognition in Video Generation Survey
**Exploring the Evolution of Physics Cognition in Video Generation**  
arXiv: [2503.21765](https://arxiv.org/abs/2503.21765) | Mar 2025

### 核心主张
> *"generated content often violates the fundamental laws of physics, falling into the dilemma of **'visual realism but physical absurdity'**. Researchers began to increasingly recognize the importance of physical cognition"*

这篇 survey 系统梳理了视频生成模型在物理认知方面的演进，提出"视觉真实但物理荒诞"是当前最核心的问题。

### 方法/框架
把物理认知分为三个层次：
1. **感知层**（Perceptual）：外观、光影、材质的真实感
2. **运动层**（Kinematic）：速度、轨迹、碰撞的合理性
3. **动力学层**（Dynamic）：力、质量、能量守恒的正确性

分析了从早期 GAN → diffusion → large-scale video foundation models 的演进中，三个层次的物理认知各自进步了多少。

### 关键发现
- 感知层：现有模型已经基本解决（高分辨率、高真实感）
- 运动层：部分解决，但细节经常错（如物体穿透、不合理加速）
- **动力学层：几乎完全未解决**，是最大空白
- 物理认知在大模型时代并没有自动涌现——规模扩大不代表物理理解增强

### 🔑 我们的 Insight
"动力学层"的物理认知评测几乎是空白，这是 VBVR-2.0 最有价值的切入点之一。**规模扩大不解决物理推理**——这意味着需要专门设计的 benchmark 和训练信号，而 VBVR 的程序化引擎可以精确生成动力学场景的 GT，这是其他 benchmark 很难复制的。

---

## Paper 11｜LoopNav
**Toward Memory-Aided World Models: Benchmarking via Spatial Consistency**  
arXiv: [2505.22976](https://arxiv.org/abs/2505.22976) | May 2025

### 核心主张
> *"The ability to simulate the world in a **spatially consistent manner** is a crucial requirement... Designing a **memory module** is the key to enabling long-term prediction"*

LoopNav 提出：世界模型的记忆能力几乎没有被系统评测过。用"循环导航"任务（从 A 走到 B 再回到 A，判断是否回到原位）测试空间记忆一致性。

### 方法设计
- 任务：给定一段导航视频，走完一圈后回到起点，问：现在的场景是否和出发时一致？
- 这需要模型"记住"出发时的场景状态，跨越整个视频长度
- 评测指标：起点和终点场景的一致性分数

### 关键发现
- 所有测试的 world model 在长循环（>20 步）后的空间一致性都急剧下降
- 无记忆机制的模型在第 10 步后基本"忘记"了起点场景
- 加入显式记忆模块能显著提升一致性，但仍远低于人类水平

### 🔑 我们的 Insight
LoopNav 证明了记忆是 world model 的核心缺失能力，且可以用程序化方式评测。**VBVR-2.0 可以设计类似的记忆测试**：视频中早期出现的信息，在后期的推理问题中被用到——这直接测试跨时间步的状态记忆，是程序化可生成的。

---

## Paper 12｜WM-ABench
**Do Vision-Language Models Have Internal World Models? Towards an Atomic Evaluation**  
arXiv: [2506.21876](https://arxiv.org/abs/2506.21876) | Jun 2025

### 核心主张
> *"Internal world models enable agents to **understand the world's state and predict transitions**, serving as the basis for **advanced deliberative reasoning**. Recent large VLMs... exhibit potential as general-purpose WMs"*

WM-ABench 的独特角度：不是评测视频生成模型，而是评测 **VLM（视觉语言模型）自身是否内置了世界模型**。把评测分解到"原子级"（atomic evaluation）。

### 方法设计
原子级能力分解：
- **状态理解**（State Understanding）：当前世界状态的感知和描述
- **转移预测**（Transition Prediction）：给定动作，预测下一个世界状态
- **约束推理**（Constraint Reasoning）：世界规则（物理、社会）的理解

评测模型：OpenAI o3、GPT-4o、Gemini 2.0、Claude 等最新大模型。

### 关键发现
- o3 在状态理解上最好，但转移预测上仍有显著错误
- GPT-4o 和 Gemini 在"约束推理"上表现出明显的物理知识缺失
- **所有 VLM 在"反事实转移预测"上表现极差**（"如果不做这个动作会怎样？"）

### 🔑 我们的 Insight
这是直接支持 VBVR-2.0 反事实推理方向的关键证据。**最强的 VLM（o3、GPT-4o）在反事实推理上都极差**——这意味着：① 这个能力很重要但被忽视；② VBVR-2.0 在这个维度上会有清晰的模型排名差异化；③ 反事实推理 benchmark 的需求是真实的。

---

## Paper 13｜Benchmarking World-Model Learning
arXiv: [2510.19788](https://arxiv.org/abs/2510.19788) | Oct 2025

### 核心主张
> *"Model-learning agents should gather information to learn world models that support **many downstream tasks**... Current methods for learning and evaluating world models **diverge from this goal**: training and evaluation are anchored to **next-step prediction** only"*

这篇从 model-based RL 视角出发，指出 world model 的评测被过度简化成了"预测下一帧"，忽略了真正下游任务需要的多种能力。

### 方法设计
提出世界模型应该支持的 4 类下游能力：
1. 预测**未观测**状态（填空能力）
2. 估计**近期/远期**行动后果
3. 规划**行动序列**
4. 检测**动态变化**（世界规则是否改变）

为每类能力设计专门的评测任务，并在经典 RL 环境中验证。

### 关键发现
- 当前 world model 训练主要优化 next-step prediction loss，但这个目标与 4 类下游任务的相关性都很低
- 在"检测动态变化"任务上，所有模型几乎都失败——它们不能识别世界规则发生了改变

### 🔑 我们的 Insight
"检测世界规则变化"是一个未被任何 benchmark 测试的全新方向。对于 VBVR，这意味着可以设计这样的任务：**前半段视频遵守规则 A，后半段突然变成规则 B，问模型能否识别这个变化**。这是高难度、高价值的推理测试，且程序化生成完全可行。

---

# 第四组：社区期望的 World Model 长什么样？

> **读这组的目的**：整理学界对"理想 world model"的具体描述，找到 VBVR-2.0 的价值观依据。

---

## Paper 14｜World Models in AI: Like a Child
**World Models in Artificial Intelligence: Sensing, Learning, and Reasoning Like a Child**  
arXiv: [2503.15168](https://arxiv.org/abs/2503.15168) | Mar 2025

### 核心主张
> *"they lack the **structured, adaptive representations** that even young children intuitively develop. Advancing beyond pattern recognition requires **dynamic, interpretable frameworks inspired by Piaget's cognitive development theory**"*

用 Piaget 的儿童认知发展理论来分析 AI world model 的缺陷——儿童在 2 岁时就具备的能力，当前 AI world model 还不具备。

### 方法/框架
提出 6 个需要研究的关键领域：
1. **物理信息化**（Physics-informed）：嵌入物理先验
2. **组合性**（Compositional）：用已知概念组合理解新场景
3. **因果推理**（Causal）：理解 A→B 的因果关系
4. **可解释性**（Interpretable）：能够解释预测的原因
5. **自适应**（Adaptive）：在新环境中快速调整
6. **安全性**（Safe）：在不确定情况下保守行动

### 关键发现
- 2 岁儿童的"物体永恒性"（object permanence）——知道物体消失后仍然存在——当前 world model 不具备
- 儿童能做反事实推理（"如果没有风，叶子会落下来吗？"）——模型完全不行
- 这 6 个领域目前都没有好的 benchmark

### 🔑 我们的 Insight
这篇给了 VBVR-2.0 很好的框架参考。**儿童认知能力作为 world model 的标杆**是个极好的叙事：VBVR-2.0 可以定位成"测试模型是否具备儿童般的物理直觉和因果推理"。这比"测物理定律"更有故事性，也更有学术深度。

---

## Paper 15｜From Masks to Worlds
**A Hitchhiker's Guide to World Models**  
arXiv: [2510.20668](https://arxiv.org/abs/2510.20668) | Oct 2025

### 核心主张
> *"We follow one clear road: from early **masked models** that unified representation learning... to **interactive generative models** that close the action-perception loop, and finally to **memory-augmented models**"*

这不是普通的 survey，而是一本"路线图"——描述 world model 的演化路径，并指出终点应该是什么样的。

### 方法/框架
描述的四阶段演化路线：
1. **Masked Models**（MAE、BERT 时代）：统一表示学习
2. **Unified Architectures**：单一架构多任务
3. **Interactive Generative Models**：关闭 action-perception 循环
4. **Memory-Augmented Models**：⭐ 当前前沿，是终点方向

### 关键发现
- Memory-augmented world model 是社区公认的下一个里程碑
- Action-perception loop closure 是"真正的"world model 必须具备的
- 当前所有开环模型都只是 world model 的过渡形态

### 🔑 我们的 Insight
这篇给出了清晰的路线图：**记忆 + 行动-感知闭环** 是终点。VBVR-2.0 的记忆评测方向完全对齐这个终点；而 VBVR 的 action-conditional 推理方向则在逼近"行动-感知闭环"的评测。这篇是 VBVR-2.0 motivation 部分最好的引用之一。

---

## Paper 16｜A Comprehensive Survey on World Models for Embodied AI
arXiv: [2510.16732](https://arxiv.org/abs/2510.16732) | Oct 2025

### 核心主张
> *"World models serve as internal simulators that capture environment dynamics, enabling **forward and counterfactual rollouts** to support perception, prediction, and decision making"*

这是具身 AI 领域最全面的 world model survey，明确把**反事实推理**列为 world model 的核心功能之一。

### 方法/框架
提出三轴分类框架：
- **功能轴**（Functionality）：感知 / 预测 / 规划 / 反事实
- **决策轴**（Decision）：model-free / model-based / hybrid
- **表示轴**（Representation）：像素 / 潜在 / 3D / 语言

### 关键发现
- 具身 AI 的 world model 必须支持**反事实推理**（counterfactual rollouts）才能有效支持 safety-critical 决策
- 当前具身 world model 全都缺失反事实推理能力
- 这一能力的缺失是阻碍具身 AI 实际部署的主要瓶颈

### 🔑 我们的 Insight
这篇来自具身 AI 社区的 survey **明确把反事实推理列为 must-have 能力**，但没有对应的 benchmark。这再次验证了 VBVR-2.0 反事实推理方向的需求是真实存在的，而且受到具身 AI 这个大社区的关注。

---

# 第五组：现有模型的技术路线

> **读这组的目的**：理解最好的模型在做什么，以及它们为什么仍不够。

---

## Paper 17｜V-JEPA 2
**Self-Supervised Video Models Enable Understanding, Prediction and Planning**  
arXiv: [2506.09985](https://arxiv.org/abs/2506.09985) | Jun 2025 (Meta AI)

### 核心主张
> *"A major challenge for modern AI is to learn to **understand the world and learn to act largely by observation**... combines internet-scale video data with a **small amount of interaction data** (robot trajectories)"*

V-JEPA 2 是 Meta AI 最新的 world model，代表了 **JEPA（Joint Embedding Predictive Architecture）** 路线的最高水平。

### 方法设计
- 在 internet 规模视频上**无监督预训练**（预测 latent space 中的未来帧）
- 加入少量**机器人轨迹数据**做 action-conditional fine-tuning
- 评测：视频理解、物理预测、机器人规划三个任务

### 关键发现
- 在 V-JEPA 2 上做 action-conditional fine-tuning 后，机器人规划成功率明显提升
- 只用少量 interaction data 就能大幅提升 action understanding
- 在物理理解 benchmark（IntPhys 等）上超过了之前所有方法

### 🔑 我们的 Insight
V-JEPA 2 的结论是：**大量 passive video observation + 少量 action data = 好的 world model**。这对 VBVR-2.0 的启示是：① VBVR-2.0 的任务应该测试这种"从观察中学习动作后果"的能力；② JEPA 风格的模型在 VBVR-2.0 的反事实和因果任务上应该有优势，可以用 VBVR-2.0 验证这一点。

---

## Paper 18｜RLVR-World
**Training World Models with Reinforcement Learning**  
arXiv: [2505.13934](https://arxiv.org/abs/2505.13934) | May 2025

### 核心主张
> *"standard training objectives such as **MLE often misalign** with task-specific goals... we present RLVR-World, a unified framework that leverages **reinforcement learning with verifiable rewards (RLVR)** to directly optimize world models"*

RLVR-World 把 RL（强化学习）引入 world model 训练——用"可验证奖励"替代 MLE 损失，直接优化 world model 的正确性而非视觉相似度。

### 方法设计
- 把 RLVR（来自 LLM 后训练的技术，如 DeepSeek-R1 的方法）迁移到视频 world model
- 奖励信号：物理正确性、状态预测准确性（需要可验证的 GT）
- 对多种视频 world model 框架验证了该方法

### 关键发现
- RLVR 训练显著提升了 world model 在物理合理性任务上的表现
- MLE 训练的模型在"好看度"上更好，但在"正确性"上差很多
- 可验证奖励是 RLVR 的关键——需要明确的 GT

### 🔑 我们的 Insight
这篇直接告诉我们：**VBVR-2.0 的程序化 GT 天然就是 RLVR 的 verifiable reward 来源**。这意味着 VBVR-2.0 不仅仅是一个 evaluation benchmark，还可以作为 RLVR training 的数据引擎——这大幅提升了 VBVR-2.0 的应用价值和引用价值。VBVR-2.0 = 评测工具 + 训练信号来源。

---

## Paper 19｜DINO-WM
**World Models on Pre-trained Visual Features enable Zero-shot Planning**  
arXiv: [2411.04983](https://arxiv.org/abs/2411.04983) | Nov 2024

### 核心主张
> *"world models should 1) be trainable on **offline, pre-collected trajectories**, 2) support **test-time behavior optimization**, 3) facilitate **task-agnostic reasoning**"*

DINO-WM 提出用 DINOv2 的预训练视觉特征作为 world model 的表示空间，在 latent space 中学习状态转移，实现 zero-shot 规划。

### 方法设计
- 用 DINOv2 特征替代原始像素作为 world model 的状态表示
- 在这个特征空间中学习 action-conditional 状态转移
- 在测试时用 MPC（模型预测控制）优化行动序列

### 关键发现
- DINO 特征空间中的 world model 比像素空间中的更稳定、更泛化
- 在 zero-shot 场景（训练时没见过的任务）中，规划成功率显著提升
- 离线轨迹训练 + 测试时优化是一个强大的组合

### 🔑 我们的 Insight
DINO-WM 的方法有一个含义对 VBVR-2.0 很重要：**world model 的推理能力和表示空间紧密相关**。用语义丰富的预训练特征（如 DINO）做 world model，可能在 VBVR 的推理任务上有内在优势。VBVR-2.0 可以分析不同表示空间的模型在因果/反事实任务上的系统性差异。

---

## Paper 20｜TesserAct
**Learning 4D Embodied World Models**  
arXiv: [2504.20995](https://arxiv.org/abs/2504.20995) | Apr 2025

### 核心主张
> *"predict the **dynamic evolution of 3D scenes** over time in response to an embodied agent's actions, providing both **spatial and temporal consistency**... training on **RGB-DN (RGB, Depth, and Normal) videos**"*

TesserAct 是第一个从 RGB-D（深度）视频学习 **4D embodied world model** 的方法，同时建模空间（3D）和时间（动态）。

### 方法设计
- 输入：RGB + Depth + Normal 的多模态视频
- 输出：对 3D 场景随时间演化的预测（4D = 3D + time）
- 应用：机器人操控的世界预测

### 关键发现
- 4D world model 在需要 3D 理解的机器人任务（如推物体、抓取）上显著优于 2D 视频模型
- Depth 信息是关键：加入深度后，模型对空间关系的预测准确率大幅提升
- 当前评测框架无法衡量 4D world model 的独特优势（都是 2D 指标）

### 🔑 我们的 Insight
TesserAct 指出：**当前 benchmark（包括 VBVR）都是 2D 的，而真实世界是 4D 的**。VBVR-2.0 的一个可能方向是设计 3D 感知的推理任务（用 RGB-D 视频提供深度线索，测试 3D 空间推理）。但这会显著提高数据生成复杂度，需要权衡。

---

# 第六组：VBVR-2.0 直接相关方向

> **读这组的目的**：找到最直接支撑 VBVR-2.0 具体设计的论文证据。

---

## Paper 21｜SVIB
**Imagine the Unseen World: A Benchmark for Systematic Generalization in Visual World Models**  
arXiv: [2311.09064](https://arxiv.org/abs/2311.09064) | Nov 2023

### 核心主张
> *"Systematic compositionality, or the ability to **adapt to novel situations by creating a mental model of the world using reusable pieces of knowledge**, remains a **significant challenge**"*

SVIB 是第一个专门测**系统性泛化**（Systematic Compositionality）的视觉 world model benchmark——模型能否用已知概念的"碎片"组合出对新场景的理解。

### 方法设计
- 训练集：基础物体（形状 A）在基础场景（背景 B）中运动
- 测试集：新的组合（形状 A' 在背景 B' 中，或新的物理规则组合）
- 评测：在"从未见过的组合"上预测动态行为

### 关键发现
- 几乎所有视觉 world model 在 OOD 组合上性能急剧下降
- 性能下降幅度远超人类（人类在 OOD 组合上几乎不退化）
- 当前模型是在"记忆训练组合"而非"学习物理规则"

### 🔑 我们的 Insight
SVIB 的核心发现是：**当前模型记忆模式，不学规则**。这是 VBVR-2.0 因果推理方向的直接动机——反事实推理需要模型"理解规则"而非"记忆结果"，因此能天然区分"记忆派"和"理解派"模型。VBVR-2.0 可以借鉴 SVIB 的 compositional test 设计思路。

---

## Paper 22｜SWIFT
**Can Test-Time Scaling Improve World Foundation Model?**  
arXiv: [2503.24320](https://arxiv.org/abs/2503.24320) | Mar 2025

### 核心主张
> *"scaling computation at **test time** emerges as both a **critical and practical alternative** to traditional model scaling for world foundation models"*

SWIFT 把 LLM 领域的 test-time scaling（推理时加算力）引入 world foundation model，探索在不增加模型参数的情况下提升 world model 能力。

### 方法设计
- 在推理时运行多次采样，用 verifier 选择最好的结果（best-of-N）
- 训练 process reward model（PRM）来指导 test-time search
- 在多个 world model 任务上验证

### 关键发现
- Test-time scaling 对 world model 有显著效果（N=8 时提升约 15-20%）
- 关键依赖：需要可靠的 verifier——而 verifier 需要 **verifiable ground truth**
- 在物理推理类任务上提升最大，在纯视觉质量任务上提升有限

### 🔑 我们的 Insight
SWIFT 再次强调：**verifiable ground truth 是 test-time scaling 的基础**，而 VBVR-2.0 的程序化设计天然提供了这个条件。这意味着 VBVR-2.0 不仅是 benchmark，还是 test-time scaling 研究的理想平台。这大幅提升了 VBVR-2.0 的影响力：可以直接成为各种 inference-time 方法的标准测试床。

---

## Paper 23｜DrivingGen
**A Comprehensive Benchmark for Generative Video World Models in Autonomous Driving**  
arXiv: [2601.01528](https://arxiv.org/abs/2601.01528) | Jan 2026 (ICLR 2026)

### 核心主张
> *"despite fast-growing research activity, the field lacks a **comprehensive evaluation framework**... Video generation models... promising agents the ability to **imagine the future by modeling the temporal evolution** of complex scenes"*

DrivingGen 是目前最全面的自动驾驶 world model benchmark（ICLR 2026），四个维度的综合评测。

### 方法设计
四个评测维度：
1. **Visual Realism**（视觉真实性）：FID/FVD 类指标
2. **Trajectory Plausibility**（轨迹合理性）：生成轨迹是否符合驾驶规律
3. **Temporal Coherence**（时间连贯性）：跨帧一致性
4. **Controllability**（可控性）：对驾驶动作指令的响应正确性

### 关键发现
- 现有自动驾驶 world model 在 visual realism 上已经相当好
- 最差的维度是 **controllability**（和 ACT-Bench 的发现一致）
- 轨迹合理性和视觉真实性之间存在 tradeoff：一个好，另一个就差

### 🔑 我们的 Insight
DrivingGen 的四维框架是 domain-specific 的最佳实践。对 VBVR-2.0，这提示：**多维度 benchmark 中，"可控性"类维度最难、最有价值**。VBVR-2.0 的 action-conditional 推理任务正好对应"可控性"维度的通用版本，覆盖了驾驶以外的更广场景。

---

# 全局总结与 VBVR-2.0 核心 Insights

## 从 23 篇论文提炼的 5 个核心 Insight

### Insight 1：评测哲学必须转变
从"好不好看"→"懂不懂"。  
**Evidence**：Paper 3 (WorldModelBench), Paper 9 (ACT-Bench), Paper 10 (Physics Cognition)

### Insight 2：反事实推理是最大空白，也是最大机会
所有 benchmark 都没有测，但所有 survey 都说它很重要。  
**Evidence**：Paper 12 (WM-ABench), Paper 16 (Embodied AI Survey), Paper 14 (WM like Child)

### Insight 3：程序化 GT 是关键基础设施
RLVR 需要它，test-time scaling 需要它，精确评测需要它。VBVR 已经有了。  
**Evidence**：Paper 18 (RLVR-World), Paper 22 (SWIFT)

### Insight 4：动力学层物理认知几乎完全未解决
感知层解决了，运动层部分解决了，动力学层（力、能量、动量）几乎空白。  
**Evidence**：Paper 10 (Physics Cognition Survey)

### Insight 5：记忆 + 行动感知闭环是终点，VBVR-2.0 应向这个方向靠近
**Evidence**：Paper 15 (From Masks to Worlds), Paper 11 (LoopNav)

---

## VBVR-2.0 最终推荐方向（优先级排序）

| 优先级 | 方向 | 核心 Evidence | 可行性 |
|--------|------|--------------|--------|
| 🥇 | 反事实推理 | Paper 12, 14, 16 | ✅ 程序化可生成 |
| 🥈 | 多步因果链 | Paper 7, 13 | ✅ 程序化可生成 |
| 🥉 | 动力学物理推理 | Paper 10, 3 | ✅ 物理引擎精确GT |
| 4 | 记忆/长时程状态追踪 | Paper 11, 15 | ✅ 程序化可生成 |
| 5 | Action-conditional 推理 | Paper 9, 17, 23 | ✅ 延伸 VBVR 框架 |
| 6 | OOD 组合泛化 | Paper 21 | ⚠️ 需要额外设计 |
