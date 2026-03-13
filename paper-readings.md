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
作者：Jingtao Ding et al.（清华大学 BNRist）| 发表于 ACM CSUR

---

### 这篇论文在说什么（一句话）
World model 领域存在根本性哲学分裂：一派在"理解现在"，另一派在"预测未来"，两个社区用同一个词做两件不同的事——这篇是第一个把两派系统统一梳理的 survey。

---

### 论文结构
```
1. Introduction
2. Background & Categorization（历史脉络 + 两派分类）
3. Implicit Representation（理解派：model-based RL、LLM 知识）
4. Future Prediction（预测派：视频生成、具身环境）
5. Application Domains（游戏、具身AI、自动驾驶、社会模拟）
6. Open Problems（物理规律与反事实、Benchmark、安全性等）
7. Conclusion
```

---

### 核心主张与论证

#### 主张 1：两派分裂的起源
> *"The definition of a world model remains a subject of ongoing debate, generally divided into two primary perspectives: **understanding the world** and **predicting the future**."*

- **理解派起源**（1960s→2018）：Minsky 的 frame representation → Ha & Schmidhuber 2018 的 recurrent world model（RWM）→ model-based RL。核心是把外部世界抽象成内部隐式表征，建立"mental model"
- **预测派起源**（LeCun 2022→Sora 2024）：LeCun 的 JEPA 路线——world model 不仅要理解，还要能"想象未来可能的状态"，服务于决策规划

#### 主张 2：Sora 是"预测派"的代表，但有根本局限
> *"A key limitation is its **causal reasoning ability**, restricting it to passively generating sequences without actively predicting how actions might alter events. Furthermore, Sora struggles to consistently reproduce correct physical laws, failing to accurately simulate complex physics like object behavior under forces, fluid dynamics, or light interactions."*

论文对 Sora 的评价是：视觉上令人印象深刻，但**本质上是条件生成，不是因果理解**。

#### 主张 3：World Model 的两种功能角色（§5.5，非常重要）
论文把 world model 分成两类部署形态：

| 形态 | 描述 | 代表 |
|------|------|------|
| **Cloud-based**（云端环境） | 大规模视频生成，作为数据引擎/RL环境/策略评估器 | Sora、WAN、CogVideoX |
| **Edge-side**（边端智能体大脑） | 不生成像素，在 latent space 中规划行动 | V-JEPA 2、DreamerV3 |

> *"Cloud-based world models can act as **data engines**, augmenting real-world data for training policy models... Edge-side world models can compress world states within a **latent space**; V-JEPA 2 trains a latent space world model that enables on-device action planning."*

---

### 关键发现

#### 发现 1：Scaling 不能自动产生物理推理能力
> *"Kang et al. show that scaling diffusion video models yields **perfect in-distribution fidelity** yet **breaks down on out-of-distribution or combinatorial tests**, indicating 'case-based' rather than rule-based generalization."*

即：大模型"记住了"训练分布里的物理现象，但没有学到可泛化的物理规则。在 OOD 场景（没见过的组合）中直接崩溃。

#### 发现 2：反事实推理是最核心的开放问题（§6.1）
这是论文最重要的部分：

> *"A key objective of world models is to capture the **causal structure of their environments** – especially the underlying physical rules – so they can **reason about counterfactuals beyond the data distribution** (Pearl, 2009). This capacity is crucial for handling rare, mission-critical events (e.g., autonomous-driving corner cases) and for narrowing sim-to-real gaps."*

论文援引 Pearl 的因果推理框架，指出反事实能力对真正的 world model 是**必须条件**，而非加分项。

当前状态：
- 纯数据驱动的方法（Sora 类）：有重力/流体/热力学等方面的**系统性失败**
- 混合方法（嵌入物理先验）：PhysGen（刚体仿真+扩散精修）、physics-informed diffusion（PDE 残差损失）正在兴起
- 结论：**"data-driven scaling alone is insufficient to recover robust physical laws"**

#### 发现 3：Benchmark 空白已被社区认识到（§6.3 表8）
论文列举了一批新的 benchmark，很多是我们之前没见过的：

| 新 Benchmark | 重点 |
|-------------|------|
| **T2VPhysBench** | 文本转视频的 12 条物理定律合规性检测 |
| **VBench-2.0** | 加入物理合理性和常识作为标准维度 |
| **PhysBench** | 10K 视频-图像-文本三元组，测 VLM 的物理属性/关系/动力学 |
| **Physics-IQ** | 五个物理领域（固体/流体/光学/热力/磁力），测视觉真实 vs 物理正确的 gap |
| **UrbanVideo-Bench** | 城市自中心视频，包含**因果推理**维度 |
| **EAI** | 具身 AI 的模块级评测（目标/子目标/动作/转移） |

