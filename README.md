# 超星作业助手

给有自主安排能力、知道自己该把时间花在哪里的大学生准备。专门用来对付水课，以及那些对你没什么用、却一点不少布置作业的课程。

少让无聊作业堆在待办里，在一定程度上减少漏作业带来的麻烦。

把时间拿回去，学你自己认为值得学的东西。

面向使用超星学习通／泛雅平台的大学生，以 Agent Skill 的形式运行，可交给具备浏览器操作和独立子代理能力的 Codex、Claude Code、Kimi Code、OpenCode 等 CLI。默认从 [i.chaoxing.com](https://i.chaoxing.com) 进入，未登录时会跳转到学习通登录页面。主代理负责操作网页和暂存，子代理负责复核，最后由你检查并提交。

## 工作流

1. 打开 Chrome，访问 [https://i.chaoxing.com](https://i.chaoxing.com)，在跳转后的学习通登录页面使用浏览器已保存的账号登录；已登录时直接继续。
2. 按实际页面进入“课程”“我的课程”等课程列表，找到指定课程与作业；未指定作业名时，打开第一个未交／未完成作业。
3. 阅读题目、病例、表格、图片、附件和老师要求，逐问作答并自查。
4. 填入答题框。遇到“不可粘贴，只可录入”时，使用 JavaScript 写入编辑器，同步数据并核对全文。
5. 点击“暂时保存”，确认保存成功。
6. 使用当前 CLI 的独立子代理，选择符合下方要求的复核模型，交接完整题目、材料和已保存答案。
7. 按复核意见修改、再次暂存，并交回同一子代理确认最终版本；CLI 无法继续原子代理时，向新的合格复核子代理补齐材料后复审。
8. 汇报保存和复核结果，停在作业页面，由你最终提交。

## 安装与调用

将本仓库中的 [`skills/gmu-elearn-assignment-assistant`](skills/gmu-elearn-assignment-assistant/) 整个文件夹复制到所用 CLI 的 skills 目录，保留 `SKILL.md` 和 `references/`。

| CLI | 用户级安装目录 |
| --- | --- |
| Codex | `~/.codex/skills/`（本项目原有安装方式） |
| [Claude Code](https://code.claude.com/docs/en/skills) | `~/.claude/skills/` |
| [Kimi Code](https://moonshotai.github.io/kimi-code/en/customization/skills.html) | `~/.agents/skills/`，或当前版本支持的 Kimi skills 目录 |
| [OpenCode](https://opencode.ai/docs/skills/) | `~/.config/opencode/skills/` 或 `~/.agents/skills/` |

`agents/openai.yaml` 是 Codex 的界面配置；其他 CLI 读取通用的 `SKILL.md` 与引用文件即可。浏览器操作和子代理能力需要在所用 CLI 中可用。

调用示例：

```text
用 gmu-elearn-assignment-assistant 帮我处理学习通里《课程名》的未完成作业。
```

也可以指定作业名称：

```text
用 gmu-elearn-assignment-assistant 打开《课程名》的《作业名称》，完成答案并暂时保存。
```

## 复核模型

不固定使用 Astra。以下模型都可以承担独立复核：

| 系列 | 接受范围 |
| --- | --- |
| OpenAI | GPT-6 Astra |
| Claude | Opus 5 及以上版本 |
| Claude Fable | Fable 系列 |
| GLM | GLM 5.3 及以上版本 |
| Kimi | Kimi K3 及以上版本 |
| DeepSeek（DS） | V4F 及以上版本 |

使用哪个 CLI，与使用哪个复核模型分开配置。按当前环境实际可用的模型选择，记录最终生效的完整模型 ID 与版本；详细规则见 [复核模型与 CLI 适配](skills/gmu-elearn-assignment-assistant/references/reviewer-selection.md)。

## 运行条件

- 当前 CLI 能加载此 skill，并提供浏览器操作或浏览器 MCP，以及独立子代理能力。
- 至少有一个符合上述范围的可用复核模型；没有合格候选时报告复核未完成。
- 遇到粘贴限制时，需要当前环境提供页面 JavaScript 执行能力。
- Chrome 中能够访问 [i.chaoxing.com](https://i.chaoxing.com)，并登录你自己的学习通账号；验证码、短信或扫码验证由你完成。

## 注意事项

- 使用墙外闭源模型时，尽量选择既能调用模型、又能正常打开超星学习通的节点。开始前用实际操控的浏览器确认 [i.chaoxing.com](https://i.chaoxing.com) 能打开，并在登录后确认课程页可访问；打不开时先换能访问超星的节点。
- 只暂存，不点击“提交”“保存并提交”或其他最终提交按钮。
- 覆盖已有人工草稿前需要确认。
- 不编造题目材料、老师要求、截止时间或参考依据。
- 只有最终保存版本获得子代理认可，才报告复核通过。

## 文件说明

- [`SKILL.md`](skills/gmu-elearn-assignment-assistant/SKILL.md)：触发条件、主流程和边界。
- [`agents/openai.yaml`](skills/gmu-elearn-assignment-assistant/agents/openai.yaml)：名称、默认调用提示和启用设置。
- [`references/site-flow.md`](skills/gmu-elearn-assignment-assistant/references/site-flow.md)：平台导航与课程、作业匹配。
- [`references/draft-and-review.md`](skills/gmu-elearn-assignment-assistant/references/draft-and-review.md)：编辑器写入、暂存和独立子代理复核。
- [`references/reviewer-selection.md`](skills/gmu-elearn-assignment-assistant/references/reviewer-selection.md)：复核模型门槛和不同 CLI 的适配方式。
- [`references/failure-and-reporting.md`](skills/gmu-elearn-assignment-assistant/references/failure-and-reporting.md)：异常处理与结果汇报。

## 验证状态

已完成 skill 格式、引用链接和独立子代理的文档复核。不同 CLI 的加载与子代理配置参考各自官方文档；尚未逐一进行各 CLI 的真实作业页面完整试运行。

## License

[MIT License](LICENSE) · Copyright (c) 2026 Amber wang
