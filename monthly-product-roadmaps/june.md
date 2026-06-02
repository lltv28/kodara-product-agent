# Kodara — June Product Roadmap

**Status:** Draft for designer + CTO review. Item Zero (instrumentation) is finalized; all feature sections — B.1 Quiz v2, B.2 Speed, B.3 Reply pacing, B.4 Voice (live phone-call), B.5 Visual components, B.6 Thinking/progress, B.7 Animation (folded into B.5/B.6 — not a standalone feature) — are drafted as ideas for review.
**Last updated:** 2026-05-31
**Owner:** Product (CPO) · **Audience:** Engineering + Design

---

## How to read this (TL;DR)

**What this is:** a set of *product ideas and recommendations* for the design + CTO review — **not a locked spec.** Our team builds the product all ~50 clients inherit; these describe the shared engine and how we'd tailor it per client.

**The one outcome we're anchoring June on:** **end-user retention / return** — framed as a *learning* goal (we've never measured it; Item Zero is what makes it measurable). The Opportunity Solution Tree maps every feature to the customer problem it's meant to serve.

**Build order (dependencies):** Item Zero (instrumentation) first → **B.2** streaming → the shared engines (**B.5** component schema, **B.6** staging layer) → their callers (B.1, B.4). **B.8 Upsell** is top-tier and runs in parallel. **B.7** is folded into B.5/B.6 (no separate build).

**Decided vs. open:**
- *Decided:* the anchor outcome (retention/return); instrument before building; upsell (B.8) is top-tier.
- *Open — for the meeting:* whether to cut to a focused core vs. keep the full menu; standing up a product trio to own it; the ethical line for vulnerable verticals; outcome-based pricing; **the quiz paywall model — "paywall the payoff" vs. the free personalized-plan preview the funnel ships today (was "Decided"; reopened by the codebase review — see Codebase reality check + bottom decisions).** *(See "Doctrine flags," "What's still missing," and the consolidated "Open questions" at the bottom.)*

