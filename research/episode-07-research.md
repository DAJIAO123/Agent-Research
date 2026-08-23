# 第 7 篇研究记录：失败恢复

## 研究基线

- 仓库：`openai/codex`
- 固定提交：`7b5b3bd5a2418a5e142449c9ab95e057d14bc98a`
- 重点文件：`codex-rs/app-server/src/request_processors/turn_processor.rs`、`codex-rs/core/src/session/rollout_reconstruction.rs`

## 源码事实

协议区分 `TurnAborted` 与完成/失败状态；中断请求按 Turn ID 关联活动 turn，事件流回 `TurnAborted`。rollout 重建逻辑负责从持久化事件恢复可继续的历史，而不是只保存最后一条回复。

## 运行时关系

`失败分类 -> 保留现场 -> 选择替代路径/请求确认 -> 重新读取外部状态 -> 继续或交接`。输入错误、环境错误、权限错误、验证错误和外部变化不能共用盲重试。

## 架构取舍

事件重建提供可解释恢复，代价是状态重放、旧版本兼容和外部变更检测。只保存最终文本更快，却无法知道哪些副作用已经发生。

## 可复现实验

分别触发不存在命令、端口占用、路径越界和测试失败，检查 Agent 是否分类、是否更换路径、是否保留已完成工作，并区分中断与失败。
