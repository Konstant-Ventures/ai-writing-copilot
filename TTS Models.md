# Text-to-Speech Models for the Writing Copilot

**Last updated:** May 30, 2026
**Purpose:** Foundational reference for selecting and integrating TTS into the AI Writing Copilot — reading drafts aloud with natural, low-latency speech.

**Hard constraints driving this evaluation:**

| Constraint | Rationale |
|-----------|-----------|
| Local models must run on **CPU only** | The writing copilot targets laptops without a dedicated GPU |
| Hosted APIs must have a **perpetual free tier** sufficient for single-user use | The app should never require ongoing API spend for one user |
| Must support **streaming or near-instant playback** | Reading drafts paragraph-by-paragraph requires real-time audio |

Estimated monthly volume: ~**500K characters** (10 hours of reading at 150 words/min).

---

## Executive Summary

Under the two hard constraints (CPU-only local, perpetual-free hosted), three viable paths exist:

| Path | Quality (Elo) | Latency | Setup | Best For |
|------|-------------|---------|-------|----------|
| **Web Speech API** | Low (robotic) | Instant | One line of JS | Prototype, fallback |
| **MOSS-TTS-Nano** (local CPU) | Unknown (unranked) | Streaming | pip install | Primary path — streaming + cloning |
| **Kokoro 82M** (local CPU) | **1062** (proven) | Batch only | pip install | Best quality guarantee |
| **Google Cloud TTS** (free tier) | ~1050-1100 | Streaming | SDK + API key | Best free hosted quality |

**Recommended architecture (fallback chain):**

```
  Read Aloud clicked
        │
        ▼
  ┌──────────────────────────┐
  │ MOSS-TTS-Nano installed? │──Yes──▶ Stream (voice cloning available)
  └──────────────────────────┘
        │ No
        ▼
  ┌──────────────────────┐
  │ Kokoro 82M installed?│──Yes──▶ Generate batch audio (best quality)
  └──────────────────────┘
        │ No
        ▼
  ┌──────────────────────────────┐
  │ Google Cloud free key set?   │──Yes──▶ Stream from cloud (1M chars/mo free)
  └──────────────────────────────┘
        │ No
        ▼
  ┌──────────────────────────┐
  │ Web Speech API (browser)  │──Always──▶ Instant, zero-setup, always works
  └──────────────────────────┘
```

**Primary recommendation:** Target **MOSS-TTS-Nano** as the default path (CPU, streaming, cloning). Fall back to the Web Speech API for instant zero-setup. Use Kokoro or Google Cloud free tier if quality demands it.

---

## 1. Use Case & Requirements

### 1.1 What the TTS Must Do

- Read draft paragraphs aloud as the user writes
- Low latency between click and audio start (streaming preferred)
- Run entirely on a laptop with no GPU
- Never incur ongoing API costs for a single user
- Support multiple languages (user base unknown)
- Voice cloning is optional but nice for personalization

### 1.2 Monthly Volume Estimate

| Metric | Value |
|--------|-------|
| Reading speed | ~150 words/min |
| Audio per month | ~10 hours |
| Words per month | ~90,000 |
| Characters per month | ~**500,000** |

This is the threshold for evaluating free tier sufficiency.

### 1.3 Evaluation Criteria (Priority Order)

1. **CPU compatibility** — must run on CPU only
2. **Cost** — zero marginal cost, perpetual free tier for hosted
3. **Voice quality** — measured by Artificial Analysis Speech Arena Elo
4. **Streaming** — first audio byte as fast as possible
5. **Voice cloning** — personalize the reading voice
6. **Language support** — breadth of languages
7. **Setup complexity** — install time, dependencies, disk footprint

---

## 2. Local CPU Models — Primary Path

Only two models satisfy CPU-only inference. Both use ONNX for CPU deployment.

### 2.1 Head-to-Head Comparison

