# AI Writing Copilot — MVP Specification

**Status:** Ready for development
**Last updated:** May 31, 2026
**Target persona:** David — Content Marketer / Blog Writer
**Target model:** DeepSeek V4 Flash (via OpenCode Go API)
**Output format:** PWA — Progressive Web App

---

## 1. Design Persona: David

**Role:** Content Marketing Manager at a B2B tech startup
**Age:** 29
**Writing volume:** 4-6 blog posts/week, 2-3 email sequences/month, landing pages, social posts
**Current tools:** Jasper, Grammarly, Google Docs, VS Code (Markdown)

**What David needs from MVP:**
- Draft a blog post from rough notes or an outline
- Get the draft in his "brand voice" (conversational but authoritative, short paragraphs)
- Get editorial feedback on structure, clarity, and consistency
- See the rendered output alongside his raw Markdown
- Make quick edits and keep writing

**What David does NOT need in MVP:**
- Voice cloning / TTS
- Real-time collaboration
- API / integrations
- Style Architect (manual profile setup is fine)

---

## 2. Tech Stack

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| **Frontend** | React + TypeScript (Vite) | Fast dev, PWA support via vite-plugin-pwa |
| **Markdown rendering** | react-markdown + remark-gfm | Renders GitHub-Flavored Markdown to HTML |
| **State management** | React Context + useReducer | Simple enough for MVP; no need for Redux |
| **PWA** | vite-plugin-pwa | Service worker, offline cache, install prompt |
| **AI provider** | OpenCode Go API (OpenAI-compatible) | Access to DeepSeek V4 Flash via $10/mo Go subscription |
| **AI SDK** | Vercel AI SDK (`ai` package) | Standardized streaming, tool calls, multi-provider |
| **Styling** | Tailwind CSS v4 | Utility-first, fast iterations |
| **Storage** | localStorage (MVP) | Drafts persist locally; no backend needed |
| **Icons** | lucide-react | Consistent, lightweight icons |
| **Build** | Vite | Fast HMR, PWA plugin, TypeScript support |

### 2.1 AI Connection Details

The app connects to DeepSeek V4 Flash via the OpenCode Go API, which is OpenAI-compatible.

```
Base URL: https://api.opencode.ai/v1 (or OpenCode Go endpoint)
Model:    deepseek-v4-flash
API Key:  User-provided OpenCode Go API key (stored in localStorage)
```

The Vercel AI SDK (`@ai-sdk/openai`) handles streaming, tool calls, and chat history. The provider configuration:

```typescript
import { createOpenAI } from '@ai-sdk/openai';

const openai = createOpenAI({
  apiKey: localStorage.getItem('opencode_api_key'),
  baseURL: 'https://api.opencode.ai/v1',
});

const model = openai('deepseek-v4-flash');
```

---

## 3. User Interface

### 3.1 Screen Layout (Desktop)

```
┌────────────────────────────────────────────────────────────────┐
│  Header: Logo │ [Doc Title (editable)] │ [Model Indicator] │ ⚙️ │
├─────────────┬───────────────┬──────────────────────────────────┤
│             │               │   Chat Panel                     │
│   Rendered  │    Editor     │  ┌──────────────────────────┐    │
│   Preview   │   (Markdown)  │  │  [Editor] [Ghost Writer] │    │
│             │               │  │                          │    │
│   📖        │   ✏️          │  │  Messages appear here     │    │
│             │               │  │  in a chat conversation  │    │
│   Markdown  │   Raw text    │  │                          │    │
│   → HTML    │   editing     │  │  _______________________ │    │
│   rendered  │               │  │  [Type a message...]  ➤ │    │
│             │               │  │  [Sounding Board mode]  │    │
│             │               │  └──────────────────────────┘    │
├─────────────┴───────────────┴──────────────────────────────────┤
│  Status bar: Word count │ Reading time │ AI credits │ Connected │
└────────────────────────────────────────────────────────────────┘
```

### 3.2 Panel Descriptions

