---
name: music-creator
description: "Write or revise song lyrics and Suno-ready Custom prompts, then generate and iterate AI music takes. Use when the user wants to write a song, generate music on Suno, fix flat or rushed vocals, improve breaths, diction, or pronunciation, or export a finished take."
---

# Music Creator

帮用户把一个创作意图，快速变成可在 [Suno](https://suno.com/create) 里生成的完整歌曲，并按试听结果迭代。先锁定歌词和风格提示，再生成；不要把试错过程写进提示词。

适用：流行歌、情歌、华语或英语歌曲、按参考歌手气质写词和唱法。本技能以 Suno Custom（歌词 + 风格）为主；其他平台也可复用同一套词曲结构。

## 工作流程

按这个顺序做，不要跳步：

1. **收齐创作简报**：主题与故事、情绪、语言、人声性别、参考歌手或风格、必须避开的词、时长偏好。缺关键信息时先问再写。
2. **先写并锁定歌词**：用下面的演唱结构写完整歌词，给用户确认。用户定稿前不要去生成。
3. **再写 Styles**：单独写风格提示，只放进 Suno 的 Styles 栏，控制在 1000 字符以内。
4. **用 Custom 全新生成**：除非用户明确要保留某版的旋律和唱法，否则用空白 Custom，不要对旧曲做 Cover / Remix。
5. **一次出两版**：让用户选 A 或 B。根据反馈只改一个变量（断句、Styles、或生成方式），再出下一轮。
6. **导出并验收**：优先官方 WAV / MP3，核对时长和文件完整性后再交给用户。

未经用户明确要求，不要开通、升级或付费订阅。

## 歌词写法

Suno Lyrics 上限约 5000 字符。目标是让演唱有呼吸、有层次，而不是把字塞进固定拍子。

### 段落标签

用简短英文标签标段落，表演说明只写在标签里，不要写进要唱的歌词正文。中文表演说明一旦出现在正文里，模型常会把它们唱出来。

```text
[Verse 1: quiet self-talk, short endings]
[Verse 2: spoken memory, then heavier]
[Chorus: soft questions, rest after each, short cutoff]
[Verse 3: tighter, conversational, clipped endings]
[Bridge: almost spoken, long pauses]
[Final Chorus: softer, letting go, not louder]
[Outro: softest self-talk; last line very short]
```

需要时也可以用 `[Intro]`、`[Pre-Chorus]`、`[Instrumental]`。

### 断句和气口

- 一行就是一个换气单位。换行处要能换气，不要为了对齐小节把字塞满。
- 长句可以拆成两个乐句，或允许跨小节，优先保证呼吸感。
- 不要在虚词或词组中间切断，例如「的」「了」「着」「过」「再」「没有」「立刻」后面不要单独断行。
- 句号是完整停顿，问号后留空拍，省略号「……」表示气口未收完、可以悬住再进入下一句。
- 用户已锁定的措辞不要擅自改；先优化断句和 Styles。

### 读音提示

遇到可能被唱错的字，在字后加简短拼音，例如把「没收到」写成 `没(méi)收到`，避免被唱成 `mò`。括号只是读音提示，尽量短，不要变成额外要唱的音节。

### 结构示例

下面只示范结构，不要把示例故事当成用户的歌：

```text
[Verse 1: almost whispered self-talk; pause after every line]
那天没什么事，
我们刚好都有空。
你说你喜欢……
我眼睛里的光。

[Chorus: ask gently; rest after each question; last line almost breath]
我想问你，
最近过得开心吗？
我们是不是回不去了……
也许分开，
你才会更开心。

[Outro: softest; last line very soft and short]
希望你一切都好……
这就够了。
```

完整标签模板见 `references/lyric-template.md`。

## Styles 写法

只放进 Styles 栏，不要写进歌词。Suno Styles 上限是 **1000 字符**（含空格和标点）。写满可以，但不要超。超限时先压掉重复形容词，保留「对比、换气、反面约束」。

一段有效的 Styles 通常包含：

1. **总体气质**：语种、曲风、人声音色、参考唱法（如 conversational male tenor like Guang Liang）、力度上限（如 never belting）。
2. **反平淡**：不要像念词。每一句的音量、远近或节奏都要有变化；整首歌同样轻、同样平，也是平淡。
3. **换气规则**：每个换行和省略号都要留真实气口和短暂停；不要抢拍，不要把音节塞进固定拍子；长句可拆句或跨小节。
4. **演唱细节**：起音柔、尾音短收、少拖长音、浅颤音。
5. **分段对比**：主歌、副歌、桥段、尾奏的音量、语感和织体都要不同；同一段内部也要有起伏。
6. **反面约束**：Avoid rap, dense packing, monotone, even softness, auto-tune, crying tone, reciting.（除非用户点名要说唱或哭腔。）

可直接改写的模板见 `references/style-templates.md`。使用前按歌曲情绪替换分段描述，并再次数清字符。

## 在 Suno 里生成

打开 [Suno Create](https://suno.com/create)，用 **Custom / Advanced**，不要用简单一句描述直接出歌（除非用户只要草稿）。

生成前核对表：

- Lyrics：完整歌词，含段落标签；字符数不超过 5000
- Styles：完整风格提示，且只出现在 Styles；字符数不超过 1000
- Title：歌曲标题
- Vocal：用户指定的性别或声线
- Duration：未指定时用 Auto
- 模型：用当前套餐下最好的正式模型，不要用 Mini，也不要提交未付费的 1 分钟预览当成成品
- 确认当前是空白 Custom，没有挂上旧曲、参考音频，也不是 Cover / Remix
- 全部核对后再点一次 Create，等两首完整歌曲生成结束

### 什么时候用 Cover，什么时候重新生成

- **Cover / Remix**：适合歌词小改、读音修正，并想尽量保住已经满意的旋律和编曲。
- **全新 Custom**：适合唱法平淡、气口不对、情绪拉不开。Cover 很容易把旧版偏平的唱法和停顿一起继承过来。

用户觉得「很多地方都在很平地唱词」时，优先改断句和 Styles，并改为全新生成，而不是在同一条 Cover 上反复微调。

分轨（Stems）和自定义声音更适合后期修混，不能代替一次更好的生成。新的一版要更好听，主要靠更好的模型和更好的提示词。

## 验收和交付

- 付费套餐优先下载官方 WAV 和官方 MP3。免费套餐可能没有官方下载，不要把不完整的页面录音当成成品，除非用户同意。
- 用播放器或 `ffprobe` 核对时长，应和页面播放条相差约 0.5 秒以内，和曲库显示相差约 1 秒以内。
- 检查中段不要出现整段空白。
- 官方 MP3 常带很长的歌词标签和封面图，部分播放器会在中途停止。若用户反馈「放到一半就停」，先检查文件是否完整：完整的话，去掉封面和歌词标签再封装一版纯音频 MP3，或提供无损 FLAC / WAV。
- 48 kHz 立体声 16-bit 的官方 WAV，四分钟半大约 50 MB，可能超过聊天或网盘附件上限。可用同样内容的无损 FLAC 交付，或把原 WAV 存到用户本机。

## 根据试听迭代

请用户重点听这几项：咬字、换气、分段情绪对比、指定读音是否正确。

然后只改最相关的一项再生成：

| 问题 | 先改什么 |
| --- | --- |
| 太赶、没气口 | 歌词断句、句号和省略号；Styles 里强调 rest / 不要塞拍 |
| 整首歌一个力度、像念词 | Styles 加强句级对比，并改为全新 Custom，不要再 Cover 旧版 |
| 某个字读错 | 只在该处加读音提示，再生成；若只要改读音并保住旋律，可用 Cover |
| 某段情绪不对 | 只改该段标签和 Styles 里对应句子 |
| 想换曲风或参考歌手 | 重写 Styles，歌词结构可保留 |

把上一轮已验证有效的约束留下，不要把失败过的长提示词整段堆回去。
