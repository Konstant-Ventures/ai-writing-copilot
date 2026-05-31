# AI Writing Copilot — User Personas

**Last updated:** May 31, 2026
**Purpose:** Design reference for feature prioritization, UX decisions, and user testing recruitment.

---

## How to Use These Personas

Each persona represents a real profession relevant to this product. They're organized into **primary** (need all agents), **secondary** (need a subset), and **edge cases** (inform but don't drive design).

When evaluating a feature, ask: *"Does this help [persona] do their job better?"* If it doesn't help at least one primary persona, deprioritize it.

---

## Primary Personas (Tier 1)

These professionals need ALL four agents — they're the ideal target users.

---

### 1. Ana — Technical Writer

**Role:** Technical Writer at a mid-sized SaaS company
**Age:** 34
**Background:** BA in English, 8 years experience, transitioned from journalism
**Writing volume:** 15-20 pages/week (API docs, tutorials, release notes, white papers)
**Tools used:** Google Docs, Confluence, Markdown editors, Grammarly

**Goals:**
- Produce clear, consistent documentation that users can actually follow
- Maintain terminology consistency across hundreds of pages
- Reduce time spent on first drafts so she can focus on researching complex features
- Keep documentation in line with evolving product changes

**Pain points:**
- "AI writers don't understand technical accuracy — they make up API endpoints"
- "I spend 60% of my time on structure and terminology, 40% on actual writing"
- "Developers ignore my docs because the language is either too vague or too verbose"
- "Every new product version means rewriting 20 pages — I need boilerplate that adapts"

**How they'd use the agents:**
| Agent | Use Case |
|-------|----------|
| Sounding Board | "I have a complex feature to document. Help me figure out the right structure and audience level." |
| Ghost Writer | "Draft the API reference section following our style guide. Use the same terminology as the existing auth docs." |
| Editor | "Check this tutorial for consistency — did I use 'endpoint' and 'route' interchangeably again?" |
| Style Architect | "My previous employer used 'shall' — current uses 'must'. Help me build a profile for our docs style." |

**What "good" looks like:**
- Drafts a tutorial in <5 minutes that needs only structural edits, not rewrites
- Catches terminology drift before reviewers flag it
- Adapts to different doc types (tutorial vs. reference vs. explanation) automatically

**Fears / objections:**
- "AI will invent technical details that aren't true"
- "If I use AI, my company will think I'm replaceable"
- "The tool won't understand our product's specific idioms"

**Recruitment priority:** HIGH. Technical writers are early adopters of documentation tools, have clear pain points, and are accustomed to structured writing.

---

### 2. Marcus — Grant Writer

**Role:** Senior Grant Writer at a mid-sized non-profit
**Age:** 41
**Background:** MA in Public Policy, 12 years experience
**Writing volume:** Varies: 5-10 grant applications/month, each 15-30 pages
**Tools used:** Word, Excel, Google Drive, Foundation Center

**Goals:**
- Increase grant win rate (currently 35%, wants 50%+)
- Reduce time per application (currently 20 hours, wants 10-12)
- Maintain strict adherence to each funder's rubric
- Reuse language across grants without sounding copy-pasted

**Pain points:**
- "Every funder has a different format, word limit, and criteria — I can't use templates"
- "The persuasive arc has to be perfect — one weak paragraph and the proposal is rejected"
- "I write the same methodology section 10 times with slight variations — it's soul-crushing"
- "My Executive Director rewrites everything because the 'voice' doesn't match the organization"

**How they'd use the agents:**
| Agent | Use Case |
|-------|----------|
| Sounding Board | "This grant is for a youth program. The funder cares about measurable outcomes. Help me frame the problem statement." |
| Ghost Writer | "Draft the methodology section following the rubric's criteria. Keep it formal and evidence-heavy." |
| Editor | "Does this proposal hit all 5 scoring criteria? Show me where the argument is weakest." |
| Style Architect | "Build an 'Executive Director' voice profile — I need all drafts to sound like her." |

**What "good" looks like:**
- Drafts a 20-page proposal in 2 hours instead of 20
- Catches when the proposal doesn't align with the rubric
- Generates persuasive language that actually works (win rate increases)

**Fears / objections:**
- "If the AI writes boilerplate, funders will smell it and reject it"
- "Grant writing is too nuanced — AI can't understand the politics of funder relationships"
- "My job is storytelling, not fill-in-the-blanks"