| Feature | Kokoro 82M v1.0 | MOSS-TTS-Nano 0.1B |
|---------|-----------------|-------------------|
| **Params** | 82M | 0.1B (100M) |
| **Quality (Elo)** | **1062** (proven, 5,368 Arena appearances) | **Unknown** (not yet in Speech Arena) |
| **Streaming** | ❌ Batch only (generate full audio then play) | ✅ ONNX streaming |
| **Voice Cloning** | ❌ 54 preset voices only | ✅ Reference audio cloning |
| **Languages** | 6 (EN, JA, ZH, KO, FR, ...) | Multi (CN, EN, more) |
| **Model size (disk)** | ~300 MB | ~400 MB |
| **CPU inference** | ONNX Runtime | ONNX Runtime |
| **RAM at inference** | ~1-2 GB | ~2-3 GB |
| **Setup** | `pip install kokoro` | `pip install moss-tts-nano` |
| **License** | Apache 2.0 | Apache 2.0 |
| **RTF on CPU (i7)** | ~0.3 (faster than real-time) | ~0.5 (estimated) |
| **Latency per sentence** | ~1-2s for 100 chars | ~100-300ms streaming |

### 2.2 Kokoro 82M — Analysis

**Strengths:**
- Proven quality floor — Elo 1062 means it beats older enterprise APIs
- Extremely lightweight (82M params, 300 MB disk)
- Fast CPU inference via ONNX
- Large Arena sample size (5,368 votes) makes the Elo score very stable (±9 CI)

**Weaknesses:**
- No streaming — generates full audio before playback starts
- No voice cloning — 54 fixed presets only
- Batch-only latency means the user waits ~1-2 seconds before hearing anything
- Limited to 6 languages

**Best use:** Short passages where quality matters and the user can tolerate a brief wait before playback.

### 2.3 MOSS-TTS-Nano — Analysis

**Strengths:**
- Streaming ONNX inference — first audio in ~100-300ms
- Voice cloning from reference audio
- 48 kHz stereo output (high fidelity)
- Runs on 4 CPU cores (tested on MacBook Air M4)
- Designed for local deployment from the ground up

**Weaknesses:**
- **Quality is completely unvalidated** — not present in the Speech Arena
- Newer, smaller ecosystem than Kokoro
- Voice cloning quality with no GPU is unknown
- Fewer preset voices

**Best use:** The ideal primary path IF quality proves adequate. Streaming is critical for paragraph-by-paragraph reading UX.

### 2.4 Recommendation

| Scenario | Pick | Reasoning |
|----------|------|-----------|
| Streaming essential | **MOSS-TTS-Nano** | Only CPU option with streaming |
| Quality guarantee needed | **Kokoro 82M** | Proven Elo 1062, very stable |
| Unknown quality risk | Benchmark MOSS-TTS-Nano | Submit to Speech Arena to resolve |

**Highest priority action:** Benchmark MOSS-TTS-Nano in the Speech Arena. Its quality is the single biggest unknown in this document.

### 2.5 Other CPU-Capable Options (Note)

- **Piper TTS** — Older, lower quality, but CPU-only and extremely small (~100 MB)
- **Coqui TTS** — Abandoned (company shut down 2024), CPU via ONNX but no longer maintained
- **Quantized larger models (GGUF)** — Qwen3-TTS quantized to 4-bit via llama.cpp is theoretically possible but immature and untested on CPU for real-time use

None are recommended over Kokoro or MOSS-TTS-Nano.

---

## 3. Free-Tier Hosted APIs — Secondary Path

### 3.1 Free Tier Survey

