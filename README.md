# World Model Survey Notes
## VBVR-2.0 调研工作区

**日期**：2026-03-12  
**目标**：梳理 world model / video generation benchmark 现状，提出 VBVR-2.0 方向

---

## 文件结构

```
survey-notes/
├── README.md                         ← 本文件（总索引）
├── phase1-benchmark-landscape.md     ← Phase 1: 全景 benchmark 分析（15篇论文）
├── phase2-gap-analysis.md            ← Phase 2: Gap 分析 + 社区关注点（10篇论文）
├── phase3-vbvr2-proposal.md          ← Phase 3: VBVR-2.0 方向提案（含 evidence）
└── papers-raw/                       ← 原始论文 abstract 存档（备查）
```

---

## 核心结论速览

### 现有 Benchmark 的最大空白

| 空白维度 | 严重程度 | 有无 Benchmark |
|---------|---------|--------------|
| 反事实推理 | 🔴 最严重 | ❌ 无 |
| 因果链推理（多步） | 🔴 严重 | ❌ 无 |
| 长时程记忆 | 🟠 较严重 | ⚠️ LoopNav（初步） |
| 物理定律（深层） | 🟠 较严重 | ⚠️ WorldModelBench（浅） |
| 闭环评测 | 🟠 较严重 | ⚠️ World-in-World（初步） |
| 3D/4D 推理 | 🟡 中等 | ⚠️ 有数据集，无 benchmark |
| 行动可控性 | 🟡 中等 | ✅ ACT-Bench（自驾） |

### VBVR-2.0 推荐方向

```
优先级排序：
1. 🥇 反事实推理（Counterfactual Reasoning）— 空白最大，影响力最高
2. 🥈 因果链推理（Causal Chain Reasoning）— 自然延伸，程序化可行
3. 🥉 物理定律深层推理（Deep Physics）— 现有 benchmark 太浅
4. 记忆/长时程一致性（Memory）— 社区关注度高
5. 具身动作后果（Embodied Action Consequence）— 应用价值高
```

---

## 核心引用来源

所有分析基于以下论文的 **arXiv abstract 直接抓取**（非二手引用）：

### Benchmark 论文（Phase 1）
- [WorldModelBench 2502.20694](https://arxiv.org/abs/2502.20694)
- [WorldSimBench 2410.18072](https://arxiv.org/abs/2410.18072)
- [WorldScore 2504.00983](https://arxiv.org/abs/2504.00983)
- [EWMBench 2505.09694](https://arxiv.org/abs/2505.09694)
- [WM-ABench 2506.21876](https://arxiv.org/abs/2506.21876)
- [SimWorld 2503.13952](https://arxiv.org/abs/2503.13952)
- [WorldPrediction 2506.04363](https://arxiv.org/abs/2506.04363)
- [EVA 2410.15461](https://arxiv.org/abs/2410.15461)
- [ACT-Bench 2412.05337](https://arxiv.org/abs/2412.05337)
- [LoopNav 2505.22976](https://arxiv.org/abs/2505.22976)
- [World-in-World 2510.18135](https://arxiv.org/abs/2510.18135)
- [OmniWorld 2509.12201](https://arxiv.org/abs/2509.12201)
- [Benchmarking WM Learning 2510.19788](https://arxiv.org/abs/2510.19788)
- [UNIVERSE 2506.17967](https://arxiv.org/abs/2506.17967)
- [SVIB 2311.09064](https://arxiv.org/abs/2311.09064)
- [DrivingGen 2601.01528](https://arxiv.org/abs/2601.01528)
- [Toward Stable WM 2503.08122](https://arxiv.org/abs/2503.08122)

### Survey/Position 论文（Phase 2）
- [Physics Cognition Survey 2503.21765](https://arxiv.org/abs/2503.21765)
- [3D/4D WM Survey 2509.07996](https://arxiv.org/abs/2509.07996)
- [WM for Embodied AI Survey 2510.16732](https://arxiv.org/abs/2510.16732)
- [From Masks to Worlds 2510.20668](https://arxiv.org/abs/2510.20668)
- [WM Safety 2411.07690](https://arxiv.org/abs/2411.07690)
- [WM like Child 2503.15168](https://arxiv.org/abs/2503.15168)
- [Is Sora a WM 2405.03520](https://arxiv.org/abs/2405.03520)
- [Understanding WM Survey 2411.14499](https://arxiv.org/abs/2411.14499)

### VBVR-2.0 方向支撑论文（Phase 3）
- [RLVR-World 2505.13934](https://arxiv.org/abs/2505.13934)
- [V-JEPA 2 2506.09985](https://arxiv.org/abs/2506.09985)
- [TesserAct 2504.20995](https://arxiv.org/abs/2504.20995)
- [DINO-WM 2411.04983](https://arxiv.org/abs/2411.04983)
- [Context-as-Memory 2506.03141](https://arxiv.org/abs/2506.03141)
- [KeyWorld 2509.21027](https://arxiv.org/abs/2509.21027)
- [SWIFT 2503.24320](https://arxiv.org/abs/2503.24320)
