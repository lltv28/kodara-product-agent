# June Roadmap v2 — What changed after reviewing the codebase

**One line:** v1 was drafted **without** the codebase. v2 is ground-truthed against `the-kodara/kodara`. The pattern across almost every section: **what we scoped as "build" is mostly "extend/consolidate," because more already exists than v1 assumed** — which frees June's real effort for the few things that genuinely don't exist yet.

---

## The three headline shifts

1. **Item Zero is ~70% already built.** PostHog is wired and firing, 21 events exist, and the funnel drop-off report ships today. We are not building instrumentation — we're **adding the one missing half (upsell + return + latency events)** and turning config on.
2. **The biggest "Speed" win is already done.** The chat already streams token-by-token. Speed retargets to time-to-first-token and the (genuinely slow) voice path — *not* "add streaming."
3. **One "Decided" item conflicts with the live product** and is now a **team decision**: B.1's "paywall the payoff" is the *opposite* of what the funnel ships today (a free personalized-plan preview at the paywall).

Plus a vocabulary fix: v1 used **ascension / upsell / cross-sell / expansion / monetization / upgrade** interchangeably. v2 standardizes on **"upsell"** (and keeps **"re-engagement/return"** as a separate concept).

---

## Section-by-section: what changed and why

| Section | v1 assumed (blind) | What the codebase showed | v2 change |
|---|---|---|---|
| **Item Zero / §A** | Build PostHog from scratch (PH-1…PH-10); invent an event taxonomy | PostHog wired + firing; 21 typed events; drop-off report + snapshot job live; defaults to PostHog adapter (gated on env keys); checkout is **Whop** (not Stripe); payment idempotency already handled | Reframe to **"confirm config + add the missing half"**: ~6 new events (`mid_ticket_subscribed`, `high_ticket_offer_clicked`/`_confirmed`, `session_started`, `nurture_reengaged`, `generation_latency`) + a `clientUpsellReport` + one deferred Calendly/GHL webhook. Don't fork the taxonomy. |
| **Channels (§A scope)** | "Web/app only; no email/SMS; no cross-channel identity problem" | **Email is live** (`lead_nurture` via Postmark); **SMS in progress** (Twilio 10DLC); identity already keys to email/phone | Multi-channel attribution is **partly in scope now**, not "additive later." |
| **B.2 Speed** | Add token-by-token streaming = the big win | Chat **already streams** (typewriter); instant-ack + optimistic UI partly exist; **only voice is batch** | Streaming is **done**. Retarget to **time-to-first-token** + skeleton paint for plan/verdict + the voice path. Hold infra spend until `generation_latency` proves a bottleneck. |
| **B.3 Reply pacing** | Build response-mode matching; fear "50-config per-client voice = nightmare" | No length/mode logic, **but a per-agent reply rubric exists** (`reply-rubric.mustache`, `RubricEditorPanel`) — already the "sounds like me" mechanism | Mode-matching **extends the rubric**; per-expert voice calibration is **already solved**. |
| **B.4 Voice** | "We have voice — just make it feel live" (polish) | Voice exists as **two batch halves**: push-to-talk dictation + TTS playback of a *finished* message. No VAD/barge-in/duplex. ElevenLabs + voice-cloning solid | It's a **replace-the-batch-pipeline** build, not polish — **but** the core move (connect the already-streaming brain → already-streaming TTS) is a **wire-up, not greenfield**. **Cost-per-call gate** is the real go/no-go. |
| **B.5 Visual components** | Build a component library + output schema | **`ui_blocks` renderer + schema already exist** (`messageUiBlocks.ts` / `MessageUiBlocksView.tsx`, currently owner-side cards only); plan/checklist deeply built | A ResultCard/2×2/Dossier is a **new block type in an existing union**, not a new framework. "Send plan in chat" is narrow. |
| **B.6 Thinking/progress** | Build a staging layer | **Both reference beats already exist**: `PersonalizedAck.tsx` (quiz "analysing…" reveal) + `WLThinkingIndicator.tsx` (chat) | **Consolidate** the two into one staging layer — don't build from scratch. |
| **B.7 Animation** | Folded/cut in v1 | (no change) | Unchanged. |
| **B.8 Upsell** *(was "Ascension")* | Build a trigger-based nurture engine | **Email-sequence engine** (triggers, enrollment), **client-plan system** (quick-win delivery), **Whop subscriptions** all exist. Missing: a *post-purchase* upsell sequence, a value-reached gate, an in-chat CTA, upsell instrumentation, high-ticket booking capture | Design the post-purchase track **on existing rails**: add a `post_purchase` sequence type + value-reached gate (enforces "don't sell before value") + in-chat upsell CTA + the upsell events/report. Defer Calendly/GHL. |

---

## Reopened decision (was "Decided" in v1)

**B.1 quiz paywall model.** v1 marked **"paywall the payoff"** as Decided. But the live funnel does the **opposite** — `lead-quiz-paywall-and-invite.md` (LQ4) shows a **free personalized-plan preview at the paywall**, and `PersonalizedAck.tsx` already reflects answers back pre-payment. So "paywall the payoff" is a **reversal of shipped behavior, not a new build.**
**What we need to decide:** keep the current free-preview model, switch to paywall-the-payoff, or a middle path (tease *structure*, reveal *substance* only post-pay)? Trade-off = conversion lift vs. trust/refund risk. **Decide before any B.1 work.**

---

## Other corrections folded in

- **Qualitative-data caveat softened:** v1 said "no qualitative data / haven't talked to end-users." `customer-research/end-customer-profile.md` + 31 dossiers exist — **start from those**, don't redo.
- **Avatar is real infra:** v1's "reuses avatar infra" is a full talking-head pipeline (`createTalkingHeadVideo` Temporal workflow, `talkingHead` routes, `speaking-head-videos` UI) — feasible today.
- **Governance:** schema/event/contract additions are human-led **PR-A** changes per `tree-architecture.md`; copy/config are Leaves.
- **Two databases:** end-users live in the **WL Supabase**; instrumentation/identity must span main + WL.

---

## Net effect on June

**What v1 thought was the work** (build instrumentation, build streaming, build a component library, build a staging layer, build a nurture engine) is **mostly already there.** The genuinely *new* June work is narrower:

1. **Upsell + return + latency instrumentation** (the missing half of Item Zero) — ~6 events + 1 report.
2. **The post-purchase upsell track** (B.8) on the existing engine + a "don't sell before value" gate.
3. **The live-voice pipeline** (B.4) — gated on the cost-per-call number.
4. **High-ticket booking capture** (Calendly/GHL webhook) — the one fully greenfield piece.
5. **One strategic decision** — the B.1 paywall model.

Everything else is extend/consolidate on rails that already exist.

---

*Deeper drop-in specs: `kodara-june-section-A-rewrite.md` (instrumentation gap + event files), `kodara-june-section-B8-rewrite.md` (upsell track on existing rails), `kodara-june-section-B2-B4-rewrite.md` (Speed + Voice). The full updated roadmap: `june-upsell-swept.md`.*
