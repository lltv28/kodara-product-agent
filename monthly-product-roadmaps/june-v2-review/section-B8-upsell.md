# B.8 (rewritten) — Upsell / Value-Ladder Hand-Off: design the post-purchase track on rails you already have

> **Terminology (locked):** **Upsell** = a buyer moving up the value ladder after the entry purchase. The two rungs are **mid-ticket subscription** and **high-ticket booking**. "Upsell" retires the scattered synonyms (ascension / cross-sell / expansion / monetization). **Re-engagement** (a buyer *returning*) is kept as a separate, named concept — it *precedes* upsell, it is not upsell. Code keeps its mechanism nouns unchanged (`subscription` / `entitlement` / `paywall` / `paid plan`).

> **Replaces the original B.8.** The blind version proposed building a "trigger-based nurture engine" from scratch. The codebase already has the engine, the delivery vehicle, and the billing rails. B.8's real job is **design + wiring + the missing instrumentation**, not a new engine.

---

## B.8.0 — What already exists (the rails)

| Capability B.8 needs | Already in the codebase | Evidence |
|---|---|---|
| Trigger-based sequencing (behavior, not calendar) | **Email Sequences engine** with condition evaluation, enrollment state, and behavioral triggers (`intake_submitted`, `payment_recorded`, `user_activity`, `profile_linked`, `drip_candidate`, `inactive_7d`) | `services/emailSequences/`, jobs `sweep_email_sequence_triggers` (hourly), `handle_email_sequence_trigger`, `run_email_sequence_enrollments` (5-min) |
| Predefined sequence types | `lead_nurture`, plus generated `drip` / `onboarding` / `reactivation` | `docs/product-specs/email-sequences.md` |
| Quick-win → desired-outcome delivery | **Client-plan system**: phases, milestones, tasks, metric check-ins, activation events | `clientPlanActivation`, `completeClientPlanMilestone`, `submitClientPlanMetricCheckIn`; events `client_first_task_completed`, `client_first_milestone_completed` |
| Mid-ticket mechanism | **Whop subscription** on a product price (up to ~2 monthly paid plans/product) | `wl_product_subscriptions` (`active_plan_type='subscription'`, `billing_status`), `applyWlProductSubscriptionBillingEvent`, `activateWhopClientPlan.workflow.ts`, `/products/[id]/pricing-plans` |
| Pitch surface (mid-ticket) | **Paywall + entitlement** (`access_kind: paid\|free\|blocked`, `markInitialPaywallShown`, `/paywall` route) | `productEntitlement.wl.service`, `WLInlinePaywallCard` |
| Pitch surface (in-chat) | **v3 reply runtime + `ui_blocks`** structured cards | `runUnifiedAgentV3Chat.workflow`, `reply.main.ts`, per-agent `agent_skill_bindings` |
| Trust signals | Per-message **sentiment** + WL like/dislike feedback | `/sentiment/[messageId]`, `useWLFeedback` |

**The honest gaps (this is the actual B.8 build):**
1. **There is no *post-purchase* upsell sequence.** `lead_nurture` is **pre-sale** and explicitly *stops* on `payment_recorded`. Nothing picks the buyer up after that.
2. **No "value-reached" gate** wired as a sequence condition (so we can enforce "don't pitch before the quick win").
3. **No in-chat upsell CTA behavior** in the v3 reply runtime (no `ui_block` upsell card, no mid-ticket→paywall / high-ticket→route emission).
4. **No high-ticket booking capture** (Calendly/GHL) — genuinely greenfield (see Section A, AN-7).
5. **No upsell instrumentation** — added in the Section A rewrite (`mid_ticket_subscribed`, `high_ticket_offer_clicked`, `high_ticket_booking_confirmed`, plus `session_started`/`nurture_reengaged` for return). *(Rename the proposed report `clientAscensionReport` → `clientUpsellReport` for consistency.)*
6. **Inline paywall is currently disabled** (tech-debt: "until the new free-plan design ships") — Track 3's mid-ticket surface depends on re-enabling it or shipping that design.

---

## B.8.1 — The three tracks, mapped onto existing rails (trigger-based, not time-based)

The original three-track design is right; here's where each track *physically lives*:

| Track | Goal | Lives on | Enters when (trigger) | Exits / advances when |
|---|---|---|---|---|
| **Track 1 — Quick Win** | First post-purchase value, **no selling** | **Client-plan** first task/milestone | `payment_recorded` → plan activated | `client_first_milestone_completed` (the value signal) |
| **Track 2 — Desired Outcome** (return) | Move from "got my number" toward the fuller outcome; drive **return** | Client-plan progression **+ a NEW `post_purchase` email sequence** + (hypothesis) B.4 voice check-in | `client_first_milestone_completed` | sustained return (`session_started` / `nurture_reengaged`) **or** ready-signal |
| **Track 3 — Upsell** | The soft pitch | Mid-ticket: **Whop subscription via paywall**; High-ticket: **in-chat `ui_block` CTA → route** | **value-reached gate passes** (Track 1/2 complete) | `mid_ticket_subscribed` **or** `high_ticket_offer_clicked` → `high_ticket_booking_confirmed` |