**Left Panel — Rendered Preview (📖):**
- Full HTML rendering of the Markdown document
- Uses react-markdown with GitHub-Flavored Markdown (tables, code blocks, lists, headings)
- Auto-updates as the user types in the Editor panel (debounced, ~500ms)
- Reading mode: can be toggled to full-screen (hides Editor and Chat panels)
- Typography: serif font for body text (reading-friendly), monospace for code blocks
- Dark mode support

**Center Panel — Editor (✏️):**
- Raw Markdown text editing (textarea or CodeMirror)
- Monospace font, line numbers optional
- Keyboard shortcuts: Ctrl+S (save), Ctrl+B (bold), Ctrl+I (italic), Ctrl+K (link)
- The editor and preview scroll independently
- Drag handle to resize Editor / Preview split ratio

**Right Panel — Chat Interface:**
- Two tabs: **Editor** and **Ghost Writer**
- Each tab has its own conversation history
- Messages stream in real-time (Vercel AI SDK streaming)
- Input box at the bottom with Send button
- Sounding Board mode toggle (checkbox or icon) — when active, the chat asks questions instead of generating/editing

**Tab: Editor**
- Used for: get feedback on the current document
- Context: the current document text is automatically included as context
- Suggested starting prompts: "Review my draft for structure", "Check for clarity issues", "Is my argument consistent?"
- Output: inline annotations (displayed as highlighted text in the Editor panel) + chat messages

**Tab: Ghost Writer**
- Used for: draft new sections, rewrite existing text
- Context: the current document + active Style Profile
- Suggested starting prompts: "Draft an introduction", "Continue from paragraph 3", "Rewrite this section to be more persuasive"
- Output: streamed text that user can accept (inserts into document at cursor) or reject

### 3.3 Mobile / Narrow Layout

On screens < 768px:
- Single column, stacked vertically
- Default: Editor panel (full width)
- Toggle buttons at top: [Edit] [Preview] [Chat]
- Chat panel opens as an overlay / bottom sheet
- Preview panel replaces Editor when selected
- PWA install prompt on first visit

### 3.4 Reading Mode

Activated via a toggle button in the header (book icon):
- Hides the Editor panel and Chat panel
- Shows only the Rendered Preview at full width
- Clean, distraction-free reading experience
- Exit via the same toggle or Escape key
- Especially useful for proofreading long sections

### 3.5 Header

| Element | Behavior |
|---------|----------|
| Logo | Links to home (new document) |
| Document title | Inline editable (click to rename) |
| Reading mode toggle | Toggle book icon → enters/exits full-screen preview |
| Model indicator | Shows "DeepSeek V4 Flash" — dropdown for future model switching |
| Settings gear | Style Profile selection, API key config, theme toggle |

### 3.6 Status Bar

| Element | Source |
|---------|--------|
| Word count | Computed from document text |
| Reading time | Word count / 200 (avg reading speed) |
| Character count | Computed |
| Save status | "Saved" / "Unsaved changes" — localStorage auto-saves every 2s |
| AI connection | Green dot when API key is configured and working |

---

## 4. Style Profile (MVP)

For MVP, a Style Profile is a simple JSON object stored in localStorage. No Style Architect agent yet — the user manually sets values via a settings panel.

```json
{
  "name": "Tech Blog Casual",
  "dimensions": {
    "formality": 0.3,
    "tone": "warm_authoritative",
    "sentence_length": 0.4,
    "audience": "developers"
  }
}
```

**Settings panel (gear icon → Style Profile):**
- Name field
- Formality slider (Casual ↔ Formal)
- Tone dropdown (neutral, warm, authoritative, playful, urgent)
- Sentence length slider (Short ↔ Long)
- Audience dropdown (general_public, professionals, developers, executives)
- "Save Profile" button
- Default profile provided if none created

When the Ghost Writer is invoked, the Style Profile is injected into the system prompt:

```
You are a ghost writer drafting content for David, a content marketer.
Write in the following style:
- Formality: {formality} (0=casual, 1=formal)
- Tone: {tone}
- Sentence length: {sentence_length} (0=short, 1=long)
- Audience: {audience}

Current document context:
{document_text}

David says: {user_message}
```

---

## 5. Agent Prompts (MVP)

### 5.1 Ghost Writer — System Prompt

