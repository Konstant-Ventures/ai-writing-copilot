# AI Writing Copilot — Architecture

**Status:** Conceptual / Pre-Prototype
**Last updated:** May 31, 2026

---

## 1. System Overview

The Writing Copilot is a multi-agent system where each agent has a single, well-defined role. Agents share context through a central store and are coordinated by an Orchestrator.

```
┌──────────────────────────────────────────────────────────────────┐
│                        User Interface                             │
│  (Sounding Board chat, Ghost Writer controls, Editor panel,       │
│   Style Profile manager, document editor, project context bar)   │
└──────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│                         Orchestrator                              │
│  Routes user intent to agents, manages shared context lifecycle,  │
│  coordinates multi-step workflows, handles error recovery         │
└──────────────────────────────────────────────────────────────────┘
         │            │              │               │
         ▼            ▼              ▼               ▼
┌─────────────┐ ┌──────────┐ ┌──────────┐ ┌────────────────┐
│  Sounding    │ │  Ghost   │ │  Editor  │ │     Style      │
│   Board      │ │  Writer  │ │          │ │   Architect    │
│ (explore /   │ │(draft /  │ │(analyze /│ │ (define/manage │
│  frame)      │ │ rewrite) │ │ suggest) │ │   profiles)    │
└─────────────┘ └──────────┘ └──────────┘ └────────────────┘
         │            │              │               │
         └────────────┼──────────────┼───────────────┘
                      ▼              ▼
             ┌──────────────────────────────┐
             │      Shared Context Store      │
             │  - Active document / draft     │
             │  - Active Style Profile        │
             │  - Project type / genre skill  │
             │  - Sounding Board brief        │
             │  - Editor annotations          │
             │  - Conversation history        │
             │  - User preferences            │
             └──────────────────────────────┘
```

---

## 2. Component Descriptions

### 2.1 Orchestrator

**Responsibility:** Route user intent to the right agent, manage context, coordinate multi-step workflows.

**Key behaviors:**
- Interprets user action → determines which agent(s) to invoke
- Loads relevant context (document, profile, project type) and passes it
- Manages the lifecycle of a "writing session" from Sounding Board → Ghost → Editor
- Returns results to the UI (streamed where possible)
- Handles errors gracefully (agent fails → fallback or clear error message)

**Design decisions:**
- Orchestrator is *not* an agent — it has no opinion, no style, no analysis
- It routes, loads, and returns — nothing more
- This keeps agent boundaries clean and testable independently

---

### 2.2 Sounding Board

**Input:** Free-form user input (text, URL, document upload, transcript)
**Output:** Structured brief (JSON)
**Model:** Conversational LLM (e.g., GPT-4, Claude) with Socratic prompting

**Architecture:**
```
User Input ──▶ [Socratic Prompt] ──▶ LLM ──▶ Conversation
                                            │
                                            ▼
                                      [Brief Generator] ──▶ Structured Brief
```

**Key components:**
- **Conversation loop:** LLM + system prompt that enforces Socratic behavior (questions, not answers)
- **Brief Generator:** Extracts structured brief from conversation state when user confirms readiness
- **Boundary detection:** Determines when the user has enough clarity to proceed

**Design decisions:**
- Stateless — each session starts fresh with context from the Shared Context Store
- Brief is validated for completeness before being saved
- User can skip Sounding Board entirely and go directly to Ghost Writer

---

### 2.3 Ghost Writer

**Input:** Brief + Style Profile + Genre Skill + optional user draft
**Output:** Generated text (streamed)
**Model:** LLM (e.g., GPT-4, Claude) + style injection via prompt or structured context

**Architecture:**
```
[Brief] ─┐
[Style Profile] ─┤
[Genre Skill] ───┤──▶ [Prompt Builder] ──▶ LLM ──▶ Stream → UI
[User Draft] ───┘
```

**Key components:**
- **Prompt Builder:** Constructs the LLM prompt from:
  - System prompt encoding the Style Profile dimensions
  - Genre skill structural guidance
  - Brief (core message, audience, tone)
  - Existing draft context for partial rewrites
- **Stream Handler:** Manages token-by-token streaming to the UI
- **Partial Rewrite Engine:** Identifies the region to rewrite and ensures continuity with surrounding text

**Design decisions:**
- Style Profile is encoded as structured context in the system prompt (not injected into every user message)
- Genre skill provides structural guidance (e.g., "a blog post should start with a hook")
- MVP uses prompt-based style injection; Phase 2 can explore fine-tuned models for style adherence

---

### 2.4 Editor

**Input:** Document + Style Profile + Genre Skill
**Output:** Annotated document + summary panel
**Model:** LLM with layered analysis prompts

**Architecture:**
```
[Document] ─┐
[Style Profile] ─┤
[Genre Skill] ───┤──▶ [Layer 1: Structural] ──┐
                 │                            │
                 │──▶ [Layer 2: Argument]     ├──▶ Aggregate ──▶ Annotations
                 │                            │              + Summary
                 │──▶ [Layer 3: Clarity]      │
                 │                            │
                 │──▶ [Layer 4: Consistency]  │
                 │                            │
                 │──▶ [Layer 5: Style]        │
                 │                            │
                 └──▶ [Layer 6: Mechanics]   ─┘
```

**Key components:**
- **Layer Analyzers:** Each feedback layer is an LLM call (or a single call with layered output parsing)
- **Aggregator:** Combines layer outputs, deduplicates, sorts by severity
- **Suggestion Generator:** For flagged issues, generates concrete rewrite suggestions
- **Summary Builder:** Creates the summary panel (counts per layer, top issues)