| Provider | Free Tier Terms | Monthly Limit | Meets 500K chars? | Quality (Elo) | Languages | Streaming |
|----------|----------------|--------------|-------------------|-------------|-----------|-----------|
| **Google Cloud TTS** | 1M chars/month, **no expiration** | 1M chars | ✅ (covers ~2 months) | ~1050-1100 (Studio/WaveNet) | 220+ voices, 40+ langs | ✅ HTTP |
| **Azure AI Speech** | 0.5M chars/month, **no expiration** | 0.5M chars | ✅ (tight) | ~1050 (Neural) | 140+ languages | ✅ |
| **Amazon Polly** | 1M chars/month **for 12 months only** | 1M | ❌ Trial | ~1000 (Neural) | 60+ | ✅ |
| **OpenAI TTS** | ❌ No free tier | — | — | 1102 | 57 | ✅ |
| **ElevenLabs** | 10K chars/month free | 10K | ❌ Far too low | 1178 (v3) | 70+ | ✅ |
| **Cartesia** | ❌ No free tier | — | — | 1203 (Sonic 3.5) | 42 | ✅ |
| **Fish Audio** | Free trial only | — | ❌ | 1123 (S2 Pro) | 80+ | ✅ |
| **Deepgram** | $200 credit (one-time) | — | ❌ | — | English | ✅ |
| **Speechify** | ❌ No free tier | — | — | 1159 (SIMBA 3.0) | Multi | ✅ |

**Only two providers qualify:** Google Cloud TTS and Azure AI Speech.

### 3.2 Google Cloud TTS Free Tier

| Detail | Value |
|--------|-------|
| **Free quota** | 1M characters/month, never expires |
| **Voices** | 220+ across Standard, WaveNet, Neural2, Studio tiers |
| **Languages** | 40+ |
| **Streaming** | ✅ HTTP chunked transfer |
| **Quality** | WaveNet/Neural2 ~Elo 1050; Studio ~Elo 1100 |
| **SSML** | ✅ Full support |
| **Setup** | GCP project + API key + client library |
| **Voice cloning** | ❌ (Custom Voice is paid) |

**Notes:**
- 1M chars/month covers ~2 months of typical use. In practice, the user may hit the limit and need to wait for the reset.
- Studio voices are the highest quality and count against the same quota.
- SSML support allows fine control of pacing, pitch, and emphasis — useful for reading prose.

### 3.3 Azure AI Speech Free Tier

| Detail | Value |
|--------|-------|
| **Free quota** | 0.5M chars/month, never expires |
| **Voices** | 300+ neural voices |
| **Languages** | 140+ |
| **Streaming** | ✅ WebSocket + HTTP |
| **Quality** | Neural ~Elo 1050 |
| **SSML** | ✅ Full support |
| **Setup** | Azure account + Speech resource + SDK |
| **Voice cloning** | ❌ (Custom Neural Voice is paid) |

**Notes:**
- 0.5M chars/month = exactly the estimated monthly volume. Tight but workable.
- Strongest language coverage of any provider (140+).
- WebSocket streaming for lower latency than Google's HTTP chunked.

### 3.4 Recommendation

| Priority | Pick | Reasoning |
|----------|------|-----------|
| Best free tier | **Google Cloud TTS** | 2x quota vs Azure, wider voice selection |
| Microsoft ecosystem | **Azure AI Speech** | 140+ languages, WebSocket streaming |
| Quality backup | Either | Both ~Elo 1050-1100 |

**Both are viable.** Google is the default for its larger quota. Azure is a strong alternative if the project already uses Microsoft infrastructure.

---

## 4. Browser-Based Fallback

### 4.1 Web Speech API

Available in every modern browser (Chrome, Edge, Firefox, Safari). Zero install, zero cost, zero latency.

| Feature | Value |
|---------|-------|
| **Cost** | $0 |
| **Setup** | `window.speechSynthesis.speak(new SpeechSynthesisUtterance(text))` |
| **Latency** | Instant (client-side synthesis) |
| **Quality** | Low — robotic, limited prosody |
| **Voices** | OS-dependent (varies by OS voice packs) |
| **Languages** | 50+ (depends on OS) |
| **Voice cloning** | ❌ |
| **Streaming** | ✅ (sentence by sentence) |
| **Controls** | Rate, pitch, volume — no SSML |

