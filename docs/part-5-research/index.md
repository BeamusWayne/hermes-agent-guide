---
hermes_version: 0.10.0
---

# 第五部 · 研究

**目标**:把 Hermes 作为**研究基础设施**——批量生成 agent 轨迹、用 Atropos 环境做 RL 训练、压缩轨迹作训练数据、评估新模型的 agent 能力。

!!! info "这一部是给谁看的"
    做 agent RL / 工具调用模型训练 / agent 能力评估的研究者、PhD、工程师。

    **日常用户可以跳过**。

## 这部分包含什么

<div class="grid cards" markdown>

-   :material-batch: __[32. 批量轨迹生成](32-batch-trajectories.md)__

    ---

    `batch_runner.py` 并行跑任务,生成结构化轨迹数据。

-   :material-package-down: __[33. 轨迹压缩](33-trajectory-compression.md)__

    ---

    `trajectory_compressor.py` —— 把原始 agent 轨迹压成 SFT 训练样本。

-   :material-robot: __[34. Atropos RL 环境](34-atropos-rl.md)__

    ---

    `environments/` + `tinker-atropos`,RL 训练 agent 模型。

-   :material-scale-balance: __[35. Agent 能力评估](35-agent-eval.md)__

    ---

    用 Hermes 评估新 LLM 的 agent 能力,mini-swe-bench 集成。

</div>

## 读完这部分,你应该能做到

- [x] 用 `batch_runner` 并行跑 100 条任务,收集结构化轨迹
- [x] 理解轨迹压缩做了什么,能调参适配你的训练格式
- [x] 启动一个 Atropos 环境,用 Tinker 做 GRPO 训练
- [x] 设计自己的 agent 能力 benchmark,用 Hermes 评估多个模型

准备好了,从 [32. 批量轨迹生成 →](32-batch-trajectories.md) 开始。
