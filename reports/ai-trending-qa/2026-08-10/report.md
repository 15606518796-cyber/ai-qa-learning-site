# GitHub 今日 AI Trending 测开分析（2026-08-10）

## AI 架构与趋势

### 今日结构分布（粗分类）
- AI Agent / 编排框架: 6 个

### 热门项目速览

#### 1. PrimeIntellect-ai/prime-agent
- 链接：https://github.com/PrimeIntellect-ai/prime-agent
- 归类：AI Agent / 编排框架
- Stars：11329
- 主要语言：TypeScript
- 项目特色（基于 description/README 片段的轻量提炼）：
  - A self-improving RLM agent for coding workflows and long-running autonomous tasks.
  - The **Recursive Language Model (RLM)（https://www.primeintellect.ai/blog/rlm）** treats context as variables (*prompt-as-a-variable*) and tools like recursive subagents as function calls (*programmatic tool /sub-agent calling*) inside a persistent REPL.
  - The **Continual Harness（https://arxiv.org/abs/2605.09998）** stores supplemental prompts, memories, skill descriptions, and reusable subagent specifications as durable state that Prime Agent can refine through small, evidence-backed updates, local to the session by default.
  - **Everything is programmatic:** persistent IPython is the built-in model tool; file operations, shell commands, tool use, subagents, and context management happen through code.
  - **Subagents are built in:** `rlm(...)` spawns real child agents for parallel or background work and returns their results programmatically.
  - **The harness can improve:** `/refine` reviews the current trajectory and can apply small, evidence-backed updates to supplemental harness state. It never rewrites the immutable base system prompt, and recorded snapshots support rollback.

#### 2. vitali87/code-graph-rag
- 链接：https://github.com/vitali87/code-graph-rag
- 归类：AI Agent / 编排框架
- Stars：3063
- 主要语言：Python
- Topics：ai, ast, claude-code, code-analysis, code-understanding, codebase-search, developer-tools, graph-database, knowledge-graph, llm, mcp, mcp-server
- 项目特色（基于 description/README 片段的轻量提炼）：
  - The ultimate RAG for your monorepo. Query, understand, and edit multi-language codebases with the power of AI and knowledge graphs
  - **Release Automation**: `NEWS.md` and the README's "Latest News" section now refresh automatically on every release, keeping the changelog current without hand edits.
  - **Ruby Support**: Ruby joins the graph through a new pluggable ast-grep tier that adds a language from a single YAML pattern file, emitting `Module`, `Function`, and `Class` nodes plus import edges without a hand-written parser.
  - **Structural Search & Replace**: Find and rewrite code by AST pattern with ast-grep, exposed as agent tools so you can match and transform structure across the whole codebase instead of relying on text or regex.
  - Ask questions about the codebase in natural language and get answers grounded in the real structure.
  - Retrieve the actual source of any function, class, or method by name or by intent.

#### 3. msitarzewski/agency-agents
- 链接：https://github.com/msitarzewski/agency-agents
- 归类：AI Agent / 编排框架
- Stars：140822
- 主要语言：Shell
- 项目特色（基于 description/README 片段的轻量提炼）：
  - A complete AI agency at your fingertips - From frontend wizards to Reddit community ninjas, from whimsy injectors to reality checkers. Each agent is a specialized expert with personality, processes, and proven deliverables.
  - **🎯 Specialized**: Deep expertise in their domain (not generic prompt templates)
  - **🧠 Personality-Driven**: Unique voice, communication style, and approach
  - **📋 Deliverable-Focused**: Real code, processes, and measurable outcomes
  - **✅ Production-Ready**: Battle-tested workflows and success metrics
  - Identity & personality traits

#### 4. pranshuparmar/witr
- 链接：https://github.com/pranshuparmar/witr
- 归类：AI Agent / 编排框架
- Stars：20742
- 主要语言：Go
- Topics：cli, containers, devops, docker, freebsd, go, golang, incident-response, kubernetes, linux, macos, monitoring
- 项目特色（基于 description/README 片段的轻量提炼）：
  - Why is this running? Trace any process, port, container, or file back to what started it - CLI + TUI.
  - Detect your operating system (`linux`, `darwin` or `freebsd`)
  - Detect your CPU architecture (`amd64` or `arm64`)
  - Download the latest released binary and man page
  - Install it to `/usr/local/bin/witr`
  - Install the man page to `/usr/local/share/man/man1/witr.1`

#### 5. addyosmani/agent-skills
- 链接：https://github.com/addyosmani/agent-skills
- 归类：AI Agent / 编排框架
- Stars：85194
- 主要语言：JavaScript
- Topics：agent-skills, antigravity, claude-code, codex, cursor, skills
- 项目特色（基于 description/README 片段的轻量提炼）：
  - Production-grade engineering skills for AI coding agents.