**When to use:**
- Prototype / MVP — test the UX before investing in local model deployment
- Fallback when no local model is installed and no cloud free tier key is configured
- Offline scenarios where the user doesn't want to download a model

**Limitations:**
- Voice quality is noticeably worse than any neural option
- Voice selection is inconsistent across operating systems
- No emotional range or prosody control
- Cannot clone voices

---

## 5. Recommendations for the Writing Copilot

### 5.1 Phased Introduction

| Phase | TTS Path | What to Build | Reasoning |
|-------|----------|---------------|-----------|
| **Prototype** | Web Speech API only | Hook up "Read Aloud" button, test UX flow, get user feedback | Zero setup, focus on product fit |
| **MVP** | Web Speech API + Google Cloud TTS free tier | Add quality option via free API key; keep browser fallback | Improve quality without cost; free tier covers ~2 months |
| **Production** | MOSS-TTS-Nano (local, CPU) | Bundle model download in onboarding; streaming paragraph playback | Zero cost, offline, privacy; streaming for real-time feel |
| **Polish** | Kokoro 82M as benchmark baseline + MOSS-TTS-Nano | Compare quality; add Kokoro path if MOSS-TTS-Nano falls short; add voice cloning | Quality guarantee + streaming; optional personalization |

### 5.2 Cost Projections

All paths are **$0 per month** under the constraints.

| Path | Setup Cost | Monthly Cost | Offline? | Quality |
|------|-----------|-------------|----------|---------|
| Web Speech API only | $0 | $0 | ✅ (always) | Low |
| Google Cloud TTS (free tier) | $0 (API key setup) | $0 (up to 1M chars/mo) | ❌ | Good (~Elo 1050-1100) |
| Azure AI Speech (free tier) | $0 (API key setup) | $0 (up to 0.5M chars/mo) | ❌ | Good (~Elo 1050) |
| Kokoro 82M (local CPU) | $0 (300 MB download) | $0 | ✅ | Very good (Elo 1062) |
| MOSS-TTS-Nano (local CPU) | $0 (400 MB download) | $0 | ✅ | Unknown (needs benchmarking) |

### 5.3 Decision Matrix

| Priority | Best Choice | Why |
|----------|-------------|-----|
| **Privacy** | MOSS-TTS-Nano or Kokoro | Everything runs locally, no data ever leaves the machine |
| **Cost** | Any of the above | All paths are $0 |
| **Quality** | Kokoro 82M (Elo 1062) or Google Cloud TTS (free) | Best Elo scores meeting constraints |
| **Latency / UX** | MOSS-TTS-Nano or Google Cloud TTS | Streaming avoids the wait for batch generation |
| **Simplicity** | Web Speech API | One line of JavaScript, no install, always works |
| **Voice cloning** | MOSS-TTS-Nano (local) | Only option in scope with cloning support |
| **Multilingual** | Google Cloud TTS (free, 40+ languages) | Broadest language coverage among qualifying options |
| **Offline** | MOSS-TTS-Nano or Kokoro | Zero dependency on internet connectivity |

### 5.4 Integration Checklist

- [ ] **Implement Web Speech API fallback** — always available, one line of JS
- [ ] **Select primary local model** — MOSS-TTS-Nano (streaming) or Kokoro (quality)
- [ ] **Bundle model download in onboarding** — first-launch download with progress bar
- [ ] **Optional Google Cloud / Azure free tier key** — user-configurable, improves quality when online
- [ ] **Streaming playback** — start reading as audio arrives, don't wait for full generation
- [ ] **Playback controls** — pause, resume, speed adjustment (0.5x-2x)
- [ ] **Voice persistence** — remember user's voice preference across sessions
- [ ] **Offline detection** — degrade gracefully from local model to Web Speech API
- [ ] **Character counting** — show free tier remaining quota if using cloud API

