# lapian-to-prompt

- 把一份拉片分析（逐秒镜头表 + 十节分析）转成一条可直接投喂的"视频复原提示词"—— 先自动判断该走 codegen（HTML/CSS/JS 动画，适合排版/图解/数据可视化/UI 动效/几何 MG 类） 还是文生视频（Veo/Seedance/LTX 逐镜，适合实拍人物/真实场景/情绪大片），或两者混合 （codegen + [IMG] 素材槽）。Use this WHENEVER the user has a 拉片 / shot-by-shot teardown / 逐秒镜头表 / 分镜分析 and wants to 复刻 / 复原 / 重现 / recreate that video, or says things like "把这个拉片转成 prompt" / "用 codegen 复刻这个视频" / "把这个分析变成能生成视频的提示词" / "reproduce this ad" / "turn this teardown into a video-gen prompt" — even if they don't name the target tech. This is the DOWNSTREAM step that pairs with the video-lapian skill (which produces the teardown): once a teardown exists, use this to turn it into a reproduction prompt. Also trigger when the user pastes a detailed 镜头表 / shot list and asks how to recreate that video with code or a video model. Default output 中文 框架 + 英文 prompt 正文。

一个 Claude Code skill。来自合集 [claude-skills](https://github.com/Minerva67/claude-skills)。

## 安装

```bash
git clone https://github.com/Minerva67/lapian-to-prompt.git ~/.claude/skills/lapian-to-prompt
```

装好后在 Claude Code 里输入 `/lapian-to-prompt`，或者直接描述任务，符合条件时会自动触发。

## 文件

```
SKILL.md
references/codegen-prompt.md
references/patterns.md
references/text-to-video-prompt.md
```
