# 复核模型与 CLI 适配

## 可用复核模型

这是本项目接受的复核模型范围。使用时从当前 CLI 或提供商实际可用的模型中选择，并确认完整型号与版本。

| 系列 | 接受范围 |
| --- | --- |
| OpenAI | GPT-6 Astra（保留原有选项） |
| Claude | Opus 5 及以上版本 |
| Claude Fable | Fable 系列 |
| GLM | GLM 5.3 及以上版本 |
| Kimi | Kimi K3 及以上版本 |
| DeepSeek（DS） | V4F 及以上版本 |

- 用户明确指定复核模型时优先按该选择执行；未指定时，优先使用当前环境已配置且符合上表的模型，不固定要求 Astra。
- “及以上”依据提供商公布的型号与版本判断。显示名称、路由别名和 CLI 名称不能代替真实模型身份，也不能凭名字相近就判定满足门槛。
- 使用当前 CLI 或提供商实际支持的模型 ID；不要把展示名直接拼成未经确认的 API 参数。
- 确认子代理最终生效的模型，而不只记录请求的模型。允许继承主代理模型，但必须能从有效配置或运行信息确认其符合要求，并保持独立上下文。
- 如果发生自动回退或模型替换，重新检查实际模型是否合格。没有合格候选、模型身份无法确认，或用户锁定的模型不可用时，保留草稿并报告复核未完成。
- 模型选择只作用于本次任务或复核子代理；不要为了运行本 skill 擅自更改用户的全局默认模型、账号或提供商配置。

## 在当前 CLI 中启动复核

先识别当前 CLI 暴露的工具、配置和恢复机制，再按 [draft-and-review.md](./draft-and-review.md) 交接材料。下列是适配要点，实际参数以当前版本为准。

### Codex

- 当前环境提供 `collaboration.spawn_agent` 时，使用实际可用且符合范围的 `model`，配合 `fork_turns: "none"` 启动独立子代理。选择原有 Astra 选项时，模型 ID 为 `gpt-6-astra`。
- 使用对应的结果等待和继续任务工具取得结论；当前环境提供 `collaboration.followup_task` 时，可用它向原子代理交付修订版。
- 其他 CLI 不需要提供这些 Codex 工具名。

### Claude Code

- 使用原生子代理，通过调用参数或子代理定义指定实际模型；完整模型 ID 比不固定版本的简称更便于核对。
- `opus`、`fable` 等简称需核对实际解析版本，不能仅凭简称确认合格。运行时的默认模型或回退也要检查。
- 依据当前版本的子代理继续机制复审；保留独立上下文与只读材料的分工。

参考：[子代理与模型选择](https://code.claude.com/docs/en/sub-agents)、[模型配置与 Fable](https://code.claude.com/docs/en/model-config)。

### Kimi Code

- 使用当前版本的 `Agent` 或等效子代理工具，并交接完整任务说明。
- 若配置了子代理模型池，按工具实际支持的池别名选择模型；没有模型池时，检查子代理继承的主模型是否满足门槛。
- 不把其他 CLI 的 `model` 配置字段直接照搬到 Kimi agent 文件中；某些版本会忽略这些字段。以实际生效配置和运行信息确认模型。
- 复审时使用当前版本支持的恢复参数或继续机制，并检查原子代理绑定的模型。

参考：[Agents and Sub-Agents](https://moonshotai.github.io/kimi-code/en/customization/agents.html)、[Agent 工具与模型池](https://github.com/MoonshotAI/kimi-code/blob/main/docs/en/reference/tools.md)。

### OpenCode

- 使用原生 subagent，按当前提供商可用列表选择 `provider/model-id`，在该子代理的 `model` 配置中明确设置。
- 可用模型以当前环境查询结果为准；复审使用其子任务或会话的继续机制。

参考：[Agents](https://opencode.ai/docs/agents/)。

## 能力不足时

- 当前 CLI 无法运行独立子代理，或无法确认合格模型时，报告复核未完成，不把主代理再读一遍答案算作独立复核。
- 复核模型无法直接读取某类图片或附件时，主代理提供可核对的内容或可读取的材料；无法补齐时明确记录受影响的题目，不宣称完整复核通过。