### 5.5 Architecture Diagram

```
┌──────────────────────────────────────────────────────────┐
│                  Writing Copilot TTS Layer                 │
├──────────────────────────────────────────────────────────┤
│                                                           │
│  onUserClickReadAloud(text)                                │
│       │                                                    │
│       ▼                                                    │
│  ┌─────────────────────────────────────┐                   │
│  │  TTS Router                          │                   │
│  │                                      │                   │
│  │  try MOSS-TTS-Nano (local ONNX)     │──✅──▶ Stream     │
│  │  │  (streaming, voice cloning)       │    audio          │
│  │  ▼                                   │                   │
│  │  try Kokoro (local ONNX)            │──✅──▶ Play batch  │
│  │  │  (best proven quality)            │    audio          │
│  │  ▼                                   │                   │
│  │  try Google/Azure free key          │──✅──▶ Stream from │
│  │  │  (if configured and online)       │    cloud          │
│  │  ▼                                   │                   │
│  │  Web Speech API (always available)  │──✅──▶ Instant     │
│  │                                      │    browser synth  │
│  └─────────────────────────────────────┘                   │
│                                                           │
│  All paths cost $0 and run on CPU only                    │
└──────────────────────────────────────────────────────────┘
```

### 5.6 Recommended Default Configuration

| Setting | Value | Rationale |
|---------|-------|-----------|
| **Default path** | MOSS-TTS-Nano (if installed) → Web Speech API (fallback) | Streaming + zero-cost + offline |
| **Quality upgrade** | User configures Google Cloud free key → MOSS-TTS-Nano route gets cloud option | Voluntarily opt-in to better quality |
| **Model download** | First launch prompt, ~400 MB, background download | Never force, explain benefit |
| **Voice cloning** | Phase 2 feature — user records 3 sentences → MOSS-TTS-Nano clones | Low priority for MVP |

---

## Appendix A: Models Excluded by Constraints

### GPU-Only Local Models (Require GPU, excluded from primary consideration)

| Model | Params | VRAM Needed | Why Not Viable | Better Alternative |
|-------|--------|------------|----------------|-------------------|
| **Qwen3-TTS** | 0.6B / 1.7B | ~4 GB GPU | Requires GPU for real-time inference | MOSS-TTS-Nano for streaming; Kokoro for quality |
| **Vui** | 100M | ~4 GB GPU | Requires GPU, CUDA | MOSS-TTS-Nano (same params, CPU) |
| **OmniVoice** | ~1B | ~4 GB GPU | Requires GPU | Google Cloud free tier for multilingual |
| **Raon-OpenTTS** | 0.3B / 1B | ~4 GB GPU | Requires GPU | Kokoro (proven quality, CPU) |
| **PilotTTS** | ~0.6B | ~4 GB GPU | Requires GPU, just released | Too early, revisit if CPU version appears |

### Paid APIs (No perpetual free tier, excluded)

| Provider | Min Cost | Why Not Viable |
|----------|---------|----------------|
| **OpenAI tts-1** | $15/1M chars ($0.74/hr) | No free tier at all |
| **ElevenLabs v3** | $100/1M chars ($4.90/hr) | Free tier is 10K chars/mo — covers 1% of monthly need |
| **Cartesia Sonic 3.5** | $39/1M chars ($1.90/hr) | No free tier |
| **Fish Audio S2 Pro** | $15/1M chars ($0.74/hr) | Free trial only, no perpetual tier |
| **Gemini 3.1 Flash** | ~$18/1M chars ($0.90/hr) | No free tier for TTS |
| **Inworld RT 1.5** | $35/1M chars | No free tier |
| **MiniMax Speech** | $100/1M chars | No free tier |
| **Deepgram Aura-2** | $15-30/1M chars | $200 one-time credit, then paid |
| **Groq Orpheus** | $22/1M chars ($1.09/hr) | No free tier |
| **Gradium** | ~$1.62/hr | No free tier |
| **Mistral Voxtral** | $16/1M chars | No perpetual free tier |
| **Speechify SIMBA 3.0** | $10/1M chars | No free tier |
| **StepAudio / StepFun** | $85/1M chars | No free tier |