---

### 方法论框架（对我们整理 survey 有用）

论文把所有 world model 工作按 **2 个主轴 × 多应用域** 组织：

```
主轴 1：理解派（Implicit Representation）
  ├── Model-based RL（Dreamer 系列、TD-MPC）
  └── LLM 世界知识（全局物理知识、局部物理知识、社会知识）

主轴 2：预测派（Future Prediction）
  ├── 视频生成 world model（Sora、VideoGPT 等）
  └── 具身环境 world model（室内/室外/动态）

应用域：游戏 / 具身AI / 自动驾驶 / 社会模拟
```

---

### 🔑 我们的 Insight（精读后更新）

**Insight 1：VBVR 天然跨越两派鸿沟**  
VBVR 的设计——用"预测派"的视频生成作为载体，但测试"理解派"的推理能力——正好落在两派的交叉点。这是 VBVR 最独特的定位，这篇论文的框架可以直接作为 VBVR motivation 的理论基础。

**Insight 2：反事实是 §6.1 的第一个开放问题，Pearl 2009 作为理论锚点**  
论文引用 Judea Pearl 的因果推理理论（《The Book of Why》的学术版），把反事实推理定位为 world model 的**必须条件**。VBVR-2.0 的反事实推理方向有这个理论基础背书。

**Insight 3：我们发现了之前没见过的 benchmark：T2VPhysBench、Physics-IQ、PhysBench**  
这三个需要加入我们的 benchmark 分析表：
- **Physics-IQ**：五个物理领域的系统性测试，可能是 WorldModelBench 的强竞品
- **T2VPhysBench**：12 条物理定律的 checklist，比 WorldModelBench 更系统
- **VBench-2.0**：VBench 加了物理和常识维度——这是主流 benchmark 的重要升级

**Insight 4："case-based vs rule-based" 是 VBVR-2.0 的核心叙事**  
> *"'case-based' rather than rule-based generalization"*

这个表述极好。VBVR-2.0 的使命可以描述为：**区分"case-based"模型（记住训练样本）和"rule-based"模型（学到可泛化规则）**。反事实推理和因果推理任务天然就是这种区分的测试手段。

**Insight 5：需要补充 3 篇新 benchmark 到我们的分析表**  
- T2VPhysBench（12条物理定律）
- Physics-IQ（5个物理领域）  
- PhysBench（VLM物理推理，10K条）

---

