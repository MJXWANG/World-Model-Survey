# Phase 3: VBVR-2.0 方向提案

> 基于 Phase 1（Benchmark 全景）+ Phase 2（Gap 分析）的系统推导  
> 所有方向均有论文 evidence 支撑

---

## 一、核心问题：VBVR-2.0 应该测什么？

VBVR-1.0 的核心假设：
> *"视频推理是下一个基础智能范式"*

VBVR-2.0 应该深化这个假设，回答：
> *"能做视频推理的 world model，是否也能做**因果**推理、**反事实**推理、和**物理定律导向**的推理？"*

---

## 二、六个候选方向（从 gap 到提案）

---

### 方向 A：因果链与多步推理
**Causal Chain & Multi-step Reasoning**

#### Gap 来源
- WorldPrediction (2506.04363): *"It is not clear how current AI models... are able to carry out **procedural planning** in diverse environments"*
- Benchmarking WM Learning (2510.19788): *"Current methods diverge from this goal... [of] estimating **near- and far-term consequences** of actions"*
- 现有 VBVR：每个 task 是独立的推理单元，没有 A→B→C 的因果链

#### 提案内容
用程序化引擎生成**多步因果链任务**：

```
任务示例：
  视频内容：球A撞击球B，球B飞向杠杆，杠杆倾斜让方块C落入容器
  问题：方块C最终落在哪个容器里？（需要理解 A→B→C→D 的因果链）
  
  难度递进：
    Level 1：1步推理（A→B）
    Level 2：2步推理（A→B→C）
    Level 3：3步+推理（multi-hop causal chain）
```

#### 为什么 VBVR-2.0 可以做
- VBVR 已有程序化数据引擎（可以生成任意复杂度的因果链场景）
- Ground truth 完全确定（物理仿真的确定性输出）
- 现有 benchmark 中**完全空白**

---

### 方向 B：反事实推理
**Counterfactual Reasoning**

#### Gap 来源
- WM for Embodied AI Survey (2510.16732): *"World models serve as internal simulators... enabling **forward and counterfactual rollouts** to support decision making"*
- WM-like-Child (2503.15168): *"Advancing beyond pattern recognition requires... [like] **Piaget's cognitive development** theory"*（儿童在 2 岁时就会做反事实推理）
- **所有现有 benchmark 中，没有一个测反事实推理**（这是最大的空白）

#### 提案内容
```
任务类型 1 - 移除干预（Remove intervention）：
  原视频：A推倒B，B打碎C
  问题：如果A不推B，C会发生什么变化？
  选项：a) C保持不变  b) C移动  c) C消失  d) C破碎
  
任务类型 2 - 替换干预（Replace intervention）：
  原视频：用红色球滚下斜面，速度为v
  问题：换成同样大小但质量更大的球，速度会如何？
  
任务类型 3 - 添加干预（Add intervention）：
  原视频：两个物体在表面滑动
  问题：如果在它们之间放置一个障碍物，轨迹如何变化？
```

#### 为什么这个方向重要
- 反事实推理是真正意义上的"世界模型理解"的核心
- 它需要模型不仅"记住"看到了什么，还要能"想象"没发生的事
- RLVR-World (2505.13934) 指出用 **verifiable rewards** 训练 world model 对这类任务特别有效

---

### 方向 C：深层物理定律推理
**Deep Physical Law Reasoning**

#### Gap 来源
- Physics Cognition Survey (2503.21765): *"**'visual realism but physical absurdity'**... deficiencies in physical cognition have gradually received widespread attention"*
- WorldModelBench (2502.20694): 只测了质量守恒这一个物理定律，*"issues overlooked by prior benchmarks"*
- VideoPhy-2（workshop paper）：测 action-centric 物理常识，但范围有限

#### 提案内容
将物理定律分层评测：

```
Layer 1 - 基础运动学（VBVR-1.0 的 Spatiality 已部分覆盖）
  - 匀速运动、加速度判断
  
Layer 2 - 动力学（新增）
  - 碰撞后动量守恒
  - 摩擦力方向判断
  - 浮力推理（密度差异）
  
Layer 3 - 热力学/电磁（更难，新增）
  - 热传导方向
  - 磁场力方向（物体受磁力影响的运动）
  
Layer 4 - 系统级物理推理（最难）
  - 多体交互（弹簧-质量系统振动）
  - 流体动力学（水流对物体的影响）
```

