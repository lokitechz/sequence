# Kiro AI — Architecture Overview Document

> **Version:** 1.0 | **Date:** April 15, 2026  
> **Category:** Agentic IDE — Spec-Driven Development  
> **Developer:** Amazon Web Services (AWS)  
> **Website:** [kiro.dev](https://kiro.dev)

---

## 1. Introduction

Kiro is an **Agentic IDE** (AI Agent-based Integrated Development Environment) developed by AWS, built on top of VS Code (Code OSS fork). Kiro launched in preview in July 2025, reached General Availability (GA) in November 2025, and has been continuously expanded at AWS re:Invent 2025 with features including Autonomous Agent, Powers, and Frontier Agents.

The core differentiator of Kiro compared to traditional AI coding assistants (Cursor, Copilot, Cline) is its **Spec-Driven Development (SDD)** methodology — transforming requirements into executable artifacts, rather than simply generating code from prompts.

---

## 2. High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        KIRO PLATFORM                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────────┐    │
│  │  Kiro IDE     │   │  Kiro CLI    │   │ Kiro Autonomous  │    │
│  │  (Desktop)    │   │  (Terminal)  │   │ Agent (Cloud)    │    │
│  └──────┬───────┘   └──────┬───────┘   └────────┬─────────┘    │
│         │                  │                     │              │
│         └──────────────────┼─────────────────────┘              │
│                            │                                    │
│                   ┌────────▼────────┐                           │
│                   │  Agent Engine   │                           │
│                   │  (Core Runtime) │                           │
│                   └────────┬────────┘                           │
│                            │                                    │
│         ┌──────────────────┼──────────────────┐                 │
│         │                  │                  │                 │
│  ┌──────▼──────┐  ┌───────▼───────┐  ┌───────▼───────┐        │
│  │ Spec Engine │  │ Powers Engine │  │ Hooks Engine  │        │
│  │             │  │               │  │               │        │
│  │ • Require-  │  │ • Dynamic MCP │  │ • Event-driven│        │
│  │   ments     │  │   Loading     │  │   Automation  │        │
│  │ • Design    │  │ • Steering    │  │ • File Events │        │
│  │ • Tasks     │  │ • Context Mgmt│  │ • Lifecycle   │        │
│  └─────────────┘  └───────┬───────┘  └───────────────┘        │
│                           │                                    │
│                   ┌───────▼───────┐                            │
│                   │  MCP Layer    │                            │
│                   │  (Protocol)   │                            │
│                   └───────┬───────┘                            │
│                           │                                    │
│         ┌─────────────────┼─────────────────────┐              │
│         │                 │                     │              │
│  ┌──────▼──────┐  ┌──────▼──────┐  ┌────────────▼──────┐      │
│  │ Local MCP   │  │ Remote MCP  │  │ Partner Powers    │      │
│  │ Servers     │  │ Servers     │  │ (Stripe, Figma,   │      │
│  │             │  │             │  │  Supabase, ...)   │      │
│  └─────────────┘  └─────────────┘  └───────────────────┘      │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│                      LLM BACKBONE                               │
│  ┌───────────────────────────────────────────────────────┐      │
│  │ Claude Sonnet 4.5 (Primary) | Auto Mode (Multi-model) │      │
│  └───────────────────────────────────────────────────────┘      │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3. Core Architecture Components

### 3.1. Kiro IDE (Desktop Client)

Kiro IDE is a desktop application based on VS Code / Code OSS, with cross-platform support (macOS, Windows, Linux).

| Attribute | Details |
|---|---|
| Platform | VS Code fork (Code OSS) |
| Plugin Ecosystem | Open VSX (compatible with VS Code extensions) |
| Authentication | Google, GitHub, Amazon account |
| Interface | Purple theme with familiar VS Code layout |
| Multimodal Input | Text, images (UI mockups, whiteboard photos), file attachments |

The primary interaction channels within the IDE include Vibe Session (free-form prompt-based coding), Spec Session (structured SDD workflow), and Chat Panel with context provider support via `#spec` and `#file` syntax.

### 3.2. Kiro CLI (Command Line Interface)

Kiro CLI allows developers to use agents directly from the terminal, suitable for SSH environments and headless workflows. The CLI supports building features, analyzing errors, tracing bugs, and automating workflows in an interactive loop.

### 3.3. Kiro Autonomous Agent (Cloud-based)

Launched at AWS re:Invent 2025, the Autonomous Agent is a frontier agent capable of operating independently for multiple days. Its architectural characteristics include:

- **Persistent Context**: Maintains context across sessions without resetting between interactions
- **Multi-repo Awareness**: Handles changes across multiple repositories as a unified task
- **Learning from Feedback**: Remembers PR review feedback and automatically applies it to future work
- **Asynchronous Execution**: Executes background tasks while the developer works on other things

---

## 4. Spec-Driven Development Engine

This is the architectural heart of Kiro, clearly distinguishing it from traditional "vibe coding."

### 4.1. Three-Phase Workflow

```
┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐
│   PHASE 1:        │     │   PHASE 2:        │     │   PHASE 3:        │
│   REQUIREMENTS    │────▶│   DESIGN          │────▶│   TASKS           │
│                   │     │                   │     │                   │
│ • User Stories    │     │ • Architecture    │     │ • Task breakdown  │
│ • Acceptance      │     │ • Sequence        │     │ • Dependency      │
│   Criteria (EARS) │     │   Diagrams        │     │   sequencing      │
│ • Assumptions     │     │ • Data Models     │     │ • Test generation │
│                   │     │ • Tech Stack      │     │ • Progress track  │
│ → requirements.md │     │ → design.md       │     │ → tasks.md        │
└───────────────────┘     └───────────────────┘     └───────────────────┘
```

### 4.2. Spec Artifacts (in `.kiro/specs/`)

Each feature or bugfix generates a separate spec directory containing three markdown files.

**requirements.md** uses EARS notation (Easy Approach to Requirements Syntax), structuring requirements in the form "When [trigger], the system shall [behavior]" to support formal reasoning and property-based testing.

**design.md** contains the overall architecture with Mermaid diagrams, component interfaces, data models, TypeScript interfaces, and implementation considerations.

**tasks.md** breaks down the design into discrete tasks, sequenced by dependency, with real-time status tracking (Pending → In Progress → Done).

### 4.3. Two Types of Specs

**Feature Spec** is used for building new capabilities, supporting two workflow variants: Requirements-First (from requirements → design) and Design-First (from architecture → requirements).

**Bugfix Spec** is used for systematic bug diagnosis and fixing, analyzing current vs. expected behavior, designing patches, and confirming no regressions are introduced.

---

## 5. Agent Hooks Engine

Agent Hooks is an event-driven automation system that allows AI agents to automatically execute actions based on events within the IDE.

### 5.1. Hook Architecture

```
┌─────────────────────────────────────────────┐
│              HOOK DEFINITION                │
│         (.kiro/hooks/*.kiro.hook)            │
├─────────────────────────────────────────────┤
│                                             │
│  ┌─────────────┐                            │
│  │   WHEN      │ ── Event Trigger           │
│  │  (Trigger)  │    • File: created/saved/   │
│  │             │      deleted               │
│  │             │    • Prompt: submit/stop    │
│  │             │    • Tool: pre/post use     │
│  │             │    • Spec: pre/post task    │
│  │             │    • Manual trigger         │
│  └──────┬──────┘                            │
│         │                                   │
│  ┌──────▼──────┐                            │
│  │  FILE       │ ── Pattern Matching        │
│  │  PATTERN    │    (e.g. src/**/*.tsx)      │
│  └──────┬──────┘                            │
│         │                                   │
│  ┌──────▼──────┐                            │
│  │   THEN      │ ── Agent Instructions      │
│  │  (Action)   │    • askAgent: prompt       │
│  │             │    • Generate tests         │
│  │             │    • Update docs            │
│  │             │    • Run linting            │
│  └─────────────┘                            │
│                                             │
└─────────────────────────────────────────────┘
```

### 5.2. Example Use Cases

- When saving a React component file → automatically create/update the corresponding unit test
- When committing code → automatically update documentation
- When creating a new component file → verify compliance with the Single Responsibility Principle
- When changing content → automatically generate translations for localization

---

## 6. Powers Engine — Dynamic Context Management

Powers is a mechanism for packaging and distributing specialized capabilities to AI agents, solving the **context window overload** problem when connecting multiple MCP servers simultaneously.

### 6.1. Power Architecture

```
power-<name>/
├── POWER.md           # Main steering file (frontmatter + instructions)
│   ├── Frontmatter    # name, displayName, description, keywords
│   ├── Onboarding     # Setup steps (install CLI, validate deps)
│   └── Workflows      # Steering file map for each task
├── mcp.json           # MCP server configuration
└── steering/          # Detailed steering files per workflow
    ├── workflow-a.md
    └── workflow-b.md
```

### 6.2. Dynamic Activation Mechanism

Rather than loading all tool definitions upfront (which consumes tokens and slows down the agent), Powers operate on an on-demand model. When a developer mentions a relevant keyword in chat (e.g., "database" → activates the Supabase Power), Kiro automatically loads the corresponding POWER.md and MCP tools into context. When switching to a different task, the previous Power automatically deactivates and frees up context.

### 6.3. Partner Ecosystem

Partner powers include UI Development (Figma), Backend (Supabase, Stripe, Neon, Postman), Agent Development (Strands SDK), Deployment (Netlify), Observability (Datadog, Dynatrace), and Database (Amazon Aurora DSQL). The open ecosystem allows the community to contribute and share powers via GitHub.

---

## 7. Steering System — Agent Configuration Layer

Steering files are the configuration layer that helps Kiro understand project context, coding conventions, and technical requirements.

### 7.1. Directory Structure

```
.kiro/
├── steering/              # Project-level steering
│   ├── product.md         # Product context, business domain
│   ├── structure.md       # Codebase structure, conventions
│   └── tech.md            # Technology stack, patterns
├── specs/                 # Feature/bugfix specifications
│   └── <feature-name>/
│       ├── requirements.md
│       ├── design.md
│       └── tasks.md
└── hooks/                 # Agent automation hooks
    └── *.kiro.hook
```

### 7.2. Steering Scope

**Global Steering** applies to all projects (shared coding standards, preferred tools).  
**Project Steering** applies to individual projects, automatically generated from codebase analysis or customized by the developer.  
**Power Steering** is dynamically loaded from Powers when a related task is activated.

---

## 8. MCP Integration Layer

Kiro natively integrates with the Model Context Protocol (MCP), the communication standard between AI agents and external tools/data sources.

### 8.1. MCP Architecture in Kiro

```
┌─────────────────────────────────────────────┐
│              KIRO AGENT                     │
│                                             │
│  ┌─────────────────────────────────┐        │
│  │      MCP Client Manager        │        │
│  │  • Connection pooling           │        │
│  │  • Tool discovery               │        │
│  │  • Dynamic loading/unloading    │        │
│  └──────────┬──────────────────────┘        │
│             │                               │
└─────────────┼───────────────────────────────┘
              │
    ┌─────────┼──────────┐
    │         │          │
┌───▼───┐ ┌──▼────┐ ┌───▼─────┐
│ Local │ │Remote │ │ Power   │
│ MCP   │ │ MCP   │ │ MCP     │
│Server │ │Server │ │(Dynamic)│
│       │ │       │ │         │
│GitHub │ │JIRA   │ │Stripe   │
│FileDB │ │Conflu-│ │Supabase │
│Docker │ │ence   │ │Figma    │
└───────┘ └───────┘ └─────────┘
```

### 8.2. Types of MCP Servers

**Local MCP Servers** run on the developer's machine, connecting via stdio (GitHub MCP, Docker, filesystem).  
**Remote MCP Servers** connect via SSE/HTTP to cloud services (JIRA, Confluence, Slack).  
**Power MCP Servers** are packaged within Powers, dynamically loaded/unloaded based on the conversation context.

---

## 9. LLM Backbone

Kiro uses large language models from Anthropic as its processing foundation.

| Mode | Model | Characteristics |
|---|---|---|
| Default | Claude Sonnet 4.5 | Advanced coding and reasoning, stable |
| Auto | Multi-model mix | Combines Sonnet 4.5 + specialized models, optimized for quality/latency/cost |

Auto mode uses intent detection to select the most appropriate model for each task, combined with caching to reduce token costs.

---

## 10. Security & Trust Model

### 10.1. Agent Control

Kiro applies a **human-in-the-loop** model with multiple levels of control. In Autopilot Mode, the agent has higher automation privileges but still requires approval for sensitive actions such as installing dependencies or running scripts. Developers can review each code change via diff view, approve all changes at once, or step through each change individually.

### 10.2. Trusted Commands

The simplified trusted commands mechanism allows developers to pre-define commands that the agent is permitted to execute without re-confirmation, reducing friction in repetitive workflows.

---

## 11. Data Flow — From Prompt to Production Code

```
Developer Prompt
       │
       ▼
┌──────────────────┐
│ Intent Detection │ ── Determine: Vibe Session or Spec Session?
└────────┬─────────┘
         │
    ┌────┴────┐
    │         │
    ▼         ▼
┌───────┐ ┌──────────────────────────────────────────────────┐
│ Vibe  │ │ Spec-Driven Pipeline                             │
│Session│ │                                                  │
│       │ │  Prompt → requirements.md → design.md → tasks.md │
│Direct │ │       ↕              ↕             ↕             │
│Code   │ │  Human Review    Human Review  Human Review      │
│Gen    │ │       ↓              ↓             ↓             │
└───┬───┘ │  Code Generation ← Guided by Spec Artifacts     │
    │     │       ↓                                          │
    │     │  Hook Triggers (auto-test, auto-doc, lint)       │
    │     │       ↓                                          │
    │     │  Validation against Acceptance Criteria           │
    │     └──────────────────────────────────┬───────────────┘
    │                                        │
    └────────────────┬───────────────────────┘
                     ▼
            Production-Ready Code
            (with docs, tests, specs)
```

---

## 12. Comparison with Competing AI IDEs

| Criteria | Kiro | Cursor | GitHub Copilot | Cline |
|---|---|---|---|---|
| Platform | VS Code fork | VS Code fork | VS Code extension | VS Code extension |
| Core Methodology | Spec-Driven Dev | Prompt-based | Code completion + Chat | Agentic coding |
| Spec Artifacts | requirements, design, tasks (.md) | None | None | None |
| Agent Hooks | Event-driven automation | None | None | None |
| Powers (Dynamic MCP) | Yes (on-demand loading) | None | None | Basic MCP support |
| Autonomous Agent | Yes (multi-day, persistent context) | None | None | None |
| LLM Backend | Claude Sonnet 4.5 / Auto | Multi-provider | GPT-4o / Claude | Multi-provider |
| Pricing | Free (preview) → Paid tiers | Subscription | Subscription | Free / Paid |

---

## 13. Deployment Architecture

```
┌─────────────────────────────────────────────┐
│            Developer Workstation            │
│  ┌────────────┐  ┌────────────┐             │
│  │ Kiro IDE   │  │ Kiro CLI   │             │
│  └─────┬──────┘  └─────┬──────┘             │
│        └────────┬──────┘                    │
└─────────────────┼───────────────────────────┘
                  │  HTTPS/WSS
                  ▼
┌─────────────────────────────────────────────┐
│           AWS Cloud Infrastructure          │
│                                             │
│  ┌──────────────────────────────────┐       │
│  │  Kiro Backend Services           │       │
│  │  • Authentication & Authorization│       │
│  │  • Session Management            │       │
│  │  • Billing & Usage Tracking      │       │
│  └──────────────┬───────────────────┘       │
│                 │                           │
│  ┌──────────────▼───────────────────┐       │
│  │  LLM Gateway                     │       │
│  │  • Model Routing (Auto mode)     │       │
│  │  • Token Management & Caching    │       │
│  │  • Rate Limiting                 │       │
│  └──────────────┬───────────────────┘       │
│                 │                           │
│  ┌──────────────▼───────────────────┐       │
│  │  Anthropic Claude API            │       │
│  │  (Sonnet 4.5 + Specialized)      │       │
│  └──────────────────────────────────┘       │
│                                             │
│  ┌──────────────────────────────────┐       │
│  │  Autonomous Agent Runtime        │       │
│  │  • Persistent Context Store      │       │
│  │  • Multi-repo Task Queue         │       │
│  │  • Feedback Learning Engine      │       │
│  └──────────────────────────────────┘       │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 14. Summary

Kiro represents a significant architectural advancement in the AI-assisted development ecosystem. Rather than providing only code completion or chat-based assistance, Kiro builds a structured pipeline from requirements → design → implementation → validation, with automation mechanisms (Hooks), dynamic context management (Powers), and persistent learning (Autonomous Agent). This architecture is particularly well-suited for teams that need to maintain code quality, documentation, and consistency at scale — something that pure "vibe coding" cannot guarantee.

---

*Document compiled from public sources: kiro.dev, AWS re:Invent 2025, GeekWire, TechCrunch, InfoQ, SiliconANGLE.*