**How to navigate (it's long):**
- **Engineers** → Section A (instrumentation + the PH-1…PH-10 tickets + the event schema), **B.4 §3** (voice architecture), **B.5 §3** (component schema), and **"Open questions & data we need"** at the bottom.
- **Designers** → the at-a-glance table, the **Opportunity Solution Tree**, and the experience layers (**B.1 §3, B.2, B.3, B.4 §1–2, B.5, B.6**).
- **Everyone** → the at-a-glance table (one-screen overview) + the OST (why these features).

**Honest caveat:** the opportunities in the tree are **hypotheses** — we have **limited fresh qualitative data** (behavioral instrumentation is still coming online), though `customer-research/end-customer-profile.md` plus 31 customer dossiers already capture a synthesized end-user/buyer picture to start from rather than from zero. We're going **data-first for June**; treat priorities as provisional until Item Zero's drop-off data lands. (A reasonable challenge from the room: "have we done fresh end-user interviews?" — not yet; but start from the existing end-customer profile, don't redo it.)

---

## Codebase reality check (verified against `the-kodara/kodara`, 2026-06-02)

This roadmap was first drafted **without** the codebase. Grounded in the actual product, several "build" items are really "extend/consolidate," and one "Decided" item conflicts with shipped behavior. Corrections, each tagged to its section:

- **B.5 (Visual components):** the component **renderer + output schema already exist** — `apps/electron/src/components/chat/messageUiBlocks.ts` + `MessageUiBlocksView.tsx` render typed `ui_blocks` on assistant messages (today only `connector_action` / `facebook_latest_snapshot`, i.e. owner-side). A ResultCard / 2×2 / Dossier is a **new block type in an existing union**, not a new framework.
- **B.6 (Thinking / progress):** the two reference beats already exist — `apps/funnel/src/components/runtime/PersonalizedAck.tsx` (the quiz "analysing…" → typewriter reveal, incl. a 4s thinking delay) and `apps/electron/src/components/chat/WLThinkingIndicator.tsx` ("{Agent} is thinking…"). B.6 = **consolidate these into one staging layer**, not build from scratch.
- **B.3 (Reply pacing):** no length/mode logic exists, **but a per-agent reply rubric does** (`apps/api/src/prompts/agent_reply/reply-rubric.mustache`, `rubric.segments.ts`, `RubricEditorPanel`, incremental rubric generation). That rubric is already the "sounds like me" mechanism — **mode-matching should extend the rubric**, and the feared "50-config per-client voice calibration" is already solved.
- **Channels (Section A scope):** the "web/app is the only surface; no cross-channel identity problem" assumption is **outdated** — **email is live** (`lead_nurture` sequences via Postmark) and **SMS is in progress** (`twilio-10dlc-registration-info.md`). Identity already keys to email/phone, so multi-channel attribution is **partly in scope now**, not "additive later." *(Also corrected inline in §A.1.)*
- **Qualitative data:** softened above — `customer-research/end-customer-profile.md` + 31 dossiers already exist; start there.
- **Avatar (B.1 backlog):** "reuses avatar infra" is real, not hypothetical — a full talking-head pipeline exists (`createTalkingHeadVideo` Temporal workflow, `talkingHead` routes, `speaking-head-videos` UI, agent `avatar_url`).

**Open question for the team (was "Decided" — now reopened):**
- **B.1 quiz paywall model.** The roadmap proposes **"paywall the payoff"** (tease only; never compute/show the answer before the gate). But the **live funnel does the opposite** — `docs/product-specs/lead-quiz-paywall-and-invite.md` (LQ4) shows **a free personalized plan preview at the paywall, built from the quiz answers**, and `PersonalizedAck.tsx` already reflects answers back pre-payment. So "paywall the payoff" is a **reversal of shipped behavior, not a new build.** **What do we think is best:** keep the current free-preview model, switch to paywall-the-payoff, or a middle path (tease the *structure*, reveal the *substance* only post-pay)? Trade-off = conversion lift vs. trust/refund risk — decide before any B.1 work starts.

---

## Strategy context (why this roadmap exists)

**The #1 outcome is improving client outcomes** — how much money each client makes from their AI product (high-ticket revenue is where it's made) and how many end-users they enroll and upsell into it.

The mechanism is a reinforcing chain, not a trade-off between "sides":

> **Delight the end-user → they enroll and get upsold → the client makes high-ticket money → the client stays → the client's now-happy enrolled users make leaving costly (they'd lose the product) → lock-in.**

The validated, evidence-backed consumer-side problem: **the end-user chat experience feels clunky and confusing next to ChatGPT** — the top driver of end-user drop-off and complaints, and not yet something users find fun to return to. That is the first link in the chain, which is why the June work is aimed there.

**The discipline this roadmap enforces:** before spending June making the product nicer, we install the scoreboard. Today we prioritize on intuition because we can't see the funnel. Every feature below claims it will "improve engagement/retention" — but none of that is provable without measurement. **Instrumentation is Item Zero; everything else gets graded against it.**

---

## June plan at a glance

| # | Item | Status | Primary outcome it moves | How we'll know it worked |
|---|---|---|---|---|
| **0** | **Funnel instrumentation (PostHog) — Item Zero** | **Finalized — build first** | Makes all 5 funnel numbers readable per client | Dashboard shows the drop-off point from ≥3 pilot clients |
| **B.1** | **Quiz v2** (the conversion engine) | Ideas for design/CTO review — see §B.1 | Enrollment (cold lead → $17) | Higher cold-lead → $17 purchase rate, without eroding post-purchase trust |
| B.2 | Speed & responsiveness | Ideas — see §B.2 | End-user retention + enrollment (closes the ChatGPT-feel gap) | "Never feels like a wait" — instant ack + streaming; drop-off down during waits |
| B.3 | Reply length & pacing | Ideas — see §B.3 | Retention (turn-to-turn continuation) + upsell | Mode-matched replies beat both current *and* a naive cap on continuation + upsell |
| B.4 | Voice — make it feel "live" (phone-call) | Ideas — see §B.4 | Retention/return + differentiation (a live call in the expert's *own* voice) | Caller return + upsell vs. text; time-to-first-audio <1s feels like a real call |
| B.5 | Visual chat components | Ideas — see §B.5 | Enrollment (teaser) + comprehension/upsell (paid) | One component, two states: locked tease lifts buy; full card lifts action |
| B.6 | Thinking / progress display | Ideas — see §B.6 | Perceived value + anticipation at payoff moments | Dramatized build at plan/verdict gen lifts stay-through; suppressed on short turns |
| B.7 | Animation / motion | **Folded into B.5/B.6 — not a standalone feature; cut welcome animation** | — (motion serves its host feature) | No "animation" metric; guardrail = must not regress time-to-first-value |
| **B.8** | **Upsell / mid-ticket hand-off** | **Ideas — see §B.8 (top-tier)** | **Upsell — where the client's money is made** | Mid-ticket sub + high-ticket intent→confirmed rise per client |

Sequencing rationale: instrumentation makes everything gradable → Speed is the safest, highest-certainty win → cheap A/B-able items → heavier bets tested cheaply before funding.

**Anchor outcome for June (learning-framed):** end-user **retention / return** — *"discover what drives an end-user to come back."* The Opportunity Solution Tree (below, before Section B) hangs every feature off this. Framed as a *learning* goal until Item Zero makes it measurable.

**Cross-feature build order (the real dependencies — several items each say "ship first"):** Item Zero first (makes everything gradable) → **B.2 streaming** (the floor B.3/B.6 are perceived through, so it precedes them) → the shared engines (**B.5 ResultCard + output schema**, **B.6 staging engine**) before their callers (B.1's experience layer, B.4's fillers) → **B.8 Upsell** is top-tier and independent of the chat-feel work, so it can run in parallel. **B.7 is folded** (no separate build). Once Item Zero's drop-off data lands, concentrate on the *one* opportunity with the biggest leak rather than advancing every branch at once.

---

# Section A — Item Zero: End-User Funnel Instrumentation (PostHog) — FINALIZED

**Goal:** Per client, read the 5 numbers we can't see today:
1. **Enrollment** — cold leads who land → buy the low-ticket entry product.
2. **Mid-ticket upsell** — low-ticket buyers → mid-ticket monthly sub.
3. **High-ticket upsell** — leads → the client's existing high-ticket (intent today; confirmed once integrated — see §A.6).
4. **Drop-off map** — the exact step where most leads disappear.
5. **End-user return/retention** — do buyers come back inside the 24–48h window and beyond.

**Reframe to hold onto:** "Implement PostHog" is an output. The outcome is *"we can see, per client, where end-users leak — and grade whether a change fixed it."* If we auto-capture everything, we get a pile of pageview data and still can't answer "what's Sandra's £17→£199 upsell rate." The value is the **event taxonomy mapped to our funnel**, not the tool.

**Scope simplifier (corrected vs. codebase):** the chat product is web/app, but **email is already a live channel** (`lead_nurture` sequences via Postmark, `intake_submitted`→`payment_recorded`) and **SMS is in progress** (`twilio-10dlc-registration-info.md`). Identity already keys to email/phone, so there is **no hard identity-stitching blocker** — but multi-channel attribution (which channel drove the return/upsell) is **partly in scope now**, not purely additive later. WhatsApp remains future.

## A.1 — The funnel we're instrumenting (from our own VSL)

```
cold lead lands → diagnostic/quiz → diagnostic result shown
   → low-ticket entry purchase ($10–100)
   → 24–48h nurture (re-engagement)
   → mid-ticket sub ($199–499/mo)  OR  high-ticket hand-off (client's external booking app)
```

Two parallel upsell exits after low-ticket (mid-ticket sub *or* high-ticket) — model both, don't force a linear order.

## A.2 — Event spec table (implement line-by-line)

Naming: `noun_verb_pasttense`, snake_case. Events fire **server-side**.
**Global properties on every event:** `client_id` (string), `vertical` (string), `session_id` (string), `surface` (string, default `"web"`), plus the **`client` group** attached.

| # | Event name | Trigger (fire when…) | Properties (name: type) | Serves metric |
|---|---|---|---|---|
| 1 | `lead_landed` | End-user's first interaction in a session with no prior `lead_landed` | `traffic_source: string` (ad/organic/pipeline/referral/unknown), `entry_point: string`, `is_first_ever: bool` | Enrollment (denominator), Drop-off |
| 2 | `diagnostic_completed` | The quiz/diagnostic arc completes. **Under the paywall model (B.1) the quiz does not compute a real answer** — for quiz clients this marks "arc completed," not a real diagnosis; the real result is `paid_diagnostic_delivered` (post-paywall, #11). | `diagnostic_type: string` (quiz_v1/conversational), `personalized: bool` (false for the quiz), `turns_to_complete: int`, `time_to_complete_sec: float` | Drop-off |
| 3 | `low_ticket_purchased` | Entry payment confirmed (own checkout success **or** client-Stripe webhook) | `amount: float`, `currency: string`, `offer_id: string`, `payment_source: string` (kodara_checkout/client_stripe), `time_since_landed_sec: float` | **Enrollment** |
| 4 | `nurture_reengaged` | End-user returns/interacts ≥1h after `low_ticket_purchased` | `hours_since_purchase: float`, `session_id: string` | **Return/retention** |
| 5 | `mid_ticket_subscribed` | Monthly sub starts (own checkout **or** client-Stripe `subscription.created`/first `invoice.paid`) | `amount: float`, `currency: string`, `payment_source: string`, `time_since_low_ticket_sec: float` | **Mid-ticket upsell** |
| 6a | `high_ticket_offer_clicked` | AI surfaces the high-ticket offer **and** the lead clicks/redirects out to the client's external booking app | `route_type: string` (calendar/application), `destination_host: string`, `time_since_landed_sec: float` | High-ticket upsell — **intent proxy** |
| 6b | `high_ticket_booking_confirmed` | A **confirmed** booking is received — only where the client has granted Calendly/GHL webhook access (PH-5b) or we own the widget (Phase 3) | `route_type: string`, `booking_source: string` (calendly_webhook/ghl_webhook/native_widget), `time_since_landed_sec: float` | High-ticket upsell — **confirmed** |
| 7 | `generation_latency` | Each AI response is generated and returned | `latency_ms: int`, `tokens_out: int`, `mode: string` (text/voice), `stage: string` (diagnostic/nurture/general), `reply_length_variant: string` (current/mode_matched/naive_cap/unset) | **Grades Speed + Reply-pacing A/B** |
| 8 | `session_started` | A new end-user conversation session opens | `mode: string` (text/voice) | Return/retention (PostHog Retention insight) |
| 9 | `quiz_step_completed` | Each quiz stage completes (per B.1 arc) | `step_index: int`, `step_id: string` | Per-stage quiz drop-off map |
| 10 | `diagnostic_started` | Lead begins the quiz/diagnostic | `diagnostic_type: string` | Drop-off (top-of-quiz) |
| 11 | `paid_diagnostic_delivered` | **Post-paywall:** the paid AI delivers the real personalized result (the actual diagnosis, computed from real inputs) | `vertical: string`, `time_since_purchase_sec: float` | The real diagnostic payoff (paid-AI track) |

**Idempotency requirement (most likely data-quality bug — call it out in tickets):** `low_ticket_purchased`, `mid_ticket_subscribed`, `high_ticket_booking_confirmed` must dedupe — pass a PostHog event `uuid` keyed to the transaction/booking ID, so a webhook retry or a double-fire (own checkout + Stripe webhook for the same sale) never double-counts.

**Do not rely on auto-capture** for these — it won't know what a "diagnostic" or "upsell" is. Auto-capture stays on only for the web app to support session replay later (Phase 3), gated on PII rules.

## A.3 — PostHog setup

**Person identity (keyed to email/phone):**
- `distinct_id` = end-user **email** (lowercased/trimmed); if no email, **E.164 phone**.
- Call `identify(distinct_id, { email, phone, client_id, vertical, first_seen_at, current_stage })` on first event of a session.
- `current_stage` person property updates on each funnel event: `lead → buyer → subscriber → booked`.

**`client` group (the per-client unlock):**
- Group type: **`client`**; attach to **every event**: `groups: { client: <client_id> }`.
- Group properties (set at onboarding, update on change): `client_name`, `vertical`, `low_ticket_price`, `mid_ticket_price`, `high_ticket_price`, `launch_date`.
- **Hard rule:** no event may be emitted without `client_id` + `client` group. Server-side assertion rejects/logs violations (prevents cross-client contamination).

**Feature flags:**
- **Reply-pacing A/B (B.3):** flag `reply_length`, **three arms** — `current` / `mode_matched` / `naive_cap` (the `naive_cap` arm exists to *disprove* a global word cap; `mode_matched` is the recommended approach), sticky per `distinct_id`. Backend applies the variant **and stamps `reply_length_variant` on every `generation_latency` event** so the experiment self-joins. Goal metrics: reply-to-reply continuation + `low_ticket_purchased` + `mid_ticket_subscribed` (upsell) — **not** session length.
- **Voice cohorting + rollout (B.4):** flag `voice_mode_enabled`, boolean — used to **cohort voice-callers vs. text users** (to measure return + upsell) and to **stage the rollout of the live phone-call experience.** *(Voice already exists — this is NOT a demand fake-door.)* Event `voice_input_used` (`{ duration_sec: float }`) reads voice usage and pairs against `nurture_reengaged`.

## A.4 — Day-one dashboard ("Client Funnel" — filter: `client` group + date range)

**Funnel (the leak-finder):**
`lead_landed → diagnostic_completed → low_ticket_purchased → (mid_ticket_subscribed OR high_ticket_offer_clicked)`, breakdown by `client`. *(Final step is intent until `6b` is available per client.)*

**Tiles:**
1. **Enrollment rate** = `low_ticket_purchased / lead_landed` (trend).
2. **Mid-ticket upsell** = `mid_ticket_subscribed / low_ticket_purchased`.
3. **High-ticket upsell (two-state):**
   - Default (no integration): `high_ticket_offer_clicked / lead_landed` — labeled **"Intent — click-through, not confirmed"** with an "estimate" visual treatment.
   - Integrated clients: add `high_ticket_booking_confirmed / lead_landed` (confirmed) **and** `confirmed / clicked` (the correction ratio).
4. **Return rate** = `nurture_reengaged / low_ticket_purchased`, bucketed by `hours_since_purchase`.
5. **Retention** = PostHog Retention insight on `session_started`, per client.
6. **Latency** = median + p90 `latency_ms` by `stage` and `mode`.

VSL benchmarks (10–20% mid / 5–10% high) appear as **reference annotation lines only — hypotheses, never targets.**

## A.5 — MVQ ticket list (ordered; drop into tracker)

1. **PH-1 — PostHog project + server SDK wired.** Done when: server-side SDK initialized; a smoke-test event lands from staging with a `client` group attached.
2. **PH-2 — Identity + `client` group model.** Done when: `identify()` keys to email/phone; `client` group type created; all test events carry `client_id` + group; an event missing `client_id` is rejected/logged.
3. **PH-3 — Funnel events #1, #2, #6a (land → diagnostic → high-ticket offer clicked).** Done when: the three fire from real backend flow on staging with correct properties/types (`6a` fires `route_type` + `destination_host` on redirect-out).
4. **PH-4 — Purchase capture: own checkout.** Done when: `low_ticket_purchased` + `mid_ticket_subscribed` fire from Kodara checkout success, idempotency `uuid` = transaction ID, no double-count on retry.
5. **PH-5 — Purchase capture: client-Stripe webhook.** Done when: a configured client's Stripe `checkout.session.completed` / `subscription.created` fires the same two events with `payment_source=client_stripe`, deduped against PH-4.
6. **PH-5b — Confirmed-booking capture (Calendly + GHL webhooks).** *Phase 2b, per consenting client.* Done when: a test booking on both a Calendly client and a GHL client lands `high_ticket_booking_confirmed`, stitched to that lead's funnel by invitee/contact **email**, deduped by booking-ID `uuid`, no double-count on retry, and the **click→confirmed ratio** renders on the High-Ticket tile. *(GHL uses location-level auth; Calendly uses the client-granted org/user-scoped token; one-time per-client setup checklist documented.)*
7. **PH-6 — Return + retention events #4, #8.** Done when: `session_started` fires per session; `nurture_reengaged` fires on post-purchase return with correct `hours_since_purchase`.
8. **PH-7 — `generation_latency` (#7).** Done when: every AI response emits latency_ms/tokens_out/mode/stage; baseline median/p90 readable.
9. **PH-8 — PII guard + verification.** Done when: automated check (or code-review sign-off) confirms no event property carries conversation/diagnostic free-text; PostHog PII scrubbing + input auto-capture-off confirmed.
10. **PH-9 — Day-one dashboard (§A.4).** Done when: dashboard loads, filters by `client` group + date range, shows non-zero data from ≥3 pilot clients.
11. **PH-10 — Feature flags (Phase 2, queue now).** Done when: `reply_length` three-arm (`current`/`mode_matched`/`naive_cap`) + `voice_mode_enabled` boolean exist, variant stamped on events, and PostHog Experiments configured with **continuation + upsell** goal metrics (not session length). *(The voice flag is for cohorting + staged rollout of the live phone-call experience — not a demand test.)*

**Pilot scope:** 3–5 clients across a spread of verticals, including ≥1 health/finance (exercises the PII path).

## A.6 — The high-ticket blind spot (read this before trusting the number)

**Our single most important number — high-ticket upsell, where the client makes the money — is the one we can least trust today.** Everything upstream happens on our surface and is confirmed. The booking happens on the client's surface (Calendly / GHL / application form), so today we see **intent (a click-out), not confirmed bookings.**

- High-ticket is already our smallest event (~5–10% of leads). The click-out → confirmed-booking conversion could plausibly be anywhere from ~20% to ~70% — **a range wider than the metric itself.** Today's intent number could overstate real bookings by **2–5×**. **Do not report it as revenue or let anyone target it.** Label it clearly on the dashboard.

**Path to closing it (cheapest-first):**
- **(a) Booking-tool webhooks — PH-5b, do next, per consenting client.** Calendly (`invitee.created` / `invitee.canceled`) and GHL (Workflow → Webhook on "Appointment booked," or native appointment webhook) both emit subscribable booking events — **same pattern as the client-Stripe webhook.** Calendly + GHL cover the bulk of clients; the small long tail stays intent-only. Match back to the lead by **email = `distinct_id`**. For integrated clients we get the **click→confirmed ratio**, the empirical correction factor we can apply to estimate true bookings elsewhere.
- **(b) Native calendar widget — Phase 3 (July+), product bet, NOT analytics.** Webhooks remove the *measurement* reason for the widget. The widget stands only on its **conversion-friction** merit: redirecting a high-intent lead off our surface at the most valuable moment is a real value leak. **Gate the widget build on the PH-5b drop-off number** — ship webhooks, measure the leak, then build the widget only if the leak is big enough. Don't build on faith.

## A.7 — Risks & gotchas for engineers

| Risk | Why it bites | Mitigation |
|---|---|---|
| **PII / consent in health & finance verticals** | Diagnostic content can be PHI or financial data | **Never send conversation/diagnostic free-text as properties** — structural metadata only (stage, latency, amount, channel). Enable PostHog PII scrubbing; auto-capture of inputs off; document consent posture per vertical. One-way door on trust. |
| **Client-data isolation** | One client must never see another's data | `client` group + `client_id` on every event; reject events without it; access controls. |
| **Purchase double-count** | Own checkout + Stripe webhook fire for the same sale | Dedupe by `uuid` = transaction ID. |
| **Calendly scope** | Wrong token scope → we miss the client's events | Confirm org/user-scoped token the client grants; match by invitee email; handle `invitee.canceled` to reverse a confirmed booking. |
| **GHL auth** | Agency-level vs. location-level; clients are usually a sub-account | Use **location-level** OAuth/API key (agency-level pulls the wrong account → isolation risk). Workflow-webhook setup is manual per client unless scripted — document a checklist. Match by contact email. |
| **Garbage-in taxonomy drift** | Engineers inventing ad-hoc event names | Lock the taxonomy in this doc; new events need product sign-off; police via PostHog Data Management. |

**Trust guardrail (Wells Fargo lesson):** benchmark lines are reference, not KPIs anyone is incentivized to hit. The instant someone optimizes "upsell" by having the AI push harder, we risk eroding the expert's reputation — the thing the whole chain depends on. Plan to instrument trust erosion too (a future `complaint`/negative-sentiment event), not just conversion.

**Ethical guardrail — vulnerable verticals (the front-page test).** Several mechanics in the roadmap are persuasion devices: the curiosity loop and tease (B.1), the dramatized "thinking" build (B.6). In emotional/vulnerable verticals (sobriety, finance, health), apply the **front-page test** — if a journalist described exactly how this beat works on someone struggling with addiction or debt, would it read as *helpful* or as *manufactured compulsion?* Same posture as the specificity gate: **stricter / more restrained by default in vulnerable verticals**, and the tease/loop must stay tethered to genuine value the product delivers, never engineered pressure on someone who can least afford it. This isn't only honesty (Rule B) — it's the harm question: are we building compulsion into an audience that's vulnerable to it?

## A.8 — Open questions / numbers still needed

- **`diagnostic_completed` signal:** is there a clean "diagnosis reached" marker in the AI pipeline, or does the agent need to emit one? (If the latter, add a small ticket.)
- **Per-client end-user volume (rough):** mid/high-ticket upsell are small-percentage events; low volume = noisy. Determines whether 3–5 pilot clients over ~2 weeks yields usable data.
- **Stripe access model for client integrations:** Stripe Connect vs. per-client API keys/webhook secrets (changes PH-5 effort + security posture).
- **GHL location-level auth** confirmed per client.
- **Long-tail booking tools** beyond Calendly/GHL stay intent-only until handled.
- **Value-metric / outcome-pricing question (forward-looking, not June).** Now that Item Zero makes *client outcomes* measurable, the standing question becomes answerable: Kodara charges a **flat retainer** while its value scales with client outcomes (enrolled/upsold users, client revenue) — the classic "value scales but price doesn't" gap. Once we can read per-client upsell, revisit whether the retainer should evolve toward an **outcome-based value metric.** One-way door on pricing — deliberate, later, but the data this roadmap builds is the prerequisite, so flag it now.

**Next action:** eng lead confirms the `diagnostic_completed` signal source, then runs PH-1 → PH-9 (+ PH-5b for a Calendly and a GHL client) against 3–5 pilot clients. **Target: first real drop-off number on the dashboard within ~2 weeks.**

## A.9 — Post-purchase activation teardown (cheap, do before adding richness)
Before we add experience richness (voice, components, motion) to the post-$17 flow, **map its straight line** — the cheapest high-ROI exercise on this roadmap and one nobody has done. Sign up and buy as a real end-user; **screenshot every single step** from the $17 purchase to the first real value (the diagnostic answer). Color each step: **green** (necessary to reach value), **yellow** (real but can be delayed), **red** (removable). **Delete the red, delay the yellow** — what remains is the highway to the first quick win. This surfaces *ability debt* (every point a just-paid user fails to reach value — where 40–60% of signups typically never return) **before** we spend effort decorating a flow whose friction we've never mapped. Likely outcome: a meaningful share of post-purchase steps are removable, and that cleanup may lift return more cheaply than any new feature. *Output: an annotated step-map + an ability-debt list, feeding Opportunity 1 ("can't tell if it's worth my time") in the Opportunity Solution Tree below.*

---

# Opportunity Solution Tree — the structure under the features (read before Section B)

> **Why this section exists:** the feature list (B.1–B.8) is a set of *solutions*. Before committing to solutions, we should be explicit about the *outcome* we're chasing and the *customer opportunities* (needs/pains) that drive it — then check which features actually serve an opportunity vs. which are solutions in search of one. This turns "should we build all seven?" into "which opportunity matters most, and what's the best way to serve it?"

**June's anchor outcome (learning-framed):** **end-user retention / return** — *"discover what drives an end-user to come back."* Framed as a **learning goal, not a performance target**, because we have never measured it: until Item Zero ships we cannot set "increase return by X%" honestly. June's job is to find *where* end-users leak and *why* they do or don't return — and to start building into whichever opportunity that learning points at.

> **Honesty flag — read this before trusting the branches.** The opportunities below are **hypotheses, framed from the end-user's perspective, not validated truth.** We have behavioral instrumentation coming (Item Zero) but **no qualitative data yet** (no interviews / "tell me about the last time you stopped using it" stories). So this tree is a **starting structure to argue with, not a finding.** The most important caveat in the doc: *we are mapping features onto opportunities we have not yet confirmed are the real ones.* Item Zero's drop-off data plus a handful of end-user conversations would move several branches.

## The tree

```
OUTCOME (root): End-user retention / return
  └─ "Discover what drives an end-user to come back" (learning goal)

  ├─ OPPORTUNITY 1 — "I can't tell if this is worth my time."
  │     (the first-session value verdict; forms in the first minutes)
  │     └─ Served by: B.1 Quiz (sets up the value promise) ·
  │                    B.5 Visual components (legible payoff) ·
  │                    [post-purchase activation teardown — see §A.9]
  │
  ├─ OPPORTUNITY 2 — "It feels slow / clunky compared to ChatGPT."
  │     (the validated, evidence-backed problem)
  │     └─ Served by: B.2 Speed (primary) · B.3 Reply pacing ·
  │                    B.6 Thinking/progress (covers genuine waits)
  │
  ├─ OPPORTUNITY 3 — "I forget it exists / nothing pulls me back."
  │     (the 24–48h window and beyond — re-engagement)
  │     └─ Served by: B.8 Upsell hand-off (primary, NEW) ·
  │                    B.4 Voice (nurture-window check-in hypothesis)
  │
  ├─ OPPORTUNITY 4 — "It doesn't feel like it's really *mine* / really *them*."
  │     (personalization + the expert's presence)
  │     └─ Served by: B.4 Voice (the expert's own voice) ·
  │                    B.1 Quiz "feel seen" ideas · B.5 Dossier
  │
  ├─ OPPORTUNITY 5 — "I got value once but don't know why I'd come back."
  │     (no reason-to-return after the first win)
  │     └─ Served by: B.8 Upsell hand-off · B.5 Plan/checklist
  │                    (a reason to return)
  │
  └─ OPPORTUNITY 6 (BUYER-SIDE) — "Can I trust this AI with my reputation?"
        (the expert/buyer — the one who pays and can churn)
        └─ Served by: almost nothing on purpose — see gap below
```

## What the tree exposes
- **Opportunity 2 (clunky-vs-ChatGPT) is the most crowded** — three features (B.2/B.3/B.6) point at it. Defensible (it's the one opportunity we have *evidence* for), but it means a large share of June's effort rides on one opportunity.
- **B.7 (Animation) serves no opportunity on its own** — consistent with its verdict (folded into B.5/B.6). The tree confirms it isn't a branch.
- **Opportunities 3 and 5 (re-engagement + reason-to-return) were under-served** until B.8 (Upsell) — and these are the branches *closest to the retention outcome itself.* The retention root is served more by re-engagement (Opp 3/5) than by first-impression polish (Opp 2), which argues for B.8's priority.
- **Opportunity 6 (expert trust) is a near-empty branch** — every consumer feature has a home; the side that *pays us and can churn* maps to one barely-served branch (see the expert-trust item under "What's still missing").
- **The honest gap:** we don't yet know the *relative size* of these opportunities (which leak loses the most end-users). That's exactly what Item Zero's drop-off map answers — so **re-prioritize this tree once the funnel data lands**, comparing opportunities by where the bleed actually is, not by where we happen to have features.

**How to use this:** treat it as the compare-and-contrast frame. Once Item Zero shows the drop-off, pick the *one* opportunity with the biggest leak and concentrate there — rather than advancing all six branches at once.

---

# Section B — Feature PRDs (reviewed; to finalize with eng + design)

> These were reviewed against the strategy. The notes below capture the current product judgment and the open work. **Each feature needs a defined outcome metric (readable once Item Zero ships) before it's committed.** Recurring flag: the original "Quality of Life Updates" framing is output language — several items were solutions with an outcome reverse-engineered; we are re-tethering each to one measurable outcome.
>
> **Test all four product risks, not just desirability.** Most A/Bs below test *desirability* (does it lift buy/return/upsell). Before committing engineering, each feature should also have a view on **usability** (can end-users figure it out unaided? — the clunky-vs-ChatGPT problem *is* a usability problem), **feasibility** (can we build it — e.g. can the brain emit the schema / stream tokens?), and **viability** (does it work for our business — e.g. Voice's cost-per-call, §B.4.7.5). A feature can lift a conversion metric while being confusing, unbuildable at quality, or uneconomic.

### B.1 — Quiz v2: The Kodara Client Quiz — Ideas for Design + CTO Review

> **Status: this whole section is a set of *ideas and recommendations* for the designer and CTO to review, pressure-test, refine, and add to — not a locked spec.** Treat every item below as a proposal.
>
> *Context: **our team builds and configures each client's quiz** — clients don't build their own. So these ideas describe a shared **template/engine** our team would reuse and tailor per client. Kat Marsalek's mortgage quiz is used only as an illustrative specimen.*

**Core proposed model:** the quiz's job is to **make the headline promise believable and open a curiosity loop — without paying it off.** The real diagnostic (real inputs, real answer) lives **behind the $17 paywall, in the paid AI.** The feeling we're after: the quiz should feel like **the first turn of the AI itself**, not a form that leads to it — so the buy feels like "keep talking to something that already gets me," not "gamble on a tool."

#### 1. The core recommendation
Build the quiz as a **reusable engine** — one shared template our team configures per client — rather than a funnel built from scratch each time. Two proposed guiding principles (worth the team's scrutiny):
1. **Paywall the payoff** — tease the answer, don't give it away free.
2. **Don't promise the payoff for free** — copy may say "your answer exists and it's personal to you," never "free / yours to keep before you pay."

Why: it turns quiz creation from bespoke work into configuration — every client gets a proven, trust-safe quiz faster, and an improvement to the engine lifts all of them at once.

> **Model-fit question (name it):** is the $17 entry a **tripwire** (self-liquidates the client's ad spend, per the VSL), a **paid trial** of the AI, or **the product**? These imply different post-purchase jobs (a tripwire must hand off hard to upsell; a trial must convert; a product must retain on its own). The paywall model assumes "tripwire → upsell" — worth confirming that's the intended frame, because it sets what B.8 must do.

#### 2. Proposed standard stage arc
Every client quiz would be built from these stages, in order. The **arc stays standard**; each stage is a slot **our team tailors per client/vertical**. *(optional)* = stage may be omitted per vertical.

| # | Stage | Job | What our team tailors per client |
|---|---|---|---|
| 1 | **Hook** | Land the headline; assert a specific, personal answer exists | Headline promise + sub (per Rule B) |
| 2 | **Qualify / Segment** | Sort into the variant the offer serves; make them feel *seen* | Segmentation questions + options |
| 3 | **Agitate** *(opt)* | Surface the pain the answer addresses | Pain framing |
| 4 | **Micro-commit** *(opt)* | Cheap yes that builds momentum | Commitment device + prompt |
| 5 | **Aspire** | Attach the answer to a desired future | Aspiration question |
| 6 | **Reframe** | Teach the insight that makes only this product able to deliver the answer | The reframe insight |
| 7 | **Results-tease** *(opt)* | Dramatize the payoff is real & imminent — **without delivering it** | Tease medium (visual / narrative / none) |
| 8 | **Curiosity-close** | Name the answer exists & is one step away; open the loop at max tension | Closing line |
| 9 | **Gate (email)** | Capture identity — the identify pivot (§7) | CTA copy |
| 10 | **Offer** | Single low-ticket price, one CTA | Offer, price, proof |
| → | **Hand to paid AI** | Post-purchase: paid AI collects real inputs, computes the real answer, **pays off the loop** | The vertical's real diagnostic + inputs |

**Proposed design rule:** stages 1–8 qualify and tantalize; the **answer is never computed or shown** before the gate. Real inputs + computation live **only in the paid AI**, after stage 10.

#### 3. ★ The six experience ideas (the heart of this doc — for review)
> These are **ideas to evaluate**, not committed features — designer + CTO should react, refine, drop, or add. They're what would make the quiz feel like an intelligent 1:1 diagnostic instead of a static form. Each is tagged **[Platform]** (built once, every client gets it) or **[Slot]** (tailored per client), is paywall-safe and honest, and includes a cheap way to test it before any heavy build.

**A. Living Diagnostic — the quiz IS the AI, conversational from turn one. [Platform]** *(top bet)*
Replace the static multiple-choice march with a real turn loop: the cloned expert AI asks, the lead answers (tap *or* free-type), and the AI **reflects the answer back in the expert's voice** before the next question. The stage arc is the rails; the felt experience is dialogue.
- *Mortgage:* "I refinanced but repayments barely moved" → AI: "Right — because you optimized the *rate* and ignored the other two levers. Hold that thought; it's exactly what your number will show."
- *Why it converts:* it *is* the differentiator made tangible pre-purchase — buying becomes "keep going with something already working," and it pre-empts the clunky-vs-ChatGPT complaint by setting the conversational standard before they pay.
- *Cheap test:* ship a **scripted, rules-based reflection** (one templated line per answer, no LLM). A/B reflections on/off; goal = buy + post-paywall return. If scripted lifts buys, real will lift more.

**B. Visible Dossier — a profile that fills in as they go, payoff locked. [Platform]** *(top bet)*
A persistent element that **assembles a personalized profile in real time** as they answer — "Your Profile: Stress-driven pattern · 5+ years · High reversibility" — while the **payoff cell stays sealed** ("Your exact number: 🔒 unlocks with your AI").
- *Why:* the cleanest resolution of "deeply seen but withheld" — everything *qualitative* is given generously (builds "it knows me"), the one *quantitative* payoff is visibly locked (curiosity loop to a peak). The lock is honest — it never promised the number free.
- *Cheap test:* add the dossier component with a locked payoff cell; A/B vs. none. Goal = buy rate.

**C. Almost-There blurred reveal — show the payoff's shape, seal its substance. [Platform, copy = Slot]** *(top bet)*
Right before the gate, **render the payoff blurred/redacted** (the chart with the number pixelated, the plan with the key line blacked out) while the AI says "I've got your number. It's higher than most people guess. Let's get it to you."
- *Why:* proximity + specificity + surprise = maximal curiosity exactly where the buy is won or lost. Honest as long as copy doesn't say "here's your free number."
- *Cheap test:* add the blurred-reveal beat before the gate; A/B vs. plain curiosity-close.

**D. Adaptive branching that visibly reacts. [Platform engine, branch logic = Slot]**
The next question changes based on the last answer, and the AI **names that it's adapting**: "Because you said X, I'll skip ahead — that doesn't apply to you."
- *Why:* "it's not asking everyone the same canned questions" is the strongest felt-intelligence signal, and skipping irrelevant questions also cuts friction.
- *Cheap test:* one visible branch fork on the highest-variance segmentation question; measure completion + buy vs. linear.

**E. Free-text "in your own words" echo. [Platform]**
One or two open text inputs where the lead types freely and the AI **echoes a specific phrase back**: "You said you feel 'invisible' — that word matters, and it's exactly what we'll address."
- *Why:* their own words mirrored back is peak felt-personalization and the most ChatGPT-like beat. Honest: we reflect, we don't *answer*.
- *Cheap test:* one free-text beat at the aspire stage; even keyword-echo (no LLM) validates it.

**F. Cohort "people-like-you" mirroring. [Slot]**
After segmentation, reflect their cohort as an identity: "You're in the group we call the 'Plateaued Optimizers' — about 1 in 4 people here, and the ones who get the biggest swing from this."
- *Why:* identity + belonging + "the biggest swing is available to *you*" raises desire and self-relevance. Honest (directional, not the figure).
- *Cheap test:* add a cohort-name interstitial post-segmentation; measure buy lift.

**Where they sit in the arc:** the conversational turn loop (A) + branching (D) + echo (E) run *through* stages 2–6; the Dossier (B) is persistent across all stages; cohort mirroring (F) fires right after Qualify/Segment; the blurred reveal (C) is the Results-tease/Curiosity-close beat (stages 7–8), immediately before the gate.

#### 4. What stays standard vs. what our team tailors per client

| **Standard across all client quizzes (the engine)** | **Tailored by our team per client** |
|---|---|
| The 10-stage arc + ordering | Headline promise + sub |
| Paywall-the-payoff model | The questions |
| Rule A (question→headline alignment) | The reframe insight |
| Rule B (trust copy: no free-payoff promise) | Results-tease **medium** (visual/narrative/none) |
| Free-vs-paid line | Micro-commit device (or omit) |
| Core experience components A–F (as platform capabilities) | Cohort names, branch logic, reflection copy |
| Email-gate identify pivot + event taxonomy | Offer, price, proof assets |
| Template-instantiation QA gate | The real diagnostic + inputs (in paid AI) |
| Single low-ticket offer, one CTA | Copy voice (the expert's) |
| Success metric = enrollment + post-paywall return | — |

Left = the standard engine every client inherits. Right = what our team tailors when building a client's quiz. **That split is the product.**

#### 5. Platform rules (the real product) + the honest line
**Rule A — Alignment.** Every question (stages 2–6) must visibly serve the headline promise, so the promise feels personally relevant. The question set is the *proof the promise applies to you*, not the delivery of it.
**Rule B — Trust copy (proposed, important).** Headline + pre-gate copy may promise a specific, personal answer *exists*; they should NOT promise it is *free, kept, or delivered before purchase.*
- *Honest tease:* "Find out what this is really costing you — your AI runs your real numbers and gives you your exact figure."
- *What to avoid:* copy like "a personalised number you keep, whether you ever speak to us or not" — that promises a free, take-home payoff and then paywalls it (bait-and-switch). The pre-launch copy check (§8) keeps every client's quiz on the right side of this.

**Free-vs-paid line (proposed policy):**

| Free, in the quiz (tease) | Paid AI, post-$17 (payoff) |
|---|---|
| Names the problem; confirms it applies to *them* | The **exact** number / result / plan |
| **Directional, non-numeric** signal ("this is likely costing you a lot") | The **real inputs** collected + the **computation** |
| The reframe | The kept, take-home answer |

Free = qualitative + directional. Paid = quantitative + specific. Directional teasers must stay **defensible** — the paid AI must show its work; no manufactured alarm. This is the Wells-Fargo trust guardrail rendered as platform policy.

#### 6. Proof it generalizes — one framework, three verticals
The framework is universal only if it holds across unlike verticals.

| Stage | **Sobriety coach** | **Weight-loss coach** | **Mortgage/debt** *(Kat = one instance)* |
|---|---|---|---|
| Hook | "Discover the real reason you reach for a drink — and what it's costing you." | "Find out which of 5 metabolic patterns is stalling your weight loss." | "Find out what your bank is really costing you." |
| Qualify | Drinking pattern / triggers | Diet history / goal | Loan stage / situation |
| Reframe | "Willpower is the wrong lever; the trigger is." | "Diets fail; your *pattern* is the lever." | "You've been shown rate, not balance+term." |
| Results-tease | **Narrative** tease — *no chart* | Visual "pattern-match" tease | Trajectory chart *(Kat's choice)* |
| Curiosity-close | "Your AI can name your exact trigger — unlock it." | "Your AI has your pattern + the fix — one step away." | "Your AI is ready to run *your* numbers." |
| Paid AI pays off | Collects history → names the trigger | Collects inputs → pattern + plan | Collects balance/rate/term → the real figure |

**This exposes a real config need:** the results-tease **medium** must be a first-class per-vertical slot — sobriety wanted *no chart*. The trajectory chart is **not** universal; hard-coding it would be Kat-anchoring at the component level.

#### 7. Measurement (for CTO review)
Proposed events, firing identically across all client quizzes, sliced by the `client` PostHog group (ties into the Section A instrumentation work).

| Stage | Event | Notes |
|---|---|---|
| Hook | `lead_landed` | `traffic_source`, `entry_point="quiz"` |
| First question | `diagnostic_started` | promoted to MVQ for quiz clients |
| Each stage | `quiz_step_completed` | `step_index`, `step_id` — universal per-stage drop-off map across all 50 |
| Curiosity-close | `diagnostic_completed` | `personalized:false` (the quiz never personalizes the answer) |
| Gate | **`identify(email,…)`** | identity pivot — merges anonymous pre-gate events into the known person; email = canonical `distinct_id` |
| Offer | `low_ticket_offer_shown` | `offer_price` |
| Purchase | `low_ticket_purchased` | `amount` ($17), `payment_source` |
| Paid AI | `paid_diagnostic_delivered` | **paid-AI track**, where the payoff + real inputs live — not quiz scope |

**PII:** structure only — never questions answered, never loan/health content (critical for finance/health). The real diagnostic, its inputs, and the deterministic calculator all live in the **paid AI**, not the quiz.

#### 8. Operational idea: a pre-launch review checklist
Since our team spins up each client's quiz from the template, the recommended standard: a **review checklist every client quiz passes before going live** — no unfilled placeholder copy, all copy matches the client's vertical (Rule A), exactly one offer + price and one CTA, a trust-copy check (Rule B), and an honest progress indicator. Cheap, and it keeps quality consistent across every client as the roster grows.

#### 9. How we'd measure success (proposed)
- **Primary:** enrollment = `low_ticket_purchased / lead_landed`, per client and template-wide.
- **Not:** quiz completion (vanity).
- **Guardrail:** post-purchase `nurture_reengaged` + refund/complaint rate. Paywalling the payoff *will* raise buys; the guardrail proves buyers got real value and weren't tricked. Buys up + returns down = the tease crossed into bait-and-switch → tighten Rule B. Measured template-wide *and* per-instance, so evidence (not the loudest client) drives the next iteration.

#### 10. Secondary experience backlog (after the core six)
Real but lower-priority; pull in once the core layer ships and instrumentation shows where the bleed is:
- **Richer input types** (drag-the-payoff-year slider, this-or-that binaries, card sorts) replacing endless multiple-choice — a platform component library, per-stage configurable.
- **Momentum craft:** accelerating progress bar + ~1s "considering" beats (tied to Speed work, not real lag).
- **One-thumb mobile choreography** + light haptics (auto-advance, big tap targets) — pure drop-off reduction on the dominant surface. *Could jump to Tier 1 if per-stage data shows mid-quiz bleed.*
- **Expert voice/face in the diagnostic** (short avatar clip at the reframe — reuses avatar infra).
- **Cohort-matched proof** (a testimonial from someone in their cohort, dropped at the hesitation point).
- **Surprise framing + ordering** (copy/config-only): seed "it's not what you think," front-load easy taps for a yes-streak, gate at peak tension.

#### 11. Suggested next step — designer + CTO review
This doc is for the designer and CTO to react to: which ideas to pursue, drop, or add, and what's feasible. A possible starting point if the direction lands:

| # | Possible action | Who |
|---|---|---|
| 1 | Turn on per-stage tracking to get a **drop-off baseline before any rebuild** (`quiz_step_completed`, `diagnostic_started`, gate `identify`); keep real-inputs / calculator / result on the **paid-AI side** | Eng / CTO |
| 2 | Shape the **standard stage arc** + a small **component library** for the optional slots | Design |
| 3 | Build the core experience ideas — **scripted Living Diagnostic (A)**, **Visible Dossier (B)**, **blurred reveal (C)** first; then **branching (D)**, **free-text echo (E)**, **cohort mirroring (F)** | Design + Eng |
| 4 | Pilot the arc across **3 verticals** (sobriety / weight-loss / mortgage) to confirm it generalizes | Design + CPO |
| 5 | A/B the changes; grade on **buy + post-paywall return, never completion** | Eng + CPO |

**Cheapest first experiment:** the **copy/config-only** ideas (question ordering, "surprise" framing, scripted reflection lines from idea A) could run as a **single A/B on current traffic — zero new components** — for a fast read while the Dossier and blurred-reveal get designed. **Note:** we don't yet have the template-wide v1 quiz→purchase rate or per-client variance — the tracking in action #1 produces that baseline, which everything else is measured against.

### B.2 — Speed & Responsiveness — Ideas for Design + CTO Review

> **Status: ideas and recommendations for the designer + CTO to review, refine, drop, or add — not a locked spec.** Universal: this is the end-user chat *product* all ~50 clients inherit; our team builds it.

**Core recommendation (reframes the feature):** the bar is **not** "be as fast as ChatGPT" — it's **"never make the user feel the wait."** ChatGPT isn't actually fast; it's *continuously responsive* (instant acknowledgement + streaming, so you never stare at nothing). Our clunky feeling is almost certainly **dead air** (silence between send and first visible response), not raw model latency. So spend the first sprint making the chat **never silent** (perceived-speed) before spending a dollar on infra (real-latency).

**Hard dependency:** target this with the `generation_latency` data from Section A (`latency_ms` / `stage` / `mode`). Until it's live we're prioritizing on inference — the per-stage numbers could reshuffle Tier 2/3.

#### 1. What "fast enough" means (proposed targets for CTO to react to)

| Moment | Target feel | Proposed metric | Type |
|---|---|---|---|
| User hits send | Instant acknowledgement | time-to-acknowledgement **< 100ms** (UI reacts before any model call) | Real responsiveness |
| First words appear | Barely-there wait | time-to-first-token **< 1s** (p90) | Real |
| Reading the answer | Keeps pace with reading | stream **≥ ~30–50 tokens/sec** | Real |
| Heavy output (plan) | Something useful immediately | time-to-first-meaningful-paint **< 1.5s** (title/first item) | Real |
| Between messages | Never blank | **zero frames of dead air** | Perceived (honest) |

#### 2. Where latency hurts most (ranked spend, worst pain first)
1. **First impression** (first AI message after purchase / first chat open) — sets the verdict; spend here first.
2. **The diagnostic moment** — dead air breaks the "intelligent conversation" illusion right where we sell it.
3. **Plan / long-output generation** — tolerable *if* we paint progressively; the pain is "blank screen," not "took 4s."
4. **Between-message lulls** — lowest pain each, highest frequency; cumulative drag.

#### 3. The ideas (perceived vs. real, tagged)

**Tier 1 — cheap perceived-speed wins (ship this sprint, all platform, honest):**
- **A. Token-by-token streaming everywhere [Real-ish] — the single biggest win.** If any path waits for full completion before rendering, this alone likely closes most of the gap (you read as it writes; total length stops mattering). *Test:* A/B streaming vs. buffered; measure continuation + drop-off.
- **B. Sub-100ms acknowledgement [Perceived, honest].** The instant the user sends, the UI reacts before the model is called (message animates in, typing indicator appears). Makes a 1.5s wait feel like 0.3s. *Test:* perceived-speed pulse + drop-off.
- **C. Optimistic UI for user actions [Perceived, honest].** Non-AI actions (send, mark task done, select) update instantly and sync in background, with rollback on failure. *Test:* tap-to-feedback time before/after.
- **D. Progressive / skeleton rendering for structured output [Perceived, honest].** Paint the frame immediately (title, empty slots, first item) and fill as it generates — never a spinner over a blank region. *Test:* A/B skeleton vs. spinner; measure abandonment during generation.

*Tier-1 verdict: these four are the bulk of the win — all platform, cheap, real UX not lipstick. Ship regardless of the latency data.*

**Tier 2 — real-latency work (gate on the `generation_latency` data):**
- **E. Context pre-warming [Real].** Warm history/plan/context on chat open so the *first* turn isn't cold. Targets pain-point #1. *Worth it only if first-turn latency is measurably worse than later — the data tells us.*
- **F. Parallelize multi-part generation [Real].** Generate plan outline + detail concurrently. *Only matters if plan-stage latency is actually high.*
- **G. Shorter replies — joint A/B (ties to B.3).** Fewer tokens = faster completion *and* less to read; A/B *jointly* with streaming since they interact. (Keep B.3's guardrail: don't truncate it out of sounding like the expert.)

**Tier 3 — only if the gap persists after Tier 1+2:**
- **H. Model/inference tuning [Real, CTO-led].** Right-size the model per task, faster provider, prompt trimming. **Guardrail: never downgrade the model on the diagnostic or any reasoning-heavy turn to shave ms — gate behind a quality-held A/B.** *CPO's bet: lower priority than it looks; don't touch infra until the data proves raw model time (not dead air) is the bottleneck.*
- **I. Speculative / prefetch next turn [Real, advanced].** Begin generating the probable next response while the user reads/answers. High effort, wasted-compute risk; last resort.

#### 4. Honest guardrail
**No quality-for-speed trades on reasoning turns.** Ideas A–F are free of quality cost. Model downsizing (H) is the one that can cost quality → gate it behind a quality-held A/B (human-rated quality must not regress). The diagnostic is the differentiator; we don't make it dumber to make it faster.

#### 5. Universal vs. per-client
Almost entirely **universal/platform** — clients touch none of it. Two things that could vary (flagged so we don't build a special): **avatar/voice rendering latency** (heavier path; treat as a platform capability with a `mode` dimension — text vs. voice/avatar — not a per-client build), and **vertical prompt length** (measure per `vertical`, but the fix — prompt trimming — is platform).

#### 6. Prioritization + first move
**Tier 1 (ship this sprint, no data needed):** streaming (A) → instant-ack (B) + optimistic UI (C) → skeleton rendering (D). **Tier 2 (gate on data):** pre-warming (E), parallel plan-gen (F), shorter-replies joint A/B (G). **Tier 3 (last resort):** model tuning (H), prefetch (I).
**Cheapest first move:** ship **streaming + instant-ack** to a traffic slice this sprint, A/B vs. current, grade on **session continuation + drop-off** (+ a perceived-speed pulse). Hypothesis: that alone closes most of the clunky-vs-ChatGPT gap, and the latency data then says whether any Tier 2/3 infra work is even warranted. *Spending infra money before reading `generation_latency` is optimizing on a guess.*

**Numbers we'd want (don't have):** current time-to-first-token + tokens/sec (CTO likely knows today — *the* number that decides perceived-vs-real); per-stage `generation_latency` (hard dependency for Tier 2); **whether any response path is currently buffered rather than streamed** (if yes, that's the smoking gun and A jumps even higher); avatar/voice-mode latency vs. text.

### B.3 — Reply Length & Pacing — Ideas for Design + CTO Review

> **Status: ideas and recommendations for the designer + CTO to review, refine, drop, or add — not a locked spec.** Universal: the chat *product* all ~50 clients inherit; our team builds it.

**Core recommendation (reframes the roadmap item):** **Don't ship a global word cap. Ship *response-mode matching* — the AI picks a length register based on the kind of moment it's in: terse for back-and-forth, room to breathe for the teaching/diagnostic/verdict moments.** The roadmap item is titled "Shorter Agent Replies / 50–75 word max" — that's the wrong instinct. A hard cap is the fastest way to flatten the one thing that differentiates us (a substantive diagnostic in the expert's voice) into a generic curt bot.

*Why:* the "wall of text" complaint isn't about *length* — it's about **length mismatched to context.** 200 words answering "yeah that makes sense" is clunky; 200 words answering "walk me through what's actually wrong with my situation" is *the product working.* ChatGPT doesn't cap length — it *modulates* (short when chatting, long when you ask it to explain). Copy the modulation, not a ceiling. *Ties:* this is a Speed lever (fewer tokens = faster, B.2), the content layer under the Living Diagnostic's conversational feel (B.1), and near-mandatory for the voice-call mode (B.4 — nobody wants a 200-word monologue read aloud).

#### 1. The right rule — response-mode matching
The AI classifies each turn into a *response mode* and writes to that mode's length register (a register tied to conversational intent, not a word count):

| Mode | When | Length register |
|---|---|---|
| **Acknowledge** | User shares feeling/status; momentum turn | 1–2 sentences, warm |
| **Probe** | Gathering diagnostic info | 1 short reflection + **one** question |
| **Teach / reframe** | The differentiating insight moment | Room to breathe — a real explanation, *chunked* (§2) |
| **Verdict / diagnose** | Delivering the assessment | Substantive but structured (often a visual component, not a wall) |
| **Transact** | Logistics, next step, offer | Brief, directive |

**Heuristic: *default to short; earn length.*** Most turns are Acknowledge/Probe (short — where the clunky feeling lives, so where shortening helps most). Length is *spent deliberately* at Teach/Verdict moments where substance is the value; the AI treats a long reply as something it must justify by the moment, not a default. A cap can't tell "yeah" from "explain my whole situation" — mode-matching can. *Universal platform — ships once for all clients.*

#### 2. Conversational chunking / one-question-at-a-time
When a turn *needs* length, deliver it as back-and-forth, not a wall:
- **One question per turn** — never batch questions; ask one, let them answer, ask the next. (The roadmap's one genuinely right instinct — keep it; raises response rate.)
- **Chunk-and-check on long content** — lead with the headline + a hook, then *offer* the depth: "The short version: you've been optimizing the wrong number. Want me to show you which one?" They control the depth; substance is available but never force-fed.
- **Interaction with B.1 (Living Diagnostic):** chunking *is* what makes the diagnostic feel like a conversation, not a form spitting paragraphs — the reflection-then-question rhythm and Probe mode are the same mechanic; build as one thing.
- **Interaction with B.4 (voice-call):** calls *demand* this — a spoken 200-word reply is unbearable. **Voice mode forces a tighter register than text**, with Teach moments chunked into spoken back-and-forth ("want me to keep going?"). Length register is **mode-aware (text vs. voice).**

#### 3. Tie to Speed (B.2) — A/B them jointly
Shortening the high-frequency Acknowledge/Probe turns cuts latency on exactly the turns that happen most — so this is partly a speed feature. But length and speed are confounded (shorter *looks* like a speed win and vice versa), and **streaming changes how length is even perceived** — a long reply that *streams* reads fine; a long reply that lands as a buffered wall feels clunky. So **sequence streaming first, then measure whether length still needs tightening**, so we don't over-cut to solve a problem streaming already fixed. *Test:* factorial A/B — streaming × mode-matching — to separate "clunky because slow" from "clunky because long."

#### 4. Guardrail — length is part of the expert's voice
Shortening must not truncate the AI out of **(a) the expert's voice** or **(b) the substance that makes the diagnostic valuable.** Two failure modes: **voice-flattening** (some experts are expansive — a cap files off their cadence and they hear "that doesn't sound like me," the buyer-side trust failure we can't afford) and **substance-gutting** (cap the real reframe to 50 words and we've shipped a fast generic bot). The honest line: **short where length adds nothing (chatter); full where length *is* the value (the insight).** The test of a good cut is "did we remove filler," not "did we hit a word count." Guardrail check belongs in the §6 expert "sounds like me" review + the upsell metric. (The B.1 §8 QA gate covers quiz-*launch* copy, not runtime chat voice — so the "sounds like me" check is the right home for this.)

#### 5. How to control it — universal vs. per-client
- **Universal (platform):** the **mode-matching logic + length registers** live in the core response policy all clients inherit (the bulk of the feature; ships once). One-question + chunk-and-check are universal behaviors.
- **Per-vertical bias (slot):** a *dial*, not a rewrite — emotional verticals allow slightly more room in Acknowledge/Teach; transactional lean shorter.
- **Per-client voice calibration (slot):** a *light* adjustment so registers respect how *this* expert actually talks (drawn from their existing content) — what keeps shortening from flattening voice. A bias on the universal registers, **not** a per-client prompt fork (that's a special + a 50-config maintenance nightmare).
- **Per-mode:** voice forces the tighter register (§2).
- **Resist:** a per-client free-text "make replies this long" knob → 50 divergent configs and bespoke drift. Keep control to *universal logic + bounded dials.*

#### 6. Measure it right
**Primary: reply-to-reply continuation + upsell**, cohorted by the `reply_length` flag (arms: `current` / `mode_matched` / optionally `naive_cap`): continuation = did the user send another message (turn-to-turn survival); upsell = `mid_ticket_subscribed` + `high_ticket_offer_clicked`. **NOT "did replies get shorter"** — that measures the lever, not the outcome (we could shorten everything and tank upsell by gutting the diagnostic, and "got shorter" would call it a win). **Guardrail metric:** a "sounds like me" qualitative expert review on a sample, to catch voice-flattening before it becomes buyer churn.

#### 7. Prioritization + first move
- **Tier 1 (cheap, high-leverage):** (1) mode-matching response policy (default-short, earn-length) — largely a prompt/policy change, low effort/high impact, replaces the word cap; (2) one-question-at-a-time; (3) A/B jointly with streaming (B.2).
- **Tier 2:** (4) chunk-and-check on Teach moments (ties to B.1); (5) voice-mode tighter register (B.4 dependency).
- **Tier 3 (data-gated):** (6) per-vertical bias + per-client voice calibration — add only where the A/B shows a vertical/expert systematically mismatched; don't pre-build 50 configs.

**Cheapest first move:** write the mode-matching policy and run a **three-arm A/B — current / mode-matched / naive-50-word-cap — jointly with streaming**, via the `reply_length` flag. Grade on **continuation + upsell**, never word count. Hypothesis: mode-matched beats both — fixes clunky where current fails *and* keeps the depth the naive cap destroys. The naive-cap arm exists to **kill the roadmap's "50–75 word max" instinct with data, not opinion.**

**Numbers we'd want (don't have):** actual end-user complaints behind "walls of text" (are they objecting to *length* or to length *in the wrong moments*? — 10 real complaints could collapse this to "just fix the Probe/Acknowledge turns"); current avg reply length by conversational moment (do we even have a length problem at Teach moments, or only in chatter?); whether streaming (B.2) ships before or after this (changes whether we measure length pre- or post-streaming); per-expert voice profiles (enough content to calibrate register without flattening?).

### B.4 — Voice Mode: Make It Feel "Live" (Phone-Call Mode) — Ideas for Design + CTO Review

> **Status: ideas and recommendations for the designer + CTO (and founder + security on the guardrail) to review — not a locked spec.** Universal: the chat *product* all ~50 clients inherit; our team builds it.
>
> *Context: we already HAVE voice input and output. This is **not** "should we build voice." The goal is making the existing voice feel **live — a ChatGPT-voice-style interface that feels like a phone call with the AI.** It does NOT need to be literal real-time duplex; it's a **fast-feeling turn-based pipeline**: user speaks → our existing AI brain generates → ElevenLabs speaks. The whole design problem is making a fast turn-based loop feel like a live call.*

**Core recommendation (the architectural fork everything hangs on):** **The "live" feeling reduces to one thing — kill the gap between "I stopped talking" and "I hear the first word back."** The highest-leverage build is the **streaming chain** (token-stream from our brain piped straight into **streaming TTS**, so the AI starts *speaking* before it's finished *thinking*) **+ auto end-of-speech detection** (so the user never taps). Those two together — no tap to send, no wait for full generation — are what make turn-based *feel* duplex. Build them first; everything else is polish.

*Why:* ChatGPT voice mode isn't real-time either — it's turn-based under the hood. It feels live because it nails three perceptual cues a human phone call has: **it knows when you stopped** (VAD, no button), **it starts talking almost instantly** (streamed TTS over streamed tokens), and **you can cut it off** (barge-in). Nail those three and a turn-based pipeline is indistinguishable from "live"; miss any one and it feels like a walkie-talkie.
*Dependency:* this is the heavy-latency **`mode=voice`** path from the Speed work (B.2) — same streaming techniques, audio at the end — and rides the existing `voice_mode_enabled` / `voice_input_used` instrumentation for cohorting.

#### 1. The "phone-call" UX (the feel we're copying)
Design it as **a call, not a chat with a mic:**
- **Full-screen call UI** — text chat recedes; the expert's avatar/orb centered; a prominent **hang-up**. Feels like tapping "call" on a contact.
- **Three unmistakable states:** **Listening** (orb/waveform reacts live to the user's voice, so they *see* it hearing them), **Thinking** (brief, covered by fillers — §2), **Speaking** (animates to the audio). The user never wonders "is it my turn?"
- **Hands-free continuous listening** — no tap each turn; after the AI speaks it auto-returns to Listening. This is the core of call-vs-walkie-talkie.
- **Hang-up + graceful exit** — one tap ends the call and drops back into the text thread, conversation preserved (call and text are one continuous relationship).
- **Text stays first-class** — a visible "switch to typing" for the self-conscious moment or a noisy room. Voice is a mode, not a trap.
*Universal platform — one call UI all clients inherit.*

#### 2. How to make turn-based feel live (the meat) — ranked by impact on the "live" feel
1. **Stream brain tokens → streaming TTS (start speaking before the reply is done). [Highest / Platform]** Don't wait for full text → synthesize → play (three serial waits). As the brain emits tokens, feed them into streaming TTS so the first sentence's audio starts while the rest generates. Collapses perceived turn-around to ~first-sentence latency. **If we do only one thing, this is it.** *Test:* A/B streamed-chain vs. generate-then-speak; measure time-to-first-audio + call continuation.
2. **Auto end-of-speech detection (VAD). [Highest / Platform]** User stops, it responds — no button. The difference between "call" and "push-to-talk radio." Tune for natural pauses (don't cut them off; don't lag after they're clearly done). *Test:* tune silence threshold; measure mis-fires vs. lag.
3. **Barge-in (interrupt by speaking). [High / Platform]** Talk over the AI and it stops and listens — like interrupting a person. Critical for the illusion *and* for not trapping users in a reply they don't want. *Test:* enable; measure interrupt usage + continuation.
4. **Conversational fillers to cover think-time. [Med-high / Platform — now permitted]** Cover the Thinking gap with natural call-fillers in the cloned voice — "mm, okay…", "let me think about that…", a soft "right." Exactly what humans do on calls, so it reads natural. *Honest line (still matters):* generic conversational acknowledgement is fine; a filler must never assert a false claim/process ("analyzing your data…" when it isn't). *Test:* fillers on/off during the gap; measure perceived-liveness + continuation.
5. **Pre-warm + speculative start. [Med / Platform — gate on Speed data]** Pre-warm brain context on call-open (first turn is the worst gap); where the next turn is predictable, begin generating during the user's pause. *Test:* first-turn time-to-first-audio warmed vs. cold.

*Ranking logic:* 1+2 are the floor (no streamed audio + no-tap = not a call). 3 makes it feel human. 4 papers over the unavoidable think-gap cheaply. 5 is real-latency tuning the Speed instrumentation targets.

#### 3. ElevenLabs + the build-vs-platform choice (CTO call — options, validate vs. current ElevenLabs capabilities)
- **Option A — Assemble it ourselves:** STT → **our brain** → ElevenLabs **streaming TTS** (low-latency model); we own VAD, barge-in, the streaming chain, fillers. *Pro:* full control of brain, guardrail boundary, UX. *Con:* we build/own the real-time orchestration — more eng.
- **Option B — Use ElevenLabs' Conversational-AI / agent layer with our brain as the custom LLM:** their stack handles STT + turn-taking + VAD + barge-in + streaming TTS; **our brain plugs in as the LLM** so the intelligence stays ours. *Pro:* they've already solved the hard real-time orchestration (the exact "live feel" problems above) — likely far faster to a good call. *Con:* dependency on their latency/pipeline; less control of turn-taking nuance + the content boundary; must verify our brain integrates cleanly **and the guardrail survives**.

**Lean (CTO to confirm/reject):** **Option B is likely the faster path to a genuinely live call** *if* (a) our brain integrates as the custom LLM without losing the content-boundary guardrail, and (b) their latency hits our budget (§4). If either fails, fall back to A. **Non-negotiable either way: our brain stays the intelligence, and voice never escapes the approved-content boundary (guardrail below).** *Test:* CTO spikes both — a latency+integration prototype of B vs. a thin A prototype — pick on measured time-to-first-audio + whether the guardrail holds.

#### 4. Target "feel" / latency budget (for the CTO to react to)
| Moment | Target | Why |
|---|---|---|
| User stops → VAD fires | **< 300ms** | Longer feels like it didn't notice you stopped |
| → first audio word back (streamed) | **< 1s e2e (p90); 1.5s ceiling** | Under ~1s feels conversational; past ~2s feels like a bad connection |
| Filler onset (if think-gap > ~600ms) | **~500ms** | Covers the gap before silence becomes dead air |
| Barge-in → AI stops | **< 200ms** | Must feel instant or it feels ignored |

**Make-or-break: time-to-first-audio < 1s p90.** Instrument it via `generation_latency` with `mode=voice` (the heaviest latency path). CTO: tighten/loosen against what ElevenLabs streaming + our brain can actually hit.

#### 5. Measure it right
**Primary: return + upsell of voice-callers vs. text users** (cohort by `voice_mode_enabled`): `nurture_reengaged` in the 24–48h window; `mid_ticket_subscribed` + `high_ticket_offer_clicked`, voice vs. text. **NOT call length / duration / turn count** — duration rises when transcription/latency *fails* (they keep repeating themselves). **Liveness-quality leading indicators** (diagnostic, not success): time-to-first-audio (the §4 budget), barge-in rate, VAD mis-fire rate, call-completion-vs-abandon — these say if the *call feel* works; return/upsell say if it *mattered*.

#### 6. Where it helps most + per-client config
**Where (hypotheses, ranked):** (1) **emotional verticals (sobriety, weight-loss) in the nurture-window check-in** — "Hey, it's Sarah — how'd today go?" as an actual *call* in her voice is parasocial warmth no text bot or ChatGPT can touch; nurture is where return/upsell is won — *strongest hypothesis*; (2) the diagnostic moment (deepens the "real conversation" differentiator, but most latency/quality-sensitive — prove the call feel in nurture first); (3) skip transactional moments (quiz, plan-gen).
**Per-client config:** voice-call is **per-client opt-in** (it's the expert's voice/reputation) — platform builds the one capability; client + us decide on/off + boundary. Everything underneath (streaming chain, VAD, barge-in, TTS) is universal platform.

#### 7. Reputation guardrail (still load-bearing — folded in)
- **Voice is a *rendering* of already-approved text, never a looser generation path.** TTS speaks only post-guardrail content — if text wouldn't say it, the voice can't. **This must survive whichever architecture (A or B) we pick — verify the boundary holds end-to-end in a B integration before shipping.**
- **Regulated verticals (doctors, financial advisors): stricter / off-by-default** — voice restricted to non-advice modes (intake/education/nurture); explicit enablement required.
- **Expert approval explicitly extends to voice behavior** — onboarding checkbox.
- The **fillers (§2.4) live inside this boundary:** generic conversational sounds are fine; a filler may never assert a false claim or process. (Lifting the manufactured-latency ban applies to *call-natural fillers*, not fabricated content.)
- Founder + security sign off that the content-boundary architecture survives the chosen build before broad ship. *(A guardrail on an existing feature — not a gate on whether to build.)*

#### 7.5 Viability gate (unit economics — before funding real-time infra)
Voice is the heaviest build *and* the only feature with a real per-use cost: streaming STT + our brain + ElevenLabs streaming TTS, per turn, per call. **Before committing to the real-time streaming infra, run the viability number:** estimated **cost-per-voice-call** (and per-conversation at expected turn counts) against (a) the **$17 entry** the client charges and (b) **Kodara's retainer** economics. A live phone-call that's delightful but costs more per conversation than the entry product nets — at the volumes the 1,000-conversation guarantee implies — is a viability problem, not a UX one. This is the **viability risk** (Cagan's fourth product risk) the rest of the roadmap mostly skips; Voice is where it bites. *Gate: CTO produces a cost-per-call estimate alongside the architecture spike (§3); if the unit economics don't pencil, the answer may be "voice output only / voice in high-value moments only," not full duplex everywhere.*

#### 8. Prioritization
- **Tier 1 — the live-feel floor (build first):** (1) streaming chain: brain tokens → streaming TTS; (2) auto end-of-speech (VAD); (3) full-screen call UI with clear listening/thinking/speaking states + hang-up. *Together these ARE "a phone call with the AI."*
- **Tier 2 — makes it feel human:** (4) barge-in; (5) conversational fillers.
- **Tier 3 — latency tuning (gate on Speed `mode=voice` data):** (6) pre-warm + speculative start.
- **Parallel / gating (not a tier):** CTO architecture spike (Option A vs. B) — run alongside Tier 1, it decides *how* we build 1–5; founder + security ratify the content-boundary guardrail survives the chosen architecture before broad ship.

**Cheapest first move:** CTO spikes the **streaming brain→ElevenLabs-TTS chain** and measures **time-to-first-audio against the <1s target**, *and* spikes whether our brain plugs into ElevenLabs' conversational layer (Option B) cleanly with the guardrail intact. That one spike decides the architecture and whether the "live" bar is reachable — which gates the whole build order. Grade the shipped feature on **caller return + upsell vs. text**, never on call length.

**Numbers we'd want (don't have):** our current voice turn-around today (user-stops → first-audio — the baseline for §4); ElevenLabs' current streaming-TTS latency + whether their Conversational-AI layer accepts our brain as a custom LLM (the Option B viability check); **whether our brain already streams tokens** (if it returns complete replies, that's the first thing to change — shared with the Speed work); which clients are regulated (the off-by-default list); voice-caller baseline return/upsell (needs instrumentation live).

### B.5 — Visual Chat Components — Ideas for Design + CTO Review

> **Status: ideas and recommendations for the designer + CTO to review — not a locked spec.** Universal: the chat *product* all ~50 clients inherit; our team builds it.

**Core recommendation (the reconciliation with the paywall *is* the architecture):** **Build a small, shared component library where every component is ONE renderer with TWO states — a teased (locked/redacted) state pre-paywall and a full (delivered) state post-paywall — driven by the same data schema.** The mistake to avoid: building "quiz teaser visuals" and "paid AI result visuals" as two separate things. They're the **same component showing more or less of itself** depending on which side of the $17 line the user is on. The lead sees the *shape* of their 2×2 with cells blurred, buys, and the *same* 2×2 fills in — that continuity ("that's the thing I saw locked, now it's mine") is itself a conversion + satisfaction mechanic. **One component, two states, one schema** prevents drift (teaser promising a chart the paid version doesn't match), double maintenance across 50 clients, and a broken "I bought it and got something different" moment.

*Scope rule up front:* a component earns its place **only if it maps to a real conversion or comprehension moment.** No component zoo. Proposing **three**, ranked; resist a fourth until data demands it.

#### 1. Which components, tied to real moments (only three)
- **#1 — Diagnostic/Verdict Result (the 2×2 / result card) — highest impact, build first.** Sits on *both* sides of the paywall. **Pre-paywall (teaser):** the blurred/redacted result (B.1's Almost-There reveal) — structure + labels visible, value cells locked ("Your figure 🔒"). **Post-paywall (full):** the same 2×2, cells filled with the real computed result. It's the conversion lever (teaser) *and* the comprehension/satisfaction lever (paid) *and* the B.3 Verdict-mode target — all one component.
- **#2 — Plan / Checklist *sendable in chat* — highest *post-purchase* retention value.** We **already have** the actionable plan/checklist built (tickable, progress-tracked) — but the AI agent **can't currently send/surface it inside the chat.** The capability to add: let the agent **drop the existing checklist into the conversation** at the right moment, with items tickable and progress shown inline (optimistic UI, ties to Speed B.2). A plan the AI hands you mid-conversation and you check off is a reason to come back. Pre-paywall at most a locked silhouette ("Your 3-step plan 🔒"). High value but narrower (almost entirely post-paywall), so it doesn't carry conversion weight like #1.
- **#3 — Visible Dossier — conversion-side, pre-paywall.** The profile that assembles during the quiz with the payoff cell locked (B.1). Inherently a teaser; **overlaps #1's locked state — likely a richer *variant* of the ResultCard, not separate code.**
- **NOT building (yet):** generic charts/graphs/comparison tables/timelines/badges — the zoo. A chart enters the library *only* when a vertical's real diagnostic needs it AND we can back it with real data. **Trust risk:** a fabricated-looking trend line in a finance/health vertical implies data rigor we must actually have.

#### 2. Structured visual vs. wall of text (division of labor — build each thing once)
- **B.3 (reply pacing)** decides *when* to go visual ("Verdict mode = a visual component, not a wall"). **B.5 builds the visual.** Wire them: when the response policy hits Verdict mode, it emits a component (via the schema, §3) instead of prose. One decision, two docs — don't re-litigate.
- **B.1 (quiz)** owns the *pre-paywall teaser placements* (blurred reveal, Dossier). **B.5 provides the renderer those placements use.** B.1 decides *where in the arc*; B.5 builds the teaser-state component.
- **Net architecture:** B.5 = the **component library + two-state renderer**; B.1 and B.3 are *callers* of it. That's the non-duplication line.
- **Honest line:** not everything should be a component — a chat that's all widgets feels like a *form*, not a coach. Components for the *structured* moments (verdict, plan); prose for the *conversational* moments. The differentiator is a conversation with occasional structure, not a dashboard.

#### 3. The component library — universal renderers, per-vertical content
- **Universal (platform, built once):** the renderers (ResultCard/2×2, PlanChecklist, Dossier-as-variant); the **two-state (locked/full) behavior** baked into each; the **output schema** the AI emits to trigger a component (e.g. `{component:"result_card", state:"locked"|"full", cells:[...]}`). The AI fills the schema; the renderer draws it — this is the reusability unlock (the roadmap's "trigger via output schema" instinct, correct).
- **Per-vertical (configurable content, not code):** *what goes in the cells* (mortgage: interest/payoff-year/savings; sobriety: trigger/pattern/risk; weight-loss: metabolic-pattern/blocker/lever — same renderer, different labels/values); which component fires at which moment.
- **Resist:** per-client *custom components* → specials + 50 divergent codebases. Hold the line at per-vertical *content* in universal *renderers*.

#### 4. Scope discipline (the gate)
**Three components, schema-driven, two-state. A fourth requires a real moment + data that the existing three can't serve.** The roadmap's "2×2, no charts" is the right constrained start — keep it. Every component must name the conversion/comprehension moment it serves and show where prose / the existing components fail. No moment, no component.

#### 5. Measure it right (two sides, two metrics — don't collapse them)
- **Teaser visual (pre-paywall) → measure CONVERSION:** via `diagnostic_result_shown.result_component` (`locked_card` / `dossier` / `text`), does a teased structured result lift **offer click-through + `low_ticket_purchased`** vs. a text tease? *Hypothesis: the locked structured result out-converts a text tease — seeing the shape of a withheld answer is a stronger loop.*
- **Paid visual (post-paywall) → measure COMPREHENSION/ACTION/UPSELL:** `result_component=full_card` vs. `text` → **return (`nurture_reengaged`), plan-task completion, upsell** (`mid_ticket_subscribed`, `high_ticket_offer_clicked`). *Hypothesis: structured delivery → better comprehension → more action → more return/upsell.*
- **NOT the metric:** "component renders" / "looks premium" (output theater). Grade teaser on the buy, paid on action/upsell. *(The hook — `diagnostic_result_shown.result_component` — is already specced in Section A; just populate it accurately on both sides.)*

#### 6. Prioritization + first move
- **Tier 1 — build first:** (1) **ResultCard/2×2 renderer with two-state (locked/full) + the output schema** — the keystone (conversion teaser *and* paid payoff *and* B.3 Verdict target, in one); (2) **wire it to B.1 (teaser placement) and B.3 (Verdict mode emits it)** — cheap once the renderer + schema exist, makes the three features cohere.
- **Tier 2:** (3) **Make the existing Plan/Checklist sendable by the AI in chat** (interactive, optimistic-UI per B.2) — strongest retention component, narrower placement. (Build is the *send-in-chat* capability, not the checklist itself — that exists.)
- **Tier 3 — only if data demands:** (4) Dossier as a richer teaser variant of the ResultCard (likely config, not new code); (5) any chart/graph — only when a vertical's real diagnostic proves the result-card insufficient *and* it's backed by real data (no decorative pseudo-data charts).

**Cheapest first move:** build the **ResultCard renderer + output schema with a locked and a full state**, instantiate it across 2–3 verticals **from config alone** (proving universality — if a vertical can't be expressed in the schema, *that's* the evidence we need another renderer), and A/B **locked-card-tease vs. text-tease** on the conversion side. Grade the teaser on **buy**, the paid version on **action + upsell** — never on "it rendered."

**Numbers we'd want (don't have):** do the verticals' real diagnostics actually fit a 2×2 / four-cell structure (need ~3 real diagnostic outputs — if most are a single number or a list, the "2×2" framing is wrong); current text-delivery comprehension/action baseline (is walls-of-text *proven* to hurt post-paywall action, or assumed?); **can the paid AI reliably emit the structured schema** (CTO — if the brain can't produce clean structured output, the renderer starves); teaser A/B baseline (current quiz→purchase rate, from B.1 instrumentation).

### B.6 — Thinking / Progress Display — Ideas for Design + CTO Review

> **Status: ideas and recommendations for the designer + CTO to review — not a locked spec.** Universal: the chat *product* all ~50 clients inherit; our team builds it.

**Core recommendation:** **Treat "thinking/progress" as one universal *staging layer* with four configurable purposes — anticipation, perceived value, transparency, latency-cover — deployed by *moment*, not globally.** The biggest wins are at the **high-stakes payoff moments** (plan/diagnostic/verdict generation), where a dramatized build makes the output feel earned and substantial. Over short conversational replies it's pure noise — **suppress it there.** The single design principle: **the drama of the thinking display scales with the perceived weight of what's being produced** — a verdict earns a dramatic build; "yeah that makes sense" earns nothing.

*Framing update:* this is now a **first-class value device, not an honest-only latency band-aid** — the manufactured-latency ban is lifted and Kodara already uses anticipation theater deliberately (the quiz "analysing…" reveal B.1, the voice fillers B.4). It's the **same device family** as those two → build it **once** as a staging layer; B.1 and B.4 are *callers/presets* of it, not separate mechanisms (same "build the engine once, callers configure" pattern as the visual components B.5).

#### 1. The four uses (different design choices, all permitted)
| Use | What it does | Design signature | Honest status |
|---|---|---|---|
| **1. Anticipation / drama** | Makes a payoff feel earned + valuable before the reveal | Sequenced, paced beats building to a reveal | Dramatized — Kodara already does this (quiz) |
| **2. Perceived value** | Makes output feel substantial/credible (why o1/Claude show thinking) | Visible "working" steps implying rigor; collapses into the answer | Real *or* dramatized |
| **3. Transparency / trust** | User trusts output because they see *real* reasoning | Genuine intermediate steps (real tool calls/considerations) | Real reasoning |
| **4. Latency-cover** | Fills genuine generation time so it's not dead air | Lightweight; runs only while actually generating | Honest by definition |
These aren't the same UI — anticipation is a *paced sequence* built for drama; latency-cover is a *lightweight* indicator that vanishes the instant tokens flow. **One staging layer, four presets.**

#### 2. Where it helps vs. where it's noise (ranked moments)
1. **Plan / diagnostic / verdict generation (the big payoffs) — highest value.** Anticipation + perceived value compound: a paced build makes the result feel earned and worth the $17. Extend the proven quiz-reveal pattern to the *paid* AI's plan/verdict generation. **Spend here first.**
2. **Genuine multi-step reasoning (real tool use/computation, e.g. the post-paywall diagnostic math) — high value via transparency.** Real steps build *true* trust and cover real latency at once. Best where steps are real.
3. **First-turn / cold-start — medium (latency-cover).** Covers the genuine first-turn wait (ties to Speed pre-warming).
4. **Short conversational back-and-forth — NEGATIVE value, suppress.** A "thinking…" shimmer over a 1s reply fakes weight on something weightless — makes the AI feel slower and more pretentious, not smarter. **Hard rule: no thinking display on short turns.**

#### 3. Real reasoning vs. dramatized progress (both allowed; choose by moment)
- **Real reasoning (transparency)** when the AI genuinely does multi-step work and the steps are credible to show (real computation/retrieval/the real diagnostic). Prefer it when real steps exist — it's true and builds durable trust.
- **Dramatized progress (anticipation/perceived value)** when the value is the *feeling of a build toward a reveal* and the generation isn't a legibly interesting process (the quiz-reveal pattern).
- **The one honesty judgment call — explicitly Lucas's call, NOT a ban:** *specific* fabricated process claims ("analysing 1,847 profiles," "running the three-variable formula"). The quiz already does this, so the question isn't whether to dramatize — it's **how specific/falsifiable a claim to put in the expert's voice.** Spectrum: **generic** dramatized progress ("analysing your situation… building your plan…") = low risk, freely usable; **specific numeric/process** claims = higher risk *only if* checkable-and-false, and in a regulated vertical (a doctor's AI claiming "cross-referencing 10,000 case studies") it becomes a credibility/compliance problem **in the expert's name.** *Recommendation (Lucas decides):* allow generic everywhere; gate *specifics* to true-or-low-stakes, **off by default in regulated (health/finance) verticals** — same default-off posture as voice. Not a ban (the quiz keeps doing what it does); a "be deliberate about specificity where the expert's reputation is exposed" guardrail.

#### 4. Relationship to Speed (B.2) — additive, not a substitute
Thinking/progress is a legitimate value device, use it for drama even where latency is fine. **But it must not become the excuse to leave fixable latency bad** ("the chat is slow, slap a thinking animation on it, ship"). Discipline: **Speed fixes the floor; thinking/progress adds the ceiling** — they're additive. A dramatized build over a *genuinely fast* generation is delight; a dramatized build *covering for* sloppy unfixed latency is rot the `generation_latency` data will expose (users drop during the "thinking" anyway).

#### 5. Relationship to B.1 (quiz reveal) and B.4 (voice fillers) — same device family
**One universal staging layer** (plays a configurable sequence of progress/thinking beats — text, or audio for voice); B.1, B.4, B.6 are *callers* with presets: quiz-reveal preset (pre-paywall anticipation), voice-filler preset (audio think-gap), plan-generation preset (paid text payoff). Build the engine once; the moments configure it.

#### 6. Universal vs. per-client
- **Universal platform:** the staging-layer engine, the four presets, the moment-routing logic (incl. "suppress on short turns"), real-reasoning surfacing for genuine multi-step work. Built once, all 50 inherit.
- **Per-vertical (config):** the *copy* of the dramatized beats ("analysing your loan profile" vs. "reviewing your drinking patterns"), and the **specificity gate** (regulated → generic only).
- **Per-client:** the expert's voice in the beat copy; nothing bespoke.

#### 7. Measure it right
By use: **anticipation/value at paid payoff moments** → completion-through-the-wait (stay to the reveal vs. abandon) + **return + upsell**; **anticipation pre-paywall (quiz)** → the **buy** (part of the B.1 A/B); **transparency** → trust/satisfaction + upsell shown vs. hidden; **latency-cover** → abandonment during genuine waits vs. a static spinner. **NOT "looks cool/premium"** (the output-theater trap — ironic for a feature that *is* theater, but theater that doesn't move completion/conversion/upsell is just cost). **A/B every deployment** (on vs. off, dramatized vs. real) per moment. **Guardrail to instrument: abandonment-*during*-thinking** — if users bail mid-build, the drama's too long or the wait too real.

#### 8. Prioritization + first move
- **Tier 1:** (1) **universal staging-layer engine + presets + moment-routing** (incl. the hard suppress-on-short-turns rule) — the reusable core B.1/B.4/B.6 all call; (2) **anticipation preset at paid-AI plan/verdict generation** — the highest-value moment, extends the proven quiz pattern to the paid payoff.
- **Tier 2:** (3) **transparency preset for genuine multi-step reasoning** (real steps where they exist — best trust-per-effort); (4) **wire B.1 + B.4 to the shared engine** (consolidate the one-offs).
- **Tier 3:** (5) **latency-cover preset** for first-turn/cold-start (gate on Speed `generation_latency` data — only where real waits remain); (6) **per-vertical beat copy + the specificity gate.**

**Cheapest first move:** A/B the **anticipation preset at the paid AI's plan/verdict generation** — dramatized build vs. instant-snap — graded on **completion-through-the-wait + return + upsell**, with **abandonment-during-thinking** instrumented as the guardrail. Hypothesis: the dramatized build lifts perceived value + upsell at the payoff moments and is noise (or negative) on short turns — confirming "scale drama with output weight."

**Numbers we'd want (don't have):** abandonment-during-generation today at plan/verdict moments (is there a wait worth dramatizing, and are we losing people in it?); where genuine multi-step reasoning actually happens in the paid AI (how much *real* transparency is available vs. must be dramatized); **Lucas's decision on the specificity line** (generic-only everywhere vs. specifics-allowed in non-regulated — a brand/trust call); which clients are regulated (the specifics-off list).

### B.7 — Animation / Motion — Verdict: Not a Standalone Feature (fold + cut)

> **Status: ideas and recommendations for the designer + CTO to review — not a locked spec.** Heavily a *designer* call. Universal; our team builds it.

**Recommendation (the honest, deflationary one): B.7 does not survive as its own feature — kill the standalone slot.** Its substance has already been absorbed by **B.5** (the result/payoff reveal) and **B.6** (the build-to-reveal drama, built as one staging engine). What's left splits into things that don't belong on the consumer-outcome roadmap *as a feature*: (a) a baseline **motion/transition system** = design-system *hygiene* the team folds into every other feature's build, not a scheduled deliverable; and (b) the generic **app-entry welcome animation** = **cut** (already agreed — it sits between the user and value). The one genuinely roadmap-worthy remainder — the **onboarding first-impression** — is an *onboarding/activation* problem, not a motion feature. **Don't invent a feature to fill the slot** — that would be output theater at the roadmap level.

#### 1. Decomposition — where each "animation" ambition lives now
| Original B.7 ambition | Where it lives now | Verdict |
|---|---|---|
| Plan-reveal "dramatic reveal" motion | **B.6** (build-to-reveal staging engine) | Folded — don't re-spec |
| Result/2×2 reveal, blurred→full payoff | **B.5** (two-state renderer) | Folded — don't re-spec |
| Plan/result *visual* itself | **B.5** | Folded |
| Micro-interactions, transitions, easing, "feel alive" polish | **Design-system layer** (cross-cutting) | Reclassify as hygiene (§2a) |
| Onboarding welcome animation (app entry) | — | **Cut** (§3) |
| First-impression / activation moment | Genuine remainder | Reframe as onboarding, not motion (§2b) |

A feature whose substance has been distributed into two other features isn't a feature; it's a theme. The pieces that attach to outcomes already got attached (B.5's conversion/comprehension, B.6's stay-through/upsell); what remains is the part that was always just "feel."

#### 2. The genuine remainder
**(a) Baseline motion/transition system — hygiene, not a feature.** The app's general "feel alive" layer (page/state transitions, consistent easing, tasteful micro-interactions, the quiz's one-thumb haptics). It matters for the clunky-vs-ChatGPT feel — but ship it as a **design-system concern**: define a small **motion vocabulary once** (token set: durations/easings + 3–4 standard transition patterns) and have *every other feature* (Speed skeletons, B.5 components, quiz, voice UI) consume it as built. It **rides along; it isn't scheduled as its own eng block** competing with the outcome features. Small, designer-led, low eng cost, applied incrementally. Don't gold-plate.
**(b) Onboarding / first-impression — the one roadmap-worthy remainder, but reframe it.** The moment a user first lands in the paid AI and forms the "clunky or magical" verdict is real and measurable — but the lever isn't "add a welcome animation," it's **"get them to first value fastest, made to feel intentional."** Straight-line onboarding *removes* friction between entry and value; a welcome animation that sits *between* the user and value is friction dressed as polish. Motion's legitimate role here is **reinforcing the value moment** (a satisfying transition *into* the first diagnostic; the payoff arriving with weight via B.5/B.6), never *gating* it. So this belongs in **onboarding/activation** work, measured on **activation = entry → first diagnostic**.

#### 3. The honest line — attach, reclassify, or cut
- **Attach to a measurable moment (keep, but it lives in the owning feature):** plan-reveal flourish → B.6 (stay-through + return); payoff reveal → B.5 (conversion/comprehension); transition into first diagnostic → onboarding (activation). Justified by the host feature's outcome, not "premium feel."
- **Reclassify as sales-enablement (off the consumer roadmap):** any motion whose real audience is the *client in the demo*. Legitimate, but measured on the **client's close rate**, owned by sales/marketing. Don't smuggle demo-polish into the consumer roadmap as retention work (the optimizing-the-wrong-side trap).
- **Cut:** the generic app-entry welcome animation.
- **The test for any motion idea:** name the consumer outcome it moves and how you'd measure it. Can't? → sales-enablement (reclassify) or decoration (cut).

#### 4. Universal vs. per-client
**Entirely universal** — motion vocabulary, transitions, reveal motion are platform/design-system, built once, all 50 inherit. **Nothing per-client; actively resist per-client motion customization** (bespoke drift for zero outcome gain). The expert's voice/avatar is the per-client expression; the motion language is universal house style. (Brand color/accent may theme per-client, but that's theming, not motion.)

#### 5. Measure it right (be skeptical — most output-theater-prone)
- **No "animation" metric.** Motion is measured *through its host feature* (B.5 reveal → conversion/comprehension; B.6 build → stay-through + upsell; onboarding motion → activation + time-to-first-value). If a motion change can't move one of those, it failed regardless of how it looks.
- **The trap to name:** "feels premium" / "demo looks better" / "smoother" are **not** consumer outcomes. Premium-feel surveys are especially seductive and especially meaningless here. Grade on activation/return/upsell, never perceived premiumness.
- **The one honest guardrail: motion must not *cost* speed.** A flourish adding 400ms between the user and their value is a *negative* that directly fights the Speed work (B.2). Instrument **time-to-interactive / time-to-first-value** and treat any motion that regresses it as a defect, not polish. (Motion is the one "improvement" that can make the product measurably *slower* while feeling fancier — gratuitous motion is more of the clunky disease, not the cure.)

#### 6. What to actually do
- **Tier 1 — decisions, not builds (free, *adds* capacity):** kill the standalone B.7 slot (substance → B.5/B.6); cut the generic welcome animation.
- **Tier 2 — hygiene (designer-led, rides inside other features):** define the small motion vocabulary the other features consume. Not a scheduled block — infrastructure.
- **Tier 3 — fold into onboarding (not motion):** handle the first-impression as onboarding/activation, motion *reinforcing* the value arrival, measured on activation → first diagnostic.
- **Off-roadmap:** demo-polish → reclassify to sales-enablement, owned by sales, measured on close rate.

**Bottom line:** B.7 isn't a feature, it's a theme that's already been absorbed. The win is the roadmap capacity reclaimed for the features that move enrollment/upsell/return — plus a single guardrail (time-to-first-value) ensuring "polish" never makes the product slower. **Numbers we'd want:** current activation rate (entry → first diagnostic) + time-to-first-value (is there even a first-impression problem motion could help?); whether the demo's "premium feel" actually correlates with client close rate (a *sales* hypothesis, tested on close rate, that would justify funding demo-polish separately).

### B.8 — Upsell / Mid-Ticket Hand-Off — Ideas for Design + CTO Review

> **Status: ideas and recommendations for the designer + CTO to review, refine, drop, or add — not a locked spec.** Universal: the chat *product* all ~50 clients inherit; our team builds it.
>
> **Priority flag: this is proposed as a top-tier item — arguably above B.5/B.6/B.7.** The review found the roadmap improves the *top* of the funnel (engagement, speed, feel) but barely touches the *upsell moment.* By lever order (retention → upsell → acquisition), and because this is **where the client's money is actually made** (the mid-ticket £199/mo sub and the existing high-ticket), the upsell hand-off has a strong claim to be built before more consumer-side polish.

**Core recommendation:** **Design the 24–48h nurture window as a deliberate, trigger-based hand-off — quick-win → desired-outcome → upsell — instead of leaving it as an instrumented-but-undesigned gap.** Today we *measure* re-engagement (`nurture_reengaged`) and the upsells (`mid_ticket_subscribed`, `high_ticket_offer_clicked`), but no feature *moves* them. This item is the conversation that turns a one-time $17 buyer into a returning, upsold customer.

*Why:* the whole VSL model is "the ones who buy small become the ones who buy big." That upsell is the link the client's revenue, retention, and lock-in all depend on — and it's the link closest to June's retention outcome (a user being well nurtured *is* a user who returns). It's also the one place the roadmap currently has the most instrumentation and the least product.

#### 1. The hand-off design (trigger-based, not time-based)
Three tracks, entered by *what the user has done*, not what day it is:
- **Track 1 — Signup → Quick Win.** The just-bought user's *first* post-purchase win (the paid AI delivers the real diagnostic/answer they were teased — ties to B.1's paywall payoff). **No selling here.** Get them to value first.
- **Track 2 — Quick Win → Desired Outcome.** Nurture messages + usage nudges that move them from "I got my number" toward the fuller outcome (the plan, the next step). Still no hard sell. This is where `nurture_reengaged` should fire and where B.4's voice check-in ("Hey, it's Sarah — how'd today go?") is a strong hypothesis.
- **Track 3 — Desired Outcome → Upsell.** *Only now* the soft-pitch: the £199/mo mid-ticket sub for the not-yet-ready, or the high-ticket route (calendar/application) for the ready. Framed as the logical next step after value delivered, not a push.

**Meet users where they are:** if a buyer hits their quick win in the first session, skip Track 1. If they're clearly ready, skip to Track 3. Don't pace the hand-off by a calendar the user doesn't share.

#### 2. Tie to instrumentation (Section A)
- **Track 2 effectiveness** → `nurture_reengaged / low_ticket_purchased`, bucketed by `hours_since_purchase` (the A.4 return tile).
- **Mid-ticket upsell** → `mid_ticket_subscribed` (confirmed, own checkout / client-Stripe).
- **High-ticket upsell** → `high_ticket_offer_clicked` (intent) and `high_ticket_booking_confirmed` where integrated — **honor the A.6 intent-vs-confirmed split:** grade Track 3's high-ticket performance on *intent* today, *confirmed* once PH-5b lands, and never report intent as revenue.

#### 3. Trust guardrail (Bush's "don't sell before you deliver value")
The one hard line: **never pitch Track 3 before the user has actually reached value in Track 1/2.** Pitching the upsell to someone who hasn't yet had their quick win is the exact trust erosion that breaks the chain — worse in an emotional/vulnerable vertical. Pair the upsell metric with the guardrail metric (refund/complaint + post-pitch drop-off): *upsell up + trust down = we pitched too early*, back it off. Same Wells-Fargo guardrail the rest of the doc carries, applied to the upsell moment where the temptation to push is highest.

#### 4. Universal vs. per-client
- **Universal platform:** the trigger-based track engine, the signal definitions (quick-win / desired-outcome / customer), the timing-by-behavior logic. Built once, all 50 inherit.
- **Per-vertical/per-client (config):** the *content* of the nurture (what "quick win" and "desired outcome" mean per vertical), the mid-ticket price, the high-ticket route (Calendly/GHL per A.6), the expert's voice in the messages.
- **Resist:** per-client bespoke nurture flows — a special. Config the universal tracks.

#### 5. Measure it right
**Primary: upsell** — `mid_ticket_subscribed / low_ticket_purchased` and `high_ticket_offer_clicked`(→confirmed) per client. **Secondary: return** — `nurture_reengaged` (Track 2 working). **NOT:** messages sent / "nurture shipped" (output theater — sending nurture into the void isn't upsell). **Guardrail:** refund/complaint + post-pitch drop-off (pitched-too-early detection).

#### 6. Prioritization + first move
- **Top-tier** — recommend slotting **ahead of B.5/B.6/B.7** in the build order (highest-leverage lever after the clunky-fix, and closest to the retention outcome).
- **Cheapest first move:** before building a track engine, **map the current post-purchase nurture as-is** (what, if anything, happens in the 24–48h window today?) and read the `nurture_reengaged` baseline once Item Zero ships. If the window is currently empty, even a *single* well-timed Track-2 quick-win message (no selling) is a cheap A/B against nothing — graded on return + downstream upsell.

**Numbers we'd want (don't have):** what the 24–48h nurture *currently* does (is the window empty or already worked?); the baseline `nurture_reengaged` and mid-ticket upsell rate per client; whether the paid AI can fire the quick-win / desired-outcome signals that gate the tracks.

---

## What's still missing from the roadmap (higher leverage than parts of the current list)

1. **The upsell hand-off / mid-ticket upsell moment** — *now addressed: added as **§B.8** (top-tier).* The roadmap previously improved the top of the funnel but barely touched the upsell moment; B.8 makes it a real item.
2. **A deliberate expert-trust feature (buyer-side — Opportunity 6 in the OST, the near-empty branch).** Experts judge us on "does it sound like me / can I trust it with my reputation" — and the expert is the one who *pays us and can churn.* Today we have reputation *guardrails* (B.4 §7, B.3 §4) but no *feature* that makes the buyer *feel* control and confidence. The expert's first quick win is **"I heard the Brain talk and it sounded like me."** Candidate (for review, not committed): a lightweight **expert-facing "this is what your AI is saying / sounds like / recommends" surface** — letting the expert hear samples, see what it will/won't say, and approve voice/boundaries, turning the VSL's "you stay in full control" promise into an experienced product moment rather than a one-time onboarding step. Small to start; it's the only deliberate work on the side of the product that renews us. *Measure: client confidence/renewal signals, not a consumer metric.*
3. **End-user churn / cold-signal detection** (post-instrumentation) so the system (or client) can re-engage a user going quiet — the forward-looking retention lever.

---

## Doctrine flags carried into finalization

- **One June outcome, chosen: end-user retention / return** (the first link in the chain; the clunky-UX evidence points straight at it) — framed as a *learning* goal until Item Zero makes it measurable. The Opportunity Solution Tree hangs every feature off this; once the funnel data lands, concentrate on the single opportunity with the biggest leak rather than advancing all branches.
- **A metric you can't read isn't a guardrail, it's a wish.** This is why Item Zero ships first.
- **Don't optimize upsell by pushing harder.** Conversion gains that erode trust break the whole chain.

---

## Open questions & data we need (consolidated)

This pulls together the "numbers we'd want" scattered across every section, grouped by who can answer. **Most of the roadmap is provisional until these land — especially the Item-Zero data.**

**A — Facts the CTO / eng can answer now (no new data needed):**
- Does the AI brain currently **stream tokens**, or return complete replies? *(Shared blocker for Speed B.2 and Voice B.4.)*
- Current **time-to-first-token + tokens/sec**, and is any response path **buffered** (full completion before render)? *(Decides the size of the Speed win — B.2.)*
- Can the brain reliably **emit the structured output schema** for visual components? *(B.5.)*
- **ElevenLabs:** current streaming-TTS latency, and does their Conversational-AI layer accept our brain as a custom LLM? *(Voice build-vs-platform — B.4 §3.)*
- **Voice unit economics:** estimated cost-per-voice-call vs. the $17 + retainer. *(Voice viability — B.4 §7.5.)*
- Where does **genuine multi-step reasoning** happen in the paid AI? *(Thinking/progress — B.6.)*
- Where is the **high-ticket booking captured** (Calendly / GHL / other), and the **Stripe access model** (Connect vs. per-client keys)? *(A.6 / PH-5 / PH-5b.)*
- What does the **24–48h nurture do today** — empty, or already worked? *(Upsell — B.8.)*
- Is there a clean **"diagnosis reached" signal** in the pipeline, or must the agent emit one? *(A.8.)*
- Which clients are in **regulated verticals** (health/finance)? *(Sets the default-off list for voice, claim-specificity, and the ethical gating.)*

**B — Numbers Item Zero will produce (the baselines everything is measured against):**
- Per-stage **funnel drop-off** map (where end-users actually leak) — re-prioritizes the whole OST.
- **Quiz → $17 purchase** rate (+ per-client variance).
- **`nurture_reengaged`** baseline + **mid-/high-ticket upsell** rates per client.
- **Post-purchase activation** rate (entry → first value) + **time-to-first-value** *(A.9 / B.7).*
- **Generation latency by stage + mode** (incl. voice).
- **Voice-caller** return/upsell baseline (once cohorted).

**C — Decisions for Lucas / the review meeting (calls, not data):**
- **Focus:** ship a focused core (Item Zero + Speed + Reply-pacing + Upsell) and defer/gate the rest, or keep all as a menu?
- **Owner:** stand up a product trio (PM + design + eng) to own the one outcome?
- **Ethical line** for vulnerable verticals (how restrained on the tease / curiosity-loop).
- **Outcome-based pricing** for Kodara's retainer (explore once outcomes are measurable).
- **$17 model-fit:** tripwire / trial / product? (Sets what B.8 must do.)
- **Quiz paywall model (flagged by codebase review):** keep the live **free personalized-plan preview** at the paywall (LQ4), switch to **paywall-the-payoff** (B.1's proposal), or a middle path (tease structure, reveal substance only post-pay)? Conversion lift vs. trust/refund risk.
- **Qualitative discovery:** add fresh user/expert interviews (starting from the existing `end-customer-profile.md`), or stay data-first for June?