### Time-Limited Trials (Expire, excluded)

| Provider | Trial Terms | Why Not Viable |
|----------|------------|----------------|
| **Amazon Polly** | 1M chars/month for 12 months | Not perpetual; expires |
| **xAI Text to Speech** | Beta pricing | No long-term guarantee |

---

## Appendix B: Quality Reference — Full Arena Context

The top of the Artificial Analysis Speech Arena for context on where our candidates sit:

| Rank | Model | Elo | Meets Constraints? |
|------|-------|-----|-------------------|
| 1 | Gemini 3.1 Flash TTS | 1214 | ❌ Paid API |
| 2 | Realtime TTS-2 Preview | 1209 | ❌ Paid API |
| 3 | Sonic 3.5 (Cartesia) | 1203 | ❌ Paid API |
| 4 | Realtime TTS 1.5 Max | 1195 | ❌ Paid API |
| 5 | xAI Text to Speech | 1194 | ❌ Paid API |
| 11 | Fish Audio S2 Pro | 1123 | ❌ Paid / GPU |
| 17 | OpenAI TTS-1 | 1102 | ❌ Paid API |
| **32** | **Kokoro 82M v1.0** | **1062** | **✅ Local CPU** |
| 33 | Voxtral TTS | 1067 | ❌ Paid API |
| — | Google Cloud TTS (Studio) | ~1100 | **✅ Free tier** |
| — | Azure Neural | ~1050 | **✅ Free tier** |
| — | MOSS-TTS-Nano | ? | **✅ Local CPU (needs benchmark)** |

---

## Appendix C: Setup Friction Details for Local Models

| Consideration | Kokoro 82M | MOSS-TTS-Nano |
|--------------|-----------|----------------|
| **Download size** | ~300 MB (ONNX weights) | ~400 MB (ONNX weights) |
| **Install time (typical)** | 1-2 min (pip) | 2-3 min (pip) |
| **Python version** | 3.9+ | 3.10+ |
| **Dependencies** | ONNX Runtime, numpy | ONNX Runtime, torch (optional) |
| **Disk space after install** | ~600 MB | ~800 MB |
| **RAM at idle** | ~200 MB | ~300 MB |
| **RAM during inference** | ~1-2 GB | ~2-3 GB |
| **CPU cores used** | 1-2 | 4 (configurable) |
| **GPU fallback** | Automatic if CUDA available | Automatic if CUDA available |
| **First inference latency** | ~2-3s (model load) | ~1-2s (model load) |
| **Subsequent inference** | ~1-2s per sentence (batch) | ~100-300ms (streaming) |

---

## Key Sources

| Source | URL | What It Measures |
|--------|-----|-----------------|
| Artificial Analysis Speech Arena | https://artificialanalysis.ai/text-to-speech/leaderboard | Blind human preference (Elo) |
| Artificial Analysis Models | https://artificialanalysis.ai/text-to-speech/models | Quality, speed, price comparison |
| Google Cloud TTS Pricing | https://cloud.google.com/text-to-speech/pricing | Free tier: 1M chars/mo, no expiration |
| Azure AI Speech Pricing | https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/ | Free tier: 0.5M chars/mo, no expiration |
| MDN Web Speech API | https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API | Browser-native SpeechSynthesis |
| HuggingFace TTS Arena | https://huggingface.co/spaces/ArtificialAnalysis/Speech-Arena-Leaderboard | Community-voted comparisons |

---

*This document is a living reference. Update when MOSS-TTS-Nano enters the Speech Arena, new CPU-capable models emerge, or free tier terms change.*