**Design decisions:**
- Layers are independent — can be run in parallel (MVP) or sequentially (for cost savings)
- Structural feedback is always shown first; mechanics are hidden behind "Show more"
- Rewrite suggestions are optional — Editor can operate in critique-only mode
- Layer 6 (Mechanics) may be delegated to an existing tool (Grammarly API or local equivalent)

---

### 2.5 Style Architect

**Input:** User questionnaire answers + writing samples + conversation history
**Output:** Style Profile (JSON)
**Model:** LLM for analysis + structured extraction

**Architecture:**
```
[Questionnaire] ──▶ [Profile Builder] ──▶ Style Profile (JSON)
[Writing Samples] ─┘

[Conversation History] ──▶ [Friction Detector] ──▶ Adjustment Proposal
```

**Key components:**
- **Profile Builder:** Constructs initial profile from questionnaire or sample analysis
- **Friction Detector:** Post-session analysis of user corrections, rejected suggestions, manual edits
  - Detects patterns: "user consistently increases formality → profile is too casual"
  - Proposes specific adjustments to dimensions
- **Profile Manager:** CRUD operations, versioning, cloning, import/export

**Design decisions:**
- Friction detection runs asynchronously (after writing session ends)
- User must approve proposed adjustments — never auto-applied
- Profile data is portable JSON — can be version-controlled, shared, or imported

---

## 3. Shared Context Store

**Purpose:** Single source of truth for all agents.

| Field | Type | Source | Used By |
|-------|------|--------|---------|
| `document.text` | string | User + Ghost Writer | All agents |
| `document.project_type` | enum | Sounding Board / Manual | Ghost Writer, Editor, Style Architect |
| `document.brief` | object | Sounding Board | Ghost Writer |
| `style_profile` | object | Style Architect / User | Ghost Writer, Editor |
| `genre_skill` | object | System loaded | Ghost Writer, Editor |
| `editor.annotations` | array | Editor | UI |
| `session.history` | array | All agents | Style Architect (friction detection) |
| `user.preferences` | object | User settings | Orchestrator |

**Implementation notes:**
- MVP: In-memory store (per-session)
- Phase 2: Persistent store (database) for cross-session profiles and history
- Phase 3: Sync across devices (if hosted)

---

## 4. Genre Skills Architecture

**Purpose:** Teach agents about specific writing genres.

```
Genre Skill Package
├── metadata.json          # Name, version, author, dependencies
├── structural.json        # Expected structure (sections, flow)
├── heuristics.json        # Editorial rules and checks
├── vocabulary.json        # Domain-specific terms and patterns
└── prompt_segments/       # LLM prompt segments (system instructions)
    ├── ghost_writer.md
    └── editor.md
```

**Loading:**
- Skills are loaded by the Orchestrator when project type is set
- Loaded into the Shared Context Store
- Referenced by Prompt Builder (Ghost Writer) and Layer Analyzers (Editor)

**MVP:** Skills are prompt-guided markdown files. No code execution.

---

## 5. Integration Points

| Integration | Type | Priority | Notes |
|-------------|------|----------|-------|
| **Markdown export** | Output | P0 | All drafts exportable |
| **TTS / Read Aloud** | Feature | P1 | See TTS Models.md for phased approach |
| **VS Code extension** | Platform | P2 | Writing copilot inside editor |
| **API** | Platform | P2 | For custom workflows |
| **Grammarly** | Optional | P3 | Delegate mechanics layer if user has Grammarly |
| **Surfer SEO** | Optional | P3 | For content marketers who want SEO optimization |

---

## 6. Agent Communication Patterns

### Synchronous (Request-Response)
- User asks Sounding Board a question → immediate LLM response
- User requests Ghost Writer draft → streamed output
- User clicks "Analyze" on Editor → full analysis returned

### Asynchronous (Background Processing)
- Style Architect friction detection runs after session ends
- Genre skill updates can be fetched in background

### Streaming
- Ghost Writer output streams token-by-token
- Editor could stream results layer-by-layer (Phase 2)

---

## 7. Error Handling

| Failure Mode | Behavior |
|-------------|----------|
| LLM call fails | Return clear error: "The writer agent is unavailable. Try again in a moment." |
| Sounding Board produces empty brief | Prompt user to continue exploring before handing to Ghost Writer |
| Editor times out (>10s) | Return what's available; flag incomplete layers |
| Style Profile not set | Force user to select or create one before Ghost Writer drafts |
| Genre skill not found for project type | Fall back to "General Writing" skill |

---

## 8. Privacy & Offline Architecture

**Principles:**
- All agent logic should work with a local LLM (for self-hosted / offline mode)
- Cloud LLMs are optional upgrades for quality
- User data never leaves their machine unless they opt into cloud LLMs

**Local inference path:**
```
User Machine
├── Local LLM (via llama.cpp / ONNX)
├── Orchestrator + Agents (local process)
├── Shared Context Store (in-memory or SQLite)
└── UI (local web app or Tauri/Electron)
```

**Cloud inference path (user opts in):**
```
User Machine                    Cloud
┌────────────┐                ┌─────────────┐
│ UI + Store │───API calls───▶│ LLM (GPT-5, │
│ Orchestrator│               │  Claude, etc)│
└────────────┘                └─────────────┘
```

---

*This document evolves with prototypes. Update when implementation reveals architectural changes, new integration needs, or performance optimizations.*
