# GitHub 今日 AI Trending 测开分析（2026-08-25）

## AI 架构与趋势

### 今日结构分布（粗分类）
- AI Agent / 编排框架: 6 个

### 热门项目速览

#### 1. openai/codex
- 链接：https://github.com/openai/codex
- 归类：AI Agent / 编排框架
- Stars：117114
- 主要语言：Rust
- 项目特色（基于 description/README 片段的轻量提炼）：
  - Lightweight coding agent that runs in your terminal
  - Apple Silicon/arm64: `codex-aarch64-apple-darwin.tar.gz`
  - x86_64 (older Mac hardware): `codex-x86_64-apple-darwin.tar.gz`
  - x86_64: `codex-x86_64-unknown-linux-musl.tar.gz`
  - arm64: `codex-aarch64-unknown-linux-musl.tar.gz`

#### 2. MadsLorentzen/ai-job-search
- 链接：https://github.com/MadsLorentzen/ai-job-search
- 归类：AI Agent / 编排框架
- Stars：34116
- 主要语言：Python
- Topics：ai, ai-agents, career, claude-code, cover-letter, cv, interview-preparation, job-application, job-hunting, job-search, latex, resume
- 项目特色（基于 description/README 片段的轻量提炼）：
  - The job search that runs on your machine. AI job application framework built on Claude Code: evaluate postings, tailor CVs, write cover letters, prep interviews. Fork it and own it.
  - Claude Code（https://claude.com/claude-code） (CLI). Using a different agent tool (Codex, Antigravity, Gemini CLI)? Start at `AGENTS.md` - the portal search skills work there out of the box, and community forks（https://github.com/MadsLorentzen/ai-job-search/discussions/78） adapt the full workflow.
  - Python 3.10+
  - Bun（https://bun.sh） (for job search CLI tools)
  - LaTeX distribution with `lualatex` and `xelatex`: TeX Live（https://tug.org/texlive/）, MacTeX（https://tug.org/mactex/）, TinyTeX（https://yihui.org/tinytex/）, or MiKTeX（https://miktex.org/）. The CV compiles with `lualatex` (pdflatex often fails on modern MiKTeX installs with `fontawesome5` font-expansion errors); the cover letter compiles with `xelatex` because `cover.cls` requires `fontspec`. If using a minimal TeX install such as TinyTeX or BasicTeX, install the extra packages listed in SETUP.md.
  - Optional: `pdftotext` from poppler（https://poppler.freedesktop.org/） (macOS: `brew install poppler`, Debian/Ubuntu: `apt install poppler-utils`, Windows: `choco install poppler`) — used by `/apply`'s ATS parseability check on the compiled CV. If missing, the check degrades gracefully to a visual keyword review.

#### 3. multica-ai/andrej-karpathy-skills
- 链接：https://github.com/multica-ai/andrej-karpathy-skills
- 归类：AI Agent / 编排框架
- Stars：206553
- 项目特色（基于 description/README 片段的轻量提炼）：
  - A single CLAUDE.md file to improve Claude Code behavior, derived from Andrej Karpathy's observations on LLM coding pitfalls.
  - **State assumptions explicitly** — If uncertain, ask rather than guess
  - **Present multiple interpretations** — Don't pick silently when ambiguity exists
  - **Push back when warranted** — If a simpler approach exists, say so
  - **Stop when confused** — Name what's unclear and ask for clarification
  - No features beyond what was asked

#### 4. NousResearch/hermes-agent
- 链接：https://github.com/NousResearch/hermes-agent
- 归类：AI Agent / 编排框架
- Stars：235837
- 主要语言：Python
- Topics：ai, ai-agent, ai-agents, anthropic, chatgpt, claude, claude-code, codex, hermes, hermes-agent, llm, nous-research
- 项目特色（基于 description/README 片段的轻量提炼）：
  - The agent that grows with you

#### 5. apache/maka
- 链接：https://github.com/apache/maka
- 归类：AI Agent / 编排框架
- Stars：2928
- 主要语言：TypeScript
- Topics：agent-runtime, ai, ai-agent, apache, cli, desktop, electron, event-sourcing, incubator, llm, local-first, maka
- 项目特色（基于 description/README 片段的轻量提炼）：
  - Apache Maka (Incubating) is a local-first AI agent workspace. Model messages, tool calls, tool results, permission decisions, and termination events are recorded as an append-only log.
  - **Your machine, your data.** Sessions, settings, and run records stay local by default. You bring the model: a cloud API, a local model, or a compatible gateway.
  - **The record is kept.** Model messages, tool calls, tool results, and how a turn ended are written down. The UI and the next model call are views of that record, not the only copy.
  - **Shorter context is not deleted history.** Maka can omit old tool output from the next prompt without throwing away the saved evidence.
  - **One place runs the agent.** Desktop, the terminal, and Maka evaluation all go through Runtime Host. Eval only owns the experiment and its scores.
  - Multiple model connections, streaming output, thinking, usage, and clearer provider errors;

#### 6. PostHog/posthog
- 链接：https://github.com/PostHog/posthog
- 归类：AI Agent / 编排框架
- Stars：39008
- 主要语言：Python
- Topics：ab-testing, ai-analytics, analytics, cdp, data-warehouse, experiments, feature-flags, javascript, product-analytics, python, react, session-replay
- 项目特色（基于 description/README 片段的轻量提炼）：
  - :hedgehog: PostHog is the leading platform for building self-driving products. Our developer tools – AI observability, analytics, session replay, flags, experiments, error tracking, logs, and more – capture all the context agents need to diagnose problems, uncover opportunities, and ship fixes. Steer it all from Slack, web, desktop, or the MCP.
  - Self-driving mode（https://posthog.com/docs/self-driving）: Turn signals in your product data (errors, rage clicks, failed queries, and more) into researched reports and pull requests you review and merge.
  - Product analytics（https://posthog.com/product-analytics）: Autocapture or manually instrument event-based analytics to understand user behavior and analyze data with visualization or SQL.
  - Web analytics（https://posthog.com/web-analytics）: Monitor web traffic and user sessions with a GA-like dashboard. Easily monitor conversion, web vitals, and revenue.
  - Session replays（https://posthog.com/session-replay）: Watch real user sessions of interactions with your website or mobile app to diagnose issues and understand user behavior.
  - Feature flags（https://posthog.com/feature-flags）: Safely roll out features to select users or cohorts with feature flags.

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