```
You are the Ghost Writer, an AI agent that drafts text for a content writer.
Your job is to write — not to critique, not to ask questions, not to brainstorm.

Write in the active Style Profile provided below. Match the voice exactly.

Style Profile: {style_profile_json}

Rules:
1. Write directly. No preamble like "Here is a draft..." or "I've written..."
2. Output pure Markdown.
3. Keep paragraphs short (2-4 sentences).
4. If continuing from existing text, match the surrounding style.
5. Do NOT add commentary about your own writing.
6. Do NOT ask the user questions.
7. Write, stop, wait for the next instruction.
```

### 5.2 Editor — System Prompt

```
You are the Editor, an AI agent that reviews written content and suggests improvements.
Your job is to critique and recommend — not to rewrite. You can show short examples of
how to phrase things differently, but the Ghost Writer will implement changes.

Review the document against these layers, starting from the most important:
1. STRUCTURAL: Does it flow well? Is the argument clear? Is anything missing?
2. CLARITY: Are any sentences hard to follow? Too long? Ambiguous?
3. CONSISTENCY: Are terms, voice, tense, and tone consistent throughout?

Style Profile to validate against: {style_profile_json}

Rules:
1. Start each response with the most critical issue first.
2. Be specific — point to exact sentences or paragraphs.
3. You may show a short rewrite example, but preface it with "Suggested rewrite:".
4. Ask questions: "What evidence supports this claim?" rather than just "Add evidence."
5. End with a summary: "2 structural issues, 3 clarity improvements, 1 consistency flag."
```

### 5.3 Sounding Board — System Prompt

```
You are the Sounding Board, an AI agent that helps a writer clarify their thinking.
Your job is to ask questions — NOT to write, NOT to suggest, NOT to draft.

The user has a rough idea. Help them figure out:
1. What is the core message they want to communicate?
2. Who is the audience?
3. What format would work best? (blog post, email, report, social post, etc.)
4. What's the one thing they want the reader to take away?

Rules:
1. Only ask questions. Do not offer suggestions or ideas.
2. Keep questions short and focused.
3. After 3-5 exchanges, ask if they're ready to hand this to the Ghost Writer.
4. When they say yes, output a brief in this format:
   
   BRIEF
   Core message: [one sentence]
   Format: [format]
   Audience: [audience]
   Key takeaway: [one sentence]
   Tone guidance: [short note]
   END BRIEF
```

---

## 6. Data Flow

### 6.1 Writing Session Flow

```
1. User opens app
   ├── New document (blank Markdown)
   └── Load existing document (from localStorage)

2. User writes in Editor panel
   ├── Preview panel auto-updates (debounced 500ms)
   └── Auto-save to localStorage every 2s

3. User switches to Ghost Writer tab
   ├── Types: "Draft an introduction about AI in marketing"
   └── System sends: document context + Style Profile + user message
       └── Response streams into chat panel
           └── User clicks "Insert" → text added to document at cursor position

4. User switches to Editor tab
   ├── Types: "Review my draft"
   └── System sends: full document + Style Profile + user message
       └── Response streams into chat panel
           └── User applies suggestions manually

5. User activates Sounding Board (checkbox)
   ├── Types: "I have an idea about..."
   └── System sends Sounding Board prompt
       └── Socratic conversation ensues
           └── When ready, brief is generated
               └── User clicks "Hand to Ghost Writer"
```

### 6.2 AI Call Architecture

```
┌────────┐    ┌──────────────┐    ┌──────────────────┐    ┌───────────────┐
│  UI     │───▶│  Chat Panel  │───▶│  Vercel AI SDK   │───▶│  OpenCode Go  │
│  Panel  │    │  (React)     │    │  (@ai-sdk/openai)│    │  API          │
└────────┘    └──────────────┘    └──────────────────┘    └───────────────┘
                                                   │
                                                   ▼
                                           ┌──────────────┐
                                           │ DeepSeek V4  │
                                           │ Flash (LLM)  │
                                           └──────────────┘
```

---

## 7. Routes / Pages

| Route | Page | Description |
|-------|------|-------------|
| `/` | Document list | List of saved documents (from localStorage) — create new or open existing |
| `/doc/:id` | Editor | The main three-panel editor (Preview + Edit + Chat) |
| `/settings` | Settings | API key configuration, Style Profile management, theme |

