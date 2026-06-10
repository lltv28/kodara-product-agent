# Conversational Design: Complete Key Lessons Reference

**Source:** *Conversational Design* by Erika Hall (A Book Apart, 2018, foreword by John Maeda)
**Purpose:** Knowledge base for product design decisions. All content paraphrased and distilled for practical application.

---

## 1. Core Thesis

- Conversation is humanity's original, oldest, and most universal interface. Every person already knows how to use it.
- "Conversational design" does NOT mean chatbots or voice UI. It means designing ANY interface (web, app, voice, text, email, notification) according to the principles that make human conversation work.
- A system can have zero natural language chat and still be deeply conversational (Google Search). A system can literally chat and be completely non-conversational (most chatbots).
- Interactive digital design is still trapped in its graphic design and publishing roots: portfolios of rectangles, screens treated as pages, words treated as "content" to pour in last.
- Words are a design material, equal to visuals and code. Language should be the basis for defining and creating the design, considered from the very start, never a separate "writing" track.
- The deeper goal: systems should evoke the best qualities of living human communities (active, social, simple, present) rather than passive, isolated, complex, or closed off.
- Software is becoming a cultural peer; a system standing in for a human should feel like interacting with a human. What matters is conversational qualities at a deep behavioral level, never a superficial chat veneer.

### The Great Divide (the problem being solved)
- Organizations run visual/interaction design decisions and word decisions on two different tracks: design decisions get iteration, collaboration, and trust; word decisions get editorial approval chains, fear of commitment, and authority battles.
- Teams form around artifacts people produce (mockups, copy decks, code) instead of the problem they should solve together.
- Users experience one system, all at once. They do not experience the information architecture, then the visual style, then the copy.
- Symptom checklist of the divide: writers asked whether icons can replace text; designers sweating over lorem ipsum; one writer assigned to 5-10 products while each product has dedicated designers; copy frozen after approval while pixels iterate freely.

---

## 2. Foundational Concepts and Definitions