**Recruitment priority:** HIGH. Deep pain, high willingness to try anything that improves win rate, measurable ROI.

---

### 3. Elena — Executive Ghostwriter

**Role:** Freelance Ghostwriter for tech executives
**Age:** 38
**Background:** Former journalist, 10 years as ghostwriter. Clientele: 3-5 C-suite executives
**Writing volume:** 2-3 LinkedIn posts/week, 1 op-ed/month, 1 book draft/year
**Tools used:** Scrivener, Google Docs, Otter.ai (for interview transcription)

**Goals:**
- Capture each executive's voice precisely — they can spot a fake sentence instantly
- Scale her business — take on more clients without burning out
- Maintain distinct voice profiles for each client
- Speed up the first-draft phase (currently 60% of her time)

**Pain points:**
- "Every client says 'just sound like me' — but they can't articulate what 'me' sounds like"
- "I spend hours reading their past writing to internalize their voice"
- "One client wants academic rigor, another wants punchy takes — I can't use the same approach"
- "Revisions are endless because the voice is slightly off and neither of us can pinpoint why"

**How they'd use the agents:**
| Agent | Use Case |
|-------|----------|
| Sounding Board | "My client wants to write about AI regulation. Let's find the angle that fits their public persona." |
| Ghost Writer | "Write a LinkedIn post in [Client A]'s voice — authoritative but accessible, short sentences, personal anecdotes." |
| Editor | "Check this op-ed — does it sound like [Client A] or does it drift into my default voice?" |
| Style Architect | "Analyze these 20 of [Client A]'s past posts. Build a style profile I can use for future drafts." |

**What "good" looks like:**
- The executive can't tell the difference between her writing and the AI's
- She takes on 2 more clients without increasing hours
- Revision cycles drop from 5 rounds to 2

**Fears / objections:**
- "If my clients find out I use AI, they'll fire me — the value I sell is 'my voice as your voice'"
- "AI can't capture the idiosyncrasies that make someone's writing distinctive"
- "This tool might make my job obsolete"

**Recruitment priority:** HIGH. The most demanding style-matching use case exists. If the tool works for Elena, the Style Profile abstraction is validated.

---

### 4. David — Content Marketer

**Role:** Content Marketing Manager at a B2B fintech startup
**Age:** 29
**Background:** BA in Marketing, 5 years experience
**Writing volume:** 4-6 blog posts/week, 2-3 email sequences/month, landing pages, social posts, case studies
**Tools used:** HubSpot, Jasper, Google Docs, Surfer SEO, Grammarly

**Goals:**
- Increase organic traffic (blog is primary growth channel)
- Maintain consistent brand voice across 4 content types and 3 channels
- Produce more content without hiring another writer
- Scale personalization (different content for SMB vs. enterprise buyers)

**Pain points:**
- "Jasper's brand voice works for blog posts but misses for emails and landing pages"
- "I spend too much time manually adjusting tone between pieces"
- "My CEO wants 'thought leadership' and my SEO tool wants 'keyword density' — they conflict"
- "Content calendars are always slipping because rewriting takes too long"

**How they'd use the agents:**
| Agent | Use Case |
|-------|----------|
| Sounding Board | "We're launching a new feature. What's the best content mix — blog, email, landing page, all three?" |
| Ghost Writer | "Draft a blog post at Flesch 60, warm-authoritative tone, targeting enterprise buyers." |
| Editor | "Check this landing page for persuasion arc. Does the problem → solution flow work?" |
| Style Architect | "Create a 'Fintech Blog' profile and a 'Sales Email' profile — they need different formality levels." |

**What "good" looks like:**
- 50% more content output without quality degradation
- Consistent brand voice across all formats
- Less time per piece (from 4 hours to 2)

**Fears / objections:**
- "I already use Jasper — why switch? It mostly works."
- "SEO requirements are non-negotiable — if the tool can't optimize, it's useless."
- "My team has existing workflows in HubSpot. Integration is mandatory."

**Recruitment priority:** MEDIUM-HIGH. Large market and clear need, but lower switching cost and less pain than grant writers or ghostwriters. Good for Phase 2.

---

### 5. Priya — Academic Writer