---

## 8. Component Tree (React)

```
<App>
  <Router>
    <DocumentList />          {/* / */}
    <EditorPage>              {/* /doc/:id */}
      <Header>
        <DocumentTitle />
        <ReadingModeToggle />
        <ModelSelector />
        <SettingsButton />
      </Header>
      <MainLayout>
        <ResizablePanelGroup direction="horizontal">
          <PreviewPanel />     {/* Markdown → HTML rendered */}
          <EditorPanel />      {/* Raw Markdown textarea */}
          <ChatPanel>          {/* Right sidebar */}
            <TabBar>
              <EditorTab />
              <GhostWriterTab />
            </TabBar>
            <MessageList>
              <Message />      {/* Streaming message */}
            </MessageList>
            <ChatInput>
              <SoundingBoardToggle />
              <SendButton />
            </ChatInput>
          </ChatPanel>
        </ResizablePanelGroup>
      </MainLayout>
      <StatusBar>
        <WordCount />
        <ReadingTime />
        <SaveStatus />
        <ConnectionStatus />
      </StatusBar>
    </EditorPage>
    <SettingsPage />           {/* /settings */}
  </Router>
</App>
```

---

## 9. Local Storage Schema

```typescript
interface StoredDocument {
  id: string;
  title: string;
  content: string;       // Raw Markdown
  styleProfileId: string;
  createdAt: string;
  updatedAt: string;
}

interface StoredStyleProfile {
  id: string;
  name: string;
  dimensions: {
    formality: number;       // 0-1
    tone: 'neutral' | 'warm' | 'authoritative' | 'playful' | 'urgent';
    sentence_length: number; // 0-1
    audience: 'general_public' | 'professionals' | 'developers' | 'executives';
  };
}

interface StoredChatHistory {
  docId: string;
  tab: 'editor' | 'ghost-writer';
  messages: Array<{
    role: 'user' | 'assistant';
    content: string;
    timestamp: number;
  }>;
}

// Keys in localStorage:
// 'documents'        → StoredDocument[]
// 'style_profiles'   → StoredStyleProfile[]
// 'chat_histories'   → Record<string, Record<string, StoredChatHistory>>
// 'opencode_api_key' → string
// 'theme'            → 'light' | 'dark'
// 'active_doc'       → string (doc ID)
```

---

## 10. Build & Deploy

### 10.1 Commands

```bash
# Install dependencies
npm create vite@latest writing-copilot -- --template react-ts
cd writing-copilot
npm install @ai-sdk/openai ai react-markdown remark-gfm
npm install tailwindcss @tailwindcss/vite lucide-react
npm install vite-plugin-pwa

# Development
npm run dev

# Build
npm run build

# Preview
npm run preview
```

### 10.2 PWA Configuration

- Service worker caches app shell for offline loading
- Manifest includes: name, icons (192x192, 512x512), theme_color, display: standalone
- Install prompt triggers on first visit (if criteria met)
- Offline: can view and edit previously opened documents from localStorage

### 10.3 Deployment Targets

| Target | Method |
|--------|--------|
| **Netlify** | `npm run build` → deploy `dist/` folder |
| **Vercel** | `npm run build` → deploy `dist/` folder |
| **Cloudflare Pages** | `npm run build` → deploy `dist/` folder |
| **GitHub Pages** | `npm run build` → deploy via gh-pages |

---

## 11. MVP Limitations (Explicit)

These are out of scope for MVP:

| Feature | Why Not |
|---------|---------|
| **Sounding Board** | Agent system prompt defined but UI integration (checkbox → brief → Ghost Writer handoff) is Phase 2 |
| **Inline annotations** | Editor feedback appears in chat only, not as highlights in the document text |
| **Diff view** | No Ghost Text streaming preview with accept/reject — text inserts directly |
| **Multiple style profiles** | MVP stores one active profile; switching is manual |
| **Style Architect** | Manual setup only; no inference, no friction detection |
| **Real-time collaboration** | Requires backend; localStorage is single-user |
| **TTS / Read Aloud** | See TTS Models.md — separate scope |
| **VS Code extension** | Web app PWA first |
| **User accounts / sync** | localStorage only; no cloud sync |
| **Persistence beyond localStorage** | No database; localStorage is cleared if user clears browser data |