#### 程序化实现可行性
- 可与物理引擎（如 PyBullet、MuJoCo、Blender Physics）集成
- 每个物理定律场景可程序化生成无限样本
- Ground truth 由仿真引擎精确提供

**关键 evidence**：
> TesserAct (2504.20995): *"predict the dynamic evolution of 3D scenes... incorporating detailed **shape, configuration, and temporal changes**"* 证明 4D 物理理解是可行的学习目标

---

### 方向 D：记忆与长时程一致性推理
**Memory & Long-horizon Consistency Reasoning**

#### Gap 来源
- LoopNav (2505.22976): *"The ability to simulate the world in a spatially consistent manner is a **crucial requirement**... Designing a memory module is the key to enabling long-term prediction"*
- Context-as-Memory (2506.03141): *"existing approaches struggle with **scene-consistent memory capabilities** in long video generation"*
- 现有 VBVR：任务都在短视频内完成，没有跨时间步的记忆测试

#### 提案内容
```
任务类型 1 - 状态追踪（State Tracking）：
  视频内容：10 个物体在视频中依次被移动/变色/消失
  问题：视频结束时，物体X的位置/颜色/状态是什么？
  
任务类型 2 - 事件顺序记忆（Event Order）：
  视频内容：5 个事件按随机顺序发生
  问题：第3个发生的事件是什么？/ 事件A发生在事件B之前还是之后？
  
任务类型 3 - 循环一致性（Loop Consistency）：
  参考 LoopNav：模型从A到B再回到A，判断是否回到原始状态
  
任务类型 4 - 长时程依赖（Long-range Dependency）：
  视频开头放置一个线索，视频结尾问相关推理问题
  测试：模型能否维持跨越长视频的状态记忆
```

---

### 方向 E：具身动作后果推理
**Embodied Action Consequence Reasoning**

#### Gap 来源
- ACT-Bench (2412.05337): *"current research... with **limited attention** to whether world models can correctly **respond to action inputs**"*
- DINO-WM (2411.04983): *"world models should... support **test-time behavior optimization**, facilitate **task-agnostic reasoning**"*
- WM for Embodied AI Survey (2510.16732): *"anticipate how **actions reshape future world states**"*

#### 提案内容
```
任务形式：给定初始帧 + 一个动作指令 → 视频展示执行后的结果
          模型需要预测某个关键属性的变化

示例任务：
  - 机械臂执行"向左推"动作后，物体B的最终位置是哪里？
  - 机器人"抓取"红色杯子后，桌面上还剩几个杯子？
  - 给定轨迹动作，预测障碍物是否被避开

这延续了 VBVR 的"先生成视频，再评测推理"框架，
但将 action 显式纳入：
  Input: (first_frame, action) → Video → Reasoning Question
  而非现有 VBVR: first_frame → Video → Reasoning Question
```

#### 与 VBVR-1.0 的关系
这是 VBVR 最自然的扩展：将 action-conditional video reasoning 变成核心能力维度

---

### 方向 F：开放世界泛化测试
**Open-world Generalization Testing**

#### Gap 来源
- SVIB (2311.09064): *"Systematic compositionality... the ability to **adapt to novel situations by creating a mental model using reusable pieces of knowledge**"*
- WM-like-Child (2503.15168): *"they lack the **structured, adaptive representations** that even young children intuitively develop"*
- 现有 VBVR：任务类型固定，没有测试 out-of-distribution 泛化

#### 提案内容
```
In-distribution (ID) tasks: 和 VBVR-1.0 类似的程序化任务
Out-of-distribution (OOD) tasks: 
  - 新的物体类型（训练时没见过的形状）
  - 新的规则组合（把 VBVR-1.0 的两个规则组合成一个新任务）
  - 新的背景环境（在不同光照/材质/背景下测同类推理）
  
评测指标: ID accuracy vs. OOD accuracy 的 gap
           这个 gap 直接衡量系统性泛化能力
```

---

## 三、最终推荐：VBVR-2.0 的核心设计

