# GitHub 今日 AI Trending 测开分析（2026-08-15）

## AI 架构与趋势

### 今日结构分布（粗分类）
- AI Agent / 编排框架: 6 个

### 热门项目速览

#### 1. cathrynlavery/diagram-design
- 链接：https://github.com/cathrynlavery/diagram-design
- 归类：AI Agent / 编排框架
- Stars：17298
- 主要语言：HTML
- 项目特色（基于 description/README 片段的轻量提炼）：
  - 29 editorial diagram types for Claude Code. Self-contained HTML + SVG. No shadows, no Mermaid-slop.

#### 2. cactus-compute/needle
- 链接：https://github.com/cactus-compute/needle
- 归类：AI Agent / 编排框架
- Stars：5623
- 主要语言：Python
- Topics：cactus, gemini, gemma, llm, on-device-ai
- 项目特色（基于 description/README 片段的轻量提炼）：
  - 14MB foundation model for tiny devices; phones, wearables, smart home, and robots.
  - **Self-contained**: weights baked into a single 14MB engine; no separate model files to manage, and inference does no network.
  - **Simple contract**: tool calls come back as structured data, text in, JSON out; a byte-level grammar compiled from your schemas constrains every token.
  - **Confidence-gated**: every response carries a calibrated confidence score from a learned head; set a threshold, act above it, escalate below it.
  - **Tool retrieval**: declare a large catalogue and a built-in retrieval head renders only the top five tools per turn, with the grammar constrained to that subset.
  - **Bounded memory**: a 256-token sliding window with the tools pinned as KV sinks, so total memory stays near 28MB no matter how long the conversation runs.

#### 3. megadose/holehe
- 链接：https://github.com/megadose/holehe
- 归类：AI Agent / 编排框架
- Stars：12858
- 主要语言：Python
- Topics：ebay, email, emails, information-gathering, instagram, open-source-intelligence, osint, osint-python, osint-tools, pypi, python, social-network
- 项目特色（基于 description/README 片段的轻量提炼）：
  - holehe allows you to check if the mail is used on different sites like twitter, instagram and will retrieve information on sites with the forgotten password function.
  - rateLitmit : Lets you know if you've been rate-limited.
  - exists : If an account exists for the email on that service.
  - emailrecovery : Sometimes partially obfuscated recovery emails are returned.
  - phoneNumber : Sometimes partially obfuscated recovery phone numbers are returned.
  - others : Any extra info.

#### 4. macro-inc/macro
- 链接：https://github.com/macro-inc/macro
- 归类：AI Agent / 编排框架
- Stars：3041
- 主要语言：Rust
- Topics：agent, ai, ai-agents, all-in-one, crm, crm-system, email, linear, mcp, messaging, notes, notion
- 项目特色（基于 description/README 片段的轻量提炼）：
  - Macro is a unified workspace for teams: email, chat, docs, tasks, agents, calls, and CRM — @-linked together with shared AI memory.
  - Multi-account. Triage all your Google accounts in a single inbox, with the same tagging and sharing system. Or triage individually.
  - Unified inbox: emails, messages, @mentions, and tasks to complete, all in the same list. Use `j` `k` and `e` to navigate everything.
  - Better AI, with a tools/MCP surface designed to work across inboxes and to help your agents more accurately retrieve information. For example, we expose a unified search tool that allows agents to search all file attachment PDFs (parsed out of email) directly, rather than pulling email threads then attachments. You can also draft, edit and send emails right from AI chats, without opening your email.
  - Multitasking ability — Macro has a built-in window manager that lets you create 3+ splits (scales with monitor size) so you can draft emails while reviewing prior threads.
  - Company/Contact objects. Macro has native CRM capability so you can `cmd+k` to a contact, like tim@acme.com to see all emails between you and that person, or companies, to see all emails and files between everyone on your team and everyone at that company, e.g. `@acme.com`. All of this right from your email without having to open a heavyweight CRM like HubSpot or Salesforce. Email aggregation by contact or company is also available to your agents so they can better assist with CRM-type queries and actions.

#### 5. smicallef/spiderfoot
- 链接：https://github.com/smicallef/spiderfoot
- 归类：AI Agent / 编排框架
- Stars：20952
- 主要语言：Python
- Topics：attacksurface, cti, cybersecurity, footprinting, hacking, information-gathering, information-security, infosec, intelligence-gathering, osint, osint-framework, osint-reconnaissance
- 项目特色（基于 description/README 片段的轻量提炼）：
  - SpiderFoot automates OSINT for threat intelligence and mapping your attack surface.
  - Web based UI or CLI
  - Over 200 modules (see below)
  - Python 3.7+
  - YAML-configurable correlation engine with 37 pre-defined rules
  - CSV/JSON/GEXF export

#### 6. citrolabs/ego-lite
- 链接：https://github.com/citrolabs/ego-lite
- 归类：AI Agent / 编排框架
- Stars：10381
- 主要语言：JavaScript
- Topics：agent-skills, ai-agent, automation, browser, browser-automation, claude-code, codex, hermes-agent, skills, skills-sh
- 项目特色（基于 description/README 片段的轻量提炼）：
  - The fastest browser for AI agents to run browser automation, built for sharing your logged-in browser state with your AI agents, like Codex or Claude Code, without disturbing you. Zero cost, zero config.

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
