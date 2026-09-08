---
name: "gmu-elearn-assignment-assistant"
description: "Use when the user asks `@use computer` to help with coursework in 广州医科大学 e学中心 / 泛雅学习平台: locate the course and assignment, read requirements, write and temporarily save a high-quality answer, have a GPT-6 Astra subagent independently review it, improve and re-save if needed, and report status without final submission."
---

# GMU E-Learn Assignment Assistant

## Purpose
- Handle browser navigation for 广州医科大学 e学中心 / 泛雅学习平台 with `@use computer`.
- Help the user open the right course and assignment, extract requirements, draft a high-quality answer, and fill the editor when feasible.
- Treat this as a high-standard coursework workflow by default: 用户希望这个作业质量要高，必须要写的非常好才行。
- Save the completed draft with `暂时保存`, have a GPT-6 Astra subagent independently evaluate quality, improve and re-save if needed, then stop before any final submit action and leave final review and submission to the user.

## Trigger patterns
- Use this skill when the user says things like:
  - `@use computer 帮我处理《公共卫生应急》这门课的未完成作业`
  - `@use computer 完成《卫生法学》的未交作业`
  - `@use computer 打开广州医科大学e学中心里《课程名》的作业并整理答案`
- Treat the course name inside `《》`, Chinese quotes, or after `课程/这门课` as the primary variable.
- If the user also provides a specific assignment name, prefer exact matching over the first pending assignment.

## Core workflow
1. Use the Computer Use plugin whenever browser control is needed.
2. Prefer the bookmark `广州医科大学e学中心`; if it is missing, use an already-open tab on the same site or the school's e-learning landing page if clearly identifiable.
3. If login is required and Chrome autofill already has the saved account and password, click `登录` without asking for an extra confirmation step. Pause only for captcha, MFA, SMS, QR code, or device-confirmation steps.
4. Enter `学习空间`, open `我学的课`, search the course name, and open the unique best match. If multiple plausible matches remain, stop and ask the user which one to use.
5. Click the left menu `作业`, then open the named assignment or else the first blue `未交` / `未完成` item.
6. You must independently read the current assignment page, including visible text, tables, images, attachments, prompts, deadlines, existing answer content, and hidden/expanded instructions where feasible.
7. Draft the answer yourself. The answer should be specific to the page facts, complete for every question, academically rigorous, naturally written in Chinese, and not padded with generic filler or invented facts.
8. Fill the answer into the editor. If the page says `不可粘贴，只可录入`, `只能录入不能粘贴`, or otherwise blocks paste, immediately use JavaScript to bypass the paste restriction and write the answer into the current answer editor. Also use this fallback for failed normal input or garbled Chinese. Follow [references/codex-astra-review.md](./references/codex-astra-review.md): use the editor's content-setting API or its actual editable content area, synchronize editor/form state, and verify the full answer visibly appears before temporary saving.
9. After the answer is fully filled and visibly present in the editor, click `暂时保存` and verify that the draft save succeeded.
10. After verifying the temporary save, use `collaboration.spawn_agent` with `model: "gpt-6-astra"` and `fork_turns: "none"` to independently review the assignment materials and the full saved answer. Follow the evidence handoff and review rules in [references/codex-astra-review.md](./references/codex-astra-review.md). The main agent retains browser control and handles revisions and temporary saves; the subagent only reviews. If changes are needed, correct the answer, replace the editor content, click `暂时保存` again, verify the save, and have the same subagent confirm the revised version. End on the assignment page without clicking `提交`, `保存并提交`, `最终提交`, or equivalent final-submit actions. Report the actual save and review status with the template in [references/failure-and-reporting.md](./references/failure-and-reporting.md).

## Boundaries
- Never click `提交`, `保存并提交`, `最终提交`, or any equivalent button.
- `暂时保存` is allowed and expected after the draft is fully written into the editor.
- Do not invent course requirements, deadlines, case facts, teacher instructions, or references that are not visible on the page.
- If page information is incomplete, say exactly what is missing.
- If an existing human draft would be overwritten, pause and ask before clearing it.
- The independent reviewer must be a subagent explicitly launched with `model: "gpt-6-astra"`; do not substitute another model or count the main agent's self-check as independent review. If that subagent cannot be started or cannot complete the review, preserve the draft and report the save status and incomplete review.

## Read these references as needed
- [references/site-flow.md](./references/site-flow.md): page navigation, recognition cues, and matching rules.
- [references/codex-astra-review.md](./references/codex-astra-review.md): Codex drafting, rich-text editor fallback, temporary save, and GPT-6 Astra subagent review workflow.
- [references/failure-and-reporting.md](./references/failure-and-reporting.md): fallback rules, stop conditions, and final report format.
