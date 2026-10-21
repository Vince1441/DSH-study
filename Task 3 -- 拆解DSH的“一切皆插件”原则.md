---
title: "Task 3 -- 拆解DSH的“一切皆插件”原则"
type: synthesis
created: 2026-10-21
updated: 2026-10-21
source-count: 0
tags: []
---

# Task 3 -- 拆解DSH的“一切皆插件”原则

## 综合概述
1. 说清 4 套 Preset 不是 4 个独立 Agent，而是同一 Runtime 中的 4 种能力组合
    **核心结论**：4套Preset（标准、PTC、极简、创造）不是4个独立的Agent进程，而是同一DSH Runtime（进程）中，通过不同Preset动态挂载的4种能力组合。
    - 每个Session通过Agent Scope（作用域）挂载一个Preset，Preset为当前Session提供会话级能力（如Prompt、Tool、工作流），这些能力仅在当前Session的作用域内生效，不影响其他Session。


2. 准确区分 4 个核心概念：**Preset、Scope、Host Plane（宿主层）、Agent Plane（会话层）**
- Preset：会话级能力组合的“模板”，定义当前Session需要挂载的Agent级能力（如Prompt、Tool、工作流）。
- Scope（作用域）：一项能力的“可见性与生命周期边界”，决定能力属于哪个Session、谁能使用、何时取消。
- Host Plane（宿主层）：组织跨Session共享能力的通用服务。
- Agent Plane（会话层）：会话级能力组合层，负责将Host Plane的基础设施、、适配到当前Session，并决定如何呈现给模型。


3. 解释 Scope Context 的意义：会话级能力从哪里来，为什么动态组合必须有边界
- 会话级能力来源自Preset，且依赖于Host Plane的共享基础设施；
- 若动态组合无边界，将导致跨会话干扰以及资源浪费等后果。


4. 分析 Standard 的能力基线：每项能力属于哪个作用域、由谁提供、向谁开放（三类组合项：面向模型的工具 / 会话级状态 / Host 能力适配项）
- Standard是完整编程类Preset，包含代码读写、命令执行、搜索、规划、Subagent等能力。其能力可分为3类组合项，分别对应不同的作用域、提供者与开放对象：
- 1.面向模型的工具：
    - tool-bash：执行Bash命令（Preset定义，Host提供shell executor）；
    - tool-fs：文件操作（Preset定义，Host提供filesystem service）；
    - tool-web：网络请求（Preset定义，Host提供web service）。
- 2.会话级状态： 
    - Plan/Goal：任务规划与目标管理（Preset定义，用于模型生成任务步骤）；
    - Workflow：工作流状态（Preset定义，用于跟踪任务进度）； 
    - Context/Compaction：上下文压缩（Preset定义，用于减少模型输入长度）。
- 3.Host能力适配项：
    - tool-subagent：调用子Agent（Preset定义，Host提供Subagent Registry）；
    - Model Route：模型路由（Preset配置，Host提供模型路由服务，用于选择模型）。


5. 对比 PTC 的"同一批能力，不同呈现方式"（Tool Presentation，mode: code）与 Minimal 的"把 Harness 压薄"（固定 Prompt + 持久 Bash + str_replace_editor）

- 1. PTC：“同一批能力，不同呈现方式”
    - 核心改变：Tool Presentation（工具呈现方式），从Native Tool Calling（原生工具调用，模型通过Tool Schema直接调用工具）变为Code Mode（模型生成TypeScript程序，通过“run_code”工具执行多步工具调用）。
- 2.Minimal：“把Harness压到非常薄”
    - **核心目标**：减少模型**直接看到和使用的Harness能力**，仅保留**必要的基础能力**（如固定Prompt、持久Bash、str_replace_editor），用于**观察模型本身的表现**（如Benchmark）。


## 关键洞察

1.  DSH的分层避免了哪两种极端？各自代价是什么？
- 极端1：所有能力都放在全局范围（Host Plane）
    - 代价：跨会话干扰（如Session A新增的Tool影响Session B）、资源冲突（如同名服务冲突）。
- 极端2：每个Agent都独立启动一套完整基础设施
    - 代价：资源浪费（如重复创建Model Route、Persistence）、管理复杂（如难以统一更新底层服务）。


2.  “会话A新增的Tool影响到会话B”——问题出在哪一层？
- 问题层：Scope（作用域），即Preset的能力错误注册到Host Scope（全局范围），而非Agent Scope（会话级）。
- 解决：将Session A新增的Tool注册到当前Session的Agent Scope，确保能力仅在当前Session可见。


3. PTC与Standard在源码层面的差异有多小？改变的是什么？
- 源码差异：极小，PTC基本保留了Standard的运行时组合（如Tool、Prompt），仅额外加入了Tool Presentation（工具呈现）配置，并将当前Agent的工具呈现模式设置为`mode: code`。

--- 
## 相关页面

#Agent #领域Area 
