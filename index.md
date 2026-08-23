---
layout: default
---

# Agent Research

一组从产品现象、原始代码、运行时调用链和架构取舍出发，理解 Codex Agent 机制的中文文章。

## 系列路线

`任务状态 → 上下文 → 工具动作 → 计划控制 → 权限边界 → 验证闭环 → 失败恢复 → 子代理协作`

## 章节目录

### 01 · Codex 保存的不是答案，而是一段可重放的执行历史

[阅读本章](articles/01-execution-history.md)

### 02 · 上下文不是仓库镜像：Agent 如何决定下一步看什么

[阅读本章](articles/02-context-selection.md)

### 03 · 模型不会直接操作环境：工具层如何把判断变成动作

[阅读本章](articles/03-tool-execution.md)

### 04 · 计划不是预测：Agent 如何让复杂任务保持可校正

[阅读本章](articles/04-plan-control.md)

### 05 · Agent 为什么会停下来：权限、沙箱与确认控制的不是同一件事

[阅读本章](articles/05-permission-boundaries.md)

### 06 · 修改成功只是执行结果：验证如何把 Agent 拉回现实

[阅读本章](articles/06-verification-loop.md)

### 07 · 当 Agent 走不通时：恢复能力比重复尝试更重要

[阅读本章](articles/07-failure-recovery.md)

### 08 · 子代理不是多开几个聊天框：并行协作的成本与边界

[阅读本章](articles/08-subagent-coordination.md)

![Agent 执行闭环](assets/visuals/01-agent-runtime-loop.png)

源码仓库：[`openai/codex`](https://github.com/openai/codex)
