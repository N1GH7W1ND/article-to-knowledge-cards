# make-knowledge-cards

一个遵循 [Agent Skills 开放规范](https://agentskills.io) 的技能：把一篇文章变成一组可以直接用来复习的知识卡片。

## 它解决什么问题

读过的文章记不住，收藏了等于没读；想让 AI 帮忙做摘要，又经常遇到三个毛病——**卡片之间重复、一张卡塞好几个知识点、为了凑数编造原文没有的内容**。

本技能针对这三个毛病给出约束式的工作流程，产出**忠实于原文、原子化、数量诚实**的知识卡片：

- 一张卡片只讲一个知识点，重复内容合并成一张
- 不编造原文没有的信息，原文的限定词（"据说""研究表明"）照原样保留
- 原文撑不起 5 张卡片时宁可少出，并说明原因，绝不注水

## 主要功能

把用户**粘贴的文章**或**本地 Markdown/TXT 文件**转换成 5~8 张 Markdown 知识卡片，每张卡片包含：

| 字段 | 说明 |
|---|---|
| 标题 | 具体、脱离原文也能看懂 |
| 核心知识 | 一句话陈述这个知识点 |
| 解释 | 2~4 句话讲清楚，忠于原文 |
| 例子 / 自测 | 原文的例子，或一个附答案的自测问题 |

**暂不支持**：网页抓取（给 URL 时会请你粘贴正文）、PDF、Anki 导出、图形界面。输出是纯 Markdown 文本，可自行粘贴到笔记工具。

## 安装方法

技能本体只有一个 Markdown 指令文件（`SKILL.md` + `agents/openai.yaml`），**无依赖、无需构建**，复制目录即完成安装。

```bash
# 方式一：项目级安装（仅当前项目可用，推荐）
mkdir -p .zcode/skills
cp -r skills/make-knowledge-cards .zcode/skills/

# 方式二：用户级安装（你的所有项目可用）
cp -r skills/make-knowledge-cards ~/.zcode/skills/
```

- 兼容 `.zcode/skills/` 与跨工具标准的 `.agents/skills/` 目录，复制到任一位置均可被发现，新会话生效
- `agents/openai.yaml` 已随目录附带，OpenAI Codex 等支持该规范的 Agent 可直接使用
- 可选的校验脚本 `scripts/quick_validate.py` 需要 Python 3 与 PyYAML（仅修改技能时需要）

## 使用方法

安装后对 Agent 说（两种输入方式任选）：

```text
# 输入方式一：指定本地文件
把 tests/testdata/http-cache.md 转成知识卡片

# 输入方式二：直接粘贴文章
帮我把下面这篇文章做成知识卡片：
<粘贴文章正文>
```

当你说"提炼知识点""制作学习卡片""生成自测问题""做复习摘要"时，技能也会被触发。

## 输入输出示例

**输入**（粘贴或 .md/.txt 文件）：

```text
复利是指利息并入本金后继续产生利息，也就是"利滚利"。假设本金 1 万元，年利率 5%，
按复利计算，10 年后约为 1.63 万元，而单利只有 1.5 万元。复利的威力主要来自时间：
年限越长，差距越大。爱因斯坦曾说复利是"世界第八大奇迹"。
```

**输出**（节选，完整见 [skills/make-knowledge-cards/SKILL.md](skills/make-knowledge-cards/SKILL.md) 内置示例）：

```markdown
从这段关于复利的文字中提炼出 3 张知识卡片。

## 卡片 1：复利是利息并入本金继续生息

**核心知识**：复利把每期利息并入本金，下一期按新本金计息，即"利滚利"。

**解释**：单利只对原始本金计息，复利则对"本金 + 已产生利息"的总额计息。这使得同样
利率下，复利的终值增长速度随时间加快。

**例子**：本金 1 万元、年利率 5% 时，复利 10 年约得 1.63 万元，单利只有 1.5 万元。

## 卡片 2：复利与单利的差距随时间拉大
……

说明：原文为一段 4 句话的短文，仅包含 3 个独立知识点，故生成 3 张卡片。
```

**信息不足时的行为**（不强行凑数的示例）：输入一则两句话的会议通知，输出 2 张事实卡片，并在末尾注明"原文只包含 2 个独立事实点，无法支撑 5 张卡片，未强行凑数"。

三类文章的完整实测输出见 [tests/outputs/](tests/outputs/)：技术教程（8 张）、科普方法（7 张）、会议通知（2 张）。

## 项目结构

```text
skills/make-knowledge-cards/   技能本体（SKILL.md + agents/openai.yaml）
scripts/quick_validate.py      官方技能校验脚本（来自 anthropics/skills）
tests/testdata/                测试文章：技术教程 / 科普方法 / 信息不足短文
tests/outputs/                 对应的知识卡片实测输出
```

## 开发与验证

```bash
py scripts/quick_validate.py skills/make-knowledge-cards
# Skill is valid!
```

Windows 下若 `python` 是应用商店占位符，请使用 `py` 启动器。

## 许可证

[MIT](LICENSE)
