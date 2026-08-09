# GitHub 今日 AI Trending 测开分析（2026-08-09）

## AI 架构与趋势

### 今日结构分布（粗分类）
- AI Agent / 编排框架: 5 个

### 热门项目速览

#### 1. PrimeIntellect-ai/prime-agent
- 链接：https://github.com/PrimeIntellect-ai/prime-agent
- 归类：AI Agent / 编排框架
- Stars：9048
- 主要语言：TypeScript
- 项目特色（基于 description/README 片段的轻量提炼）：
  - A self-improving RLM agent for coding workflows and long-running autonomous tasks.
  - The **Recursive Language Model (RLM)（https://www.primeintellect.ai/blog/rlm）** treats context as variables (*prompt-as-a-variable*) and tools like recursive subagents as function calls (*programmatic tool /sub-agent calling*) inside a persistent REPL.
  - The **Continual Harness（https://arxiv.org/abs/2605.09998）** stores supplemental prompts, memories, skill descriptions, and reusable subagent specifications as durable state that Prime Agent can refine through small, evidence-backed updates, local to the session by default.
  - **Everything is programmatic:** persistent IPython is the built-in model tool; file operations, shell commands, tool use, subagents, and context management happen through code.
  - **Subagents are built in:** `rlm(...)` spawns real child agents for parallel or background work and returns their results programmatically.
  - **The harness can improve:** `/refine` reviews the current trajectory and can apply small, evidence-backed updates to supplemental harness state. It never rewrites the immutable base system prompt, and recorded snapshots support rollback.

#### 2. addyosmani/agent-skills
- 链接：https://github.com/addyosmani/agent-skills
- 归类：AI Agent / 编排框架
- Stars：84606
- 主要语言：JavaScript
- Topics：agent-skills, antigravity, claude-code, codex, cursor, skills
- 项目特色（基于 description/README 片段的轻量提炼）：
  - Production-grade engineering skills for AI coding agents.

#### 3. google/skills
- 链接：https://github.com/google/skills
- 归类：AI Agent / 编排框架
- Stars：16763
- 主要语言：Python
- Topics：google, googlecloud, skills
- 项目特色（基于 description/README 片段的轻量提炼）：
  - Agent Skills for Google products and technologies
  - **Getting started with Google Cloud**
  - **Authenticating to Google Cloud**
  - **Google Cloud Recipe: Foundation Builder**
  - **Onboarding to Google Cloud**
  - **Multi-product solution skills**

#### 4. mattpocock/skills
- 链接：https://github.com/mattpocock/skills
- 归类：AI Agent / 编排框架
- Stars：210083
- 主要语言：Shell
- 项目特色（基于 description/README 片段的轻量提炼）：
  - Skills for Real Engineers. Straight from my .agents directory.
  - Ask you which issue tracker you want to use (GitHub, Linear, or local files)
  - Ask you what labels you apply to tickets when you triage them (`/triage` uses labels)
  - Ask you where you want to save any docs we create
  - `/grill-me` - for non-code uses
  - `/grill-with-docs` - same as `/grill-me`, but adds more goodies (see below)

#### 5. TauricResearch/TradingAgents
- 链接：https://github.com/TauricResearch/TradingAgents
- 归类：AI Agent / 编排框架
- Stars：96516
- 主要语言：Python
- Topics：agent, finance, llm, multiagent, trading
- 项目特色（基于 description/README 片段的轻量提炼）：
  - TradingAgents: Multi-Agents LLM Financial Trading Framework
  - [2026-07] **TradingAgents v0.3.1** released with correctness and stability fixes: Alpha Vantage look-ahead filtering, graph-router crash-safety, graph-shape-aware checkpoint resume, working crypto sentiment sources, a configurable LLM retry budget, Bedrock API-key auth, and Claude Sonnet 5 / Fable 5 support. See CHANGELOG.md for the full list.
  - [2026-06] **TradingAgents v0.3.0** released with a verified data-access contract, an expanded provider registry (NVIDIA, Kimi, Groq, Mistral, Bedrock, and any OpenAI-compatible endpoint), FRED and Polymarket data vendors, a current-generation model catalog, and a CI gate.
  - [2026-05] **TradingAgents v0.2.5** released with the grounded Sentiment Analyst, GPT-5.5 etc. model coverage, Qwen/GLM/MiniMax dual-region support, `TRADINGAGENTS_*` env-var configurability with API-key auto-detection, remote Ollama support, non-US alpha benchmarks, and ticker path-traversal hardening.
  - [2026-04] **TradingAgents v0.2.4** released with structured-output agents (Research Manager, Trader, Portfolio Manager), LangGraph checkpoint resume, persistent decision log, DeepSeek/Qwen/GLM/Azure provider support, Docker, and a Windows UTF-8 encoding fix.
  - [2026-03] **TradingAgents v0.2.3** released with multi-language support, GPT-5.4 family models, unified model catalog, backtesting date fidelity, and proxy support.

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
