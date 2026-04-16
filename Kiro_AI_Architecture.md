# Kiro AI — Tài liệu Kiến trúc Tổng quan

> **Phiên bản:** 1.0 | **Ngày:** 15/04/2026  
> **Phân loại:** Agentic IDE — Spec-Driven Development  
> **Nhà phát triển:** Amazon Web Services (AWS)  
> **Website:** [kiro.dev](https://kiro.dev)

---

## 1. Giới thiệu

Kiro là một **Agentic IDE** (Môi trường phát triển tích hợp dựa trên AI Agent) do AWS phát triển, được xây dựng trên nền tảng VS Code (Code OSS fork). Kiro ra mắt bản preview vào tháng 7/2025, đạt trạng thái General Availability (GA) vào tháng 11/2025, và liên tục được mở rộng tại AWS re:Invent 2025 với các tính năng Autonomous Agent, Powers, và Frontier Agents.

Điểm khác biệt cốt lõi của Kiro so với các AI coding assistant truyền thống (Cursor, Copilot, Cline) là phương pháp **Spec-Driven Development (SDD)** — biến requirements thành artifacts có thể thực thi, thay vì chỉ đơn thuần sinh code từ prompt.

---

## 2. Kiến trúc Tổng quan (High-Level Architecture)

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

## 3. Các thành phần kiến trúc chính

### 3.1. Kiro IDE (Desktop Client)

Kiro IDE là ứng dụng desktop dựa trên VS Code / Code OSS, hỗ trợ đa nền tảng (macOS, Windows, Linux).

| Thuộc tính | Chi tiết |
|---|---|
| Nền tảng | VS Code fork (Code OSS) |
| Plugin Ecosystem | Open VSX (tương thích VS Code extensions) |
| Xác thực | Google, GitHub, Amazon account |
| Giao diện | Purple theme với VS Code layout quen thuộc |
| Multimodal Input | Text, hình ảnh (UI mockup, whiteboard), file đính kèm |

Các kênh tương tác chính trong IDE bao gồm Vibe Session (prompt-based coding tự do), Spec Session (quy trình SDD có cấu trúc), và Chat Panel hỗ trợ context provider thông qua cú pháp `#spec`, `#file`.

### 3.2. Kiro CLI (Command Line Interface)

Kiro CLI cho phép developer sử dụng các agent trực tiếp từ terminal, phù hợp cho môi trường SSH và headless workflow. CLI hỗ trợ xây dựng features, phân tích lỗi, trace bugs, và tự động hóa workflow trong vòng lặp tương tác.

### 3.3. Kiro Autonomous Agent (Cloud-based)

Ra mắt tại AWS re:Invent 2025, Autonomous Agent là frontier agent có khả năng hoạt động độc lập trong nhiều ngày. Các đặc điểm kiến trúc bao gồm:

- **Persistent Context**: Duy trì ngữ cảnh xuyên suốt các session, không bị reset giữa các lần tương tác
- **Multi-repo Awareness**: Xử lý thay đổi trên nhiều repository như một tác vụ thống nhất
- **Learning from Feedback**: Ghi nhớ phản hồi từ PR review và áp dụng tự động cho công việc tương lai
- **Asynchronous Execution**: Thực thi tác vụ nền trong khi developer làm việc khác

---

## 4. Spec-Driven Development Engine

Đây là trái tim kiến trúc của Kiro, phân biệt rõ ràng với "vibe coding" truyền thống.

### 4.1. Workflow ba pha

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

### 4.2. Spec Artifacts (trong `.kiro/specs/`)

Mỗi feature hoặc bugfix tạo ra một thư mục spec riêng biệt chứa ba file markdown.

**requirements.md** sử dụng ký pháp EARS (Easy Approach to Requirements Syntax), cấu trúc yêu cầu dưới dạng "When [trigger], the system shall [behavior]" để hỗ trợ formal reasoning và property-based testing.

**design.md** chứa kiến trúc tổng thể với Mermaid diagrams, component interfaces, data models, TypeScript interfaces, và các implementation considerations.

**tasks.md** phân rã thiết kế thành các task rời rạc, sắp xếp theo dependency, với trạng thái tracking real-time (Pending → In Progress → Done).

### 4.3. Hai loại Spec

**Feature Spec** dành cho xây dựng tính năng mới, hỗ trợ hai workflow variant là Requirements-First (từ yêu cầu → thiết kế) và Design-First (từ kiến trúc → yêu cầu).

**Bugfix Spec** dành cho chẩn đoán và sửa lỗi có hệ thống, phân tích hành vi hiện tại vs. kỳ vọng, thiết kế bản vá và xác nhận không gây regression.

---

## 5. Agent Hooks Engine

Agent Hooks là hệ thống event-driven automation cho phép AI agent tự động thực thi hành động dựa trên các sự kiện trong IDE.

### 5.1. Kiến trúc Hook

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

### 5.2. Ví dụ Use Cases

- Khi save file React component → tự động tạo/cập nhật unit test tương ứng
- Khi commit code → tự động cập nhật documentation
- Khi tạo file component mới → kiểm tra tuân thủ Single Responsibility Principle
- Khi thay đổi content → tự động generate translations cho localization

---

## 6. Powers Engine — Dynamic Context Management

Powers là cơ chế đóng gói và phân phối khả năng chuyên biệt cho AI agent, giải quyết vấn đề **context window overload** khi kết nối nhiều MCP server cùng lúc.

### 6.1. Kiến trúc Power

```
power-<name>/
├── POWER.md           # Steering file chính (frontmatter + instructions)
│   ├── Frontmatter    # name, displayName, description, keywords
│   ├── Onboarding     # Setup steps (install CLI, validate deps)
│   └── Workflows      # Steering file map cho từng tác vụ
├── mcp.json           # MCP server configuration
└── steering/          # Steering files chi tiết theo workflow
    ├── workflow-a.md
    └── workflow-b.md
```

### 6.2. Cơ chế Dynamic Activation

Thay vì load tất cả tool definitions lên front (gây tiêu tốn token và làm chậm agent), Powers hoạt động theo mô hình on-demand. Khi developer đề cập keyword liên quan trong chat (ví dụ "database" → kích hoạt Supabase Power), Kiro tự động load POWER.md và MCP tools tương ứng vào context. Khi chuyển sang tác vụ khác, Power trước đó tự động deactivate và giải phóng context.

### 6.3. Partner Ecosystem

Các power từ đối tác bao gồm UI Development (Figma), Backend (Supabase, Stripe, Neon, Postman), Agent Development (Strands SDK), Deployment (Netlify), Observability (Datadog, Dynatrace), và Database (Amazon Aurora DSQL). Hệ sinh thái mở cho phép community đóng góp và chia sẻ powers qua GitHub.

---

## 7. Steering System — Agent Configuration Layer

Steering files là lớp cấu hình giúp Kiro hiểu context dự án, quy ước coding, và yêu cầu kỹ thuật.

### 7.1. Cấu trúc thư mục

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

### 7.2. Phạm vi Steering

**Global Steering** áp dụng cho tất cả projects (coding standards chung, preferred tools).  
**Project Steering** áp dụng riêng cho từng project, tự động generate từ codebase analysis hoặc do developer tùy chỉnh.  
**Power Steering** được load động từ Powers khi tác vụ liên quan được kích hoạt.

---

## 8. MCP Integration Layer

Kiro tích hợp native với Model Context Protocol (MCP), chuẩn giao tiếp giữa AI agents và external tools/data sources.

### 8.1. Kiến trúc MCP trong Kiro

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

### 8.2. Các loại MCP Server

**Local MCP Servers** chạy trên máy developer, kết nối qua stdio (GitHub MCP, Docker, filesystem).  
**Remote MCP Servers** kết nối qua SSE/HTTP tới dịch vụ cloud (JIRA, Confluence, Slack).  
**Power MCP Servers** được đóng gói trong Powers, load/unload động theo context conversation.

---

## 9. LLM Backbone

Kiro sử dụng các mô hình ngôn ngữ lớn từ Anthropic làm nền tảng xử lý.

| Chế độ | Mô hình | Đặc điểm |
|---|---|---|
| Default | Claude Sonnet 4.5 | Coding và reasoning nâng cao, ổn định |
| Auto | Multi-model mix | Kết hợp Sonnet 4.5 + specialized models, tối ưu chất lượng/latency/chi phí |

Chế độ Auto sử dụng intent detection để chọn model phù hợp nhất cho từng tác vụ, kết hợp caching để giảm chi phí token.

---

## 10. Security & Trust Model

### 10.1. Kiểm soát Agent

Kiro áp dụng mô hình **human-in-the-loop** với nhiều cấp độ kiểm soát. Trong Autopilot Mode, agent có quyền tự động hóa cao hơn nhưng vẫn yêu cầu approval cho các hành động nhạy cảm như cài đặt dependencies hoặc chạy scripts. Developer có thể review từng thay đổi code qua diff view, approve tất cả hoặc step-through từng change.

### 10.2. Trusted Commands

Cơ chế simplified trusted commands cho phép developer định nghĩa trước các lệnh mà agent được phép thực thi mà không cần hỏi lại, giảm friction trong workflow lặp đi lặp lại.

---

## 11. Data Flow — Từ Prompt đến Production Code

```
Developer Prompt
       │
       ▼
┌──────────────────┐
│ Intent Detection │ ── Xác định: Vibe Session hay Spec Session?
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

## 12. So sánh với các AI IDE cạnh tranh

| Tiêu chí | Kiro | Cursor | GitHub Copilot | Cline |
|---|---|---|---|---|
| Nền tảng | VS Code fork | VS Code fork | VS Code extension | VS Code extension |
| Phương pháp cốt lõi | Spec-Driven Dev | Prompt-based | Code completion + Chat | Agentic coding |
| Spec Artifacts | requirements, design, tasks (.md) | Không có | Không có | Không có |
| Agent Hooks | Event-driven automation | Không có | Không có | Không có |
| Powers (Dynamic MCP) | Có (on-demand loading) | Không có | Không có | Hỗ trợ MCP cơ bản |
| Autonomous Agent | Có (multi-day, persistent context) | Không có | Không có | Không có |
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

## 14. Tổng kết

Kiro đại diện cho một bước tiến kiến trúc quan trọng trong hệ sinh thái AI-assisted development. Thay vì chỉ cung cấp code completion hoặc chat-based assistance, Kiro xây dựng một pipeline có cấu trúc từ requirements → design → implementation → validation, với các cơ chế automation (Hooks), dynamic context management (Powers), và persistent learning (Autonomous Agent). Kiến trúc này phù hợp đặc biệt cho các team cần duy trì chất lượng code, tài liệu, và tính nhất quán trên quy mô lớn — điều mà "vibe coding" thuần túy không thể đảm bảo.

---

*Tài liệu được tổng hợp từ các nguồn công khai: kiro.dev, AWS re:Invent 2025, GeekWire, TechCrunch, InfoQ, SiliconANGLE.*
