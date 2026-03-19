# Skill 类型分类与选择指南

## 9 种类型分类表

| 类型 | 用途 | 触发示例 | 示例 |
|------|------|----------|------|
| **Slash Command** | 执行可重复任务 | 用户输入 `/commit`、`/deploy` | `/commit`、`/review-pr` |
| **Library & API Reference** | 传递 Claude 不熟悉的非显而易见知识 | 涉及内部 SDK、私有 API | 内部监控平台用法、私有 API 文档 |
| **Workflow Automation** | 编排多步骤流程 | "帮我完成整个发布流程" | CI/CD 流程、代码评审流程 |
| **Code Generation** | 生成符合项目规范的样板 | "创建一个新页面" | 新建 MVC 页面、新建组件 |
| **Context Injection** | 给 Claude 提供项目特定上下文 | 处理特定领域问题时 | 业务术语表、架构约束 |
| **Tool Integration** | 封装外部工具调用 | 需要访问外部系统 | Linear 工单操作、部署命令 |
| **Interactive Config** | 收集用户配置后执行 | 首次运行需要参数 | 初始化项目、设置环境 |
| **Quality Gate** | 在提交/发布前检查质量 | 代码评审、格式检查 | 安全扫描、lint 检查 |
| **Learning Aid** | 传递领域知识或最佳实践 | 用户问"如何做 X" | skill-guide 本身 |

---

## 如何选择类型

按以下优先级依次判断，第一个匹配的即为正确类型：

**1. 用户会用 `/xxx` 触发吗？**
→ **Slash Command**（最常见的可重复任务）

**2. Claude 需要了解你的内部系统/私有 API 才能工作吗？**
→ **Library & API Reference**（知识补充，不是流程编排）

**3. 任务是否跨越多个工具/系统，需要编排？**
→ **Workflow Automation**（多步骤、有依赖顺序的流程）

**4. 任务是生成代码样板，且必须符合项目特定规范？**
→ **Code Generation**（强调"符合规范"，通用生成不需要 skill）

**5. 需要在执行前收集用户参数（不只是一次性问答）？**
→ **Interactive Config**（有持久化配置需求）

**6. 其他情况：**
- 传递领域背景/约束 → **Context Injection**
- 封装特定外部工具操作 → **Tool Integration**
- 在提交/发布节点做检查 → **Quality Gate**
- 传递最佳实践或教学内容 → **Learning Aid**

> **注意**：一个 skill 可以兼具多种类型特征。选最主要的一种命名和定位，其余作为辅助功能。
