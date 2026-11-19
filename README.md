# Music Creator Skill

面向主流编码代理的歌曲创作技能：把主题快速写成可演唱的歌词，并生成适合 [Suno](https://suno.com/create) Custom 模式的 Styles 提示，再按试听结果迭代。

它整理的是已经验证有效的工作方式，去掉了反复试错的提示词堆砌。同一份 `SKILL.md` 可给 Cursor、Claude Code、Codex 和 DeepSeek Harness 使用。

## 它能做什么

- 根据主题、情绪和限制写出分句合理、带气口的歌词
- 把唱法要求写成不超过 1000 字符的 Suno Styles
- 判断该用全新生成，还是用 Cover 保留已有旋律
- 处理读音提示、平淡唱法、中途停播、官方音频导出等常见问题

## 仓库结构

```text
.
├── README.md                      # 说明与安装
├── SKILL.md                       # 技能正文（克隆后可直接作为技能目录）
└── references/
    ├── lyric-template.md          # 歌词段落模板
    └── style-templates.md         # 可改写的 Styles 模板
```

安装时请保证目标目录里**直接**含有 `SKILL.md`，不要多套一层文件夹。

## 安装

先克隆本仓库，再按你使用的代理放到对应技能目录。下面以个人（全局）安装为例。

```bash
git clone https://github.com/lhjiang89/music-creator-skill.git
```

### 一条命令，装到多个代理都能读到的位置

`.agents/skills/` 是跨工具的常用目录（Codex、Cursor、DeepSeek 都会读）。Claude Code 还需要 `~/.claude/skills/`。可以用一份文件、多处符号链接：

```bash
# macOS / Linux
git clone https://github.com/lhjiang89/music-creator-skill.git ~/.agents/skills/music-creator

mkdir -p ~/.claude/skills ~/.cursor/skills ~/.dsh/skills
ln -sfn ~/.agents/skills/music-creator ~/.claude/skills/music-creator
ln -sfn ~/.agents/skills/music-creator ~/.cursor/skills/music-creator
ln -sfn ~/.agents/skills/music-creator ~/.dsh/skills/music-creator
```

Windows PowerShell：

```powershell
git clone https://github.com/lhjiang89/music-creator-skill.git "$env:USERPROFILE\.agents\skills\music-creator"

New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills","$env:USERPROFILE\.cursor\skills","$env:USERPROFILE\.dsh\skills" | Out-Null
New-Item -ItemType SymbolicLink -Force -Path "$env:USERPROFILE\.claude\skills\music-creator" -Target "$env:USERPROFILE\.agents\skills\music-creator"
New-Item -ItemType SymbolicLink -Force -Path "$env:USERPROFILE\.cursor\skills\music-creator" -Target "$env:USERPROFILE\.agents\skills\music-creator"
New-Item -ItemType SymbolicLink -Force -Path "$env:USERPROFILE\.dsh\skills\music-creator" -Target "$env:USERPROFILE\.agents\skills\music-creator"
```

只使用其中某一个代理时，按下面的目录单独安装即可。

### Cursor

- 个人：`~/.cursor/skills/music-creator/SKILL.md` 或 `~/.agents/skills/music-creator/SKILL.md`
- 项目：仓库内 `.cursor/skills/music-creator/SKILL.md` 或 `.agents/skills/music-creator/SKILL.md`

```bash
git clone https://github.com/lhjiang89/music-creator-skill.git ~/.cursor/skills/music-creator
```

在 Agent 对话里用 `/music-creator` 手动调用，或在写歌、生成音乐时让它自动匹配。新开一个聊天或重载窗口后生效。若要给 Cursor Cloud Agents 使用，需开启「为 Cloud Agents 同步技能」，并且技能放在 `~/.cursor/skills/`。

### Claude Code

- 个人：`~/.claude/skills/music-creator/SKILL.md`
- 项目：仓库内 `.claude/skills/music-creator/SKILL.md`

```bash
git clone https://github.com/lhjiang89/music-creator-skill.git ~/.claude/skills/music-creator
```

一般会自动发现。若刚新建技能目录但列表里没有，可执行 `/reload-skills`。也可以用 `/music-creator` 调用。

### Codex

- 个人：`~/.agents/skills/music-creator/SKILL.md`（旧路径 `~/.codex/skills/` 仍然可用）
- 项目：从当前工作目录到仓库根目录之间的 `.agents/skills/music-creator/SKILL.md`

```bash
git clone https://github.com/lhjiang89/music-creator-skill.git ~/.agents/skills/music-creator
```

在 CLI 或 IDE 里用 `$` 选择技能，或运行 `/skills` 查看是否已加载。必要时重启 Codex。

### DeepSeek Harness

DeepSeek Harness 会扫描这些目录（项目目录优先于用户目录）：

- 项目：`.dsh/skills/music-creator/SKILL.md` 或 `.agents/skills/music-creator/SKILL.md`
- 个人：`~/.dsh/skills/music-creator/SKILL.md` 或 `~/.agents/skills/music-creator/SKILL.md`

```bash
git clone https://github.com/lhjiang89/music-creator-skill.git ~/.dsh/skills/music-creator
```

目录必须是单层 `music-creator/SKILL.md`，不要再嵌套。可用「列出当前可用 skills」确认是否被发现。

### 装到某个代码仓库里（项目级，方便和同事共用）

在目标仓库根目录：

```bash
mkdir -p .agents/skills
git clone https://github.com/lhjiang89/music-creator-skill.git .agents/skills/music-creator
```

Claude Code 若只读 `.claude/skills/`，再加一个链接：

```bash
mkdir -p .claude/skills
ln -sfn ../../.agents/skills/music-creator .claude/skills/music-creator
```

DeepSeek Harness 也可以放到 `.dsh/skills/music-creator`。

## 使用方法

安装后直接说你的创作需求，例如：

- 「写一首错过或怀念的华语情歌，男声，偏光良那种说话式唱法，然后生成 Suno 提示词。」
- 「这首词定了，请按呼吸感优化断句，不要改措辞，再写一版 Styles。」
- 「现在这版唱得太平，不要再用 Cover，按新的断句和 Styles 全新生成两首。」
- 「『没收到』里的『没』要读 méi，不是 mò。」

代理应先确认歌词，再写 Styles，最后给出可粘贴进 Suno 的 Lyrics / Styles / 标题 / 人声选项。详细规则见 [SKILL.md](./SKILL.md)。

## 更新

如果是 git clone 安装的：

```bash
cd ~/.agents/skills/music-creator   # 或你实际安装的目录
git pull
```

符号链接会一起更新。若是复制过去的文件夹，重新复制即可。

## 设计来源

本技能来自完整写歌和 Suno 生成实践，只保留稳定有效的约束，例如：

- 先锁词，再写 Styles，再生成
- 表演说明放进英文段落标签，不放进歌词正文
- 换行即换气，长句可拆句或跨小节
- Styles 不超过 1000 字符，并写明「不要平铺直叙」
- 唱法偏平时应全新生成，而不是 Cover 旧版
- 官方 MP3 的歌词标签和封面可能导致部分播放器中途停止

没有收录反复试错、已被淘汰的提示词。
