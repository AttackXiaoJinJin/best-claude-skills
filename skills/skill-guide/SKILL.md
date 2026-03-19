---
name: skill-guide
description: >
  当用户要求创建、编写、改进或评审 Claude Code skill（SKILL.md）时调用。
  讨论 skill 架构、触发条件设计、分发方式或目录结构时也应调用。
  普通编码任务不触发。
---

# Claude Code Skill 编写指南

帮助你写出真正有价值的 skill：让 Claude 突破默认行为，而不只是重复它已知的知识。

来源：Thariq《Lessons from Building Claude Code: How We Use Skills》核心经验。

---

## 前置条件

- 已有 `.claude/skills/` 目录（或有权限创建）
- 明确 skill 要解决的问题（"Claude 的默认行为在哪里不够用"）
- 了解 skill 的触发场景（用户会说/做什么）

---

## 编写规则

**1. 不要陈述显而易见的内容**
Skill 的价值在于推动 Claude 突破默认行为。只写 Claude 不知道的：项目特定约束、内部规范、真实失败教训。

**2. Gotchas 段落是价值密度最高的部分**
从真实失败中积累。这是 skill 最难被替代的部分，也是最容易被忽略的部分。

**3. Skill 是文件夹，不是单个文件**
用子文件实现渐进式信息披露。SKILL.md 是入口，详细内容按需读取。结构示例：
```
.claude/skills/my-skill/
├── SKILL.md          # 入口：触发条件 + 高层流程
├── api-reference.md  # 详细文档（按需读取）
└── templates/        # 模板文件
```

**4. 避免过度规定步骤**
给目标和关键约束，不要给逐步操作列表。过度规定让 Claude 无法处理边缘情况。

**5. 配置模式：config.json + AskUserQuestion**
首次运行时用 `AskUserQuestion` 收集配置，写入 skill 目录下的 `config.json`。
持久用户数据写入 `${CLAUDE_PLUGIN_DATA}`，不放在 skill 目录内（skill 目录可能随版本更新被覆盖）。

**6. `description` 字段是触发条件，不是摘要**
模型用 `description` 决定是否加载此 skill。写用户会说/做什么，明确写出不触发的情况。

**7. 包含辅助脚本**
将重复的 shell 操作封装进脚本，skill 中直接引用，让 Claude 做组合而不是重建样板。

**8. Hooks：在 skill 中声明，不要硬编码到主流程**
如需在特定时机触发行为（如 `PostToolUse[Bash]` 在每次 Bash 执行后校验输出），在 SKILL.md 中声明 hook 意图和校验逻辑，由用户通过 Claude Code 的配置机制注册，不要在 skill 主流程中硬编码调用。

---

## 验证

Skill 写完后，用对照测试确认它真正改变了 Claude 的行为：

1. **写 2-3 个真实触发 prompt**（用户实际会说的话，覆盖典型场景）
2. **对照运行**：
   - with-skill：保持 skill 启用，发送 prompt
   - baseline：临时移除/注释掉 skill，发送同一 prompt
3. **判断标准**：
   - Skill 有没有被触发？（检查 description 是否够具体）
   - 输出是否朝期望方向改变？
   - 改变是否稳定（多次运行结果一致）？

**泛化原则**：根据测试结果修改 skill 时，针对通用规律而非某个具体 prompt——
skill 需要在成千上万次不同的调用中都有效，不能过拟合到测试用例。

---

## Gotchas

- SKILL.md 建议控制在 2000 token 以内；超过时末尾内容被截断，把最关键的规则放前面
- `description` 中避免"可以"、"也许"等模糊词；确定性表述触发更准确
- 子文件相对路径的基准是 SKILL.md 所在目录，不是项目根目录
- 多个 skill 的 `description` 有重叠时，模型可能同时加载多个；注意 skill 之间不要有冲突指令
- `config.json` 若被 git 追踪，团队成员的本地配置会互相覆盖；要么加入 `.gitignore`，要么写入 `${CLAUDE_PLUGIN_DATA}`

---

## 子文件参考

- **[type-catalog.md](type-catalog.md)** — 9 种 skill 类型分类表 + 选择决策树
- **[templates/skeleton.md](templates/skeleton.md)** — SKILL.md 骨架模板（可直接复制）

---

## 快速检查清单

创建或评审 skill 时逐项确认：

- [ ] `description` 写的是触发条件，不是功能摘要
- [ ] 没有陈述 Claude 已知的通用知识
- [ ] 有 Gotchas 段落（或明确说明暂无已知陷阱）
- [ ] 有前置条件段落
- [ ] 步骤说明了"为什么"，而不只是"做什么"
- [ ] 大型 skill 拆分了子文件，SKILL.md 保持简洁（建议 ≤2000 token）
- [ ] 持久数据写入 `${CLAUDE_PLUGIN_DATA}`，不在 skill 目录内
- [ ] 文件夹结构（不是单文件），子文件有引用链接
- [ ] 已用 2-3 个对照测试验证 skill 真正改变了输出

---

## 维护

- 团队成员使用 skill 时遇到新陷阱，应优先补充到对应 skill 的 Gotchas
- 本指南随 Claude Code skill 机制更新而迭代；关注 Anthropic 官方文档变更