**Role:** PhD Candidate, Computational Biology
**Age:** 27
**Background:** MSc in Bioinformatics, 4th year PhD
**Writing volume:** 1-2 paper drafts/year, grant applications, lit reviews, dissertation chapters
**Tools used:** Overleaf (LaTeX), Zotero, Google Scholar, ChatGPT for brainstorming

**Goals:**
- Publish 3 papers before defending
- Write clearly about complex methods
- Reduce the dread of the blank page
- Structure arguments that reviewers won't tear apart

**Pain points:**
- "Literature review sections take weeks — I have to read 50 papers just to write 3 pages"
- "My advisor says my writing is 'unclear' but can't explain what's wrong"
- "I freeze at the blank page — I know the science but can't start writing"
- "Reviewers always ask for better 'motivation' and 'framing' — I don't know how to improve it"

**How they'd use the agents:**
| Agent | Use Case |
|-------|----------|
| Sounding Board | "I have these 5 experiments and I think they tell a story. Help me frame the paper's narrative." |
| Ghost Writer | "Draft the methods section in Nature-style passive voice. Match the terminology from our prior publication." |
| Editor | "Does the introduction logically set up the results? Is there a gap between paragraphs 2 and 3?" |
| Style Architect | "Build a profile for 'computational biology journal' — formal, jargon-heavy, long sentences, passive voice." |

**What "good" looks like:**
- Finishes a paper draft in 2 weeks instead of 2 months
- Reviewer feedback shifts from "unclear" to "minor revisions"
- Less anxiety about starting to write

**Fears / objections:**
- "Will journals detect AI writing and reject my paper?"
- "AI doesn't understand biology — it will make subtle errors that ruin my credibility"
- "My advisor thinks AI tools are cheating"

**Recruitment priority:** MEDIUM. Large potential market (1.5M+ grad students and researchers globally), but high barriers (AI detection fears, advisor stigma). Good for Phase 2-3.

---

### 6. James — Copywriter

**Role:** Freelance Copywriter specializing in DTC brands
**Age:** 32
**Background:** BA in Advertising, 8 years experience
**Writing volume:** 5-10 landing pages/month, 10-20 ad variants/month, email sequences, product descriptions
**Tools used:** Google Docs, Sudowrite (for ideas), Grammarly, Airtable

**Goals:**
- Write more persuasive copy that converts
- Generate more A/B test variants without spending all day
- Quickly switch between brand voices (4-5 clients)
- Beat his own conversion benchmarks

**Pain points:**
- "Each client has a completely different voice — switching costs me 30 minutes of re-reading their old work"
- "Writing 10 ad variants manually is tedious — but they need to feel distinct, not like find-and-replace"
- "I know the persuasion frameworks (AIDA, PAS, etc.) but applying them manually is slow"
- "Clients are increasingly using AI themselves — I need to differentiate by being *better*, not cheaper"

**How they'd use the agents:**
| Agent | Use Case |
|-------|----------|
| Sounding Board | "The client wants to sell a $200 skincare product. What's the emotional hook?" |
| Ghost Writer | "Generate 8 ad variants for Facebook — playful tone, short sentences, CTA in the first line." |
| Editor | "Does this landing page follow the AIDA structure? Where's the Desire section?" |
| Style Architect | "Build profiles for each of my 5 clients. I need to switch between them instantly." |

**What "good" looks like:**
- Generates 10 ad variants in 10 minutes instead of 2 hours
- Each client's work is instantly on-voice, zero warm-up time
- Conversion rates improve (his real KPI)

**Fears / objections:**
- "If my clients can use the same tool, what's my value?"
- "AI copy always sounds generic — the edge is human creativity"
- "I sell my taste and judgment, not my typing speed"

**Recruitment priority:** MEDIUM. Clear use case, but resistance to AI is higher. The "voice switching" pain is real and directly addressed by Style Profiles.

---

## Secondary Personas (Tier 2)

These professionals need a subset of agents but would benefit from the full tool.

### 7. Tom — Journalist

**Role:** Freelance Journalist, covering tech and business
**Age:** 36
**Background:** MA in Journalism, 10 years experience (former staff writer at a national paper)
**Writing volume:** 2-3 features/month + 2-3 news briefs/week
**Tools used:** Google Docs, Notion, Otter.ai, Slack

