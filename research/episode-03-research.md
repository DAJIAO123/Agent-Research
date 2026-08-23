# 第 3 篇研究记录：工具执行

## 研究基线

- 仓库：`openai/codex`
- 固定提交：`7b5b3bd5a2418a5e142449c9ab95e057d14bc98a`
- 重点文件：`codex-rs/app-server-protocol/src/protocol/v2/item.rs`、`codex-rs/app-server/src/request_processors/turn_processor.rs`

## 源码事实

协议层把 `CommandExecution`、`FileChange`、`McpToolCall` 等动作建模为独立 Item，并携带状态、标识、工作目录、输出或变更摘要。App-server 将 `turn/start` 转交 core，执行结果再以 item 事件流回客户端。

## 运行时关系

`model tool call -> 参数解析 -> 权限检查 -> 执行器 -> stdout/diff/error -> item completed -> 下一次 sampling`。模型产生的是意图，执行器负责把意图翻译成受控副作用。

## 架构取舍

协议化 Item 使 CLI、IDE 和桌面客户端共享状态语义，也让拒绝、超时和失败可被审计；代价是需要维护适配器、状态机和结果归一化。

## 可复现实验

记录一次成功、一次参数错误、一次权限拒绝和一次超时，比较每个 Item 的状态、cwd、退出码、输出和后续 step。不要只记录绿色成功链路。