---

## 12. Development Phases Within MVP

| Phase | Scope | Estimated Effort |
|-------|-------|------------------|
| **1. Shell** | Vite + React + TypeScript setup, PWA config, 3-panel layout, resizable panels, Tailwind theme, dark mode | 2-3 days |
| **2. Editor + Preview** | Markdown textarea, react-markdown rendering, debounced live preview, reading mode toggle | 1-2 days |
| **3. Chat Panel** | Tab bar (Editor / Ghost Writer), message list, streaming input, Sounding Board toggle | 2-3 days |
| **4. AI Connection** | OpenCode Go API integration via Vercel AI SDK, streaming responses, error handling, connection status | 1-2 days |
| **5. Agent Prompts** | Ghost Writer system prompt, Editor system prompt, Sounding Board prompt, Style Profile injection | 1 day |
| **6. Document Management** | localStorage CRUD, document list page, auto-save, title editing | 1-2 days |
| **7. Settings** | API key config, Style Profile editor (sliders + dropdowns), theme toggle | 1 day |
| **8. Polish** | Status bar, responsive mobile layout, PWA install prompt, loading states, error boundaries | 1-2 days |

**Total estimated MVP effort: 10-16 days for a single developer.**

---

## 13. Key Files

```
writing-copilot/
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts              # Vite + PWA + Tailwind config
├── public/
│   ├── manifest.json
│   └── icons/
│       ├── icon-192x192.png
│       └── icon-512x512.png
├── src/
│   ├── main.tsx
│   ├── App.tsx
│   ├── index.css               # Tailwind imports
│   ├── lib/
│   │   ├── ai.ts               # Vercel AI SDK setup (OpenCode Go provider)
│   │   ├── storage.ts          # localStorage helpers
│   │   └── prompts.ts          # Agent system prompts
│   ├── components/
│   │   ├── Header.tsx
│   │   ├── StatusBar.tsx
│   │   ├── DocumentList.tsx
│   │   ├── PreviewPanel.tsx     # react-markdown renderer
│   │   ├── EditorPanel.tsx      # Markdown textarea
│   │   ├── ChatPanel.tsx
│   │   ├── ChatMessage.tsx
│   │   ├── ChatInput.tsx
│   │   ├── TabBar.tsx
│   │   ├── ReadingMode.tsx
│   │   └── StyleProfileEditor.tsx
│   ├── pages/
│   │   ├── EditorPage.tsx
│   │   ├── DocumentListPage.tsx
│   │   └── SettingsPage.tsx
│   ├── context/
│   │   ├── DocumentContext.tsx
│   │   ├── ProfileContext.tsx
│   │   └── ThemeContext.tsx
│   └── types/
│       └── index.ts
```

---

## 14. Testing the MVP

| Test | What to Check |
|------|---------------|
| **PWA install** | Can you install the app on desktop/mobile? |
| **Markdown rendering** | Does the preview match expected output for headings, lists, code blocks, tables, links? |
| **Live preview** | Does typing in the editor update the preview within 500ms? |
| **Ghost Writer** | Does it generate text in the configured Style Profile? Try "Draft an introduction about AI in marketing" |
| **Editor** | Does it identify structural, clarity, and consistency issues? Try "Review my draft" |
| **Style Profile** | Does changing formality from 0.3 to 0.8 produce noticeably different output? |
| **Reading mode** | Does full-screen preview work? Does Escape exit it? |
| **Auto-save** | Does refresh restore the document content? |
| **Mobile layout** | Does the single-column layout work on a phone screen? |
| **Dark mode** | Do all panels render correctly in dark mode? |

---

## 15. Success Criteria

The MVP is successful when David can:

1. Open the app, write a blog post outline in Markdown, and see it rendered
2. Ask the Ghost Writer to draft a section and get output that matches his style
3. Ask the Editor to review the draft and get actionable feedback
4. Read the rendered version in distraction-free reading mode
5. Close the browser, reopen, and find his work saved
6. Do all of the above without touching a backend or database