- **System:** a set of interconnected elements that influence one another. A person is a system; a computer is a system; an airline is a system.
- **Interface:** a boundary across which two systems exchange information. A prerequisite for interaction.
- **Interaction:** the means by which systems influence each other.
- **Value exchange:** people interact on purpose to get value. An interface is effective to the extent it helps both parties get what they need from each other.
- Until recently the interface between an organization and a customer was usually another human. Now organizations deploy many digital interfaces, and customers expect them all to behave as one interconnected system (even when they aren't).
- Modern context fragmentation: people occupy multiple contexts and roles simultaneously across multiple devices, like being in several rooms at once. Therefore "experience design" overstates control: any single system contributes only a fraction of someone's overall experience. Reward existing expectations instead of demanding people learn new interface conventions.

### Orality and literacy (the historical argument, via Walter Ong)
- Speech is roughly 200,000+ years old (possibly emerging 1.75M years ago); writing is only ~6,000 years old (cuneiform, ~3200 BCE). Humans have had writing for about 4% of human existence. Conversation is the default; writing is the recent, effortful exception.
- **Orality (pre-literate culture):** words exist only as ephemeral events in time; knowledge survives only by being shared memorably (meter, formula, vivid concrete imagery, repetition, proverbs); knowledge is social, communal, and intimate; communication is immediate, face-to-face, and context-dependent.
- **Literacy:** decoupled knowledge from social interaction; enabled precision, complexity, solitude, authority, ownership, and intellectual property; writers address generic future readers they will never meet and get no feedback from. Excellent for precision, terrible for interaction.
- Literate culture promotes authority and ownership; oral culture promotes participation and community.
- Every leap in communication technology disrupts culture and upsets incumbents (clergy vs printing press, teachers vs texting). Resistance is futile.
- **Secondary orality:** digital communication (texting, social media, Slack, Wikipedia) returns us to oral dynamics: immediate, group-minded, conversational, collaborative, intertextual, present-tense. But business and educational culture still rewards literary values (individual authorship, posterity, approval from authority), creating cultural lag inside organizations.
- Wikipedia as case study of secondary orality: anonymous collective authorship, free output, conversation-driven revision (talk pages), arguments resolved inside living documents. It demonstrated that conversation can be more powerful than authority, while still depending on traditionally published sources for credibility. Lesson: new technology is valuable when it brings more of what is already meaningful, never because it is new.

### From documents to events
- Documents organize information in space; conversation and modern digital life organize it in time. Design must shift from "how does information occupy space" to "how does the exchange unfold in time."
- Texting proves minimal media can carry maximal humanity: with a few words and considered capitalization, text alone delivers personality, pathos, humor, and narrative.
- Texting's grip on attention comes from always-available social connection plus dopamine-driven unpredictability, never from rich media or feature complexity. The interesting part of an interaction happens in the mind, and language is the substrate of mind.
- Approaching communication as assembled "content" makes customers who expected a conversation feel handed a manual instead.
- The screen-based perspective was never truly human-centered. The ideal interface is unnoticeable: thought-to-action distance collapses. Design should consider the whole constellation of inputs, outputs, events, and information around a person.

---

## 3. How Conversation Works (the linguistics)

- The study of context contributing meaning beyond words is **pragmatics**.
- **Grice's Cooperative Principle (paraphrased): find the goal of the exchange and do your part.** Conversation only works because all parties tacitly cooperate to keep it on track (like a crowd keeping a beach ball aloft). Without cooperation there is no conversation, only misunderstanding.

### Grice's four maxims + Lakoff's politeness
1. **Quantity (just enough information):** be as informative as required, and no more. Requires empathy: "the right amount" is judged from the other person's point of view.
2. **Quality (be truthful):** do not say what you believe false; do not claim what you lack evidence for. Beyond not-lying: be authentic and transparent about identity, motives, and agenda. Trust requires each party to sense the other's identity and motivations.
3. **Relation (be relevant):** contribute in ways relevant to the purpose of the conversation, which is often implicit (small talk's real purpose is establishing goodwill, not exchanging weather data). Skilled conversationalists are appropriate, never merely relevant: higher-order context awareness includes knowing when to gather more information or yield the floor.
4. **Manner (be brief, orderly, unambiguous):** get to the point; sequence information logically (directions must come in navigation order); kill ambiguity, which taxes the listener's mind and time. Brevity is necessary but insufficient: short and empty is still a failure.
5. **Politeness (Robin Lakoff, 1973):** don't impose; give options; make the listener feel good. Respect and good feeling for all participants.

- Violating any maxim derails conversation: the too-vague answer (Quantity), the agenda-driven misdirection (Quality), the self-centered non-answer (Relation), the rambling monologue (Manner), the condescending reply (Politeness).
- Humans tolerate unintentional violations gracefully when overall cooperation holds; conversations recover without missing a beat. This graceful recovery is natural for humans and extremely hard for computers, which is exactly why it is a design target.

---

## 4. The Eight Principles of Conversational Design

Hall's translation of the maxims into design principles. Apply to ANY interface in any mode or medium, from online banking to appliances.

1. **Cooperative**
   - The system actively supports the customer; using it requires no special effort, knowledge, or "computer literacy" (a euphemism for forcing humans to think like software, per Alan Cooper).
   - Requires deep organizational commitment to stay on the customer's side; absent that, technology dictates the interaction.

2. **Goal-oriented**
   - The most basic principle of interaction design. A successful interaction helps BOTH parties meet goals pleasantly and efficiently.
   - Design is impossible without a clear goal. If you cannot answer "who benefits from using this," cut it.

3. **Context-aware**
   - Device data (location, time zone) is a start, but only user research reveals what customers need at different times, places, and circumstances.
   - Anti-patterns: automated promotional posts during disasters; security questions assuming married parents, living grandmothers, dating histories, and pain-free childhoods.
   - The ability to respond to context is THE fundamental difference between documents and conversation.

4. **Quick and clear**
   - Perceived speed is subjective and matters as much as actual speed. Few things delight more than finishing fast.
   - Ambiguity slows everything and burdens the customer. Precision is what machines do best; never undermine it with humanly imprecise language. Save ambiguity for poetry; keep it out of payment systems.
   - Error messages are the classic ambiguity failure ("Fatal Error 773"); even natural language can create anxiety when wording implies risk at the wrong moment (a save dialog that sounds like gambling with your data).

5. **Turn-based**
   - Conversation is a brisk, relatively even exchange; otherwise it becomes a monologue.
   - Acceptable turn length scales with value delivered and expectations set. Complex processing may take time IF the system signals what's happening (e.g., a visual "thinking" cue prevents users from assuming breakage).
   - Request or provide the right information at the right time; verify before consequential actions (a server repeats the order; a computer validates input before hard-to-undo actions).
   - Everyone must always know whose turn it is (conference calls fail here).

6. **Truthful**
   - Truthfulness means intentionally setting correct expectations, never merely avoiding literal lies. Credibility is the foundation of business relationships and small missteps damage it.
   - Defining truth as "what the system knows to be true" or "not technically a lie" is lazy. Interactive truth = strong correlation between what the user expects and what the system delivers. Unreachable without research.
   - Example: vague "Get started" links perform no better than login walls because they trick users into committing before understanding (Nielsen Norman Group finding).

7. **Polite**
   - Respect the time of someone giving you attention. Politeness can mean making their time MORE productive than expected, or anticipating needs before they ask.
   - Whether more choices or fewer choices is politer depends entirely on context and the customer's goals.
   - Full-screen mobile ads are the design equivalent of a stranger at the bus stop aggressively pivoting small talk into a car sales pitch.
   - Polite designs meet business goals WITHOUT interrupting the customer's pursuit of their own objectives.

8. **Error-tolerant**
   - Machines break but don't make mistakes; humans err constantly regardless of intelligence. Brains run on stimulus-response and habit, not logic. Errors come from carelessness, nerves, or old habits in new situations.
   - A truly conversational interaction tolerates faults, anticipates errors, and recovers seamlessly. By this standard most "conversational interfaces" are poor, and many ordinary websites are better.
   - How a machine responds to human error is the best available test of seeming humane: calculation and retrieval are easy; supportive redirection is hard.

### The paradox of conversational interfaces
- A superficial resemblance to conversation (natural language in text or voice) OVERPROMISES cooperation and ends up delivering a LESS human experience when the system can't keep up.
- Donald Norman's complementarity argument (*The Invisible Computer*): stop simulating human minds in computers and stop forcing humans to conform to machine demands. The calculator is valuable BECAUSE it is unlike the brain; capitalize on the difference.
- Humans act on emotion and rationalize afterward; computers follow programmed logic. The design challenge: use intentional reasoning to help people decide without thinking and form habits without effort.
- Exchanging structured information (e.g., a menu) can feel MORE conversational than chat because the value of the exchange is apparent.

---

## 5. The Principles in Practice: Case Studies

### Google Search (the gold standard)
- A conversational interface that predates the chatbot hype by twenty years, with essentially the same starting point since 1998: wordmark, text box, two buttons. It never needed redesign, only cleanup, because the conversational model was right; voice input layered in later without breaking anything.
- Lesson: with the right framework, capabilities can be layered in without breaking the model. Interactions should flow like conversation regardless of medium, letting users switch modes by context.
- It is fast AND advertises its speed (results count + fraction of a second), making it FEEL fast. Perceived speed is subjective: identical engines under different visual layouts test as different speeds (the Yahoo/Google licensing anecdote, confirmed in usability testing).
- It matches user intent rather than diverting users for business reasons (contrast: supermarkets put milk in the back on purpose). Its business improves the better it serves intent, a rare and powerful alignment.
- Error tolerance as competitive advantage: garbage input ("lsgn recp") still returns the intended lasagna recipe; the same string on the source site returns nothing. Forgiving input made Google feel like a partner and made it rich.
- Effortless turn-taking teaches users to be better information seekers: each query revision is cheap, each turn teaches something, and the system never seems to break.
- Counter-lesson: do NOT conclude that open input fields are the ideal navigation. An open input is a near-infinite choice set, appropriate only when possible responses vastly outnumber possible requests. Menus are often faster and friendlier than endless bad guesses.

### Chat is often the wrong tool (2018-era warning, still structurally true)
- Documented drawbacks of chat/voice interfaces: weak context awareness (probing would be creepy); slower than self-service clicking; unpredictable (users can't tell what's possible or what input works); fragile under unexpected input.
- An H&M shopping bot on a messenger created a more awkward interaction than simply browsing the website; hybrid chat/menu UIs can consume screen space without adding value over a website.
- Before building chat, ask honestly whether it makes life easier than the alternative.

---

## 6. Key Moments Framework

Every interaction is a series of moments: stages in the customer's knowledge of and relationship with the system. Same principles apply throughout, with shifting emphasis. All moments intertwine; elements serve multiple moments and must reinforce each other harmoniously.

- **Introduction:** creates strong positive first impressions, invites interest, encourages trust.
- **Orientation:** establishes (and re-establishes) system boundaries, concept organization, and possibilities for action toward a goal.
- **Action:** the tasks supported and the controls for accomplishing them.
- **Guidance:** how the system ensures success: instructions, feedback, ongoing relationship.
- (Plus error recovery throughout.)

Human analogy: a ski instructor. First you need credible identity and enthusiasm; then how the lesson meets your goal and your options; mid-lesson you need help succeeding and care when things go wrong; returning the next day you only need recognition that it's the same trusted person.

### 6a. Introductions: who are you?
- First impressions form in milliseconds and persist. Even famous hosts re-introduce themselves every episode; reintroduction reinforces emotional connection.
- From the customer's perspective, ALL representatives of the system (site, app, emails, support, ads) are one entity. Coordinate them.
- Be descriptive and concrete in first-moment copy. A Slack team invitation email works because it states plainly what the thing is and what to do; Twitter's early signup page wasted its critical moment with vagueness.
- Dale Carnegie logic applies: become genuinely interested in the other party; speak in terms of their interests, never your org chart's.

### 6b. Orientation: where am I, what is this?
- Structural navigation answers (per James Kalbach): Will I find what I need here? Where am I? How do I start over? What was I doing? What is the scope of this place?
- Navigation labels must be straightforward, conventional category names (Budgets, Goals). Navigation is NEVER the place for novel concepts or clever branding ("mystery meat" labels age terribly; imagine a highway sign reading "Crude & Nom-Noms" instead of "Gas & Food").
- Information foraging (Peter Pirolli): users run continuous cost-benefit analysis like hunting animals; they want the fastest possible signal of whether they're on a path to success. Surprises and mysteries hurt.
- Event boundaries (Gabriel Radvansky): crossing a doorway resets memory; the same happens crossing information spaces. As visual cues shrink or vanish (voice, IoT), reorientation must be deliberately designed.
- Orient around the user's intention, not the device. The Amazon Echo failure: ~80% of owners used it only for music and timers (2016 Experian study) because nothing in the interaction cued the full range of possibilities or linked one task to the next. Ask "how do we make this device more valuable to both parties, and where do cues go," never "how do we make customers buy more through it."
- Some information is easy to say but hard to type, and vice versa (Laura Klein). Use cross-modal orientation: the speaker talks, the phone screen shows.

### 6c. Choices are work
- Hick's Law: more choices = longer decisions. Decisions are work. Minimizing and sequencing choice is among the most humane things a design can do (echoes Quantity and Manner).
- Offer the right choices at the right time: Google narrows AFTER results, never before; food delivery asks for your address first because the best restaurant that won't deliver to you is irrelevant; travel booking starts with flight/hotel/car before dates.
- Order by consequence: most meaningful or constraining choices first. The anti-pattern is the automated phone tree: unpredictable, banal, interminable.
- No one with a goal wants the full range of possibilities (paralyzing). They want to know whether they can do the thing they have in mind right now. Impress the full range, then eliminate nearly all of it.
- Remembering past behavior enables timely, appropriate choices (reorder previous order) as long as suggestions never block new paths.
- Prediction is delicate: nobody wants to feel predictable. Preserve at least the illusion of control.
- The ethics of menus (Tristan Harris): people rarely ask what's NOT on the menu, why these options, whose goals the menu serves, or whether the menu distracts from the original need. Limited choice sets can manipulate while feeling like freedom. Facebook's Like flattens billions of relationships into one-dimensional snap reactions, and social convenience makes the hijack stick.
- This adds an ethical dimension to Truthfulness: a cooperative system does not lead users down bad paths, is transparent about the business goals it represents, and encourages actions valuable to both parties. Each choice is minor; they compound.

### 6d. Action: what can I do?
- The verbs ARE the product. The most fundamental design decision is what the system lets people do; the verbs are the point of the conversation.
- The hardest interface work is translating between human action (habit, hope, feelings, fears, patchwork mental models) and machine action (rules, precision). Often you must decompose a single customer action into parts and rebuild it.
- Actions must be goal-oriented (move customer toward objective), context-aware (reflect customer state AND system state), and provide feedback (success/failure plus what's next). Implications must be apparent. Truth in interaction = matching expectations; this is the path to trust.
- Sequence must feel cooperative; unexpected ordering creates work. It is the designer's duty to do hard work so the customer's interaction is easy.
- All actions must be reversible OR carry very clear warning when they represent commitments. Omitting or misrepresenting consequences = failing truthfulness. Recovery flows are actions too; the same principles apply, with the goal of restoring confidence that the system is on the user's side.
- **Context-of-action checklist.** The system must communicate, implicitly or explicitly, and efficiently:
  1. Prerequisites to action (only show actions the user can actually take)
  2. Encouragement to action (articulate benefits)
  3. Instructions for action (simple click or complex path?)
  4. Consequences of action (set expectations for what happens next)
  5. Level of commitment (is undo possible?)
- A good example: a streaming purchase button that names the action, the immediate benefit, and the financial consequence (price/subscription) all at once. All information needed to act is present at the point of action.
- The phrase defines the action. Button copy matters more than button style; styling only supports understanding, and no styling overcomes misleading wording. Icons still represent commands that must be unambiguous.
- Beware the single stray word: a polite-sounding qualifier ("especially") on an ATM option introduced doubt, pulled the customer out of flow, created anxiety, and stole time. Every word is a choice with implications.

### 6e. Feedback, habit, and delight
- Habits are cognitive shortcuts: cue, routine, reward, strengthened by repetition (Duhigg/MIT framing). Habits free conscious attention and are often disconnected from goals.
- Nothing requiring conscious thought will ever be as usable as habit. Habits are hard to compete with.
- Learning loops (Amy Jo Kim): the simplest coherent system is a feedback loop. System feedback that helps users get visibly better at using the system creates mastery, which is its own reward and aligns customer goals with system goals.
- Facebook example: post (action), Likes (feedback), feeling better at Facebook (reward), regardless of any higher-order goal.
- "Delight" warnings: designers underestimate the cognitive cost of unfamiliar systems and overestimate the pleasure of the reward. Result: novel-but-hard interfaces that frustrate, or jokes that charm once and grate on repetition (the barista's knock-knock joke). Design for the twentieth exposure, not the first.

### 6f. Guidance: the system wants you to succeed
- Even well-designed systems need to explain themselves. Misplaced minimalism produces confusion (and profanity). Unambiguous action labels PLUS contextual guidance serve both habitual and new users; expecting a label or icon to do all the work makes interfaces worse.
- Five guidance opportunities: sales (pre-commitment problem/solution fit), instructions (in-context stage directions, hints, onboarding), contingency messages (off-path events, including errors), notifications (interruptions outside the interaction context), documentation (reference material for study outside interaction).
- Best service is nearly invisible (the drink topped up unnoticed; the pen offered with paperwork). In interaction design this is progressive disclosure: just what's needed for the task at hand, with paths to more.
- **Memory rule: computers store and recall; humans don't.** All information storage and retrieval in the relationship belongs to the computer. Any system that depends on human memory has failed its human. Password authentication is the web's most epic failure by this standard, making password recovery the most-used feature of secure systems. Never require users to retain information; always confirm before dangerous actions; warn gently near the rails. Tax the robots, never the apes.
- Use stored information to benefit customers, but don't be creepy: surveillance-flavored "we noticed you..." messaging destroys the comforting illusion of neutral utility.
- Instructions belong AFTER the choice point they support, never piled up before it (a bank's password recovery page buried the choice under preemptive explanations: too much information at the wrong time, violating Quantity and Manner). Read instructions aloud to catch this.

### 6g. Notifications
- Notifications let systems serve without continuous attention, and are trivially easy to abuse. Engagement hunger clouds judgment; proliferation across systems and devices produces constant interruption, the opposite of feeling served.
- Require affirmative consent; earn trust and deliver value BEFORE asking. Approach from politeness, with a holistic strategy across every messaging channel the system uses.
- Helpful notifications are: well-timed (arrive when the customer can respond); concise and clear; personalized and relevant (general messages go elsewhere); valuable and actionable (urgency without possible action only manufactures anxiety); trust-rewarding (every notification is a potential trigger for shutting all of them off).
- Appropriate (per Google's Material guidelines, broadly applicable): messages from a person the customer wants to hear from; help meeting a goal or improving quality of life (catch the delayed flight); system state changes suggesting or requiring action.
- Never appropriate: advertising without explicit opt-in; messages with no customer value; situations with no possible action.
- Context-aware exemplar: routing notifications only to the device the person is actively using (Skype's "active endpoint"), keeping every other device silent.

### 6h. Onboarding
- Onboarding = helping a new (or long-absent) customer feel at ease, in control, and productive. Dropping people in cold is unkind; nobody likes feeling inept.
- Required amount depends on three factors: (1) how different the system is from what the customer already knows, (2) conceptual complexity, (3) effort required before useful payoff.
- Draw on existing mental models and minimize effort-before-value. If concepts are genuinely new or complex, design a dedicated new-user experience: integrated into the overall product and as lightweight as possible, the opposite of a long up-front tutorial.
- Let users self-sort by expertise and let them skip ahead; make time commitments explicit (Duolingo pattern).
- The best onboarding is the least intrusive. It is NOT a delightful experience in itself; it is the shortest defensible path to value. Identify the actions that deliver the greatest value (requires research), the barriers to reaching them, and exactly what's needed to clear each barrier.

### 6i. Errors and mistake-proofing
- Expecting "correct" behavior is a failure of imagination and produces fragile interactions. Designers demand creativity of themselves but don't anticipate it in users.
- Human error, from the computer's perspective, is just unanticipated input. Most system responses remain variations of "does not compute." The humane response mirrors good human behavior: gently correct, redirect, and prevent recurrence.
- When the SYSTEM fails: branding can charm for minor outages (Twitter's fail whale became a cultural icon), but glibness is unacceptable for serious setbacks, especially data loss. Imagine a doctor burning your medical records and shrugging. Sincere apology + fast path to resolution.
- BASAAP ("be as smart as a puppy," Matt Jones): endearing failure works for systems still learning, and sets honest expectations for machine intelligence. BUT nobody depends on puppies for important goals: failure is not endearing during flight booking or money transfer. Match the metaphor to the stakes.
- Never block a path forward: an error that announces a roadblock while presuming knowledge the customer lacks (e.g., demanding device deregistration with no link or path) is a hostage situation.
- **Poka-yoke** (Japanese manufacturing mistake-proofing; the microwave that can't run with the door open): constrain input within safe boundaries; menus instead of open text inputs mistake-proof forms; walk every scenario where correct-as-designed use could still cause harm (misspelled medication names).
- The Gmail pattern: detecting "attached" phrasing with no attachment and asking before send. Preventing your customer from looking careless earns loyalty forever.
- Error prevention starts at the first moment of awareness: begin with a concept matching users' mental models. You can't control expectations and associations, only understand them, looking beyond your own product to find where they come from. Assume everyone is on autopilot, drawing unconscious inferences from the barest cues: open-ended input implies any answer works; a too-human voice sets expectations too high.

---

## 7. The Power of Personality

### Why personality is mandatory
- Personality is the force that attracts or repels and heavily influences decision-making (Aarron Walter). Conversational principles are nothing without a unifying personality: the animating spirit that brings the experience to life.
- The more sophisticated the interaction, the larger personality's role (a golden retriever has more personality than a goldfish).
- Humans anthropomorphize involuntarily (faces in outlets, talking to plants). **If you don't craft a personality intentionally, customers will assign one in their minds, and it will be worse than the one you would design.**
- Without intention, organizations default to interchangeable corporate sameness: everything sounds like the same pair of beige slacks. If your offering is genuinely distinct, the way you talk about it should be too. A world of undifferentiated identical voices is dystopian (the *Anomalisa* example).
- The fewer senses a service engages, the more work falls to voice. Chatbots are nothing without personality; a bot may have to compress its entire experience into two lines of text (Ben Brown). Voice UIs additionally force gender presentation decisions with real emotional and social consequences.

### Definitions (use precisely)
- **Brand:** the sum of associations in the customer's mind. You influence it; you don't control it.
- **Personality:** the consistent set of human characteristics the system is designed to embody in sound and behavior. Even a wordless system can have one if it displays or elicits sufficiently human emotion.
- **Voice:** increasingly best reserved for the audible aspects of an interface (since devices now literally speak).
- **Character:** a named entity apart from the brand that personifies some or all attributes (mascots; a chimp that smiles and winks but never speaks). Optional.
- Identity architecture is a strategic choice: a company can offer multiple identities (platform, product, named agent) or deliberately decline to personify ("you're interacting with all of Google"). Naming a female persona to perform domestic tasks carries gender-politics weight that an unnamed system avoids (the Alexa vs Google Home contrast); these choices affect real people's feelings and require nuance.

### How to build a personality (process)
1. **Research first, always.** To connect emotionally you must understand what people are emotionally connected to; otherwise you build the personality YOU want to befriend and rationalize it. Do user research before design and continuously through it. Non-optional.
2. **Listen to customer language.** Interview representative customers about their typical day; be quiet and let them talk; do this about six times; then extract all nouns and verbs from notes. That vocabulary becomes the reference for labels, actions, and phrases. The goal is to be intelligible and meaningful to customers and trigger the right associations, NOT to mimic them (the interface is not a peer). Shed internal phrasings.
3. **Map the emotional terrain.** Anticipate negative states: anxious, confused, overwhelmed, skeptical, bored, hangry, frustrated. Work through negative scenarios with other people to find the balance of clarity and humanity. Understand how the product helps someone be who they want to be.
4. **Like people.** (Screenwriter's discipline.) You can't hate people and effectively sell them ideas; cynicism produces impersonal system-centered design under a marketing veneer (you can taste a sandwich made without love; orgs that say "eyeballs" and "uniques" ship it). Liking customers means appreciating their different perspective, not identifying with them. Care or don't expect care back.
5. **Clarify values collaboratively.** Every business has implicit values in its business model; unexamined ones default to "we're here to make money doing stuff," which produces bland marketing tone. You can't adopt values that contradict how you make money. Values work must involve key stakeholders, in the context of designing a living interaction, NEVER handed down from on high as abstract brand guidelines for designers to interpret later.
   - Mad lib exercise 1 (values): "We will be successful when [outcome]." / "We care about [thing] because [reason]" (x3). Answers must sound like something a person would say aloud.
   - Mad lib exercise 2 (role + adjectives): "If we were a person serving our customer, our job would be [role]. Customers would describe us as the most [adj], [adj], and [adj] of any in that profession. We never want to come off as [neg adj], [neg adj], or [neg adj]."
   - Worked example (a music streaming service): success = providing the soundtrack to subscribers' lives; role = DJ/music librarian; aspire to savvy, eclectic, perceptive; never snobby, stale, narrow.
   - Then: list all words you want your audience to use about you, list all adjectives to avoid, narrow to three unique positives and three biggest concerns. Those six words guide all work.
6. **Find the human analog for your role.** Skip abstract exercises (what car would your app be?) and celebrity-emulation exercises (research shows everyone converges on George Clooney). Instead name the real profession: real estate agent, maître d', bike shop mechanic, banker, funeral director. Streamline its language for online use.
- "Too conversational" never means "too informal": doctors, bankers, and funeral directors all have conversations within professional propriety. A banking system should sound like a reliable helpful banker; a funeral service like a compassionate director; a game villain like a malevolent synthetic intelligence (GLaDOS). Conversational ≠ casual.
- Register mismatch is jarring in both directions: a skate shop sounding like a bank, or a mortgage lender saying "sick."

### Personality dimensions to define explicitly
- **Identity:** Is the product the face of the org? Are customers talking to the company, the service, or a named agent? Degree of distinct identity sets expectations for functionality and familiarity.
- **Expertise:** What should the system know, about what? A teller expresses expertise differently than a private banker or advisor.
- **Mood and attitude:** Most interfaces default to neutral-cheerful. Define it explicitly, especially for more human personalities. Cautionary tale: the chronically depressed robot as a spoof of over-humanized computers (Marvin).
- **Relationship:** Advisor, teacher, assistant, or tool? Clarity here yields cohesive cues. Many systems blend several.
- Roles flex by surface while core character stays stable: the same personality can sound like a supervisor in one flow and a support agent in another, the way different employees of one company do. Context determines tone; character stays fixed.
- Brand-true interaction design: a luxury jeweler frames "send a hint about this gift" with discretion in keeping with its role of stylishly interceding between giver and recipient; the same copy on a mass retailer would be fussy, and a generic "email this to a friend" would tarnish the jeweler. Personality lives in mundane interaction details.
- Never cast the product as a clingy romantic partner in win-back or cancellation flows. Supportive and on the customer's side at every point, including negative moments.

### Localization
- **Transcreation, never translation:** adapting personality across languages and cultures means recreating the emotional effect and implications, not the literal meaning (advertising industry concept since the 1960s). Example: idiomatic expressive phrases require region-specific versions per locale ("speechcons" localized for UK/Germany).
- Human-mimicry features (e.g., whispering assistants) can land creepy rather than warm; test the uncanny valley.

### Words: craft guidance
- Rediscover the joy of language; banish habits absorbed from exposure to worst practices. Humanity's verbal peaks: jokes and poetry. Its most shameful sin: soulless corporate jargon.
- **Humor:** depends on context and timing, like interaction design itself; it relieves tension and aids attention/learning, but what's funny to one person is incomprehensible or offensive to another. The reliable rule: don't TRY to be funny; deep audience understanding reveals what tickles them. Humor that plays against expectations works (an exasperated photo caption delights BECAUSE captions never talk like that; a nihilist fast-food parody works against chipper brand voice); humor on repeat-exposure surfaces wears out fast (the unicorn high-fiving a t-rex is funny once, infuriating by the twentieth meal).
- **Design for repetition and mood:** begin with the customer's likely state (hungry, impatient, multitasking). Lead with the crucial information (when does food arrive), never canned enthusiasm. Triumphal copy for trivial acts reads as trying too hard; random fake-personalization ("roof parties, commutes, fight club") advertises how little you know the customer. Test: would this sound odd coming from the family at your corner store? Then it's odd from your product.
- **Expose yourself to excellence:** language is socially imitative; immersion in banking sites and clickbait makes you parrot mush. Study conventions in your domain, but seek inspiration in the physical world: go where customers go, listen to how people in the target role actually talk, collect and catalog good and bad voice samples (notebook, screenshots, shared doc, wall, Slack), note what makes each effective. Send the team out to eavesdrop and report back.
- **Read poetry aloud, together:** deliberate intentional language unrelated to your work descales the mind like vinegar in a coffee maker. Recommended entry point: the Imagists (William Carlos Williams, H.D., Amy Lowell, Marianne Moore): common speech, novel rhythms, clear precise images. Pound's manifesto, applied to interfaces: no superfluous word; no adjective that doesn't reveal; fear abstractions; the natural object is the adequate symbol. He would hate "innovative solutions" and the Submit button. The plums poem ("This Is Just to Say") proves spare, concrete, conversational language lodges permanently in brains.
- Capable writers don't embellish.

### The avoidable-words list (build your own; starters)
- A warm-up: write the most bureaucratic or robotic version of your interface first, for fun, then find the real voice. Starting with negative space is easier.
- Avoid: meaningless filler that costs time without benefit; clichés any of a million products could use (dilutes differentiation; even world-class orgs ban "world-class").
- Specific bans with reasons:
  - **Compelling:** wishful, not descriptive.
  - **Helpful:** never self-describe; customers decide.
  - **Quick:** is it, really?
  - **Innovative** (and intelligent, smart): used up.
  - **Important:** say WHY it's important, then delete the word; calling one thing important demotes everything else.
  - **Check out these hot topics:** adds nothing; be specific, describe.
  - **Oops!:** adults reserve it for trivial mishaps; systems must own errors in adult words.
  - **Submit:** describe the specific action the button triggers. Forever.
  - **My [items]:** see pronoun rule below.
  - **Click here:** a crutch that breaks across voice/text mode switching.
- Rule of thumb: if you have to say it, you probably aren't it.
- Make the list explicit BECAUSE language is social and repeating threadbare phrases is the path of least resistance.

### Pronoun rules
- Natural conversation uses I/we and you. Third-person self-reference is strange.
- "My X" interfaces (a lineage running from 90s desktop and portal conventions through endless imitators) feel like sticky labels on objects: presumptuous and alienating rather than ownership-reinforcing; mixing "My" and "We" surfaces produces absurd collisions ("We Apologize" next to "My Account").
- Correct mapping: things belonging to the company = "our" (our privacy policy); things belonging to the user = "your" (your cart, your profile); ambient experience parts often need no possessive at all.
- Voice interfaces force the issue: a spoken "from my music library" makes the digital butler assert property rights in your living room. Address customers as "you," consistently.

### Non-verbal design
- Words carry only a fraction of human communication; body language, expression, and vocal tone infuse meaning. Digital systems lack bodies, so conventions evolve to compensate (ALL CAPS = shouting).
- Every nonverbal channel is part of the personality: timing, motion, sound, color, typography, spacing. These must be designed with the same intention as words and must agree with the words (a coordinated whole, per the consistency lessons throughout).

---

## 8. Getting It Done: Process and Organization

### Conway's Law and Hall's corollary
- Conway's Law (Melvin Conway, 1967): any organization designing a system produces a design copying its own communication structure. First websites mirroring org charts is the classic case; even subtler designs fossilize the processes and priorities that produced them.
- **Hall's corollary: the degree to which a system feels human and goal-oriented in its interactions reflects how well its creators interacted with each other.**
- A harmonious interface = functional interdisciplinary communication + clear, well-informed decisions. Visible seams = handoffs and unresolved arguments. Diagnostic reading of a product: a wall of legal text means legal owns that step; overwhelming choices mean territory battles or aimlessness; indecipherable errors mean design left before engineering arrived, or errors were never designed at all.
- A design project is a series of decisions; how the org decides determines how much each person's skill reaches the product.
- Design's fundamental paradox: the process for making new things becomes itself a comfortable barrier to change. Organizations are shared habits; "process" is a business word for habit; changing habits without a crisis takes tremendous effort even when the new way is better. Creating a design process IS interaction design: people sharing a system need the right behavior and the right information at the right time.

### What needs to change: the four problems (waste + missed opportunity)
1. **The words-and-pictures divide:** verbal and nonverbal decisions must happen in the same process at the same time. Teams divide along tool/artifact lines; few tools support cross-disciplinary collaboration. (Case study: a design-committed software company gave every product dedicated visual + interaction designers but one writer across 5-10 products, traditional edit workflows, fiat changes from above, and zero feedback loops, so writers could never learn whether their work succeeded; writers in turn guarded a polished-first-draft "creative process" suited to documents, not systems. Result: multidisciplinary but not collaborative; boxes filled in with language; authority over wit.)
2. **Lack of innovation:** comfort with current methods is the greatest barrier; artifact-centric process lets teams repackage familiar thinking in shinier wrappers and call it progress.
3. **Confusing polish for value:** teams uncomfortable communicating across disciplines create artifacts to prove their worth; once an artifact exists, sunk investment makes weak ideas hard to discard.
4. **Systems-oriented tunnel vision:** Agile/Lean at their core optimize building software, not solving human and business problems; build speed and feature count get conflated with value. Goal of all good design: maximum value, minimum features. More thinking, less building.
5. **The Tower of Babel:** disciplines cling to their dialects; in-group/out-group instincts fragment information. Orgs can't solve problems holistically from different information sets. Talking together early beats passing documentation around.

### The conversational work culture (the 8 principles applied to teams)
- Cooperative: members support each other toward mutual success; the culture's products reflect and influence one another.
- Goal-oriented: all efforts serve a clear goal.
- Context-aware: priorities, constraints, and real-world conditions inform all problem-solving.
- Quick and clear: everyone keeps things moving and works to eliminate fuzzy thinking.
- Turn-based: listening is the underrepresented workplace skill; smart verbal professionals are trained to talk. Everyone waits their turn, truly listens, contributes, and responds; work is an interactive collective process, never a sum of individual contributions.
- Truthful: candid assessments and complete information are welcome AND expected.
- Polite: shared, respected communication norms.
- Error-tolerant: a safe environment where wrong suggestions and occasional failure are a productive part of process.
- These qualities recognize the humanity of every team member, and that style of interaction transfers into systems that feel human, humane, and lively.
- Realism: organizations at scale legitimately require some authority and documentation: a fact, never a flaw. The requirements are clear goals plus willingness to continuously reflect and improve.

### Concept → Script → Sketch (replacing screens-first design)
- Concepts beat patterns: most software remains bad because of concepts, not buttons (Alan Cooper). The moment you start drawing screens, you stop being human-centered.
1. **Concept:** the big idea, the reason to exist. Surface delight can't save a weak idea. Strong concepts are device-independent and survive across modes.
2. **Script:** the exchange of information that breathes life into the interaction; the core. Work collaboratively (whiteboard, shared doc, text editor, interactive story tools like Twine). Multimodal multi-device interaction design is new; tools are scarce; the medium matters less than scripting meaning in time.
3. **Sketch:** only now, the interface supporting that exchange, situated in the larger context of the user's life.
- Benefit: the same value exchange can then be designed through multiple channels and interfaces coherently. It feels unnatural to postpone spatial visualization; do it anyway.

### Minimum Meaningful Conversation (pre-MVP)
- MVP still centers the system; it's fine for iterating within existing systems but answers questions nobody verified were asked. For higher-order problems (you don't yet know whether the thing is a product), step further back: a thought experiment before the experiment, about understanding the exchange of value from the customer's perspective, in moments of time rather than screens in space. Otherwise you keep patching bad ideas with features.
- Start from customer intent: the questions they ask, the needs they express, and crucially WHERE and HOW you show up at the moment they have the problem (too often abandoned to marketing; if you don't design this moment, solution quality is irrelevant).
- Key questions:
  1. How will the customer express their need, and how will you understand their intention?
  2. What exchange happens at that point?
  3. What must the customer give you for you to solve their problem?
  4. What choices do you give them?
  5. How do you close the conversation leaving a felt impression of value delivered?
  6. How do you leave the door open to future interactions?

### Minimum Viable Conversation Worksheet (complete the grid BEFORE any solution sketches, as a team)
| Moment | Customer side | System side |
|---|---|---|
| Context | What's happening? Where are they? What tools, abilities, information do they carry? | Where is the system present in this context? |
| Event | What makes the customer aware of the problem? | How does the system become aware of the customer? |
| Intention | How do they respond to and express the need? | How does the system register the intention? |
| Introduction | How must the customer be identified to the system? | How does the system appear meaningful and credible? |
| Orientation | What's the customer's model of the conceptual space? | How does the system establish and communicate boundaries? |
| Action | What motivates interaction? | What action is offered, and what is its value to the customer? |
| Guidance | What help might they need to complete the action? | How does the system support their success? |
| Error | What could go wrong? | How does the system get them back on track? |
| Closure | How do they know it concluded successfully? | How does the system finish strong and seed future interaction? |

### Prototyping and testing
- Early prototypes exist to separate production polish from idea value (because teams fall in love with labor-intensive artifacts, even shiny vessels for weak ideas).
- The best way to design, prototype, AND test interactive systems is interacting with people.
- **Wizard of Oz method:** a human simulates the system, intercepting all user-system communication. Works for messaging and voice naturally, and for screen UIs using sketches. The human proxy can be visible or hidden; representative test participants suspend disbelief either way. This removes technological constraints from the design space, reveals true customer expectations and ideal interaction logic, and tests far more flows and language far faster than building. Afterward, creative problem-solving closes the gap between the ideal and available technology; perceived machine intelligence can be delivered by human ingenuity. Playing the system yourself tests the interaction from both sides and is uncomfortable for screen-trained designers, productively so.

### The best small changes (immediately adoptable practices)
1. **Reiterate goals and principles** constantly; forgetting is easy, repetition is powerful (surgeons and pilots use checklists; yours prevents killing time).
2. **Work in real time:** include all perspectives simultaneously instead of circulating artifacts for comments; use real-time collaboration tools; minimize internal email.
3. **Encourage candid feedback:** everyone practices giving, receiving, and responding; feedback and critique are the essence of a lively iterative process.
4. **Include decision-makers** early and live (even remotely); it saves multiples of the meeting time later.
5. **Talk about decisions over artifacts:** frame design conversations around creating an experience and exchanging information in time, never laying out elements in space.
6. **Never permit lorem ipsum.** No placeholder language, ever. Language and meaning belong at the center; specific language is part of design and subject to continuous iteration, never a parallel "writing" process.
7. **Read aloud every customer-facing word.** The essential test for meaning and timing; the only way to verify the interface works across text and speech modes; also lightens the false permanence of "the written word."

### Closing principles
- People are complicated and messy; digital tools and processes are too often used to avoid dealing directly with one another, even while designing tools for other people. The true purpose of process is making humans more efficient, never eradicating the humanity.
- Look past surface, components, and code to what the problem means to human beings.
- Future-proofing argument: even mind-controlled computing won't change human nature. Humans have always been creative, social, insecure, argumentative creatures looking for meaning; communicating is our greatest joy and terror; machine intermediaries let us feel close but not too close, and can make things weird. Every day is an opportunity to avoid making things weirder than necessary.
- The compressed method: know your goal; listen before you speak; create a system that sings.
- The best conversation starter, for products and teams alike, is a question: what are we here to do?

---

## 9. Quick-Reference Heuristics (operational distillation)

### Red flags in a product review
- Any screen, feature, or message whose beneficiary you cannot name
- "Get started" or any CTA that hides what the thing is before commitment
- Open text input where a short menu would do (or a phone-tree-style menu where memory of context would do)
- Instructions stacked before a choice instead of after it
- An error message with no path forward
- An action without visible consequences, commitment level, or undo
- A qualifier word introducing doubt at a decision point
- Notification without possible user action
- Copy that wouldn't survive being read aloud
- Cheerful filler ahead of the one fact the user needs
- "My X" labels, "Submit" buttons, "Click here" links, "Oops!" errors
- Identical message tone regardless of customer mood or stakes
- Personality that varies randomly across surfaces (introduction email vs error vs billing)
- A delightful flourish positioned where users will hit it daily
- Reliance on the user remembering anything the system could remember

### Red flags in a team/process review
- Copy on a separate approval track from design
- Lorem ipsum anywhere
- One writer fractionally allocated across many products
- No feedback loop telling writers whether their language worked
- Artifacts circulating for asynchronous comments as the primary collaboration mode
- Deliverables discussed instead of decisions
- Sign-off culture where authority can change approved work by fiat
- New features justified by build velocity rather than user value
- Discipline jargon walls between design, engineering, writing, business

### Decision rules
- Mode selection: structured choices when intent is predictable; open input only when possible responses vastly outnumber possible requests; chat/voice only when genuinely easier than self-service
- Choice design: smallest number of most appropriate choices, most consequential first, narrowing after intent is expressed, remembered behavior offered but never blocking
- Memory: machine remembers everything, human remembers nothing; confirm before consequence; warn near rails
- Tone: match the human professional analog for your role; stable character, context-flexed tone; design for the twentieth exposure
- Truth: expectation match, verified by research, including what you show before signup and what the button says will happen
- Process: concept before script before sketch; conversation worksheet before prototype; Wizard-of-Oz before code; read it all aloud before shipping
