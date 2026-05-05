---
name: vibe-coding-guidance
description: 引导用户根据项目类型、技术背景、目标质量，选择正确的 Vibe Coding 等级，并给出对应的工作方式建议和注意事项。
---

# Vibe Coding Level Guide

> 结合 Karpathy 原推定义 + MenuGen 博文实战 + [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) 四条准则 + 9 哥工程实践

## 使用方法

加载这个 skill 后，先问用户**三个问题**：
1. 你要做什么类型的项目？（原型/内部工具/商用SaaS/基础设施）
2. 你的技术背景如何？（非开发者/有基础/中级/资深）
3. 你愿意投入多少精力做验证？（跑一下就行/手动测试/自动化测试）

根据答案路由到对应 level。

---

## 路由逻辑

### 问用户这三个问题，按以下判定树走：

**Q1: 项目类型？**
- 一次性原型 / 个人 demo → 继续 Q2
- 内部工具 / 个人长期产品 → 跳到 L2-L3（看 Q2 技术背景）
- 商用 SaaS / 有用户数据 → 跳到 L3-L4
- 基础设施 / 底层库 / 安全关键 → 建议不要 vibe

**Q2: 技术背景？**
- 非开发者 / 刚入门 → L1-L2（需提醒部署坑）
- 有基础能看懂代码 → L2-L3
- 资深工程师 → L3-L4（你自己能控场）

**Q3: 验证意愿？**
- 跑一下没报错就行 → L1-L2
- 手动测试 → L2-L3
- 自动化测试 + CI → L3-L4

---

## Level 详情

### L1: 纯 Vibe

**适合场景：** 单文件 demo、一次性原型、纯前端展示、个人测试

**怎么干：**
- 一句话描述需求，让 AI 直接出
- 不 review 代码，只看效果
- 不满意就改 prompt 重新来
- 不 git，不测试，不部署

**Karpathy 准则应用：** 基本不适用。追求速度 > 质量。
- Think Before Coding → 跳过
- Simplicity First → 轻度，如果跑不起来再简化
- Surgical Changes → 不适用（基本都是新建）
- Goal-Driven Execution → "能看到就行"

**局限：**
- 代码不可维护
- 一上线就炸（参考 Karpathy MenuGen 的 local → deploy 落差）
- 不要用于任何有用户或有数据的项目

**Karpathy 原话：** *"fully give in to the vibes, embrace exponentials, forget that the code even exists"*

---

### L2: 引导式 Vibe

**适合场景：** 个人小工具、内部脚本、单页面应用、轻度三方 API 调用

**怎么干：**
- 每个功能：说需求 → AI 写 → 跑一下看 → 不满意改 prompt
- 能识别明显错误但不深究根因
- 会做简单的 git 提交
- 三方服务集成由人来注册和配置，AI 只写调用代码

**Karpathy 准则应用：**
- **Think Before Coding** — 让 AI 先列假设，对关键决策要它说清楚
- **Simplicity First** — 重点看：是不是写了不需要的功能？抽象过度？
- **Surgical Changes** — 如果改已有代码，告诉 AI "只改必要的，别碰别的"
- **Goal-Driven Execution** — 给步骤："先做 A，验证 B 能跑，再做 C"

**特别注意（Karpathy MenuGen 踩过坑）：**
- API key 记得放环境变量，别硬编码
- 本地跑通不等于能部署（Vercel 构建 ≠ 本地 dev）
- 三方 API 有 rate limit，第一次跑不通可能是限流不是代码有 bug
- AI 可能用已弃用的 SDK，报错了先查官方文档

**代表案例：** Karpathy 的 llm-council（99% vibe coded 的周六 hack）

---

### L3: 混合式工程

**适合场景：** 多模块应用、涉及 API/DB/三方服务、给团队用、需要长期维护

**怎么干：**
- **人控架构 + AI 填实现**
- 先写技术方案：架构图、接口定义、数据模型、路由设计
- 拆颗粒任务：一次只给一个 task，完成验证再给下一个
- 关键代码自己做 review（特别是安全相关逻辑）
- 每个任务有明确输入/输出定义 + success criteria

**Karpathy 准则应用（全量）：**
- **Think Before Coding** — 强制 AI 实施前列假设 + 提出多种方案，推回不合理的需求
- **Simplicity First** — 严格审查：有没有不必要的抽象？有没有"以备将来"的代码？
- **Surgical Changes** — 审查 diff 时确认每一行都能 trace back 到需求。**发现 AI 多改了无关代码立即纠正**
- **Goal-Driven Execution** — 每个任务转成可验证目标（"写测试 → 通过 → 重构 → 测试仍然通过"）

**9 哥的实践：**
> *"要给他写 skill，技术方案，技术架构，接口方案，需求文档，功能实现，甚至具体到字段命名规则，方法定义规则都要详细的给他准备好，然后要一个一个小需求拆解后再让他写让他改。"*

**特别注意：**
- 三方服务集成是最大坑——Karpathy 的惨痛教训：OpenAI → Replicate → Clerk → Stripe → Vercel KV
- 每个服务都是独立的账户、key、配置、文档
- 建议在实施前先把所有服务列出来，人先注册好，再让 AI 写集成代码

---

### L4: 系统化编排

**适合场景：** 生产级 SaaS、多人协作、有用户数据、需要 CI/CD

**怎么干：**
- 多个 AI agent 分工（前端 agent、后端 agent、测试 agent）
- 任务拆成 TDD 颗粒，自动化测试覆盖核心路径
- CI 验证 + 类型检查 + lint
- 灰度发布 + 数据库迁移可回滚
- 架构层有抽象，方便切换厂商（不绑定某个 API 或服务）

**Karpathy 准则应用（严格）：**
- 四条准则全量严格执行
- 对每个 PR 做 Karpathy 准则审查
- **Goal-Driven Execution 是核心** — 所有任务必须有可验证的 success criteria，AI 自己 loop 到通过

**Karpathy 的展望：**
> *"Some app development platform could come with all the batteries included... Something opinionated, concrete, preconfigured with all the basics: domain, hosting, authentication, payments, database, server functions."*

这就是 L4 要搭建的基建。

---

## Karpathy 通用陷阱清单（所有 level 通用）

AI 的共同毛病，跟 level 无关：

- [ ] **偷偷改无关代码** — 改 A 顺带"优化"了 B C D。永远审 diff
- [ ] **不会说不知道** — AI 不懂的 API 它会假装懂。关键路径要你亲自确认
- [ ] **过度抽象** — 明明只用一次的函数，它给你写个基类+接口+工厂。看见了就叫停
- [ ] **不验证就继续** — 没等上一个 API 调用成功就写下一个。要它先跑通再往下
- [ ] **安全隐患** — 硬编码 key、日志里打印密码、用邮箱匹配用户。安全相关的逻辑必须人 review

---

## Tradeoff 说明

这四个准则偏向**谨慎 > 速度**。
- L1-L2：可以放水，追求速度
- L3-L4：严格执行，追求可维护性

**判断准则是有效的，当：**
- diff 中不必要的变更减少
- 过度设计导致的返工减少
- 澄清性问题出现在实施前，而不是出 bug 后
