# AI Article Translator

一个 [Claude Code Skill](https://docs.anthropic.com/en/docs/claude-code/skills)，通过六步翻译工作流实现高质量的 AI 文章翻译，在保证语义准确的同时去除 AI 翻译痕迹，输出自然流畅的译文。

## 工作原理

本技能采用**两轮「翻译 → 审校 → 修正」**机制，将翻译准确性和语言自然度分为两个独立阶段处理：

```
第一轮：翻译准确度
  Step 1  初译 ──→ Step 2  准确性审校 ──→ Step 3  准确性修正（如需）
                                    ↓ 无问题则跳过 Step 3

第二轮：语言自然度
  Step 4  去AI化润色 ──→ Step 5  语义保真审校 ──→ Step 6  最终修正（如需）
                                         ↓ 无问题则跳过 Step 6
```

### 六步详解

| 步骤 | 名称 | 说明 |
|------|------|------|
| 1 | 初译 | 以「用目标语言重写」的理念翻译全文，而非逐字直译 |
| 2 | 准确性审校 | 逐句对比检查遗漏、误译、术语一致性、数据准确性 |
| 3 | 准确性修正 | 仅修正 Step 2 发现的具体问题（无问题则跳过） |
| 4 | 去AI化润色 | 基于完整的 24 项 AI 写作特征检测指南，消除 AI 翻译痕迹，输出五维度质量评分（50 分制） |
| 5 | 语义保真审校 | 检查润色是否引入语义偏移、内容丢失或语气偏差 |
| 6 | 最终修正 | 修正 Step 5 的问题，同时保持润色后的自然风格（无问题则跳过） |

## 去AI化润色

Step 4 是本技能的核心差异化能力。它基于完整的 Humanizer-zh 指南，覆盖 **4 大类 24 项 AI 写作特征**：

- **内容层面**：过度强调重要性、浅层分析、宣传语气、模糊归因等
- **语言语法**：AI 高频词汇（此外、至关重要、深入探讨…）、否定并列滥用、三连排比等
- **风格层面**：破折号/粗体滥用、emoji 堆砌、标题首字母大写等
- **沟通填充**：协作痕迹（希望这对您有帮助…）、知识截止声明、过度限定等

润色完成后会输出五维度评分卡：

| 维度 | 分值 | 含义 |
|------|------|------|
| 直接性 | 0-10 | 是否直截了当，避免绕弯 |
| 节奏感 | 0-10 | 句式长短是否有变化 |
| 信任度 | 0-10 | 是否信任读者的理解力 |
| 真实感 | 0-10 | 读起来是否像真人写的 |
| 简洁度 | 0-10 | 是否去除了冗余表达 |

## 安装

**方式一：手动安装**

将本仓库克隆到 Claude Code 的技能目录：

```bash
git clone https://github.com/kant799/ai-article-translator.git ~/.claude/skills/ai-article-translator
```

**方式二：ZIP 下载**

从 GitHub 下载 ZIP，解压后将文件夹放入 `~/.claude/skills/` 目录。

## 使用方法

在 Claude Code 中对话时提及以下关键词即可触发技能：

- `翻译文章`
- `翻译成[语言]`
- `把这篇文章翻译`
- `去AI翻译痕迹`
- `自然翻译`
- `人工翻译质量`

触发后，Claude Code 会自动按照六步工作流执行翻译。

### 自定义提示词

所有六步提示模板位于 `references/prompts.md`，使用 `{{变量}}` 占位符：

- `{{source_text}}` — 原文
- `{{target_language}}` — 目标语言
- `{{source_language}}` — 源语言
- `{{translated_text}}` — 已翻译文本
- `{{review_findings}}` — 审校发现
- `{{optimized_text}}` — 润色后文本

你可以直接编辑 `prompts.md` 来调整各步骤的提示词，无需修改 `SKILL.md` 的工作流逻辑。

## 项目结构

```
ai-article-translator/
  SKILL.md                     # 技能定义，包含完整工作流指令
  LICENSE                      # MIT 许可证
  references/
    prompts.md                 # 六步提示模板（可自定义）
    humanizer-zh.md            # 完整的去AI化指南（24项特征检测）
    de-ai-checklist.md         # 翻译专用去AI化检查清单
```

## 致谢

本项目翻译流程中的去AI化能力直接借鉴并整合了 [op7418/Humanizer-zh](https://github.com/op7418/Humanizer-zh) 项目的内容。

Humanizer-zh 是一个出色的 Claude Code Skill，专注于识别和消除 AI 生成的中文写作痕迹，帮助文本回归自然、真实的人类表达方式。它整理了系统化的 AI 写作特征清单，并提供了一套完整的去AI化指导原则和评分体系。

本项目的 `references/humanizer-zh.md` 和 `references/de-ai-checklist.md` 均源自该项目的成果。正是 Humanizer-zh 对「什么样的文字读起来像 AI」的深入洞察，才使得在翻译场景中有效去除机器痕迹成为可能。

> Humanizer-zh 本身也汇集了多个优秀项目的心血，包括 [blader/humanizer](https://github.com/blader/humanizer)（英文原版）和 [hardikpandya/stop-slop](https://github.com/hardikpandya/stop-slop)（实用规则与评分），以及维基百科 AI Cleanup 项目维护的 AI 写作特征指南。

在此向以上所有项目的作者致敬。

## 许可证

[MIT](LICENSE)