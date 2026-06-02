# B.2 (Speed) & B.4 (Voice) — rewritten against the actual codebase

> Same treatment as Section A / B.8: what the blind roadmap *assumed*, what the code *actually does*, and what that changes. Metrics use the locked term **upsell** (return is kept separate).

---

# B.2 — Speed & Responsiveness (rewritten)

## What the blind roadmap assumed
- The "single biggest win" is **token-by-token streaming everywhere** — and if any path waits for full completion before rendering, adding streaming "alone likely closes most of the gap."
- It hedged: *"whether any response path is currently buffered rather than streamed — if yes, that's the smoking gun."*

## What the code actually does
**The end-user text chat already streams token-by-token, with a typewriter effect.** The smoking gun the roadmap hoped for does not exist for text.

- `apps/electron/src/wl/queries/tasks.ts` — `sendWLMessageStreaming({ onChunk: (chunk) => { currentContent += chunk; ...setQueryData(content: currentContent) } })`. Tokens append to the live message as they arrive.
- Per-message `isStreaming` flag, a **Stop** control during generation, `useWLCancelStream`, `onStreamDone`, and a `WLTypewriter` component (referenced at `tasks.ts:518`).
- API side streams via SSE: `POST /wl/chat/unified/send` and `POST /chat/send` (ARCHITECTURE.md: *"Streaming — Real-time token streaming to clients via event emitters"*).
- **Instant-ack + optimistic UI partly exist too**: `Chat.tsx` appends the user's message and an `isStreaming` AI placeholder immediately, shows a typing indicator (`isTyping={generating}`), and re-focuses the input on stream-done. So Tier-1 items B (sub-100ms ack) and C (optimistic UI) are largely already there for the user's own message.

**The one genuinely non-streamed path is voice** (see B.4): TTS waits for the *finished* text reply, then synthesizes. That's the real "buffered" path — not text.

## What changes
1. **Delete "add streaming" as the headline win — it's shipped.** Don't fund a streaming project; it exists.
2. **Retarget Speed at the two things that are actually still slow:**
   - **Time-to-first-token (TTFT)** — the dead-air *between send and the first streamed chunk*. This (not total latency, not "add streaming") is the remaining text-chat "clunky." Instrument it (`generation_latency.time_to_first_token_ms`, added in the Section A rewrite) before spending any infra money.
   - **The voice path** — genuinely batch end-to-end (B.4).
3. **Skeleton/progressive rendering for structured output** (plans/verdicts) is the one Tier-1 item with real remaining value, because plan/verdict generation is heavier than a chat turn — paint the frame (title, empty slots) before content. The plan system exists (`WLChatHomeTasksCard`, plan rendering); this is about *first-paint during generation*, not building the plan UI.
4. **The Tier-2/Tier-3 infra work (pre-warming, model right-sizing, prefetch) stays gated on the `generation_latency` data** — but now the gate is real, because the event is specced and the brain already streams, so TTFT is measurable on day one.

## Revised Speed scope (what June should actually do)
- **SP-1** — Emit `generation_latency` with `time_to_first_token_ms` + `tokens_out` + `mode` + `stage` from the v3 reply runtime (`reply.main.ts`); stamp TTFT at first streamed chunk. *(Shared with Section A AN-5.)*
- **SP-2** — Read the baseline: is TTFT actually bad, and where (onboarding vs reply vs plan_generation)? **Only then** decide if any infra work is warranted.
- **SP-3** — Skeleton/progressive paint for plan/verdict generation (the heaviest turns), measured on abandonment-during-generation.
- **SP-4 (gated on SP-2 data)** — pre-warm context on chat open / model right-sizing — *only if* TTFT data proves raw model time (not dead-air) is the bottleneck. Guardrail unchanged: never downsize the model on a diagnostic/reasoning turn to shave ms.

**Grade on:** session continuation + drop-off-during-wait + **upsell** (not "did we add streaming," not raw latency in isolation).

**Numbers now answerable from code/PostHog (were "don't have"):** the brain streams (confirmed — yes); the text UI renders progressively (confirmed — yes); current TTFT + tokens/sec (readable once SP-1 lands). The roadmap's open question *"is any path buffered?"* is answered: **only voice.**

---

# B.4 — Voice: Make It Feel "Live" (rewritten)

## What the blind roadmap assumed
- "We already HAVE voice input and output" — the job is making it *feel live* (streaming chain + VAD + barge-in), and it's mostly "polish."

## What the code actually does
**The premise is correct — but both halves are turn-locked "walkie-talkie," exactly what the roadmap contrasts against a live call.** This is a bigger build than "polish," and it starts from a `legacy/` endpoint.

