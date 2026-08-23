# 第 5 篇研究记录：权限边界

## 研究基线

- 仓库：`openai/codex`
- 固定提交：`7b5b3bd5a2418a5e142449c9ab95e057d14bc98a`
- 重点文件：`codex-rs/app-server/src/request_processors/turn_processor.rs`、`codex-rs/app-server/README.md`

## 源码事实

`turn/start` 接收 approval policy、sandbox policy、permissions、cwd 等运行设置；代码明确拒绝同时提供互斥的权限配置。App-server 文档还区分 sandbox 内命令、用户发起的 shell 命令和审批请求。

## 运行时关系

`能力声明 -> 沙箱范围 -> 具体审批 -> 执行 -> Item 状态`。能力决定能不能做，沙箱决定触及哪里，确认决定现在是否放行。

## 架构取舍

在运行层统一边界能让不同工具使用同一策略并留下审计事件；代价是兼容旧配置、跨平台沙箱和用户体验。把权限判断散落在工具内会增加策略裂缝。

## 可复现实验

依次尝试只读文件、工作区写入、工作区外路径和网络操作，记录被哪一层拦截、是否出现审批、拒绝后是否保留 Item。