### 推荐的核心扩展

基于以上分析，**最有影响力**的 VBVR-2.0 设计是组合 **方向 A + B + C**：

```
VBVR-2.0 = "物理因果推理基准"
全名建议：A Very Big Video Reasoning Suite for Physical Causal Intelligence

核心新增能力维度：
  6. Causality    - 因果链推理（A→B→C）
  7. Counterfactual - 反事实推理（"如果X不发生"）
  8. Physics-Law  - 物理定律推理（动力学、热力学等）
  [保留原有] 1.Knowledge 2.Abstraction 3.Spatiality 4.Transformation 5.Perception
```

---

## 四、VBVR-2.0 的技术实现路径

### 数据引擎设计

```
VBVR-2.0 Data Engine 架构：

[物理仿真层]
  ├── 物理引擎集成（MuJoCo / PyBullet / Blender Physics）
  ├── 因果图生成器（Causal Graph → Video Scenario）
  └── 反事实扰动器（对已有场景做 do-interventions）

[任务生成层]
  ├── 因果链任务生成器（depth 1/2/3/4-step chains）
  ├── 反事实任务生成器（remove/replace/add interventions）
  ├── 物理定律任务生成器（按层次：运动学→动力学→热力学）
  └── 记忆任务生成器（state tracker + event sequencer）

[评测层]
  ├── 程序化 Ground Truth（仿真引擎精确输出）
  ├── 自动评测器（不需人工标注，与 VBVR-1.0 一致）
  └── Leaderboard（统一排行榜）
```

### 与 VBVR-1.0 的一致性

| 特性 | VBVR-1.0 | VBVR-2.0 |
|------|----------|----------|
| 数据生成 | 程序化 ✅ | 程序化 ✅（继承） |
| Ground truth | 精确 ✅ | 精确 ✅（物理仿真） |
| 人类基准线 | 97.4% ✅ | 待测（预计仍高） |
| 任务维度 | 5 个 | 5+3=8 个（扩展） |
| 模型接口 | Image→Video→Answer | Image+Action→Video→Answer |
| Leaderboard | ✅ | ✅ |

---

## 五、VBVR-2.0 的预期贡献与影响

### 学术贡献

1. **填补最大的空白**：因果 + 反事实推理在所有现有 benchmark 中完全缺失
2. **统一框架**：将 VBVR-1.0 的程序化引擎扩展到物理因果领域
3. **清晰的人类上界**：揭示当前模型在因果推理上距人类的差距

### 对社区的价值

| 受益群体 | 价值 |
|---------|------|
| 视频生成模型研究者 | 明确的改进信号（不是只看 FVD 变好） |
| 具身 AI 研究者 | 可以在 benchmark 上验证世界模型的因果理解 |
| RL/Planning 研究者 | Counterfactual reasoning 直接相关 |
| 基础模型研究者 | 测试 LLM/VLM 的真实世界理解能力 |

### Evidence 支持这个方向会被引用

> RLVR-World (2505.13934): *"verifiable rewards"* → VBVR-2.0 的程序化 GT 天然是 verifiable reward 的来源

> V-JEPA 2 (2506.09985): *"models capable of understanding, predicting, and **planning** in the physical world"* → VBVR-2.0 直接评测这个目标

> WM for Embodied AI (2510.16732): *"counterfactual rollouts to support **perception, prediction, and decision making**"* → VBVR-2.0 的反事实维度直接对接

---

## 六、与竞品的差异化定位

| 竞品 | 覆盖什么 | VBVR-2.0 比它更好在哪 |
|------|---------|---------------------|
| WorldModelBench | 物理合理性（质量守恒） | 更深层因果链 + 反事实 |
| WorldPrediction | 过程规划 | 有程序化GT，可自动扩展 |
| SVIB | 系统性组合泛化 | 加入物理 grounding |
| EWMBench | 具身场景生成质量 | 测推理而非生成质量 |
| ACT-Bench | 行动可控性（驾驶） | 通用场景 + 因果推理 |
| LoopNav | 空间一致性记忆 | 加入因果+反事实维度 |

---

*下一步 → [Phase 4: Survey 提纲](./phase4-survey-outline.md)*