#### 6. ZhuLinsen/daily_stock_analysis
- 链接：https://github.com/ZhuLinsen/daily_stock_analysis
- 归类：AI Agent / 编排框架
- Stars：61275
- 主要语言：Python
- Topics：a-stock, ai-agent, aigc, llm, quant, quantitative-finance, quantitative-trading
- 项目特色（基于 description/README 片段的轻量提炼）：
  - LLM 驱动的多市场股票智能分析系统：多源行情、实时新闻、决策看板与自动推送，支持零成本定时运行。 LLM-powered multi-market stock analysis system with multi-source market data, real-time news, decision dashboard, automated notifications, and cost-free scheduled runs.

## 对日常 QA 工作的工程化启发（如何测试此类架构）

### 1) 面向 AI Agent 产品质量的通用原则

- 把 LLM 当作不可控依赖：测试要尽可能确定性（Mock/回放/固定评测集），线上靠观测性兜底。
- 优先把输出结构化：JSON Schema / 受控枚举 / error code，让断言从‘主观’变成‘可自动化判定’。
- 关键路径必须可回放：对话、工具调用、检索命中、模型版本，都要可复现。

### 2) 按架构类型给测试策略（可直接套用）

#### AI Agent / 编排框架
- 将“正确性”拆成：接口契约正确 + 业务规则正确 + 模型/提示词行为可控 + 观测性可追溯。
- 默认把 LLM 视为“不确定的外部依赖”，用 Mock/录制回放/固定种子/评测集来把测试变成确定性。
- 把可测性当作架构能力：强制结构化输出（JSON Schema）、明确错误码、全链路 trace_id。
- 重点测：工具调用（tool/function calling）分支覆盖、状态机/工作流回滚、长链路超时与重试策略。
- 用 Golang Ginkgo 做后端校验：对每个工具 API 做 contract test + 幂等性测试 + 权限边界测试。
- 把关键对话流固化成“场景回放测试”：同一输入在固定依赖下输出必须稳定（snapshot / golden）。

### 3) Golang Ginkgo 后端校验：最小可用模板

以下片段用于说明思路（按你们的框架/路由替换即可）：

```go
package api_test

import (
  "net/http"
  "github.com/onsi/ginkgo/v2"
  "github.com/onsi/gomega"
)

var _ = ginkgo.Describe("Tool API Contract", func() {
  ginkgo.It("should return stable JSON schema for success", func() {
    resp, err := http.Get("http://localhost:8080/api/tool/foo?x=1")
    gomega.Expect(err).ToNot(gomega.HaveOccurred())
    gomega.Expect(resp.StatusCode).To(gomega.Equal(http.StatusOK))
    // TODO: 读取 body 做 JSON Schema 校验 / 字段断言
  })
})
```

### 4) Playwright 端到端自动化：关键路径回放模板

```ts
import { test, expect } from '@playwright/test';

test('chat streaming should be stable', async ({ page }) => {
  await page.goto('https://your-console.example.com');
  // TODO: 登录

  await page.getByRole('textbox', { name: '输入' }).fill('解释一下这个项目的核心能力');
  await page.getByRole('button', { name: '发送' }).click();

  // 关键：对流式输出做“最终一致性”断言
  await expect(page.getByTestId('assistant-message').last()).toContainText('核心');
});
```

## 可落地的行动指南（如何在现有自动化框架中应用）

1. 在现有自动化仓库中新建 `ai_agent_quality/` 目录，沉淀：评测集、对话回放用例、golden snapshots。
2. 为后端（Golang）增加 Ginkgo 套件：
  - Contract tests（OpenAPI/JSON Schema）
  - 工具 API 幂等性 + 权限边界
  - 关键业务规则的 table-driven tests
3. 为前端/控制台增加 Playwright 套件：
  - 关键路径回放（含流式输出断言）
  - 断网/慢网/重试场景
  - 可访问性（a11y）与错误提示一致性
4. 把 LLM 依赖抽象为 Provider 接口：测试环境默认 Mock（录制回放），必要时才走真实模型。
5. 建立‘变更影响面’机制：prompt/模型/检索策略/工具列表任一变化，都要触发评测回归 + 差分报告。

---
### 附：生成数据说明
- 数据源：GitHub Trending +（优先）GitHub REST API；API 受限时自动降级为抓取 GitHub Repo HTML 页面
- 说明：AI 过滤与分类为规则驱动，可按团队需求持续迭代；如需更智能的总结，可在此报告基础上再做人工/LLM 精炼。
