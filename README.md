# 超星作业助手

给有自主安排能力、知道自己该把时间花在哪里的大学生准备。专门用来对付水课，以及那些对你没什么用、却一点不少布置作业的课程。

少让无聊作业堆在待办里，在一定程度上减少漏作业带来的麻烦。

把时间拿回去，学你自己认为值得学的东西。

面向使用超星学习通／泛雅平台的大学生，以 Codex skill 的形式运行。默认从 [i.chaoxing.com](https://i.chaoxing.com) 进入，未登录时会跳转到学习通登录页面。主代理负责操作网页和暂存，子代理负责复核，最后由你检查并提交。

## 工作流

1. 打开 Chrome，访问 [https://i.chaoxing.com](https://i.chaoxing.com)，在跳转后的学习通登录页面使用浏览器已保存的账号登录；已登录时直接继续。
2. 按实际页面进入“课程”“我的课程”等课程列表，找到指定课程与作业；未指定作业名时，打开第一个未交／未完成作业。
3. 阅读题目、病例、表格、图片、附件和老师要求，逐问作答并自查。
4. 填入答题框。遇到“不可粘贴，只可录入”时，使用 JavaScript 写入编辑器，同步数据并核对全文。
5. 点击“暂时保存”，确认保存成功。
6. 启动独立子代理，明确指定 `model: "gpt-6-astra"`、`fork_turns: "none"`，交接完整题目、材料和已保存答案进行复核。
7. 按复核意见修改、再次暂存，并交回同一子代理确认最终版本。
8. 汇报保存和复核结果，停在作业页面，由你最终提交。

## 安装与调用

将本仓库中的 [`skills/gmu-elearn-assignment-assistant`](skills/gmu-elearn-assignment-assistant/) 文件夹复制到你的 Codex skills 目录，通常为 `~/.codex/skills/`。

调用示例：

```text
@use computer 用 gmu-elearn-assignment-assistant 帮我处理学习通里《课程名》的未完成作业。
```

也可以指定作业名称：

```text
@use computer 用 gmu-elearn-assignment-assistant 打开《课程名》的《作业名称》，完成答案并暂时保存。
```

## 运行条件

- Codex 环境支持 Computer Use 浏览器操作，以及可指定模型的子代理工具。
- 复核模型必须为 `gpt-6-astra`；不可用时报告复核未完成，不自动换模型。
- 遇到粘贴限制时，需要当前环境提供页面 JavaScript 执行能力。
- Chrome 中能够访问 [i.chaoxing.com](https://i.chaoxing.com)，并登录你自己的学习通账号；验证码、短信或扫码验证由你完成。

## 保留规则

- 只暂存，不点击“提交”“保存并提交”或其他最终提交按钮。
- 覆盖已有人工草稿前需要确认。
- 不编造题目材料、老师要求、截止时间或参考依据。
- 只有最终保存版本获得子代理认可，才报告复核通过。

## 文件说明

- [`SKILL.md`](skills/gmu-elearn-assignment-assistant/SKILL.md)：触发条件、主流程和边界。
- [`agents/openai.yaml`](skills/gmu-elearn-assignment-assistant/agents/openai.yaml)：名称、默认调用提示和启用设置。
- [`references/site-flow.md`](skills/gmu-elearn-assignment-assistant/references/site-flow.md)：平台导航与课程、作业匹配。
- [`references/codex-astra-review.md`](skills/gmu-elearn-assignment-assistant/references/codex-astra-review.md)：编辑器写入、暂存和 Astra 子代理复核。
- [`references/failure-and-reporting.md`](skills/gmu-elearn-assignment-assistant/references/failure-and-reporting.md)：异常处理与结果汇报。

## 验证状态

已完成 skill 格式、引用链接和 GPT-6 Astra 子代理的文档复核；尚未进行真实作业页面的完整试运行。

## License

[MIT License](LICENSE) · Copyright (c) 2026 Amber wang