## Paper 2｜Is Sora a World Simulator?
**A Comprehensive Survey on General World Models and Beyond**  
arXiv: [2405.03520](https://arxiv.org/abs/2405.03520) | May 2024  
作者：Zheng Zhu et al.（GigaAI + 中科院 + NUS 等）

---

### 这篇论文在说什么（一句话）
Sora 发布后第一篇综合 survey，正式拷问：Sora 算不算真正的世界模拟器？答案是：**视觉令人印象深刻，但在因果推理、物理合规、泛化三个核心维度上根本不够格**。

---

### 论文结构
```
2. Video Generation as General WM（技术综述 + Sora 分析）
   2.3 Towards World Models: Sora（框架/数据/能力）
3. WM for Autonomous Driving
4. WM for Autonomous Agents
5. Discussion ★ 最重要
   5.1.1 Challenges（因果推理/物理/泛化/效率/评测）
   5.1.2 Future Perspectives（3D WM / 具身智能）
```

---

### Sora 技术解析（§2.3）

架构三部分：VAE 压缩 → DiT 扩散（latent space）→ 语言编码器注入指令。  
论文关键判断：Sora 本质是**条件视频生成器**，不是推理世界规律的模型。

---

### 核心挑战（§5.1.1）——直接原文

> *"**Video generation is not synonymous with world models.** While video generation may serve as one manifestation of world models, it does not fully address the core challenges inherent to world models."*

**挑战 1：因果推理（最关键）**
> *"The model should be capable of inferring outcomes of decisions **never encountered before**... We expect world models to possess the ability of **counterfactual reasoning**, whereby outcomes are inferred through rational imagining."*

引用 Pearl 三层智能层级（Figure 7）：  
- 第一层：关联（seeing）→ 当前所有视频生成模型  
- 第二层：干预（doing）→ action-conditional WM  
- 第三层：**反事实（imagining）→ 真正的 world model 应达到此层**

**挑战 2：物理法则**
> *"Realism, seen in Sora's videos, **isn't the same as reality**... Training Sora with just video-text pairs isn't enough to grasp these complexities. Combining Sora with **physics-driven simulators** could be beneficial."*

**挑战 3：泛化**
> *"This requires the model to move beyond simply **memorizing the training data** and instead develop a robust understanding of the **underlying principles**."*

**挑战 4：评测体系（对 VBVR 最直接）**
> *"Generation metrics alone **cannot reflect the predictive rationality of world models**. This highlights the need for **human-centric evaluation**, which measures whether the generated videos meet users' expectations or align with human reasoning."*

---

### 未来方向（§5.1.2）

| 方向 | 描述 |
|------|------|
| 3D World Simulator | "The world exists fundamentally in three dimensions" |
| Embodied Intelligence | World model 作为 simulator 训练具身 agent 决策 |

---

### 关键发现总结

| 维度 | Sora 表现 | 论文结论 |
|------|----------|---------|
| 视觉真实性 | ✅ 优秀 | 不等于 world model 能力 |
| 因果/反事实推理 | ❌ 停留第一层关联 | 反事实是核心缺失 |
| 物理法则 | ❌ 流体/热力学失败 | 需结合物理仿真器 |
| 泛化 | ❌ case-based | 需 rule-based 理解 |
| 评测体系 | ❌ 2024年空白 | 需 human-centric 评测 |

---

### 🔑 我们的 Insight（精读后）

**Insight 1：VBVR 正好填了"评测体系"这个 2024 年明确的空白**  
挑战 4 说 generation metrics 无法反映 predictive rationality——这正是 VBVR 出现的原因。VBVR 做到了 human-centric、程序化、有精确 GT。

**Insight 2：Pearl 三层框架是 VBVR-2.0 最好的定位叙事**  
- 第一层（关联）= 当前视频生成模型，VBVR-1.0 已在测
- 第三层（反事实）= VBVR-2.0 的核心目标  
在 VBVR-2.0 paper 里直接用这个框架，清晰、有权威性。

**Insight 3：物理仿真器 + 生成模型结合，正是 VBVR 的数据引擎思路**  
论文建议把 Sora 和物理仿真器结合。VBVR 的程序化引擎是这个思路的 benchmark 实现：用仿真提供 GT，用生成模型接受评测。

**Insight 4："视频生成 ≠ 世界模型"是最强的 motivation 句**  
直接可引用进 VBVR-2.0 的 introduction。

**Insight 5：两篇 Survey（Paper 1 + Paper 2）共同指向同一核心**  
Paper 1（清华）："case-based vs rule-based" = scaling 不解决物理推理  
Paper 2（GigaAI）："memorizing training data" vs "underlying principles" = 因果/泛化是核心挑战  
→ 社区共识已形成，VBVR-2.0 站在这个共识上出发。

---

# 第二组：现有 Benchmark 在测什么？

> **读这组的目的**：搞清楚每个 benchmark 的设计哲学、测了什么、没测什么。

---

## Paper 3｜WorldModelBench
**Judging Video Generation Models As World Models**  
arXiv: [2502.20694](https://arxiv.org/abs/2502.20694) | Feb 2025  
作者：Dacheng Li, Yunhao Fang et al.（UC Berkeley + UCSD + NVIDIA + MIT）

---

### 这篇论文在说什么（一句话）
第一个把"世界模型能力"和"视频生成质量"系统分开的 benchmark——用 67K 人工标注、14 个模型、数字证明：**好看的视频 ≠ 好的 world model，VBench 高分和物理合规的相关性只有 0.28**。

---

### 数据集设计

| 属性 | 数值 |
|------|------|
| 测试 prompts | **350 条**（图像+文本条件对） |
| 应用领域 | **7 个**（机器人/自动驾驶/人体预测等） |
| 子领域 | **56 个** |
| 支持类型 | T2V 和 I2V 均支持 |
| 人工标注量 | **67K 条**（8336 次完整投票） |
| 测试模型 | **14 个**（7 开源 + 7 闭源） |

---

### 评测维度（§3.1）

**维度 1：Commonsense**（保留传统指标，降权）
- 帧质量 + 时序质量（即 VBench 测的那些）

**维度 2：Instruction Following**（4 个等级）
- 从"完全未执行"到"完全正确执行"

**维度 3：Physics Adherence**（5 类违规）

| 违规 | 描述 |
|------|------|
| (a) Newton's First Law | 无外力却运动 |
| (b) Solid mechanics | 固体形变不合理 |
| (c) Fluid mechanics | 液体流动不自然 |
| (d) Impenetrability | 物体互相穿透 |
| (e) Gravity | 重力行为不一致 |

**Judger**：2B 参数 VLM，在 67K 标注上 fine-tune，预测误差比 GPT-4o 低 **9.9%**

---

### 关键发现（§4.1）—— 直接数字

**① 最好模型任务完成率只有 61%**
> *"kling has only **61%** of videos correctly finish the task. **12%** violate mass conservation law; **11%** have object penetration."*

**② 视觉质量高 ≠ World Model 强（核心结论）**
> *"Luma: frame quality 0.81 (better) but instruction following 44% (worse). Mochi: frame quality 0.63 (lower) but instruction following 53% (better)."*

**③ I2V 系统性差于 T2V**
- CogVideoX: T2V 7.31 vs I2V 6.75
- OpenSoraPlan: T2V 7.62 vs I2V 6.62
- OpenSora: T2V 6.11 vs I2V 5.83

**完整模型排名（总分/10）**：
```
闭源：kling 8.82 > minimax 8.59 > mochi-official 8.37 > runway 8.08 > luma 7.72
开源：mochi 7.62 ≈ OpenSoraPlan 7.61 > CogVideoX-T2V 7.31 > ... > OpenSora-I2V 5.83
```

---

### 最震撼结论：VBench 与物理合规几乎无关（§4.3）

> *"**low correlation (0.28)** between WorldModelBench's physics adherence and VBench's total score."*

各 VBench 维度与物理合规的相关性：

| VBench 维度 | 与物理合规相关性 |
|------------|---------------|
| aesthetic quality（最高） | **0.41** |
| dynamic degree（最低） | **-0.05** |
| **总分** | **0.28** |

→ **VBench 高分完全不能预测 world model 的物理理解能力**

---

### 🔑 我们的 Insight（精读后）

**Insight 1：0.28 是 VBVR-2.0 最有力的 motivation 数字**
WorldModelBench 物理合规 vs VBench 总分相关性只有 0.28。社区用了多年的 VBench 在评测 world model 物理能力上基本无效。VBVR 用推理任务揭示的 gap（人类 97.4% vs 最强模型 68.5%）和这个 0.28 形成完美呼应。

**Insight 2：5 类违规检测 → VBVR-2.0 可以做更高阶的主动推理**
WorldModelBench 的物理测试是被动的（这段视频有没有违规？）。VBVR-2.0 可以做主动的（给你这个物理场景，结果会是什么？）。被动检测 < 主动推理——这是 VBVR 的本质区别。

**Insight 3：程序化 GT vs 67K 人工标注——VBVR 的根本优势**
WorldModelBench 需要 67K 人工标注 + 2B Judger 才能做自动评测。VBVR 的程序化 GT 是零成本、零误差、可无限扩展的。这是工程上的根本优势，也是 scalability 的来源。

**Insight 4：I2V 比 T2V 更弱，VBVR 测的是更难的场景**
VBVR 用 I2V 框架（first frame → video → answer），而 I2V 系统性弱于 T2V。这意味着 VBVR 测的 gap 更大、更有压迫感，对社区的刺激更强。

**Insight 5：kling 61% 任务完成率 → 对照 VBVR 的 68.5%**
两个 benchmark 揭示的都是同一件事：当前最好的模型离人类还差 30-40%。这两个数字可以并排放在 VBVR-2.0 的 introduction 里，形成强有力的 narrative。

---

## Paper 4｜WorldSimBench
**Towards Video Generation Models as World Simulators**  
arXiv: [2410.18072](https://arxiv.org/abs/2410.18072) | Oct 2024  
作者：Yiran Qin et al.（中国科大 + 上海 AI Lab + 港大）

---

### 这篇论文在说什么（一句话）
第一个把所有"预测模型"按照**具身程度**分成四层（S0-S3）的分类框架，并为最高层（World Simulators / S3）提出了双评测体系——视觉感知 + 动作一致性，覆盖三大具身场景。

---

### 核心框架：S0-S3 预测模型层级

> *"From lower to higher stages, the models are capable of generating: **text, images, videos, and actionable videos**"*

| 层级 | 模型类型 | 输出 | 评测方式 |
|------|---------|------|---------|
| **S0** | Predictive Text Model（LLM） | 文本规划 | 任务完成率 |
| **S1** | Predictive Image Model | 目标图像 | 美学质量（FID等） |
| **S2** | Predictive Video Model | 视频 | 美学质量（FVD等） |
| **S3** | **World Simulator** | **可执行动作的视频** | **WorldSimBench（本文）** |

S3 的关键：集成了**3D 场景理解 + 物理规律先验**，生成的视频可以被翻译成可执行的控制信号。

---

### 双评测框架

**评测 1：Explicit Perceptual Evaluation（显式感知评测）**
- 测什么：视觉保真度（visual fidelity）—— 生成视频是否符合人类对物理属性的感知偏好
- 怎么测：HF-Embodied Dataset（**35,701 条**，**20 个维度**的人工反馈）→ 训练 Human Preference Evaluator
- Human Preference Evaluator **全面超越 GPT-4o**（GPT-4o 在零样本下甚至出现负相关）

**评测 2：Implicit Manipulative Evaluation（隐式操控评测）**
- 测什么：视频-动作一致性（video-action consistency）—— 生成的视频能否被正确翻译成控制信号
- 怎么测：在 3 个仿真环境中，把生成视频输入 video-to-action 模型，看执行成功率
- 关键性质：**有可验证的 GT**（仿真环境中动作结果确定）

**三大具身场景：**
- **OE**（Open-Ended Embodied / Minecraft）：开放世界
- **AD**（Autonomous Driving / CARLA）：自动驾驶
- **RM**（Robot Manipulation / CALVIN）：机器人操控

---

### 关键发现（§5）

**发现 1：现有评测方法对 S3 完全不适用**
> *"conventional evaluation methods are inadequate for assessing the actionability of the generated videos, as there is **no definite ground truth** for actionable videos towards completing a specific embodied task"*

这正是 WorldSimBench 提出的动机：S2 级别的 FVD 对 S3 无效。

**发现 2：Human Preference Evaluator 必要性**
> *"GPT-4o exhibits a **negative correlation** with human preferences in evaluating OpenSora in AD under zero-shot setting"*

GPT-4o 零样本下判断视频质量与人类判断负相关——自动评测器的质量至关重要。

**发现 3：多数模型在具身交互维度严重不足**
> *"most models struggle with Embodied Intelligence tasks"*

即使是当时最好的模型，在具身场景的真实任务执行中表现都很差。

---

### 自身局限（论文明确承认）

> *"the World Simulator can be applied to **more scenarios than just robots**, and different scenarios have more physical representations, so how to effectively evaluate the World Simulator in **other scenarios** requires more exploration."*

只覆盖了 3 个具身场景（Minecraft/CARLA/CALVIN），没有覆盖通用场景。

---

### 🔑 我们的 Insight（精读后）

**Insight 1：S0-S3 层级是 VBVR 定位的绝佳参照系**
VBVR-1.0 在 S2 和 S3 之间——它用 S2（视频生成）测的是接近 S3 的能力（action understanding via reasoning）。VBVR-2.0 的反事实/因果推理任务，本质上是在测 S3 所需的"物理规律先验理解"，这是最清晰的定位方式。

**Insight 2：Implicit Manipulative Evaluation 的思路对 VBVR-2.0 的启发**
WorldSimBench 用"生成视频 → 翻译成动作 → 执行成功率"来隐式评测视频质量。VBVR 用"生成视频 → 推理问题 → 正确率"是类似思路，但不需要仿真环境——更轻量，但同样是 implicit evaluation（推理题的正确率隐式测视频的世界模型质量）。

**Insight 3：GPT-4o 作为评测器的负相关发现**
GPT-4o 在零样本下评测视频质量出现负相关——说明 LLM-as-judge 在视频世界模型评测中极不可靠。VBVR 的程序化 GT 完全绕开了这个问题，这是又一个根本优势。

**Insight 4：局限性 = VBVR 的机会**
WorldSimBench 承认只覆盖了 Minecraft/CARLA/CALVIN 三个固定场景。VBVR 的程序化引擎是通用的——不绑定特定场景，可以覆盖任意物理推理场景。这是 scalability 上的根本差异。

**Insight 5：35,701 条多维人工反馈 vs VBVR 程序化 GT**
WorldSimBench 构建数据集需要 35,701 条人工打分。VBVR 的程序化引擎理论上可以生成无限条零成本 GT。这在 VBVR-2.0 paper 中值得直接对比。

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
