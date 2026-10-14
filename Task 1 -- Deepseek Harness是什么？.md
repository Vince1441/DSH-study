---
title: "Task 1 -- Deepseek Harness是什么？"
type: entity
created: 2026-10-14
updated: 2026-10-14
source-count: 0
tags: []
---

# Task 1 -- Deepseek Harness是什么？

## 定义
DSH将Model Adapter、Tools Registry、Session Log、Agent Loop等产品能力打包成插件，根据不同的使用场景调用不同的能力，相对稳定的核心负责规定组件如何组合和退出。

传统Agent：固定Agent Lop=Tools+MCP+Hooks
DSH：Agent Runtime=Context+Tools+State+Loop+Policy+Execution


## 关键信息

- ”一切皆插件“=这些能力如何参与整个系统的组合，DSH让它们遵循同一套插件组合规则，底层运行时统一管理它们的加载、依赖、作用域和生命周期，并不等同于可以将产品能力想换就换、想删就删。
- 学习了DeepSeek Harness与其他Agent产品的区别：“一切皆插件!”：相当一部分主流Agent产品将工具执行、模型调用、会话管理等能力写在核心程序中；但是在某些会话场景中，未必会需要调用全部能力，且如果想改变其中某一部分，可能需要修改核心代码。（这一部分让我想到了前端模块化的进程，但感觉DSH的设计思想会更加深入，待后续学习验证）
- 关于DSH是否会成为行业标准，如我学习本课程所想的，DSH的设计思想相较于其他主流Claude Code、Codex确实有较大不同，但DSH目前时期或未来也未必是行业标准，随着大模型能力的不断升级迭代，可能只需要一个稳定的核心和明确的目标，就能够调用已有能力、生成缺失能力、验证新能力，并把成功的能力沉淀为可复用插件。

--- 
## 相关页面

#Agent #领域Area 