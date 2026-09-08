---
name: "gmu-elearn-assignment-assistant"
description: "Use when the user asks for help with coursework in 超星学习通 / 泛雅学习平台: start at https://i.chaoxing.com, sign in, locate the course and assignment, read requirements, write and temporarily save a high-quality answer, have an independent subagent using an approved review model review it, improve and re-save if needed, and report status without final submission."
---

# 超星作业助手

## Purpose
- Handle browser navigation for 超星学习通 / 泛雅学习平台 using the current CLI's available browser or computer-use tools, with `https://i.chaoxing.com` as the default entry for students at different institutions. This workflow can be loaded in Codex, Claude Code, Kimi Code, OpenCode, or another compatible Agent Skills host with the required tools.
- Help the user open the right course and assignment, extract requirements, draft a high-quality answer, and fill the editor when feasible.
- Treat this as a high-standard coursework workflow by default: 用户希望这个作业质量要高，必须要写的非常好才行。
- Save the completed draft with `暂时保存`, have an independent subagent using an approved review model evaluate quality, improve and re-save if needed, then stop before any final submit action and leave final review and submission to the user.

## Trigger patterns
- Use this skill when the user says things like:
  - `用超星作业助手帮我处理学习通里《课程名》的未完成作业`
  - `用 gmu-elearn-assignment-assistant 完成《课程名》里的《作业名称》并暂时保存`
  - `打开超星泛雅里《课程名》的作业并整理答案`
- A host-specific prefix such as `@use computer` may be used when supported; it is not required to trigger this skill.
- Treat the course name inside `《》`, Chinese quotes, or after `课程/这门课` as the primary variable.
- If the user also provides a specific assignment name, prefer exact matching over the first pending assignment.

## Core workflow
1. Identify the current CLI and use its available browser automation, computer-use, or browser MCP tools. Use tool names and parameters actually exposed by that host; Codex-specific plugins and `collaboration.*` calls are not prerequisites in other CLIs.
2. Open `https://i.chaoxing.com` in Chrome by default. When signed out, follow its redirect to the Chaoxing login page; when already signed in, continue from the landing page. Use a different course or institution entry only if the user explicitly provides one.
3. If login is required and Chrome autofill has the saved account and password, use them and click `登录` without an extra confirmation step. If no saved credentials are available, or captcha, MFA, SMS, QR code, or device confirmation is required, let the user complete that step on the page. If login requires an institution or identity that cannot be determined from the user's instructions or the page, ask rather than guessing.
4. After login, use the actual page labels to find the course list, such as `课程`, `我的课程`, or `我学的课`; if a `学习空间` entry is present, enter it as needed. Search the course name and open the unique best match. Do not depend on a particular school's menu layout. If multiple plausible matches remain, ask the user which one to use.
5. Find the course's `作业` entry wherever it appears, then open the named assignment or else the first `未交` / `未完成` item. Use the visible status text rather than requiring a particular color.
6. You must independently read the current assignment page, including visible text, tables, images, attachments, prompts, deadlines, existing answer content, and hidden/expanded instructions where feasible.
7. Draft the answer yourself. The answer should be specific to the page facts, complete for every question, academically rigorous, naturally written in Chinese, and not padded with generic filler or invented facts.
8. Fill the answer into the editor. If the page says `不可粘贴，只可录入`, `只能录入不能粘贴`, or otherwise blocks paste, immediately use JavaScript to bypass the paste restriction and write the answer into the current answer editor. Also use this fallback for failed normal input or garbled Chinese. Follow [references/draft-and-review.md](./references/draft-and-review.md): use the editor's content-setting API or its actual editable content area, synchronize editor/form state, and verify the full answer visibly appears before temporary saving.
9. After the answer is fully filled and visibly present in the editor, click `暂时保存` and verify that the draft save succeeded.
10. After verifying the temporary save, select an available model under [references/reviewer-selection.md](./references/reviewer-selection.md) and launch an independent reviewer through the current CLI's native subagent mechanism. Confirm the effective model and hand over the complete assignment materials and saved answer. Follow [references/draft-and-review.md](./references/draft-and-review.md). The main agent retains browser control and handles revisions and temporary saves; the subagent only reviews. If changes are needed, correct the answer, replace the editor content, click `暂时保存` again, verify the save, and have the same subagent confirm the revised version. End on the assignment page without clicking `提交`, `保存并提交`, `最终提交`, or equivalent final-submit actions. Report the actual model, save status, and review result with [references/failure-and-reporting.md](./references/failure-and-reporting.md).

## Boundaries
- Never click `提交`, `保存并提交`, `最终提交`, or any equivalent button.
- `暂时保存` is allowed and expected after the draft is fully written into the editor.
- Do not invent course requirements, deadlines, case facts, teacher instructions, or references that are not visible on the page.
- If page information is incomplete, say exactly what is missing.
- If an existing human draft would be overwritten, pause and ask before clearing it.
- The reviewer must be an independent subagent using a model allowed by [references/reviewer-selection.md](./references/reviewer-selection.md). Respect an explicit user model choice; otherwise select among eligible models already available in the current environment. Do not silently use a model outside that policy or count the main agent's self-check as independent review. If no eligible reviewer is available or review cannot finish, preserve the draft and report the actual save status and incomplete review.

## Read these references as needed
- [references/site-flow.md](./references/site-flow.md): page navigation, recognition cues, and matching rules.
- [references/draft-and-review.md](./references/draft-and-review.md): Drafting, rich-text editor fallback, temporary save, and independent subagent review workflow.
- [references/failure-and-reporting.md](./references/failure-and-reporting.md): fallback rules, stop conditions, and final report format.
- [references/reviewer-selection.md](./references/reviewer-selection.md): approved model families, version thresholds, and CLI-specific model selection.
