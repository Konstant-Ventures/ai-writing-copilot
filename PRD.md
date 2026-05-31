# AI Writing Copilot — Product Requirements Document

**Status:** Draft / Pre-MVP
**Last updated:** May 31, 2026
**Authored by:** Vision & Product Design

---

## 1. Product Overview

### 1.1 Elevator Pitch

An AI Writing Copilot that gives you a team of specialized agents — Sounding Board, Ghost Writer, Editor, and Style Architect — so you stay in control while getting expert help at every stage of writing, from messy idea to polished draft.

### 1.2 Problem Statement

Current AI writing tools fall into two camps:

1. **Generators** (Jasper, Sudowrite, ChatGPT): You prompt, they produce. You have limited control over style, no structural editor, and the output often needs heavy rewriting to sound like you.

2. **Editors** (Grammarly, Hemingway): They fix surface issues but have no awareness of what you're building, no ability to draft, and no understanding of genre conventions.

Neither camp gives you a *collaborative team* that helps you *think*, *draft*, *critique*, and *refine* in a unified workflow.

### 1.3 Target Audience (Primary)

Professional and semi-professional writers who produce text as a core part of their work:
- Technical writers
- Grant writers
- Ghostwriters
- Content marketers
- Academics
- Copywriters
- Journalists
- Business writers

Secondary: Anyone who writes regularly and cares about quality.

### 1.4 Key Differentiators

| Differentiator | Why It Matters |
|----------------|----------------|
| Multi-agent architecture | Each agent has one job — clarity of purpose, no feature bloat |
| Style Profiles | Persistent, learnable, user-defined — not a one-shot prompt |
| Genre Skills | Modular domain knowledge — the tool knows what a blog post needs vs. a grant proposal |
| Sounding Board | Helps you think before you write — most tools assume you already know what to say |
| Progressive Editor feedback | Structural first, mechanics last — the right level of detail at the right time |
| Potential open source | Privacy, self-hosting, community-contributed skills |

---

## 2. User Personas

Detailed personas are in **Personas.md**. Key archetypes for the MVP:

- **Ana, Technical Writer** — needs precision, consistency, and structured docs
- **Marcus, Grant Writer** — needs to follow strict rubrics, write persuasively
- **Elena, Executive Ghostwriter** — needs to nail someone else's voice exactly
- **David, Content Marketer** — needs volume across genres while maintaining brand voice

---

## 3. Features & Requirements

### 3.1 Agent: Sounding Board

**Purpose:** Help the user go from a vague thought to a concrete writing brief.

**User stories:**
- As a user with a vague idea, I want to talk it through with the Sounding Board so I can clarify what I actually want to say.
- As a user reacting to an article or conversation, I want to share the source material and get help formulating my own position.
- As a user who doesn't know what format fits my idea, I want recommendations (blog post? report? email? memo?) with reasoning.
- As a user, I want the Sounding Board to produce a brief that the Ghost Writer and Editor can use, so I don't have to repeat myself.

