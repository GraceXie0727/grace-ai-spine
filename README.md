# grace-ai-spine：给 AI 装上脊梁骨

两个 Claude Code 的 Agent Skill，一个负责做（grace-builder），一个负责撕（grace-critic）。配对使用，让 AI 对你的文章、方案、想法做真正有脊梁骨的审查——不是"总体不错，建议优化一下"那种废话，而是逐条引用原文举证、打分、回答"该不该叫停"。

解决的问题：**AI 默认只会鼓掌。** 你让它审稿，它说"很好，可以考虑加一些……"；你让它提意见，它先夸三段再轻轻带一句。这两个 skill 就是把 AI 的讨好模式关掉。

---

## 两个角色怎么配合

| 角色 | 干什么 | 触发词 |
|---|---|---|
| **grace-critic（批评者）** | 找最弱一环、逐条引用原文批评、按维度打 1-10 分、回答该不该叫停 | /grace-critic、「批评一下」「帮我打个分」「找茬」 |
| **grace-builder（建设者）** | 产出作品、交稿前自报弱点；收到批评后逐条"修或驳"——接受就动手改，不服就拿证据反驳 | /grace-builder、「对打」「逐条回应这些批评」 |

**单独用**：只装 grace-critic 当毒舌审稿人，完全够用。

**对打用**：两个一起装，让它们自动攻防两轮——grace-critic 出批评报告 → grace-builder 逐条修或驳 → grace-critic 复审（被说服的收回，敷衍的继续追打）→ 谈不拢的交你拍板。

## 为什么不是一个 skill

拆成两个是刻意的：grace-critic 只管攻击不管修，grace-builder 只管修和驳不管打分。混在一起，AI 会"左手打右手"——自己批评完又自己和稀泥。分开后各自有各自的脊梁。

---

## 安装

前提：你在用 [Claude Code](https://claude.com/claude-code) 或其他支持 Agent Skills 的工具。

### 方式一：一行命令安装（推荐）

```
npx skills add https://github.com/GraceXie0727/grace-ai-spine --skill grace-critic
npx skills add https://github.com/GraceXie0727/grace-ai-spine --skill grace-builder
```

只想要毒舌审稿功能，装第一行就够。

### 方式二：把下面这段话直接发给 AI

> 帮我安装 `grace-critic` 和 `grace-builder` 这两个 Claude Code skill。请：
> 1. 确保 `~/.claude/skills/` 目录存在（不存在就创建）
> 2. 执行 `git clone https://github.com/GraceXie0727/grace-ai-spine.git` 到临时目录
> 3. 把临时目录里的 `grace-critic/` 和 `grace-builder/` 两个文件夹复制到 `~/.claude/skills/`
> 4. 验证：`ls ~/.claude/skills/grace-critic/` 和 `ls ~/.claude/skills/grace-builder/` 应该都能看到 `SKILL.md`
> 5. 告诉我安装好了，之后我说"批评一下"或"对打"就会触发这两个 skill

把这段话复制粘贴给 Claude Code / Cursor / 任何有 shell 权限的 AI Agent，它会自动完成安装。

### 方式三：手动命令行

```
git clone https://github.com/GraceXie0727/grace-ai-spine.git /tmp/grace-ai-spine && cp -r /tmp/grace-ai-spine/grace-critic /tmp/grace-ai-spine/grace-builder ~/.claude/skills/ && rm -rf /tmp/grace-ai-spine
```

或者最原始的手动方式：点本页右上角绿色的「Code」按钮 →「Download ZIP」，解压后把 `grace-critic` 和 `grace-builder` 两个文件夹（各自里面有 `SKILL.md`）复制到 `~/.claude/skills/`（Windows 是 `C:\Users\你的用户名\.claude\skills\`），重启 Claude Code。

装完后目录长这样：

```
~/.claude/skills/
├── grace-critic/
│   └── SKILL.md
└── grace-builder/
    └── SKILL.md
```

## 开始使用

装好后，把你想审查的东西（文章、方案、想法、代码设计）发给 Claude Code，然后说：

- 「批评一下」或 `/grace-critic` — grace-critic 出批评报告
- 「对打」— grace-critic 和 grace-builder 自动攻防两轮，最后交你拍板

### grace-critic 会输出什么

```
批评报告

叫停条件：如果核心论点已被公开事实证伪（来源：我自拟）
最弱的一环：「……」（引用原文）
被回避的问题：……

━━━ 致命伤 ━━━
#1 原文：「……」
   问题：……
   为什么重要：……
   方向：……

━━━ 打分 ━━━
| 维度 | 分数 | 理由 |
|------|------|------|

━━━ 叫停裁决 ━━━
继续 / 建议放弃：……
```

### grace-builder 的攻防回应

```
建设者 · 攻防回应

对致命伤 #1：【修】
- 批评说：……
- 裁决理由：……
- 动作：改动前：「……」→ 改动后：「……」

━━━ 修订汇总 ━━━
共 5 条批评：修 3 条，驳 2 条
```

## 设计原则

- **没有证据不许开口。** 每条批评必须引用原文 + 说清为什么重要。"感觉不够好"这种话禁止。
- **不为凶而凶。** 作品真的好就打高分，但照样找最弱一环。假装凶和假装夸一样是失职。
- **禁止第三条路。** grace-builder 收到批评，要么接受并动手改（给前后对照），要么拿证据驳回。「有道理我会注意的」——不存在这个选项。
- **两轮封顶。** 谈不拢的进"遗留分歧"交你拍板，不无限循环。

## 致谢

- 灵感来自 [Greg Isenberg (@gregisenberg)](https://x.com/gregisenberg) 关于 Builder vs Critic 对打模式的推文

## 授权

本项目采用 [CC BY-NC 4.0](LICENSE) 授权。人话版：

- 可以：免费使用、修改、分享给任何人
- 必须：保留署名和来源说明
- 不可以：商业用途（比如放进付费产品或课程出售）。有商业需求请联系作者取得授权

---

作者：Grace（[X @Gracexie0727](https://x.com/Gracexie0727)），一个不会编程的文科生创业者。有问题欢迎提 [Issue](../../issues)~
