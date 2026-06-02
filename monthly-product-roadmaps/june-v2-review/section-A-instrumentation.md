# Section A (rewritten) — Funnel Instrumentation: close the *upsell* gap on the analytics package we already ship

> **Replaces the original "Item Zero — Build PostHog (PH-1…PH-10)."** That section was written without the codebase. The codebase shows PostHog is already wired, firing from real flows, and a drop-off report ships today. So Item Zero is **not** "build instrumentation" — it's **"turn config on (if off) + add the three event families the existing slice is missing, using the existing contract pattern."**

---

## A.0 — What already exists (do not rebuild)

`apps/analytics/` is a typed, provider-neutral event-contract package; `apps/api/src/services/analytics.service.ts` defaults to the **PostHog adapter** (`posthog.service.ts`), and `posthogQuery.service.ts` reads back via HogQL.

- **21 events already fire** from production call sites (see `docs/product-specs/product-analytics.md`): `funnel_visitor_seen → quiz_started → lead_captured → paywall_viewed → checkout_initiated → payment_completed → client_registered → client_onboarding_completed → client_plan_created → client_first_task_completed → client_first_milestone_completed → client_plan_completed`, plus `coach_*` health events.
- **The drop-off map ships today**: `apps/analytics/src/reports/clientDropOffReport.ts` + UI at `/reports/client-activation-drop-off` and `/super-admin/reports`, refreshed hourly by `jobs.refresh_analytics_report_snapshots` into `analytics_report_snapshots`.
- **Identity + dedupe partly solved by the contract layer**: `distinct_id` already resolves to coach/profile/wlProfile/**client** id, falling back to `anonymousId`/`lead_id` (`events/shared.ts` `getUserId` / `buildAnalyticsPayload`). The original roadmap's "identify by email/phone" work is already done — `clientEntitySchema` carries `email`/`phone`.
- **PII posture already correct**: every event property is structural (IDs, enums, amounts) — no conversation/diagnostic free-text. Keep that invariant.

**The one real status check (ops, not eng):** confirm prod has `POSTHOG_API_KEY` + `POSTHOG_ENABLED` set (capture no-ops without them) and `POSTHOG_PERSONAL_API_KEY` + `POSTHOG_PROJECT_ID` for server-side reports. If those are set, the activation funnel is already readable.

## A.1 — The gap (what the "first May slice" deliberately omits)

The existing slice measures **acquisition + activation** (visitor → buy → onboard → plan → complete). It does **not** measure the two things the strategy says matter most, nor the one Speed needs:

1. **Upsell** — no `mid_ticket_subscribed`, `high_ticket_offer_clicked`, `high_ticket_booking_confirmed`. (The funnel currently ends at plan completion, i.e. *engagement*, not *money*.)
2. **Return / retention** — no `session_started`, no `nurture_reengaged`.
3. **Speed grading** — no `generation_latency`. (And since the WL chat already streams token-by-token — `apps/electron/src/wl/queries/tasks.ts` `onChunk: currentContent += chunk` — the metric that matters is **time-to-first-token**, not raw total latency.)

Everything below adds those, **in the existing pattern**, so the same adapter/report/snapshot machinery picks them up for free.

---

## A.2 — New event contracts (drop into `apps/analytics/src/events/`)

Conventions followed: `defineAnalyticsEvent`, schemas extend the `requiredOrganization*` bases, props are snake_case derived from camelCase inputs, amounts in integer **cents**, `checkout_provider`/`route_type`/`mode` as enums. Base IDs + groups are auto-derived — not re-declared.

### 1. `events/midTicketSubscribed.ts` — mid-ticket upsell

```typescript
import { z } from 'zod';

import { defineAnalyticsEvent, requiredOrganizationProductClientSchema } from './shared';

export const midTicketSubscribedEvent = defineAnalyticsEvent({
  event: 'mid_ticket_subscribed',
  description: 'A client starts a recurring mid-ticket subscription (sub created or first invoice paid).',
  schema: requiredOrganizationProductClientSchema.extend({
    subscriptionId: z.string().min(1), // dedupe key — see A.5
    productPriceId: z.string().min(1).optional(),
    checkoutProvider: z.enum(['whop', 'external', 'manual']).optional(),
    amountCents: z.number().int().nonnegative().optional(),
    currency: z.string().min(1).optional(),
    billingInterval: z.enum(['month', 'year']).optional(),
    timeSinceLowTicketSec: z.number().nonnegative().optional(),
    isFirstSubscription: z.boolean().optional(),
  }),
  properties: (input) => ({
    subscription_id: input.subscriptionId,
    product_price_id: input.productPriceId,
    checkout_provider: input.checkoutProvider,
    amount_cents: input.amountCents,
    currency: input.currency,
    billing_interval: input.billingInterval,
    time_since_low_ticket_sec: input.timeSinceLowTicketSec,
    is_first_subscription: input.isFirstSubscription,
  }),
});
```

### 2. `events/highTicketOfferClicked.ts` — high-ticket **intent** proxy

```typescript
import { z } from 'zod';

import { defineAnalyticsEvent, requiredOrganizationProductClientSchema } from './shared';

export const highTicketOfferClickedEvent = defineAnalyticsEvent({
  event: 'high_ticket_offer_clicked',
  description: 'The AI surfaces the high-ticket offer and the client clicks/redirects out to the booking surface.',
  schema: requiredOrganizationProductClientSchema.extend({
    routeType: z.enum(['calendar', 'application', 'link']).optional(),
    destinationHost: z.string().min(1).optional(),
    offerId: z.string().min(1).optional(),
    placement: z.enum(['chat', 'plan', 'nurture_email', 'paywall']).optional(),
    timeSinceLandedSec: z.number().nonnegative().optional(),
  }),
  properties: (input) => ({
    route_type: input.routeType,
    destination_host: input.destinationHost,
    offer_id: input.offerId,
    placement: input.placement,
    time_since_landed_sec: input.timeSinceLandedSec,
  }),
});
```

### 3. `events/highTicketBookingConfirmed.ts` — high-ticket **confirmed**

```typescript
import { z } from 'zod';

import { defineAnalyticsEvent, requiredOrganizationClientSchema } from './shared';

export const highTicketBookingConfirmedEvent = defineAnalyticsEvent({
  event: 'high_ticket_booking_confirmed',
  description: 'A confirmed booking is received from the client booking tool (Calendly/GHL) or native widget.',
  schema: requiredOrganizationClientSchema.extend({
    product: requiredOrganizationClientSchema.shape.product.optional(),
    bookingId: z.string().min(1), // dedupe key — see A.5
    routeType: z.enum(['calendar', 'application', 'link']).optional(),
    bookingSource: z.enum(['calendly_webhook', 'ghl_webhook', 'native_widget']).optional(),
    timeSinceLandedSec: z.number().nonnegative().optional(),
  }),
  properties: (input) => ({
    booking_id: input.bookingId,
    route_type: input.routeType,
    booking_source: input.bookingSource,
    time_since_landed_sec: input.timeSinceLandedSec,
  }),
});
```

### 4. `events/sessionStarted.ts` — return/retention base event

```typescript
import { z } from 'zod';

import { defineAnalyticsEvent, requiredOrganizationClientSchema } from './shared';

export const sessionStartedEvent = defineAnalyticsEvent({
  event: 'session_started',
  description: 'A new end-user conversation session opens (powers PostHog Retention).',
  schema: requiredOrganizationClientSchema.extend({
    agent: requiredOrganizationClientSchema.shape.agent.optional(),
    task: requiredOrganizationClientSchema.shape.task.optional(),
    sessionId: z.string().min(1),
    mode: z.enum(['text', 'voice']).optional(),
    isFirstSession: z.boolean().optional(),
  }),
  properties: (input) => ({
    session_id: input.sessionId,
    mode: input.mode,
    is_first_session: input.isFirstSession,
  }),
});
```

### 5. `events/nurtureReengaged.ts` — post-purchase return in the 24–48h window

```typescript
import { z } from 'zod';

import { defineAnalyticsEvent, requiredOrganizationClientSchema } from './shared';

export const nurtureReengagedEvent = defineAnalyticsEvent({
  event: 'nurture_reengaged',
  description: 'A client returns and interacts >=1h after their low-ticket purchase.',
  schema: requiredOrganizationClientSchema.extend({
    agent: requiredOrganizationClientSchema.shape.agent.optional(),
    sessionId: z.string().min(1).optional(),
    hoursSincePurchase: z.number().nonnegative(),
    reengagementSource: z.enum(['organic', 'nurture_email', 'voice_checkin', 'push']).optional(),
  }),
  properties: (input) => ({
    session_id: input.sessionId,
    hours_since_purchase: input.hoursSincePurchase,
    reengagement_source: input.reengagementSource,
  }),
});
```

### 6. `events/generationLatency.ts` — Speed grading (TTFT-first, since chat already streams)

```typescript
import { z } from 'zod';

import { defineAnalyticsEvent, requiredOrganizationSchema } from './shared';

export const generationLatencyEvent = defineAnalyticsEvent({
  event: 'generation_latency',
  description: 'Latency profile of one AI response (TTFT is the primary metric because chat already streams).',
  schema: requiredOrganizationSchema.extend({
    agent: requiredOrganizationSchema.shape.agent.optional(),
    client: requiredOrganizationSchema.shape.client.optional(),
    task: requiredOrganizationSchema.shape.task.optional(),
    timeToFirstTokenMs: z.number().int().nonnegative().optional(),
    timeToFirstAudioMs: z.number().int().nonnegative().optional(), // mode=voice only
    latencyMs: z.number().int().nonnegative(),
    tokensOut: z.number().int().nonnegative().optional(),
    mode: z.enum(['text', 'voice']).optional(),
    stage: z.enum(['onboarding', 'reply', 'plan_generation', 'diagnostic']).optional(),
    streamed: z.boolean().optional(),
    replyLengthVariant: z.enum(['current', 'mode_matched', 'naive_cap', 'unset']).optional(),
  }),
  properties: (input) => ({
    time_to_first_token_ms: input.timeToFirstTokenMs,
    time_to_first_audio_ms: input.timeToFirstAudioMs,
    latency_ms: input.latencyMs,
    tokens_out: input.tokensOut,
    mode: input.mode,
    stage: input.stage,
    streamed: input.streamed,
    reply_length_variant: input.replyLengthVariant,
  }),
});
```

## A.3 — Register them (`events/index.ts`)

Add the six exports/imports and append to the `analyticsEvents` array (order: upsell, then retention, then latency):

```typescript
export { midTicketSubscribedEvent } from './midTicketSubscribed';
export { highTicketOfferClickedEvent } from './highTicketOfferClicked';
export { highTicketBookingConfirmedEvent } from './highTicketBookingConfirmed';
export { sessionStartedEvent } from './sessionStarted';
export { nurtureReengagedEvent } from './nurtureReengaged';
export { generationLatencyEvent } from './generationLatency';
// ...matching imports...

export const analyticsEvents = [
  // ...existing 21...
  midTicketSubscribedEvent,
  highTicketOfferClickedEvent,
  highTicketBookingConfirmedEvent,
  sessionStartedEvent,
  nurtureReengagedEvent,
  generationLatencyEvent,
] as const;
```

## A.4 — Typed track methods (`apps/analytics/src/analytics.service.ts`)

Mirror the existing `trackX` methods (one per event), e.g.:

```typescript
trackMidTicketSubscribed(input: z.input<typeof midTicketSubscribedEvent.schema>) {
  return this.trackContract(midTicketSubscribedEvent, input);
}
trackHighTicketOfferClicked(input: z.input<typeof highTicketOfferClickedEvent.schema>) {
  return this.trackContract(highTicketOfferClickedEvent, input);
}
trackHighTicketBookingConfirmed(input: z.input<typeof highTicketBookingConfirmedEvent.schema>) {
  return this.trackContract(highTicketBookingConfirmedEvent, input);
}
trackSessionStarted(input: z.input<typeof sessionStartedEvent.schema>) {
  return this.trackContract(sessionStartedEvent, input);
}
trackNurtureReengaged(input: z.input<typeof nurtureReengagedEvent.schema>) {
  return this.trackContract(nurtureReengagedEvent, input);
}
trackGenerationLatency(input: z.input<typeof generationLatencyEvent.schema>) {
  return this.trackContract(generationLatencyEvent, input);
}
```

## A.5 — One required contract change: idempotency keys (PR-A / Tree change)

Money/booking events must dedupe (Whop webhook retries, own-checkout + webhook double-fire). The current payload builder doesn't set a PostHog event `uuid`. Add an optional `dedupeKey` to `analyticsBaseSchema` and map it into the PostHog payload as `uuid` in `buildAnalyticsPayload` (`events/shared.ts`):

```typescript
// shared.ts → analyticsBaseSchema
dedupeKey: z.string().min(1).optional(),

// buildAnalyticsPayload → posthog mapping
posthog: {
  event: input.event,
  distinct_id: distinctId,
  ...(input.input.dedupeKey ? { uuid: input.input.dedupeKey } : {}),
  properties: { ...properties, $groups: groupIds, event_time: input.input.occurredAt || null },
},
```

Then callers pass `dedupeKey: subscriptionId` / `dedupeKey: bookingId` / `dedupeKey: paymentId`. **This touches the shared contract + the PostHog mapping type — per `tree-architecture.md` it's a TREE/BRANCHES change → human-led PR-A before any Leaf PRs that emit the new events.**

## A.6 — New report: `reports/clientUpsellReport.ts`

Don't overload "Client Activation Drop-Off" (it ends at plan-complete). Add a sibling report in the identical `metric({...})` shape so the snapshot/cache/UI machinery reuses it. Anchor every metric on `payment_completed` (the confirmed low-ticket buy that already exists):

```typescript
import {
  paymentCompletedEvent,
  sessionStartedEvent,
  nurtureReengagedEvent,
  midTicketSubscribedEvent,
  highTicketOfferClickedEvent,
  highTicketBookingConfirmedEvent,
} from '../events';

export const clientUpsellReport = {
  id: 'client-upsell',
  name: 'Client Upsell & Return',
  description: 'Track return and monetization after the low-ticket purchase: return, mid-ticket sub, high-ticket intent vs. confirmed.',
  metrics: [
    metric({ id: 'payment_to_returned',          name: 'Buyer returned (any session)', denominatorLabel: 'payment', denominatorEvent: paymentCompletedEvent, numeratorLabel: 'returned session', numeratorEvent: sessionStartedEvent }),
    metric({ id: 'payment_to_nurture_reengaged', name: 'Buyer re-engaged in window',    denominatorLabel: 'payment', denominatorEvent: paymentCompletedEvent, numeratorLabel: 're-engaged',       numeratorEvent: nurtureReengagedEvent }),
    metric({ id: 'payment_to_mid_ticket',        name: 'Mid-ticket upsell',          denominatorLabel: 'payment', denominatorEvent: paymentCompletedEvent, numeratorLabel: 'mid-ticket sub',   numeratorEvent: midTicketSubscribedEvent }),
    metric({ id: 'payment_to_high_ticket_click', name: 'High-ticket intent',            denominatorLabel: 'payment', denominatorEvent: paymentCompletedEvent, numeratorLabel: 'offer clicked',    numeratorEvent: highTicketOfferClickedEvent }),
    metric({ id: 'high_ticket_click_to_confirmed', name: 'Intent → confirmed (correction ratio)', denominatorLabel: 'offer clicked', denominatorEvent: highTicketOfferClickedEvent, numeratorLabel: 'booking confirmed', numeratorEvent: highTicketBookingConfirmedEvent }),
    metric({ id: 'payment_to_high_ticket_confirmed', name: 'High-ticket confirmed',      denominatorLabel: 'payment', denominatorEvent: paymentCompletedEvent, numeratorLabel: 'booking confirmed', numeratorEvent: highTicketBookingConfirmedEvent }),
  ],
} as const;
```

**Honest labeling carries over:** `payment_to_high_ticket_click` is **intent**, not revenue — render it with the "estimate" treatment and surface `high_ticket_click_to_confirmed` as the correction factor (it'll be empty until a client has Calendly/GHL webhooks wired). **Retention** itself is the PostHog Retention insight on `session_started` per `client` group — config, not a metric row.

## A.7 — API call sites (where each fires — mirrors the product-analytics.md table)

| Event | Fire from |
|---|---|
| `mid_ticket_subscribed` | Whop subscription webhook / `applyWlProductSubscriptionBillingEvent` (`activateWhopClientPlan.workflow.ts`), on `subscription.created` / first `invoice.paid`, `dedupeKey = subscriptionId` |
| `high_ticket_offer_clicked` | v3 reply runtime when the high-ticket CTA `ui_block` is clicked/redirected (and the nurture-email click handler) |
| `high_ticket_booking_confirmed` | **NEW** Calendly/GHL webhook ingestion route (still greenfield), matched to the lead by email = `distinct_id`, `dedupeKey = bookingId` |
| `session_started` | WL session/task open (server-side at task create in the unified chat path), with `mode` text/voice |
| `nurture_reengaged` | `handle_email_sequence_trigger` job + WL session-open guard computing `hours_since_purchase` from `payment_completed` |
| `generation_latency` | v3 reply graph (`runUnifiedAgentV3Chat.workflow` / `reply.main.ts`) on response completion; stamp `time_to_first_token_ms` at first streamed chunk; `time_to_first_audio_ms` from the voice path |

## A.8 — Revised ticket list (replaces PH-1…PH-10)

1. **AN-0 — Confirm PostHog config in prod** (`POSTHOG_API_KEY`/`POSTHOG_ENABLED` + personal key/project for reports). *Done when: the existing activation drop-off report shows non-zero data for ≥3 live products.* (Likely already true — verify first.)
2. **AN-1 (PR-A) — Add `dedupeKey` to the contract + PostHog `uuid` mapping** (A.5). Human-led contract PR.
3. **AN-2 (PR-A) — Add the 6 event contracts + registry + track methods** (A.2–A.4). Human-led (schema/registration).
4. **AN-3 — Emit upsell events**: `mid_ticket_subscribed` (Whop sub path), `high_ticket_offer_clicked` (v3 CTA). Leaf PRs once AN-2 is on main.
5. **AN-4 — Emit return events**: `session_started` (session open), `nurture_reengaged` (email-trigger + window guard).
6. **AN-5 — Emit `generation_latency`** with TTFT (text) + time-to-first-audio (voice).
7. **AN-6 — Ship `clientUpsellReport`** (A.6) + wire into the existing snapshot/refresh job + reports UI.
8. **AN-7 (defer) — Calendly/GHL webhook ingestion** for `high_ticket_booking_confirmed` (per consenting client). This is the only genuinely greenfield integration; gate the native-widget build on its drop-off number, as the original A.6 argued.

**Net:** the original Section A's 10 tickets collapse to "config check + 6 events in the existing pattern + 1 report + 1 deferred integration." The heavy lifting (SDK, identity, dedupe-of-payments, drop-off report, snapshot cache, server-side capture, PII posture) is already done.
