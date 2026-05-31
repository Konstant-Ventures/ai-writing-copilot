# AI Writing Copilot — Business Case

**Status:** Pre-evaluation / Hypothesis
**Last updated:** May 31, 2026

---

## 1. Problem & Opportunity

### 1.1 The Problem

Writers at all levels face a fundamental tension when using AI: they want help without losing control.

| Current Approach | Limitation |
|-----------------|------------|
| AI generators (Jasper, Sudowrite, ChatGPT) | You get a draft but it doesn't sound like you. You spend as much time editing as you would writing from scratch. |
| AI editors (Grammarly, Hemingway) | They fix surface issues but don't help with structure, argument, or genre conventions. They can't draft. |
| Direct LLM usage (ChatGPT, Claude) | Requires prompt engineering skill. No persistence — every session starts from zero. No awareness of your style, your project, or your genre. |

**The market gap:** No tool combines drafting, editing, and thinking support in a unified workflow with persistent style awareness.

### 1.2 The Opportunity

The AI writing tool market is projected to grow significantly, driven by:
- Proliferation of content creation across industries
- Demand for personalized, on-brand AI output
- Growing recognition that "write for me" tools produce generic content
- Writers seeking AI that *collaborates* rather than *replaces*

Our angle: **quality over quantity.** We target writers who care about craft, not marketers who need volume. This is the underserved niche.

### 1.3 Market Size (TAM / SAM / SOM)

**Total Addressable Market:** All professional writers globally.

| Source | Stat |
|--------|------|
| U.S. Bureau of Labor Statistics | ~135,400 writers and authors employed in the US (2024) |
| Content Marketing Institute | ~70% of companies now produce content in-house |
| Global freelance writing market | Estimated at $15B+ annually |
| AI writing assistant market | Projected to reach $5.9B by 2028 (Grand View Research) |

**Serviceable Addressable Market:** Writers who produce long-form, craft-oriented content (excludes high-volume SEO content mills).

| Segment | Estimated Size |
|---------|---------------|
| Technical writers | ~55,000 (US) |
| Grant writers | ~30,000 (US, non-profit sector) |
| Content marketers (craft-oriented) | ~200,000 (US, mid-market+) |
| Ghostwriters / book writers | ~50,000 (global, freelance) |
| Academics writing papers | ~1.5M (global, research-active) |
| Copywriters (specialized) | ~150,000 (US) |
| **Total SAM** | **~2M+ writers globally** |

**Serviceable Obtainable Market (Year 1-2):**
- 0.1% of SAM = ~2,000 users
- At $15-20/month = ~$360K-480K ARR
- Conservative estimate for a niche tool with no marketing budget

---

## 2. Competitive Landscape

See **Competitive-Analysis.md** for the full breakdown.

**Key finding:** No existing competitor combines multi-agent architecture with persistent Style Profiles and genre-aware editing. The closest competitors (Lex, Sudowrite, Jasper) each own one piece but lack the rest.

**Our advantages:**
- Multi-agent team with clear roles (no other tool has this)
- Style Profiles as a first-class data object (vs. one-shot prompting)
- Genre skills as modular knowledge (vs. one-size-fits-all)
- Potential open source (no competitor is open source)
- Sounding Board (no competitor helps you think before you write)

---

## 3. Business Model Options

### Option A: Open Source Core + Paid Hosted

| Component | Model |
|-----------|-------|
| Core application | Open source (MIT or AGPL) |
| Hosted version | SaaS subscription ($12-19/month) |
| Premium genre skills | Paid skill packs ($5-10 each, or included in subscription) |
| Enterprise features | SSO, audit, custom skills, dedicated support |

**Pros:** Community contributions, trust, distribution via GitHub, low customer acquisition cost.
**Cons:** Requires sustained engineering investment; open source users don't pay.

### Option B: SaaS Only (Closed Source)

| Tier | Price | Features |
|------|-------|----------|
| Free | $0 | Sounding Board + basic Ghost Writer (2 genres, 2 style dims) |
| Pro | $19/mo | Full Ghost Writer + Editor + all genres + unlimited Style Profiles |
| Team | $49/mo per user | Shared profiles, collaboration, admin controls |

**Pros:** Simpler monetization, easier to build, more control.
**Cons:** Higher CAC, privacy concerns, no community leverage.

### Option C: Hybrid (Recommended)

Start with **Option A** — open source the core, charge for hosted + premium skills. This maximizes distribution and trust while building a revenue stream.

---

