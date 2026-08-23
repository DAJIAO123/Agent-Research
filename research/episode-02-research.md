# 第 2 篇研究记录：上下文选择

## 研究基线

- 仓库：`openai/codex`
- 固定提交：`7b5b3bd5a2418a5e142449c9ab95e057d14bc98a`
- 重点文件：`codex-rs/core/src/session/turn.rs`、`codex-rs/core/src/session/rollout_reconstruction.rs`

## 源码事实

`Turn` 在发送模型请求前先捕获 step context，再从 thread history 生成 `for_prompt(...)` 输入。两者分别代表当前执行环境快照和可供模型读取的历史投影。历史不是简单 `Vec` 拼接，而是经过输入模态、长度和重建策略处理。

## 运行时关系

`用户输入 -> TurnContext -> step context -> history.for_prompt -> model request -> tool result -> history`。工具结果进入历史后，下一 step 会重新选择可见输入；因此“保存完整现场”和“发送有限上下文”是两个职责。

## 架构取舍

集中式投影便于统一裁剪和审计；代价是需要处理旧事件、长输出和不同模型模态。把选择逻辑分散到每个工具会更灵活，却会造成规则漂移。

## 可复现实验

在根目录与子目录放置相互冲突的 `AGENTS.md`，让 Agent 修改目标文件，记录实际读取的规则、工具输出变长后的重读行为，以及最终 prompt 是否包含冲突规则。实验只能证明可观察行为，不能替代所有版本实现。