- **Voice output = TTS playback of a *finished* message.** `apps/api/src/api/legacy/wl/voiceStream.ts` (`streamMessageAudio`): takes an already-complete `wlMessage.message_content`, calls `textToSpeechStream(agent.elevenlabs_voice_id, message_content)`, streams the mp3 to the client **and** caches it to R2 (`tts/<agent>/<messageId>.mp3`). It's an on-demand **play button** per AI message (`Chat.tsx` `handlePlayAudio` / `AudioPlayButton` / `getStreamingUrl` → `/wl/voice/stream/:messageId`), not automatic. *Note the `legacy/` path.*
- **Voice input = push-to-talk dictation.** `apps/electron/src/wl/hooks/useVoiceRecorder.ts`: records the **whole utterance** (`MediaRecorder`), on stop builds one `Blob`, base64-encodes it, sends it to `transcribeVoiceChunk` (server-side STT), and drops the returned text into the input box. Despite the `…Chunk` name it sends the **complete** recording — no streaming STT, no VAD, no auto-send.
- **What's solid and reusable:** ElevenLabs is wired (`elevenlabs.service.ts` `textToSpeechStream`), per-agent **voice cloning** exists (`agent.voice_enabled`, `elevenlabs_voice_id`, `voice-cloning/page.tsx`, `add_voice_cloning_to_agents.sql`), and `generateMessageAudio.workflow.ts` exists.
- **What's absent (confirmed by search):** no VAD, no barge-in, no streaming brain→TTS chain, no continuous listening, no duplex/realtime/WebRTC.

So today's "voice" = text chat with a dictation button bolted on the front and a read-aloud button bolted on the back — three serial waits (record→transcribe, then generate full text, then synthesize). That is the precise pattern B.4 §2 says to replace.

## What changes
1. **Reframe scope: not "polish," but "replace two batch halves with a live pipeline."** The roadmap's premise stands; its effort estimate doesn't.
2. **The starting point is a `legacy/` TTS endpoint + a whole-blob recorder** — so step one is replacing, not decorating.
3. **The §3 Option A vs B architecture fork is still the right call**, but framed as: "replace the legacy generate-then-speak path." Option B (ElevenLabs Conversational-AI layer with our brain as the custom LLM) is more attractive *precisely because* we'd otherwise be hand-building VAD + barge-in + streaming-STT + streaming-TTS orchestration that does not exist today.
4. **The viability gate (§7.5) gets sharper, not softer.** Today voice = one cached mp3 per message (cheap, on-demand). A live pipeline adds streaming STT + streaming TTS *per turn, per call* — a real per-use cost. Run the cost-per-call number against the $17 entry + retainer **before** funding real-time infra.
5. **Voice output streaming already half-exists** — `voiceStream.ts` streams ElevenLabs audio to the client; it just starts *after* the full text. The live-feel build reuses that streaming plumbing but feeds it **token-by-token from the brain** (which already streams — see B.2) instead of waiting for the finished message. That's the highest-leverage single change and it's smaller than greenfield because both ends (brain stream, audio stream) already exist — they're just not connected.

## Revised Voice scope (what June should actually do)
- **VO-1 (the spike that gates everything)** — CTO connects the **already-streaming brain tokens** → **already-streaming ElevenLabs TTS** (today they're serial), measure **time-to-first-audio** vs the <1s target; in parallel spike whether the brain plugs into ElevenLabs' Conversational-AI layer (Option B) with the content-boundary guardrail intact. *This one spike decides the architecture.*
- **VO-2** — Auto end-of-speech (VAD) to replace push-to-talk in `useVoiceRecorder` (record→stop→send becomes hands-free).
- **VO-3** — Barge-in (interrupt by speaking).
- **VO-4** — Full-screen call UI with Listening/Thinking/Speaking states + hang-up (today it's a per-message play button, not a call surface).
- **VO-5 (gated on VO-1 cost number)** — broad rollout vs. "voice in high-value moments only," per the viability gate.
- **Guardrail (unchanged, now load-bearing):** TTS speaks only post-guardrail content; verify the content boundary survives whichever architecture (esp. Option B). Regulated verticals off-by-default.

**Grade on:** caller **return + upsell** vs. text users (cohort by `voice_mode_enabled`) — never call length/duration. Liveness leading indicators: time-to-first-audio, VAD mis-fire rate, barge-in rate, call-completion.

**Numbers now answerable from code (were "don't have"):** does the brain stream tokens? **yes** (so the streaming-chain is a *connect*, not a *build*). Current voice turn-around = record-stop → transcribe → full-generate → synthesize (fully serial — that's the baseline to beat). Whether voice input exists: **yes, as PTT dictation** (so VAD replaces it, doesn't add a missing capability).

---

## Net for both
- **B.2:** the big assumed win (streaming) is **done**; retarget to TTFT + skeleton paint + the voice path, and stop before infra spend until `generation_latency` proves a real bottleneck.
- **B.4:** the premise is **right** but it's a **replace-the-batch-pipeline** build, not polish — and the single highest-leverage move (connect the already-streaming brain to the already-streaming TTS) is smaller than the roadmap feared because both ends already exist. The cost-per-call gate is the real go/no-go.