## 4. Go-to-Market Strategy

### Phase 1: Community (Months 1-6)
- Release open source MVP on GitHub
- Target technical writers and developers who write (they're most likely to self-host)
- Post on Hacker News, /r/writing, /r/technicalwriting
- Write about the architecture (multi-agent, Style Profiles)
- Goal: 500 GitHub stars, 100 active users

### Phase 2: Niche Launch (Months 6-12)
- Launch hosted version
- Target one persona deeply: **Technical Writers**
- Partner with a technical writing conference (Write the Docs)
- Create genre skills for technical documentation
- Goal: 500 paid users, $100K ARR

### Phase 3: Expansion (Months 12-24)
- Add genre skills for grant writers, content marketers, academics
- VS Code extension launch
- Community-contributed skill marketplace
- Goal: 2,000 paid users, $400K ARR

---

## 5. Revenue Projections (Conservative)

| Year | Users (Paid) | ARPU | ARR | Costs (Est.) | Margin |
|------|-------------|------|-----|--------------|--------|
| 1 | 200 | $15/mo | $36K | $60K (development) | Negative |
| 2 | 1,000 | $17/mo | $204K | $120K | $84K |
| 3 | 3,000 | $18/mo | $648K | $240K | $408K |

**Assumptions:**
- LLM API costs: ~$3-5/user/month (including inference for generation and analysis)
- Hosting costs: ~$1/user/month
- Development: 1-2 FTE in Year 1, 3-4 by Year 2
- Open source reduces customer acquisition cost to near-zero

---

## 6. Key Risks

| Risk | Severity | Mitigation |
|------|----------|------------|
| **LLM advances commoditize the approach** | High | If a future model does all this in one prompt, the multi-agent architecture loses its edge. Mitigation: focus on UX, persistence, and workflow — things no LLM API provides. |
| **Open source competitors emerge** | Medium | There's no open source multi-agent writing tool today. First-mover advantage + community = moat. |
| **Target market is too small** | Medium | 2M+ SAM is real but fragmented. Focus on one persona (technical writers) and expand. |
| **Editor feedback is ignored** | High | If users don't act on suggestions, the Editor has no value. Test early, tune aggressively. |
| **LLM API costs eat margins** | Low | Costs are falling. Caching and local inference (via quantized models) can reduce dependency. |
| **Privacy concerns with cloud AI** | Medium | Open source + self-hosting directly addresses this. Hosted version should offer data residency options. |

---

## 7. Success Metrics

| Metric | Year 1 Target | Why |
|--------|--------------|-----|
| Active users (monthly) | 500 (OSS) + 200 (paid) | Validates demand before scaling |
| Editor suggestion acceptance rate | >40% | Proves the Editor adds value |
| Style Profile satisfaction | >70% "sounds like me" | Core promise must deliver |
| Net revenue retention | >100% | If we solve the problem, users stay |
| Community contributions | 20 skills / 50 PRs | Validates the open source premise |

---

## 8. Key Assumptions to Validate

These are the highest-risk assumptions that prototypes must test:

1. **Style Profiles produce noticeably different output.** Build a blind A/B test: same prompt, different profiles. Can users consistently identify which is theirs?

2. **Sounding Board provides value beyond a blank page.** Test with writers who have vague ideas. Do they write better pieces after using it? Or do they find it annoying?

3. **Editor suggestions are actionable.** Give real drafts to real writers with and without Editor annotations. Measure revision quality and time.

4. **Users will pay for this.** Canary test: 100 users, ask them what they'd pay. If <20% say $10+, rethink the model.

5. **Writers trust multi-agent enough to use it.** The concept is novel. Users might find 4 agents confusing. Test single-agent vs. multi-agent and measure comprehension.

---

## 9. Competitive Moats

| Moat | Description | Durability |
|------|-------------|------------|
| **Style Profile data** | User profiles improve with use; switching costs increase | Long-term |
| **Genre skills ecosystem** | Community-contributed skills create network effects | Medium-term |
| **Open source community** | Contributors, index, trust | Medium-term |
| **UX workflow** | The interaction design (Sounding Board → Ghost → Editor) | Short-term (can be copied) |
| **Agent architecture** | Technical design is not unique but the integration is | Short-term |

**Defensible narrative:** The combination of persistent Style Profiles + genre skills + multi-agent workflow is the moat. A competitor can copy one piece but replicating the whole system while matching UX quality takes 12-18 months.

---

*This is a living document. Update as prototypes provide real data and market feedback.*
