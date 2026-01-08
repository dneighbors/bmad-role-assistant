~ AI Assistant Platform Proposal
## Role-Based Personal AI Assistants for the Enterprise

**Version:** 1.0 Draft  
**Date:** January 8, 2026  
**Author:** Derek Neighbors, CTO  
**Status:** Proposal for Review

---

## Executive Summary

This proposal outlines a vision for deploying **role-based AI assistants** across the organization. Every employee, from executives to individual contributors, would have access to a personalized AI assistant tailored to their role, with specialized agents for specific tasks.

The platform leverages **BMAD Method** as the foundational architecture for agents, workflows, and memory systems, extended with **MCP (Model Context Protocol)** integrations for external tools, **GitHub** for sharing and persistence, and a future **agent-to-agent communication layer** for distributed collaboration.

### Key Outcomes
- Every role has a personalized AI assistant
- Agents are shareable and composable across roles
- Institutional knowledge persists and improves over time
- External tools (email, calendar, documents, etc.) integrate seamlessly
- Foundation for future agent-to-agent collaboration

---

## Table of Contents

1. [Core Concepts](#core-concepts)
2. [Architecture Overview](#architecture-overview)
3. [BMAD as Foundation](#bmad-as-foundation)
4. [Extensions Beyond BMAD](#extensions-beyond-bmad)
5. [Role Examples](#role-examples)
6. [Shared vs. Unique Agents](#shared-vs-unique-agents)
7. [Implementation Phases](#implementation-phases)
8. [Open Questions & Future Work](#open-questions--future-work)

---

## Core Concepts

### The Assistant Model

Every role in the organization gets a **Personal AI Assistant** that consists of:

```
┌─────────────────────────────────────────────────────────────────┐
│                    ROLE ASSISTANT (e.g., CTO)                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │
│  │ Agent 1  │  │ Agent 2  │  │ Agent 3  │  │ Agent N  │        │
│  │ (Role    │  │ (Exec    │  │ (Meeting │  │ (Custom) │        │
│  │ Advisor) │  │ Asst)    │  │ Prep)    │  │          │        │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘        │
│       │             │             │             │               │
│  ┌────┴─────────────┴─────────────┴─────────────┴────┐         │
│  │              SHARED INFRASTRUCTURE                 │         │
│  │  • Workflows  • Templates  • Rules  • Memories    │         │
│  └───────────────────────────────────────────────────┘         │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### The Five Pillars

| Pillar | Description | BMAD Support |
|--------|-------------|--------------|
| **Agents** | Personas with specific expertise, communication styles, and capabilities | ✅ Native |
| **Workflows** | Structured multi-step processes that agents can execute | ✅ Native |
| **Templates** | Reusable output formats (emails, agendas, reports, etc.) | ✅ Native |
| **Rules** | Contextual instructions injected based on situation | ✅ Partial |
| **Memories** | Persistent context that grows over time | ✅ Native (Sidecar) |

### Additional Capabilities Needed

| Capability | Description | BMAD Support |
|------------|-------------|--------------|
| **External Integrations** | Email, Calendar, Documents, APIs | ❌ Requires MCP |
| **Sharing & Distribution** | Share agents/modules across organization | ❌ Requires GitHub |
| **Agent-to-Agent Comms** | Agents communicating with other agents | ❌ Future Build |
| **Async Operations** | Long-running tasks, scheduled jobs | ❌ Future Build |

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         USER'S LOCAL ENVIRONMENT                         │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                         CURSOR IDE                                 │  │
│  │  ┌─────────────────────────────────────────────────────────────┐  │  │
│  │  │                    BMAD RUNTIME                              │  │  │
│  │  │                                                              │  │  │
│  │  │   ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐    │  │  │
│  │  │   │ Role    │   │ Shared  │   │ Company │   │ Personal│    │  │  │
│  │  │   │ Module  │   │ Agents  │   │ Module  │   │ Agents  │    │  │  │
│  │  │   │ (CTO)   │   │ (EA,etc)│   │         │   │         │    │  │  │
│  │  │   └────┬────┘   └────┬────┘   └────┬────┘   └────┬────┘    │  │  │
│  │  │        │             │             │             │          │  │  │
│  │  │   ┌────┴─────────────┴─────────────┴─────────────┴────┐    │  │  │
│  │  │   │              _bmad/ (Local Installation)           │    │  │  │
│  │  │   │   • agents/  • workflows/  • templates/  • memory/ │    │  │  │
│  │  │   └────────────────────────────────────────────────────┘    │  │  │
│  │  │                              │                              │  │  │
│  │  └──────────────────────────────┼──────────────────────────────┘  │  │
│  │                                 │                                  │  │
│  │  ┌──────────────────────────────┼──────────────────────────────┐  │  │
│  │  │                    MCP SERVERS                               │  │  │
│  │  │   ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐           │  │  │
│  │  │   │ Linear │  │ Google │  │ Slack  │  │ Custom │           │  │  │
│  │  │   │        │  │ Cal/   │  │        │  │ APIs   │           │  │  │
│  │  │   │        │  │ Gmail  │  │        │  │        │           │  │  │
│  │  │   └────────┘  └────────┘  └────────┘  └────────┘           │  │  │
│  │  └─────────────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Git Sync
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          GITHUB (Shared Layer)                           │
│                                                                          │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐         │
│  │ company-agents  │  │ role-modules    │  │ shared-memories │         │
│  │ (org-wide)      │  │ (CTO, CEO, etc) │  │ (team context)  │         │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘         │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Future: Agent-to-Agent Protocol
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                    DISTRIBUTED AGENT NETWORK (Future)                    │
│                                                                          │
│   ┌─────────┐      ┌─────────┐      ┌─────────┐      ┌─────────┐       │
│   │ Derek's │ ◄──► │ Sarah's │ ◄──► │ System  │ ◄──► │ Mike's  │       │
│   │ CTO     │      │ CEO     │      │ Agents  │      │ CFO     │       │
│   │ Agent   │      │ Agent   │      │         │      │ Agent   │       │
│   └─────────┘      └─────────┘      └─────────┘      └─────────┘       │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## BMAD as Foundation

### What BMAD Provides

BMAD (BMad Agile Development Method) v6 provides a battle-tested architecture for:

#### 1. Agent System
```xml
<agent id="cto-advisor" name="Atlas" title="Strategic Technology Advisor" icon="🎯">
  <persona>
    <role>Chief Technology Officer Strategic Advisor</role>
    <identity>Senior technology executive with 25+ years experience...</identity>
    <communication_style>Direct, strategic, balances technical depth with business clarity</communication_style>
    <principles>Technology serves business outcomes. Simplicity wins. Build for change.</principles>
  </persona>
  <menu>
    <item cmd="SP" workflow="strategic-planning">[SP] Strategic Planning Session</item>
    <item cmd="TR" workflow="tech-review">[TR] Technology Review</item>
    <item cmd="DD" workflow="decision-doc">[DD] Create Decision Document</item>
  </menu>
</agent>
```

#### 2. Workflow System (Step-File Architecture)
```
workflows/
├── prepare-1on1/
│   ├── workflow.md           # Entry point
│   ├── steps/
│   │   ├── step-01-load-context.md
│   │   ├── step-02-review-actions.md
│   │   ├── step-03-generate-agenda.md
│   │   └── step-04-finalize.md
│   └── templates/
│       └── agenda-template.md
```

#### 3. Memory/Sidecar System
```
_memory/
├── config.yaml                    # Global user config
├── cto-sidecar/
│   ├── strategic-priorities.md    # Current priorities
│   ├── decision-log.md            # Past decisions
│   └── team-context.md            # Team information
└── 1on1-sidecars/
    ├── alice-chen.md              # 1:1 history with Alice
    ├── bob-smith.md               # 1:1 history with Bob
    └── carol-jones.md             # 1:1 history with Carol
```

#### 4. Module System
BMAD organizes agents into **modules**:
- **BMM** - Software development (PM, Architect, Dev, etc.)
- **CIS** - Creative & Innovation (Brainstorming, Storytelling, etc.)
- **BMB** - Meta (Agent Builder, Workflow Builder, etc.)
- **[NEW] Executive** - Executive roles (CTO, CEO, CFO, etc.)
- **[NEW] Shared** - Cross-role agents (EA, Email, Meeting, etc.)

#### 5. Party Mode (Multi-Agent)
BMAD's "Party Mode" allows multiple agents to collaborate on a task:
```yaml
# executive-party.yaml
name: Executive Strategy Session
agents:
  - cto-advisor
  - ceo-advisor  
  - cfo-advisor
mode: round-robin
```

### BMAD Limitations

| Limitation | Impact | Mitigation |
|------------|--------|------------|
| No native external integrations | Can't read email, calendar, etc. | MCP servers |
| File-based only | No database, no APIs | Git + MCP |
| Single-user runtime | No multi-user, no async | Future architecture |
| No agent-to-agent messaging | Agents can't talk to each other | Custom protocol needed |

---

## Extensions Beyond BMAD

### 1. MCP (Model Context Protocol) Integrations

MCP servers extend Cursor's capabilities to external systems:

```
┌─────────────────────────────────────────────────────────────────┐
│                    MCP SERVER ECOSYSTEM                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  PRODUCTIVITY                    COMMUNICATION                   │
│  ┌──────────────┐               ┌──────────────┐                │
│  │ Google       │               │ Slack        │                │
│  │ • Calendar   │               │ • Messages   │                │
│  │ • Drive      │               │ • Channels   │                │
│  │ • Gmail      │               │ • Search     │                │
│  └──────────────┘               └──────────────┘                │
│                                                                  │
│  PROJECT MGMT                    DATA & DOCS                     │
│  ┌──────────────┐               ┌──────────────┐                │
│  │ Linear       │               │ Notion       │                │
│  │ • Issues     │               │ • Pages      │                │
│  │ • Projects   │               │ • Databases  │                │
│  │ • Cycles     │               │ • Search     │                │
│  └──────────────┘               └──────────────┘                │
│                                                                  │
│  CUSTOM                                                          │
│  ┌──────────────┐               ┌──────────────┐                │
│  │ Internal     │               │ HR Systems   │                │
│  │ APIs         │               │ • BambooHR   │                │
│  │              │               │ • Workday    │                │
│  └──────────────┘               └──────────────┘                │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Example: Email Agent with Gmail MCP**
```xml
<agent id="email-composer" name="Mercury" title="Executive Email Specialist">
  <capabilities>
    <mcp server="gmail">
      <tool>gmail_search</tool>
      <tool>gmail_read</tool>
      <tool>gmail_draft</tool>
      <tool>gmail_send</tool>
    </mcp>
  </capabilities>
  <menu>
    <item cmd="IN">[IN] Review Inbox & Prioritize</item>
    <item cmd="DR">[DR] Draft Response to Email</item>
    <item cmd="CO">[CO] Compose New Email</item>
  </menu>
</agent>
```

### 2. GitHub for Sharing & Persistence

GitHub serves as the **distribution and persistence layer**:

```
┌─────────────────────────────────────────────────────────────────┐
│                    GITHUB REPOSITORY STRUCTURE                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ORGANIZATION REPOS                                              │
│                                                                  │
│  company-ai-assistants/          # Org-wide shared resources    │
│  ├── modules/                                                    │
│  │   ├── shared/                 # Shared agents (EA, Email)    │
│  │   │   ├── agents/                                            │
│  │   │   ├── workflows/                                         │
│  │   │   └── templates/                                         │
│  │   ├── executive/              # Executive-specific           │
│  │   │   ├── cto/                                               │
│  │   │   ├── ceo/                                               │
│  │   │   └── cfo/                                               │
│  │   └── engineering/            # Engineering-specific         │
│  │       ├── vp-eng/                                            │
│  │       └── tech-lead/                                         │
│  ├── memories/                                                   │
│  │   └── company-context/        # Shared company context       │
│  │       ├── strategy.md                                        │
│  │       ├── org-chart.md                                       │
│  │       └── okrs.md                                            │
│  └── installer/                  # Installation scripts         │
│                                                                  │
│  PERSONAL REPOS (Private)                                        │
│                                                                  │
│  derek-ai-assistant/             # Derek's personal instance    │
│  ├── _bmad/                      # Full BMAD installation       │
│  │   ├── executive/              # Symlink or copy from org     │
│  │   ├── shared/                 # Symlink or copy from org     │
│  │   └── personal/               # Personal customizations      │
│  └── _memory/                                                    │
│      ├── 1on1-sidecars/          # Private 1:1 notes            │
│      └── personal-context/       # Private context              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Distribution Model:**
1. **Organization maintains** shared modules in `company-ai-assistants` repo
2. **Users clone/fork** to their personal `{name}-ai-assistant` repo
3. **Updates propagate** via git pull/merge from upstream
4. **Personal customizations** stay in personal repo
5. **Private memories** never sync to org repo (gitignore or separate repo)

### 3. Agent-to-Agent Communication (Future)

**The Problem:** Currently, agents operate in isolation. Derek's CTO agent can't:
- Ask Sarah's CEO agent about strategic priorities
- Request information from the Finance system agent
- Coordinate with team members' agents on scheduling

**Potential Solutions:**

#### Option A: Shared File Protocol
```
shared-agent-bus/
├── outbox/
│   └── derek-cto/
│       └── 2026-01-08-meeting-request.json
├── inbox/
│   └── derek-cto/
│       └── 2026-01-08-response.json
└── system/
    └── announcements/
```
- Pros: Simple, works with existing tools
- Cons: Polling-based, not real-time

#### Option B: MCP Message Bus
```
┌──────────┐     ┌──────────────┐     ┌──────────┐
│ Derek's  │────►│ Agent Message│────►│ Sarah's  │
│ Agent    │◄────│ Bus (MCP)    │◄────│ Agent    │
└──────────┘     └──────────────┘     └──────────┘
```
- Pros: Real-time, proper protocol
- Cons: Requires custom MCP server development

#### Option C: External Orchestration Layer
```
┌─────────────────────────────────────────┐
│         ORCHESTRATION SERVICE           │
│  (Cloud function / dedicated service)   │
├─────────────────────────────────────────┤
│  • Message routing                      │
│  • Agent registry                       │
│  • Permission management                │
│  • Async job queue                      │
└─────────────────────────────────────────┘
         │           │           │
    ┌────┴───┐  ┌────┴───┐  ┌────┴───┐
    │ Agent  │  │ Agent  │  │ System │
    │   A    │  │   B    │  │ Agent  │
    └────────┘  └────────┘  └────────┘
```
- Pros: Most capable, proper async
- Cons: Most complex, requires infrastructure

**Recommendation:** Start with Option A (Shared File Protocol) for v1, plan for Option C long-term.

---

## Role Examples

### Executive Roles

#### CTO (Chief Technology Officer)

**Primary Agent:** CTO Advisor (Atlas)
- Strategic technology direction
- Technical decision frameworks
- Architecture oversight
- Vendor/build-buy analysis

**Supporting Agents:**
| Agent | Purpose | Shared? |
|-------|---------|---------|
| Executive Assistant | Calendar, tasks, scheduling | ✅ Shared |
| Meeting Prep | Agenda creation, context loading | ✅ Shared |
| One-on-One | 1:1 prep with direct reports | ✅ Shared |
| Email Composer | Executive communication | ✅ Shared |
| Research Analyst | Deep technical research | ⚡ CTO-specific |
| Tech Review | Technology evaluations | ⚡ CTO-specific |
| Architecture Review | System design review | ⚡ CTO-specific |
| Board Prep | Board meeting preparation | ✅ Shared (Exec) |

**Unique Workflows:**
- `tech-strategy-review` - Quarterly technology strategy
- `build-vs-buy` - Make/buy decision framework
- `architecture-decision-record` - ADR creation
- `tech-debt-assessment` - Technical debt analysis

**Memory/Sidecars:**
```
_memory/
├── cto-sidecar/
│   ├── tech-strategy.md           # Current tech strategy
│   ├── architecture-decisions.md  # Past ADRs
│   ├── vendor-evaluations.md      # Vendor assessments
│   └── tech-debt-log.md           # Known tech debt
├── 1on1-sidecars/
│   ├── vp-engineering.md
│   ├── staff-engineer-1.md
│   └── architect-1.md
└── team-context/
    └── engineering-org.md
```

---

#### CEO (Chief Executive Officer)

**Primary Agent:** CEO Advisor (Athena)
- Strategic direction and vision
- Stakeholder communication
- Board management
- Executive team coordination

**Supporting Agents:**
| Agent | Purpose | Shared? |
|-------|---------|---------|
| Executive Assistant | Calendar, tasks, scheduling | ✅ Shared |
| Meeting Prep | Agenda creation, context loading | ✅ Shared |
| One-on-One | 1:1 prep with direct reports | ✅ Shared |
| Email Composer | Executive communication | ✅ Shared |
| Board Prep | Board meeting preparation | ✅ Shared (Exec) |
| Investor Relations | Investor communication | ⚡ CEO-specific |
| Strategic Planning | Company strategy sessions | ⚡ CEO-specific |
| All-Hands Prep | Company meeting preparation | ⚡ CEO-specific |

**Unique Workflows:**
- `board-meeting-prep` - Full board prep workflow
- `investor-update` - Monthly investor communication
- `strategic-planning-session` - Annual/quarterly planning
- `executive-review` - Executive team performance

---

#### CFO (Chief Financial Officer)

**Primary Agent:** CFO Advisor (Minerva)
- Financial strategy and planning
- Budget management
- Investor relations (financial)
- Risk management

**Supporting Agents:**
| Agent | Purpose | Shared? |
|-------|---------|---------|
| Executive Assistant | Calendar, tasks, scheduling | ✅ Shared |
| Meeting Prep | Agenda creation, context loading | ✅ Shared |
| One-on-One | 1:1 prep with direct reports | ✅ Shared |
| Email Composer | Executive communication | ✅ Shared |
| Board Prep | Board meeting preparation | ✅ Shared (Exec) |
| Budget Analyst | Budget review and planning | ⚡ CFO-specific |
| Financial Modeling | Scenario planning | ⚡ CFO-specific |
| Audit Prep | Audit preparation | ⚡ CFO-specific |

---

### Other Role Examples

#### VP of Engineering
- Engineering Advisor (primary)
- Team Health Monitor
- Sprint Review Prep
- Hiring Pipeline
- All shared executive agents

#### Product Manager
- Product Advisor (primary)
- User Research Analyst
- PRD Writer
- Roadmap Planner
- Stakeholder Update
- All shared productivity agents

#### Individual Contributor (Engineer)
- Dev Assistant (primary)
- Code Review Helper
- Documentation Writer
- Learning Coach
- Career Development
- Minimal shared agents

---

## Shared vs. Unique Agents

### Shared Agent Library

These agents are useful across multiple roles and should be maintained centrally:

```
shared/
├── agents/
│   ├── executive-assistant.md      # Task/calendar management
│   ├── meeting-prep.md             # Meeting preparation
│   ├── one-on-one.md               # 1:1 preparation
│   ├── email-composer.md           # Email drafting
│   ├── research-analyst.md         # General research
│   ├── document-writer.md          # Document creation
│   ├── presentation-builder.md     # Slide/deck creation
│   └── brainstorming-coach.md      # Ideation facilitation
├── workflows/
│   ├── prepare-meeting/
│   ├── prepare-1on1/
│   ├── weekly-review/
│   ├── email-triage/
│   └── research-topic/
└── templates/
    ├── meeting-agenda.md
    ├── 1on1-template.md
    ├── email-templates/
    └── document-templates/
```

### Role-Specific Agents

These agents are unique to specific roles or role categories:

```
executive/
├── cto/
│   ├── agents/
│   │   ├── cto-advisor.md
│   │   ├── tech-review.md
│   │   └── architecture-review.md
│   └── workflows/
│       ├── tech-strategy/
│       └── build-vs-buy/
├── ceo/
│   ├── agents/
│   │   ├── ceo-advisor.md
│   │   ├── investor-relations.md
│   │   └── strategic-planning.md
│   └── workflows/
│       ├── board-prep/
│       └── investor-update/
└── cfo/
    ├── agents/
    │   ├── cfo-advisor.md
    │   ├── budget-analyst.md
    │   └── financial-modeling.md
    └── workflows/
        ├── budget-review/
        └── audit-prep/
```

### Composition Model

Users compose their assistant from multiple sources:

```yaml
# derek-assistant-config.yaml
modules:
  # Core BMAD
  - source: bmad-method
    modules: [core, bmb]
  
  # Company shared
  - source: github:company/ai-assistants
    modules: [shared, executive/cto]
  
  # Personal additions
  - source: local
    path: ./personal
```

---

## Implementation Phases

### Phase 1: Foundation (Weeks 1-4)
**Goal:** Establish base infrastructure and first working assistant

- [ ] Install BMAD alpha in pilot repo
- [ ] Create initial CTO module with 2-3 agents
- [ ] Set up memory/sidecar structure
- [ ] Configure basic MCP servers (Linear)
- [ ] Document patterns and conventions
- [ ] Test with CTO as pilot user

**Deliverables:**
- Working CTO assistant
- Documentation for creating agents
- Initial template library

### Phase 2: Shared Agents (Weeks 5-8)
**Goal:** Build shared agent library

- [ ] Create Executive Assistant agent
- [ ] Create Meeting Prep agent + workflow
- [ ] Create One-on-One agent + workflow
- [ ] Create Email Composer agent
- [ ] Add Gmail/Calendar MCP servers
- [ ] Set up GitHub distribution repo
- [ ] Test with 2-3 additional executives

**Deliverables:**
- Shared agent library (4-6 agents)
- GitHub-based distribution
- MCP server integrations

### Phase 3: Role Expansion (Weeks 9-12)
**Goal:** Expand to additional roles

- [ ] Create CEO module
- [ ] Create CFO module
- [ ] Create VP Engineering module
- [ ] Create Product Manager module
- [ ] Refine shared agent library based on feedback
- [ ] Document best practices

**Deliverables:**
- 4+ role modules
- Improved shared library
- Best practices documentation

### Phase 4: Scale & Polish (Weeks 13-16)
**Goal:** Production-ready for broader rollout

- [ ] Create onboarding workflow
- [ ] Build self-service installation
- [ ] Create agent customization guides
- [ ] Performance optimization
- [ ] Security review
- [ ] Pilot with 10+ users

**Deliverables:**
- Self-service installation
- User documentation
- Security-approved deployment

### Phase 5: Agent Communication (Future)
**Goal:** Enable agent-to-agent collaboration

- [ ] Design agent communication protocol
- [ ] Implement shared file bus (v1)
- [ ] Create system agents (scheduling, notifications)
- [ ] Build async job capabilities
- [ ] Design permission model

**Deliverables:**
- Agent communication protocol
- System agent library
- Async operation support

---

## Open Questions & Future Work

### Technical Questions

1. **Memory Privacy:** How do we handle sensitive memories (1:1 notes, performance discussions)?
   - Option A: Separate private repo
   - Option B: Encrypted memories
   - Option C: Never-synced local folders

2. **MCP Server Management:** How do users configure MCP servers?
   - Option A: Central IT management
   - Option B: Self-service with approval
   - Option C: Pre-configured packages

3. **Version Management:** How do we handle agent updates?
   - Option A: Semantic versioning with changelogs
   - Option B: Rolling updates
   - Option C: User-controlled updates

### Organizational Questions

1. **Governance:** Who owns and maintains shared agents?
2. **Training:** How do we onboard users to their assistants?
3. **Support:** Who helps when agents don't work as expected?
4. **Customization Limits:** What can users change vs. what's locked?

### Future Capabilities

1. **Voice Interface:** Integration with voice assistants
2. **Mobile Access:** Assistant access from mobile devices
3. **Proactive Agents:** Agents that initiate based on triggers
4. **Learning Agents:** Agents that improve from feedback
5. **Cross-Company Agents:** Agents that work across organizational boundaries

---

## Appendix A: Agent Template

```xml
<agent id="{agent-id}" name="{Name}" title="{Title}" icon="{emoji}">
  <activation critical="MANDATORY">
    <step n="1">Load persona from this current agent file</step>
    <step n="2">Load config from {project-root}/_bmad/{module}/config.yaml</step>
    <step n="3">Load sidecar memories from {project-root}/_memory/{agent}-sidecar/</step>
    <step n="4">Greet user by name, display menu</step>
    <step n="5">Wait for user input</step>
  </activation>
  
  <persona>
    <role>{Primary role description}</role>
    <identity>{Background, experience, expertise}</identity>
    <communication_style>{How the agent communicates}</communication_style>
    <principles>{Core principles that guide behavior}</principles>
  </persona>
  
  <capabilities>
    <mcp server="{mcp-server-name}">
      <tool>{tool-name}</tool>
    </mcp>
  </capabilities>
  
  <menu>
    <item cmd="MH">[MH] Menu Help</item>
    <item cmd="CH">[CH] Chat</item>
    <item cmd="{XX}" workflow="{path}">[{XX}] {Description}</item>
    <item cmd="DA">[DA] Dismiss Agent</item>
  </menu>
</agent>
```

---

## Appendix B: Glossary

| Term | Definition |
|------|------------|
| **Agent** | An AI persona with specific expertise, communication style, and capabilities |
| **Workflow** | A multi-step process that an agent can execute |
| **Template** | A reusable output format (email, document, etc.) |
| **Memory/Sidecar** | Persistent context files that agents load on activation |
| **Module** | A collection of related agents, workflows, and templates |
| **MCP** | Model Context Protocol - standard for LLM tool integrations |
| **Party Mode** | BMAD feature for multi-agent collaboration |
| **BMAD** | BMad Agile Development Method - the foundational framework |

---

## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-01-08 | Derek Neighbors | Initial draft |

---

*This document is a living proposal. Feedback and contributions welcome.*