**Functional requirements:**
- Conversational, Socratic interaction (asks questions, doesn't generate content)
- Can accept unstructured input: free text, URLs, document uploads, conversation transcripts
- Produces a structured **brief** containing: core message, recommended format, target audience, tone guidance, tentative structure
- Brief is shareable with Ghost Writer and Editor via the shared context store
- Can transition user to Ghost Writer with a single action ("Hand this to the Ghost Writer")

**Non-functional requirements:**
- Session should feel like a conversation with a thoughtful colleague, not an interrogation
- Should complete within 3-5 exchanges for simple ideas, 8-12 for complex ones
- Brief generation should take <5 seconds after user confirms readiness

**MVP scope:** Yes — agent 1 of 3 in MVP.

---

### 3.2 Agent: Ghost Writer

**Purpose:** Draft text that matches the user's Style Profile and the project's genre.

**User stories:**
- As a user, I want the Ghost Writer to draft a section from my outline, matching my Style Profile.
- As a user who has written 80% of a piece, I want the Ghost Writer to complete the remaining 20% in my style.
- As a user, I want to select a Style Profile (or create one) before the Ghost Writer drafts anything.
- As a user, I want to request a tone-shift on existing text ("make this more casual").
- As a user, I want to regenerate specific sections without losing the rest of the draft.

**Functional requirements:**
- Generates drafts from: Sounding Board brief, user outline, bullet points, free-form notes, or existing draft
- Respects active Style Profile across all dimensions
- Supports partial rewrites (regenerate paragraphs 3-5 while keeping the rest)
- Supports tone-shift rewriting
- Works with genre skills to structure output appropriately for the selected genre
- Can suggest completions as the user types (Phase 2)

**Non-functional requirements:**
- Draft generation should return first tokens within 2 seconds (streaming)
- Must handle documents up to 10,000 words without degradation
- Style adherence must be measurable (Style Architect can evaluate)

**MVP scope:** Yes — agent 2 of 3 in MVP.
- MVP dimensions: Formality, Tone, Sentence Length, Audience (not all 10)
- MVP genres: Blog Post, Email, Report

---

### 3.3 Agent: Editor

**Purpose:** Provide actionable feedback on a draft, organized by severity and type.

**User stories:**
- As a user, I want the Editor to flag structural issues first (before surface-level details).
- As a user, I want to see a summary of all issues organized by layer.
- As a user, I want inline annotations that tell me *what* to fix, not just *that* something is wrong.
- As a user, I want to see concrete rewrite suggestions that I can accept or reject.
- As a user, I want genre-specific feedback (e.g., "in a blog post, the hook should appear in the first 2 paragraphs").

**Functional requirements:**
- Six feedback layers, progressively disclosed:
  1. **Structural** — architecture, flow, completeness
  2. **Argument / Logic** — reasoning, gaps, unsupported claims
  3. **Clarity** — ambiguous sentences, readability issues
  4. **Consistency** — terms, voice, tense, tone
  5. **Style** — alignment with selected Style Profile
  6. **Mechanics** — grammar, spelling, punctuation
- Produces inline annotations anchored to specific text ranges
- Produces a summary panel with counts per layer
- Can show concrete rewrite suggestions for flagged sentences
- Rewrite suggestions are *recommendations* only — user or Ghost Writer applies them
- Learns genre conventions from genre skills

**Non-functional requirements:**
- Full analysis must complete within 10 seconds for a 1,000-word document
- Inline annotations must not break the editing experience
- Progressive disclosure must be the default — full detail only on explicit request

**MVP scope:** Yes — agent 3 of 3 in MVP.
- MVP layers: Structural, Clarity, Consistency (Argument and Style deferred)
- MVP genres: Blog Post, Email, Report

---

### 3.4 Agent: Style Architect

**Purpose:** Help users define, refine, and manage their Style Profiles.

**User stories:**
- As a new user, I want to set up a Style Profile via a questionnaire so I can start using the Ghost Writer immediately.
- As a user with past writing, I want the Style Architect to analyze samples and infer my style.
- As a user, I want to manage multiple Style Profiles for different contexts (Work, Casual, Academic).
- As a user, I want the Style Architect to detect when my profile doesn't match my actual preferences and suggest adjustments.

**Functional requirements:**
- Questionnaire mode: 5-7 questions to bootstrap a profile (formality, audience, tone, etc.)
- Analysis mode: user provides 3-5 writing samples → inferred dimensions
- Friction detection: reviews conversation history (user corrections, rejected suggestions) → proposes profile adjustments
- Profile management: CRUD on profiles, naming, cloning, sharing
- Each profile is a structured data object (JSON) with all dimensions

**Non-functional requirements:**
- Profile inference from samples must complete within 30 seconds
- Friction detection is an async process — can run after a writing session ends
- Profile data must be portable (export/import)

**MVP scope:** No — deferred to Phase 2.
- MVP uses a simple questionnaire + manual profile editing
- No friction detection, no sample analysis

---

### 3.5 Genre Skills

**Purpose:** Modular domain knowledge that teaches agents about specific writing genres.

**Requirements:**
- Each skill contains: structural templates, editorial heuristics, vocabulary guidance, audience expectations
- Skills are loaded dynamically based on the selected project type
- Skills are independent — a bug in "Narrative" doesn't affect "Report"
- Users can create custom skills (Phase 2)
- Community-contributed skills possible if open source (Phase 3)

**MVP scope:** 3 skills shipped with MVP (Blog Post, Email, Report). Skills are prompt-guided, not code.

---

### 3.6 Style Profile Specification

**Data structure (JSON):**

```json
{
  "id": "profile_01",
  "name": "Tech Blog Casual",
  "dimensions": {
    "formality": { "value": 0.3, "range": [0, 1] },
    "vocabulary_richness": { "value": 0.5, "range": [0, 1] },
    "sentence_length": { "value": 0.4, "range": [0, 1] },
    "rhythm_variation": { "value": 0.6, "range": [0, 1] },
    "tone": "warm_authoritative",
    "structure_preference": "problem_solution",
    "voice_active_pct": 0.85,
    "jargon_tolerance": 0.7,
    "audience": "developers",
    "pacing": 0.5
  },
  "metadata": {
    "version": 1,
    "created": "2026-05-31",
    "source": "questionnaire",
    "usage_count": 12
  }
}
```

**MVP dimensions (subset):**
- Formality (0-1)
- Tone (enum: neutral, warm, authoritative, playful, urgent)
- Sentence Length (0-1, short to long)
- Audience (enum: general_public, professionals, experts, executives)

---

## 4. MVP Scope

See **MVP.md** for the complete implementation specification. This section summarizes the scope.

### 4.1 What's In (MVP)

| Component | Details |
|-----------|---------|
| **UI** | Three-panel layout: Rendered Preview (left) + Markdown Editor (center) + Chat Panel (right) |
| **Reading Mode** | Full-screen rendered preview, distraction-free |
| **Markdown** | All documents are Markdown; auto-rendered to HTML via react-markdown |
| **Ghost Writer** | Draft text in configured Style Profile; streaming output into chat → user inserts into document |
| **Editor** | 3 feedback layers (Structural, Clarity, Consistency); output appears in chat panel |
| **Sounding Board** | Socratic conversation; generates brief → can be handed to Ghost Writer (Phase 2 integration) |
| **Style Profile** | 4 dimensions (Formality, Tone, Sentence Length, Audience); manual configuration via settings panel |
| **AI Provider** | DeepSeek V4 Flash via OpenCode Go API (Vercel AI SDK) |
| **Persistence** | localStorage (documents, profiles, chat history, API key) |
| **PWA** | Installable, offline-capable (app shell cache) |

### 4.2 What's Out (Phase 2)

| Feature | Reason |
|---------|--------|
| **Sounding Board → Ghost Writer handoff** | Sounding Board prompt defined but UI integration deferred |
| **Inline annotations** | Editor feedback appears in chat only, not as text highlights |
| **Diff view / Ghost Text** | No streaming preview with accept/reject — text inserts directly |
| **Style Architect (full)** | Manual setup sufficient for MVP; inference and friction detection deferred |
| **Argument/Logic Editor layer** | Requires deeper reasoning; Structural + Clarity + Consistency cover the most value |
| **Multiple genre skills** | MVP treats all writing as "general writing"; genre skills are prompt guidance |
| **Voice cloning / TTS** | Covered in TTS Models.md, separately scoped |
| **Real-time collaboration** | Requires backend; MVP is single-user |
| **Cloud sync / accounts** | localStorage only |
| **Open source release** | Decision deferred; architecture supports it |

---

## 5. UX Principles

1. **One agent per job** — the user should always know which agent they're talking to and what it does
2. **Progressive disclosure** — start simple, reveal detail on demand
3. **Stay out of the way** — the editor experience should feel like a writing tool first, AI tool second
4. **The user is the author** — the tool amplifies, never replaces. All AI output is clearly marked as suggestions
5. **Friction is data** — every correction, rejection, and manual edit is a signal for the Style Architect

---

## 6. Technical Constraints (from TTS Models.md)

| Constraint | Implication |
|------------|-------------|
| CPU-only local inference | Agent models must work without GPU; ONNX-compatible or quantized |
| Perpetual free tier for hosted services | No user should ever need to pay for API usage |
| Streaming preferred | Ghost Writer should stream output; TTS should stream audio |

---

## 7. Success Metrics (MVP)

See **MVP.md §15** for the complete success criteria. High-level metrics:

| Metric | Target | How to Measure |
|--------|--------|----------------|
| Ghost Writer output used | User inserts >50% of generated text | Track insert button vs. discard |
| Editor feedback actionable | >40% of suggestions lead to document edits | Compare pre/post editor session |
| Style consistency | User rates output >7/10 on "sounds like me" | In-app prompt after 3 generations |
| Session completion | >50% of sessions end with "ready to publish" | User marks document as complete |
| PWA engagement | >60% return rate within 7 days | localStorage session tracking |

---

## 8. Phases & Timeline

| Phase | Focus | UI | Agents | Style | Timing |
|-------|-------|----|--------|-------|--------|
| **MVP** | Single-user PWA, Markdown editor, basic agents | 3-panel (Preview + Editor + Chat), reading mode, PWA | Ghost Writer + Editor + Sounding Board (prompts only) | 4 dims, manual | Now (see MVP.md) |
| **Phase 2** | Depth and UX | Inline annotations, Ghost Text diff view, handoff flow | Full Sounding Board → Ghost Writer handoff, Style Architect | 6 dims, inference | After MVP |
| **Phase 3** | Scale | Collaboration, TTS, integrations, API | All agents + Librarian | All 10 dims, friction learning | After Phase 2 |

---

## 9. Integration Points

| Integration | Priority | Notes |
|-------------|----------|-------|
| Markdown export | P0 | All drafts must be exportable |
| TTS / Read Aloud | P1 | See TTS Models.md — phased approach from Web Speech API to local models |
| VS Code extension | P2 | Writing copilot inside the editor |
| Web app | P0 | Primary interface for MVP |
| API | P2 | For custom workflows and integrations |

---

## 10. Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Style Profile dimensions don't produce noticeably different output | Medium | High | Test early; iterate based on blind A/B comparisons |
| Sounding Board feels like a gimmick | Medium | Medium | Test with real writers; if they skip it, merge into Ghost Writer |
| Editor suggestions are ignored | Medium | High | Measure acceptance rate; tune feedback quality and quantity |
| Genre skills are too expensive to build and maintain | Low | Medium | Start with 3 prompt-guided skills; add code-backed skills later |
| Users don't trust AI-generated drafts | High | Medium | Always mark AI output; make Ghost Writer output editable inline; never lock user out |

---

*This document evolves with prototypes and user research. Each phase updates requirements based on what we learn.*
