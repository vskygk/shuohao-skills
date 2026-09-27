# H3 视频提示词 · 写法规范（内化版）

方法论学自 MiniMax-H3 官方提示词指南（I2VA 与 Ref2VA），**内化成本 skill 自带文档——不依赖任何外部 skill**。写每段的 `h3Prompt` 照这份做，结构部分有质量门逐字对账。

**正文内容怎么写（一个运镜、动作要做得完、台词逐字、声音分层、不写画风）见 `shot-writing.md`，本文只讲 H3 特有的语法和字段。**

## 语言分工

- **默认整条英文**（`promptLang: 'en'`）——官方规范的口径：正文、对齐指令、字段名、镜头标记全英文，禁角色名（用 an old ferryman 这类通用身份）
- 三样东西保留原文语言（官方规定）：**台词**（`<d>[Chinese] …</d>` 逐字原文，一个标点都不许动，门盯着）、歌词、画面里可见的文字（英文双引号原样引用）
- `promptLang: 'zh'` 可切整条中文（对齐指令、字段名、镜头标记都有中文版）——偏离官方推荐的备选项，实测中文效果不稳就回英文。**禁角色名这一条不跟着语言变**：它来自官方规范，中文版同样用通用身份

## 结构（validate 逐字对账的部分）

旧 I2VA 多图对齐包可使用下面的三字段结构。带多张具体关键帧、角色/场景/音色参考的投产包优先使用后面的 Ref2VA 六段式；两种格式均受校验器支持，但不要在同一段混用。

```text
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot 2) aligns with the 3.00-second mark of the target video; ….
（单分镜的段改用官方 I2VA 固定句：For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.）

integrated_multimodal_description:
[Shot 1] 按 <Picture 1> 的构图锚定人物与状态，再写这几秒发生什么、镜头怎么动、谁说了什么（全英文，**不写画风句**——画风由调用方统一附加）。
[Shot 2] At 00:03.000, the camera cuts to <Picture 2>: ……（**每个镜头独立一行**，切点时刻开头，等于前面分镜秒数的累计）

overall_soundscape: 1–4 句英文：环境声、动作声、非语言人声。不复述台词。

non_diegetic_music: 1–3 句英文写配器与速度（角色听不见、只有观众听得见）。没有就写 N/A。
```

中文模式（promptLang=zh）的对应 token：`参考图与目标视频的对齐——` / `整体视听描述：` / `[镜头 k] 于 00:0X.XXX，`，配乐没有写「无」。

首行对齐指令和切点时刻**由分镜秒数推导**，改了秒数忘改提示词，validate 当场拦。

## Ref2VA 六段式（推荐用于关键帧投产）

```text
subject_definitions:
<Subject 1> is the reusable character or environment reference.
<Picture 1> is the concrete keyframe and composition lock for [Shot 1] at 00:00.000, defining viewpoint, screen direction, subject placement, and spatial layout.
<Picture 2> is the concrete keyframe and composition lock for [Shot 2] at 00:03.000, defining viewpoint, screen direction, subject placement, and spatial layout.
<Audio 1> is the voice-timbre reference for <Subject 1> (S1).

summary:
[keyframe completion + reference generation + audio reference] …

retention_analysis:
<Picture 1> ([Shot 1] keyframe at 00:00.000): fully_preserved - …
<Audio 1>: reference - … without copying the source signal.

detailed_description:
Segment-wide visual rules for every shot: …
[Shot 1] The shot begins from <Picture 1> …
[Shot 2] At 00:03.000, the camera cuts to <Picture 2>, the locked keyframe for this cut: …

overall_soundscape: …

non_diegetic_music: …
```

- 每个供 H3 挂载的 `<Picture N>` 都必须独立定义，并且在对应 `[Shot N]` 中引用；其时间等于前面分镜秒数的累计。
- 段级公共约束写在 `detailed_description` 的镜头之前，不能只塞进 `[Shot 1]`。
- `<Audio N>` 只描述音色参考或信号复用关系。上传顺序、文件路径和时长属于 `prompt.md` 的分隔线**之前**的投产材料区，不属于模型提示词正文。
- 若角色出现对白或情绪特写，允许 45° 可读面部特写，但眼神必须指向场内人、物、威胁或界面；除非剧情明确要求，不得直视镜头。

## 运镜词按语言

内容规则（一切一个运镜、写成动作句）见 `shot-writing.md`。H3 特有的是**词的语言**：英文模式用官方词（static shot / push in / tracking shot……），中文模式用词表的中文词（固定/推/拉/跟拍……）——门按 `promptLang` 检查，且**必须落在自己那一行里**。

## 说话人与台词

- 台词逐字、身份写在台词外面（见 `shot-writing.md`）。H3 特有的是**标记方式**：台词进 `<d>[Chinese] …</d>`，`<d>` 里只放语言标签和台词原文
- 说话人编号 `(S1)` `(S2)` 全段稳定，第一次出现给足辨识信息（身份、年龄段、音色、语速）；同说不同人用 `(S1,S2)`
- **画外音**：中文写「以画外音说（唇形完全闭合）」；英文用官方句式 `says in an off-screen voiceover … while their lips remain completely closed`
- **画外音优先后期分轨**：MiniMax 对画外说话人的归因不稳定时，把远程通讯、旁白、系统播报写入段级 `postAudioCues`，台词和该说话人的音色参考都不要进入 H3；由 VoxCPM 单独生成，按 `voiceover-manifest.json` 的切点在剪辑阶段混入。只有明确要让 H3 同步生成、并已验证不会误驱动画内人物口型时，才使用画外音句式。
- 画面里真实可见的文字（招牌、字条）用英文双引号原样引用，不翻译

## 声音字段名

三层怎么分见 `shot-writing.md`。落到 H3 的字段：台词、歌声、剧内音乐 → `integrated_multimodal_description` 的镜头行；环境与动作声 → `overall_soundscape`；配乐 → `non_diegetic_music`（没有写 `N/A`，中文模式写「无」）。

## 关键帧在 H3 里怎么引用

主分镜图（f1）钉 0.00 秒，是这一段世界观的完全参照；每个 `[Shot k]` 先写 `<Picture k>` 锚定这一切的构图与人物状态，再写动作展开。动作能不能做完、人物此刻在哪要和图一致，见 `shot-writing.md`。
