# 第 8 篇研究记录：子代理协作

## 研究基线

- 仓库：`openai/codex`
- 固定提交：`7b5b3bd5a2418a5e142449c9ab95e057d14bc98a`
- 重点文件：`codex-rs/app-server-protocol/src/protocol/v2/thread_data.rs`、`codex-rs/app-server-protocol/src/protocol/v2/item.rs`

## 源码事实

Thread 数据包含 `parent_thread_id`、agent nickname/role 等协作元数据；Item 协议包含 `CollabAgentToolCall` 与 `SubAgentActivity`。这证明协作有独立的线程和事件投影，但不单独证明具体调度策略。

## 运行时关系

`主代理拆解 -> 子任务输入/边界 -> 独立执行 -> 协作 Item -> 主代理检查证据 -> 合并与验收`。并行收益必须超过上下文复制、同步和冲突成本。

## 架构取舍

独立 Thread 隔离局部状态和失败，代价是上下文复制、权限继承、结果合并与工作区所有权。顺序执行状态简单，但无法缩短互不依赖的研究线。

## 可复现实验

把任务拆成文档核查、源码追踪、实验设计三条独立线，比较串行/并行的耗时、重复内容、冲突数和证据质量；再故意并行修改同一文件观察合并成本。
