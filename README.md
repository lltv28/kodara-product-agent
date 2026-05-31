# Kodara Product Agent

A configured AI **Chief Product Officer** for Kodara — a system prompt plus the source material it reasons from. Drop it into the Anthropic API (or any LLM) to get a product executive that critiques your thinking *and* produces real artifacts (opportunity solution trees, opportunity assessments, interview guides, funnel teardowns, pricing analyses).

The agent synthesizes three books into one operating doctrine:

- **Marty Cagan, _Inspired_** — product philosophy & org design (discovery before delivery; valuable/usable/feasible/viable; customer ≠ user; beware of specials).
- **Teresa Torres, _Continuous Discovery Habits_** — the operating system (weekly customer contact, the Opportunity Solution Tree, outcomes over outputs, test assumptions not ideas).
- **Wes Bush, _Product-Led Growth_** — monetization & growth (value metric, value-based pricing, value gaps & ability debt, churn > ARPU > acquisition).

---

## Contents

| Path | What it is |
|---|---|
| `kodara-cpo-system-prompt.md` | The CPO agent's system prompt. Stable doctrine + grounded Kodara context. This is the file you load into the `system` field. |
| `source-material/vsl-transcript.md` | The Video Sales Letter transcript — the **authoritative statement of Kodara's _promised_ value**. The agent uses this as the "promised" side of every value-gap analysis, and cross-checks it against the unfinished internal reality (the "TK" pricing placeholders, the two competing funnel scripts, the delivery-heavy scaling constraint). |
| `source-material/inspired-cagan-summary.md` | Comprehensive summary of _Inspired_ (Marty Cagan) — the agent's philosophy & org-design foundation. |
| `source-material/continuous-discovery-habits-torres-summary.md` | Comprehensive summary of _Continuous Discovery Habits_ (Teresa Torres) — the agent's discovery operating system. |
| `source-material/product-led-growth-bush-summary.md` | Comprehensive summary of _Product-Led Growth_ (Wes Bush) — the agent's monetization & growth layer. |

> **On the book summaries:** the doctrine is already baked into `kodara-cpo-system-prompt.md`, so you don't need to load these three files at runtime for the agent to behave correctly — they're kept in the repo as the canonical reference for *why* it reasons the way it does, and so the doctrine can be audited or updated over time. Load them into context only when you want the agent to cite chapter-level detail or you're revising the system prompt itself.

---

## How it's configured

The agent was built around four deliberate choices (change these in the system prompt if your situation changes):

- **Business type:** sales-led B2B SaaS, founder-led done-for-you delivery, pre-repeatable-PMF.
- **Mode:** advisor **and** operator — it both challenges your thinking and produces deliverables.
- **Posture:** direct & challenging — it disagrees openly, demands evidence, and names traps (specials, output theater, optimizing the wrong side of the product).
- **Sharpest at:** discovery & roadmap + team/process/stakeholders. Pricing and PLG are present but secondary.

The single idea that overrides everything: **fall in love with the outcome and the problem — never the solution, feature, or offer.**

The central thing it keeps in front of you: Kodara is a **two-sided product** — the *expert buyer* (judges it on "does it sound like me / can I trust it") vs. the *consumer leads* the AI actually talks to (where conversion value is created). Most product mistakes here come from optimizing one side when the real lever is the other.

---

## How to wire it into the Anthropic API

Load the system prompt as the `system` field and the VSL as a cached reference document. The system prompt is stable, so mark it for prompt caching to avoid re-billing it on every call.

```python
import anthropic

client = anthropic.Anthropic()

with open("kodara-cpo-system-prompt.md") as f:
    cpo_system = f.read()
with open("source-material/vsl-transcript.md") as f:
    vsl = f.read()

resp = client.messages.create(
    model="claude-opus-4-8",
    max_tokens=2048,
    system=[
        # Stable doctrine — cached so it isn't re-billed every call.
        {"type": "text", "text": cpo_system,
         "cache_control": {"type": "ephemeral"}},
        # Source material the agent reasons from — also cached.
        {"type": "text",
         "text": "AUTHORITATIVE SOURCE — Kodara VSL (current positioning & offer):\n\n" + vsl,
         "cache_control": {"type": "ephemeral"}},
    ],
    messages=[
        {"role": "user",
         "content": "How do I productize delivery without weakening the VSL's "
                    "'done-for-you, you don't lift a finger' promise?"}
    ],
)
print(resp.content[0].text)
```

Notes:
- Put the **stable content first** (system prompt, then VSL) so the cache prefix stays warm across calls.
- The Anthropic prompt cache has a ~5-minute TTL; back-to-back calls hit the cache, occasional calls re-warm it.
- On other LLMs, paste `kodara-cpo-system-prompt.md` into the system/custom-instructions field and attach the VSL as a reference document. The prompt is written to be model-agnostic.

---

## Maintaining it

The system prompt is doctrine and rarely needs to change. The one block worth keeping current is **"What Kodara is"** in `kodara-cpo-system-prompt.md` — update it the moment you confirm:

- Real **ARR and client count** (currently marked unverified — the agent is told to flag when advice depends on numbers it doesn't have).
- Final **pricing and guarantee terms** (the "TK" placeholders).
- Which **funnel script** is canonical (two competing versions currently exist).
- Any **product-org / roadmap-ownership** changes.

When the VSL changes, replace `source-material/vsl-transcript.md` so the "promised value" reference stays accurate.

---

## Suggested first prompts

- "Draw the expert-side and consumer-side Opportunity Solution Trees for the 'productize delivery' outcome."
- "Audit the consumer funnel for ability debt and value gaps against what the VSL promises."
- "Where is our current roadmap likely just sales requests in disguise? How would you fix the prioritization?"
- "Propose a value metric and pricing structure to retire the 'TK' placeholders."
- "Design the weekly customer-contact cadence for both sides without overloading me as founder."
