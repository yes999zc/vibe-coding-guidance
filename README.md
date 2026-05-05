# Vibe Coding Level Guide

引导用户根据项目类型、技术背景和目标质量，选择正确的 Vibe Coding 等级 (L1-L4)。

## 这是什么

Vibe Coding 是 Andrej Karpathy 在 2025 年 2 月提出的概念：

> *"There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and forget that the code even exists."*

但实际体验（见 Karpathy 的 [MenuGen 博文](https://karpathy.bearblog.dev/vibe-coding-menugen/)）告诉我们：**不是所有项目都适合无脑 vibe**。这个 guide 通过三个问题帮你找到对的 level。

## 核心内容

- **4 个成熟度等级** — L1 纯 Vibe → L4 系统化编排
- **路由逻辑** — 根据项目类型、技术背景、验证意愿推荐对应 level
- **每个 level 的具体做法** — 怎么干、Karpathy 准则应用程度、注意事项
- **Karpathy 通用陷阱清单** — AI 编码的常见毛病，不分 level
- **结合 Karpathy 4 条行为准则** — Think Before Coding / Simplicity First / Surgical Changes / Goal-Driven Execution

## 出处

- [Karpathy 原推（2025.02）](https://x.com/karpathy/status/1886192184808149383)
- [Karpathy MenuGen 博文（2025.04）](https://karpathy.bearblog.dev/vibe-coding-menugen/)
- [Karpathy-Inspired Claude Code Guidelines](https://github.com/forrestchang/andrej-karpathy-skills) — 4 条行为准则的完整实践
- [awesome-vibe-coding](https://github.com/bluegalaxy111/awesome-vibe-coding) — Vibe Coding 工具大全

## 使用方法

### 作为 Hermes Skill（推荐）

```bash
skill_view(name='vibe-coding-guidance')
```

加载后 AI 会自动问你三个问题，然后路由到对应 level。

### 作为独立文档

直接阅读 `SKILL.md`，按判定树自己判断。

## 本地开发

```bash
git clone https://github.com/yes999zc/vibe-coding-guidance.git
cd vibe-coding-guidance
# 编辑 SKILL.md
```

## License

MIT