**How they'd use the agents:**
- **Ghost Writer:** Draft standard formats (news briefs, daily roundups) to save time
- **Editor:** Check for structural issues, clarity, and whether the lede is buried
- **Style Architect:** Profile for "investigative feature" (long sentences, evidence-heavy) vs. "news brief" (tight, inverted pyramid)

**Key difference from Tier 1:** Sounding Board is less critical — journalists usually have an assignment and an angle before they start writing. But useful when developing feature pitches.

---

### 8. Sarah — Novelist

**Role:** Debut novelist, writing literary fiction
**Age:** 31
**Background:** MFA in Creative Writing, works part-time as a writing tutor
**Writing volume:** Varies (2-5 pages/day when in flow)
**Tools used:** Scrivener, physical notebooks, pen and paper

**How they'd use the agents:**
- **Sounding Board:** Explore plot ideas, character motivations, thematic throughlines
- **Ghost Writer:** Overcome writer's block — generate a paragraph in the narrator's voice
- **Style Profile:** Maintain consistent narrative voice across 300 pages

**Key difference from Tier 1:** Editor feedback must be *very* careful with fiction — structural critique can destroy voice. Style Architect would need to be trained on literary samples. Lower priority for MVP.

---

### 9. Rachel — Business Writer

**Role:** Strategy Consultant at a management consulting firm
**Age:** 28
**Background:** MBA, 3 years experience
**Writing volume:** 2-3 memos/week + 1-2 reports/month + daily emails
**Tools used:** Word, PowerPoint, Outlook

**How they'd use the agents:**
- **Ghost Writer:** Draft memos and report sections from bullet points
- **Editor:** Check for conciseness, clarity, action orientation
- **Sounding Board:** Structure a recommendation before writing it

**Key constraint:** Consulting firms have strict compliance requirements. Tool must run locally or have enterprise-grade security.

---

## Edge Cases

These inform design but aren't primary targets.

### 10. Mark — Writing Instructor

**Role:** Teaches first-year composition at a university
**Age:** 45
**Background:** PhD in Rhetoric, 15 years teaching

**Value for our design:** Mark would use the Editor to generate critique examples for students, and Style Architect to teach style awareness. His feedback would help us understand how the *absence* of good writing skills affects tool usage.

### 11. Linda — Resume Writer

**Role:** Professional resume writer
**Age:** 40
**Background:** HR → freelance resume writing, 6 years
**Writing volume:** 10-15 resumes/month

**Value for our design:** Very specific format constraints, high consequence for errors. Helps us think about how Style Profiles handle *format* as well as *voice*.

### 12. Carlos — Technical Blogger (Solo Developer)

**Role:** Senior Developer who writes a popular tech blog
**Age:** 35
**Background:** Self-taught developer, blogs for fun + reputation
**Writing volume:** 2 posts/month

**Value for our design:** Represents the "solo writer who cares about craft but has no budget." Validates open source / free tier as a distribution strategy.

---

## Persona Prioritization for Testing

| Priority | Persona | Why |
|----------|---------|-----|
| **P0** | Ana (Technical Writer) | Clear pain, clear ROI, will test rigorously |
| **P0** | Elena (Ghostwriter) | Hardest style-matching test case |
| **P1** | Marcus (Grant Writer) | Deep pain, measurable win rate, but harder to recruit |
| **P1** | David (Content Marketer) | Largest market, but lower switching cost from Jasper |
| **P2** | James (Copywriter) | Good use case but high resistance |
| **P2** | Priya (Academic) | Large market but high barriers |
| **P3** | Tom (Journalist), Sarah (Novelist) | Secondary — test after core validation |

---

## Appendix: Persona Archetype Template

Use this template for new personas discovered during research:

```markdown
### [Name] — [Role]

**Age:** [Age]
**Background:** [Education, experience]
**Writing volume:** [Per week/month]
**Tools used:** [Current tools]

**Goals:**
- [Goal 1]
- [Goal 2]

**Pain points:**
- "[Quote — real or synthesized]"
- "[Pain point]"
- "[Pain point]"

**How they'd use the agents:**
| Agent | Use Case |
|-------|----------|
| [Agent] | [Use case] |

**What "good" looks like:**
- [Measurable outcome]

**Fears / objections:**
- "[Objection]"

**Recruitment priority:** [HIGH / MEDIUM / LOW]
```

---

*Update when user research validates or invalidates persona assumptions.*