**Meet-users-where-they-are** (already supported by the engine's condition model): if the buyer hits the quick win in session one, skip Track 1's drip; if a ready-signal fires, jump to Track 3. Behavior gates, not a calendar — which is exactly what `handle_email_sequence_trigger` + condition evaluation already do.

## B.8.2 — The one hard rule, enforced as a sequence *condition*

**Never enter Track 3 before the value signal fires.** This is Bush's "don't sell before you deliver value" and your Wells-Fargo trust guardrail, made mechanical:

- Gate Track 3 entry on `client_first_milestone_completed` (or a per-product configurable value signal). The email-sequence engine already evaluates enrollment conditions — add this as a condition, not new infrastructure.
- Pair the upsell metric with a **guardrail metric**: refund/complaint rate + **post-pitch drop-off** + **negative sentiment** (you already capture per-message sentiment + like/dislike). *Upsell up + trust down = pitched too early → back the gate off.*
- **Vulnerable-vertical posture** (sobriety/finance/health): stricter gate, softer pitch, off-by-default for aggressive Track-3 mechanics — same default-restraint posture as voice and the quiz tease.

## B.8.3 — Universal vs. per-product (matches the existing config model)

- **Universal platform (build once):** the track state machine (entry/advance/exit triggers + the value-reached gate), the `post_purchase` sequence type, the in-chat upsell-CTA behavior, the upsell events/report.
- **Per-product config (no code):** what "quick win" and "desired outcome" *mean* per vertical, the mid-ticket price (`product_prices`/`pricing-plans`), the high-ticket route (Calendly/GHL link per product), the expert's voice in messages. This mirrors how products already configure paid plans + sequences today.
- **Resist:** per-client bespoke upsell flows — that's a "special." Config the universal tracks.

## B.8.4 — Measurement (uses the Section A events; no new analytics infra)

- **Primary — upsell:** `mid_ticket_subscribed / payment_completed` and `high_ticket_offer_clicked`(→`high_ticket_booking_confirmed`) per product, via **`clientUpsellReport`** (the sibling report defined in the Section A rewrite).
- **Secondary — return:** `nurture_reengaged` / `session_started` (Track 2 working). **Re-engagement ≠ upsell** — report them as distinct rows.
- **NOT the metric:** "messages sent" / "sequence enrolled" (output theater — pushing nurture into the void isn't upsell).
- **Guardrail:** refund/complaint + post-pitch drop-off + negative-sentiment delta around Track-3 entry.
- **Honor the intent-vs-confirmed split:** grade high-ticket on **intent** (`high_ticket_offer_clicked`) today, **confirmed** (`high_ticket_booking_confirmed`) once the Calendly/GHL webhook lands — never report intent as revenue.

## B.8.5 — Build order & tickets

Governance note: a new sequence type + new enrollment conditions + the in-chat CTA contract are **schema/registration changes → human-led PR-A** per `tree-architecture.md`; the content/copy and per-product config are Leaves.

- **Cheapest first move (do before building the engine):** **map what the 24–48h window does today.** `lead_nurture` stops at `payment_recorded`, so the post-purchase window is likely **empty**. If so, a *single* well-timed Track-2 quick-win message (no selling) is a cheap A/B against nothing — graded on `nurture_reengaged` + downstream `mid_ticket_subscribed`.
- **US-1 (PR-A)** — Add a **`post_purchase` predefined sequence type**, entered on `payment_recorded`, mirroring the `lead_nurture` scaffold but post-sale. *Done when: a paid client auto-enrolls and the engine drives steps.*
- **US-2 (PR-A)** — Add the **value-reached gate** as an enrollment condition (`client_first_milestone_completed`) that unlocks Track-3 steps. *Done when: Track-3 steps cannot send before the value signal.*
- **US-3 (PR-A + Leaves)** — **In-chat upsell CTA**: a v3 reply behavior/skill that emits a `ui_block` upsell card at gated moments — mid-ticket → `/paywall` (depends on re-enabling the inline paywall), high-ticket → external route — firing `high_ticket_offer_clicked`. *Done when: clicking the card routes correctly and fires the event.*
- **US-4 (Leaves)** — Emit `mid_ticket_subscribed` from the Whop subscription path (`dedupeKey = subscriptionId`) and wire **`clientUpsellReport`** into the existing snapshot/refresh job + reports UI.
- **US-5 (Leaves)** — Track-2 voice check-in hypothesis (B.4 dependency): "Hey, it's Sarah — how'd today go?" as a Track-2 step in emotional verticals. Gate on the B.4 live-voice work.
- **US-6 (defer → Section A AN-7)** — Calendly/GHL webhook ingestion for `high_ticket_booking_confirmed`. The only fully greenfield piece; gate the native-widget build on its drop-off number.

**Numbers we'd want (some now answerable from code/PostHog):** does the 24–48h window do anything today? *(Likely no — `lead_nurture` stops at payment.)* · baseline `nurture_reengaged` + `mid_ticket_subscribed` per product *(readable once US-4 + Section A land)* · can the plan system reliably fire the value signal that gates Track 3? *(`client_first_milestone_completed` exists — confirm it fires for all product types, not just plan products.)*

---

### Net

B.8 stops being "build a nurture/upsell engine" and becomes **"add a post-purchase sequence type + a value-reached gate + an in-chat CTA + the upsell events, on top of the email engine, client-plan system, and Whop billing you already run."** The trigger model, enrollment runtime, delivery vehicle, and subscription mechanism all exist. The genuinely new work is the **post-purchase track design, the trust gate, and the high-ticket booking webhook** — which is exactly where the leverage (and the trust risk) actually is.
