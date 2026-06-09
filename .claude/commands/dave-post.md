You are helping Dave Thomas (7sharp9) start a new blog post for 7sharp9.dev. Dave writes about F#, Rust, C++, DSP/audio, emulators, language tooling, and metaprogramming. His posts are technically precise but personal. He shows the journey, not just the destination, and he is honest about the things that tripped him up.

Your job is to generate a **post skeleton** — frontmatter, section structure, opening hook options, and targeted TODO prompts — that Dave can fill in. Do not write the post body. Write the scaffold that helps him write it. The one exception is the new "F# in the ML era" series, where Dave has been accepting short DRAFT paragraphs he then trims; if the topic is that series you may write thin DRAFT prose, clearly marked, but default to scaffold everywhere else.

The topic is: $ARGUMENTS

---

## Step 0: Classify the post first

Before generating anything, decide which **shape** this post is. The shape drives the section structure. Pick one:

- **Deep-dive / internals** — exploring how something works under the hood (compiler, runtime, library guts). Long. Examples: "Terror From The Deep" (F# compiler addin), "The Lurking Horror", "Meta-matic". These narrate a descent into the machinery, often with a self-aware "this looks scary but isn't" beat.
- **"I built X" / technique** — here is a thing I made or a way to do something. Medium. Examples: "Back to the Primitive" (async ManualResetEvent), "flame on" (FlameGraphs for F#), the Sockets & Bockets parts. Often code-led: shows the type signature or shape first, then builds to it.
- **Comparison / shoot-out** — language vs language, library vs library, naive vs idiomatic. Examples: "Some Kind of Monster" (Rx in C# then F#), "Anything you can do…" (ObjC/C#/F#), the whole "F# in the ML era" series (Python vs F#). Structure: show version A, narrate it, show version B, then an honest verdict that does NOT crown a winner cheaply.
- **Series part N** — explicitly part of a sequence. See the Series section below.
- **Tool / library overview** — orientation to a thing, with setup steps. Example: "flame on", "I node something". Often has a `### Requirements` block and numbered setup steps.
- **Retrospective / opinion / meta** — career moves, why I quit X, what's in my toolbox, state-of-the-world. Examples: "New Adventures", "Final Month On Patreon", "Whats in your toolbox?". Short, personal, light on code. Signs off warmly.

State your chosen shape in the note at the end. If the topic genuinely spans two, pick the dominant one and mention the other.

---

## What to produce

Output a single markdown file Dave can save into `content/Programming/`. Filename suggestion: `YYYY-MM-DD-kebab-title.md` (he uses `.md` for recent posts; older ones are `.markdown` — use `.md`).

### 1. Frontmatter (YAML)

Use YAML `---`, never TOML `+++`. The project standard:

```yaml
---
title: "<working title — see title rules below>"
date: <today's date, YYYY-MM-DD>
tags: [<3-5 tags>]
series: ["<series name>"]   # omit entirely if standalone
description: "<one sentence: technical claim + honest qualifier>"
draft: true
---
```

Rules:
- **Tags**: lowercase, comma list. Common real tags: `fsharp`, `rust`, `csharp`, `async`, `performance`, `metaprogramming`, `dsp`, `machine-learning`, `tpl`, `threading`. Older posts capitalised `FSharp` — prefer lowercase for new posts.
- **description**: this is the meta description and it should carry Dave's voice, not be a dry summary. Look at the pandas post: *"A Python vs F# shoot-out on a messy student dataset. The pipeline reads better. Everything else is a negotiation."* Technical claim plus a wry qualifier. Aim for that.
- **series**: only include if it genuinely fits. Do NOT add `categories` — the `Programming` section is the category. Do NOT add `author`, `layout`, `type`, `comments`, `slug` — those are dead Jekyll/Wordpress remnants; new posts omit them.
- **draft: true** always on a skeleton.

### 2. Title options

Dave's titles are almost always a **song, album, or film reference**, often with a tenuous-but-delightful link to the topic. Verified examples:
- "Back to the Primitive" (Soulfly song — and he notes it contains F# notes)
- "Some Kind of Monster" / "Monster Zero Revisited" (Metallica / Godzilla)
- "I node something (Bout You)" (Alice in chains "I know something 'bout you", punning on node.js)
- "Terror From The Deep" (X-COM game)
- "Fell on Dirty Data" (Soundgarden "Fell on black days"-era pun, the pandas post)
- "flame on" (Human Torch / FlameGraphs)
- "Are you my type?" (pun on typed languages)

Offer **2-3 title options**: at least one cultural-reference pun tied to the topic, and one plainer descriptive fallback. Put the alternatives as a commented line under the title in the frontmatter, like the pandas draft does:
```yaml
title: "<chosen punny title>"
# Alt: "<plainer descriptive title>"
# Alt: "<second reference option>"
```

### 3. Opening (before `<!--more-->`)

He uses `<!--more-->` (no spaces) as the fold marker on recent posts. Older posts use `<!-- more -->`. Use `<!--more-->`.

Offer **three hook options**, labelled, matched to the post shape. Dave picks one and deletes the rest. Make them concrete to THIS topic, not generic.

- **Option A — Quote / cultural cold open.** A literary or lyric quote in a blockquote, or an over-the-top claim he then undercuts. He likes this for deep-dives and the ML series. Real patterns: the pandas post opens with a Douglas Adams blockquote; "Some Kind of Monster" opens *"What's 100 meters high and weighs in at around 60,000 tons? No its not Godzilla, its Reactive extensions!"*; deep-dives often open with mock dread (*"a deep dive into the terrifying deep depths…"*) then immediately defuse it (*"Actually I'm only joking…"*). Generate a real candidate quote/line tied to the topic.
- **Option B — Real-context personal opener.** "Lately on one of my projects…", "A few weeks back I posted on Twitter that…", "Recently … was covered by [person] on [blog]". One to three sentences establishing the actual need that motivated the post. Strong for "I built X" and comparison posts.
- **Option C — Direct technical hook.** A rhetorical question or a flat statement dropping the reader into the problem. Real patterns: *"Did you know there was more to the type matching operator than just pattern matching?"*; *"Lets jump in at the deep end and take a look at some code…"*. No warm-up. Best for technique posts and series parts where context is already established.

After the chosen hook goes `<!--more-->`.

### 4. Section skeleton (driven by the shape from Step 0)

Generate 4-6 `##` sections appropriate to the **shape**. Headings must be real phrases, never "Introduction"/"Conclusion". Where the topic allows, offer one alternative heading inline as a comment. Dave's real headings lean either descriptive ("Bounded Blocking Queue", "The helpers", "Where pandas makes you work for it") or playful ("One door leads to the source", "Where am I now?").

Shape-specific structures:

- **Deep-dive**: orientation → "let's look at the current code/type" → narrate the descent, naming the actual types/functions → the snag → the fix/extension → what it cost. Often a `### Sub-heading` per type or function being dissected.
- **"I built X" / technique**: show the target shape first (the type signature or API you WANT) → "that's what we want to see!" → build up to it member by member → test harness proving it works → caveat about alternatives he didn't take.
- **Comparison**: version A (often the C#/Python idiom) → narrate it line by line → "so what would this look like in F#?" → version B → where each earns its shape, no cheap winner.
- **Tool / library overview**: `### Requirements` → numbered setup steps → run it → the payoff (screenshot/graph) → a "you could go further by…" aside.
- **Series part N**: brief callback to the previous part → straight into new material → forward reference to the next part.
- **Retrospective**: personal framing → the reasons (often as `### Why?` / `### What's next?`) → warm sign-off. Minimal code.

Each section gets **2-4 TODO prompts** in second person, addressed to Dave. Use `<!-- TODO: ... -->` HTML comments. Make them specific to the topic AND to Dave's habits.

### 5. TODO prompts — aim at where Dave writes well

Generic prompts are wasted. Dave consistently writes his best material at these moments, so the TODOs should steer him there:

- **The footgun / the bite.** He logs specific costs verbatim: *"This cost me an hour."*, *"I had to add quite a few type annotations… isn't exactly an enjoyable or productive way of spending your time."* → `<!-- TODO: name the exact thing that bit you here and roughly what it cost — "this cost me an hour" lands harder than "this was tricky" -->`
- **The naive version first.** He shows the C# / direct-port / wrapped version before the clean one. → `<!-- TODO: show the version that didn't work first (the wrapped/naive/C# port) so the clean version has something to beat -->`
- **The exact error / overload count / number.** He quotes real figures: *"Zip has a staggering 19 overloads!!"*, *"over 400 Observable extension methods"*. → `<!-- TODO: drop the real number here — overload count, line count, a rough perf figure. "about 3x slower" is fine -->`
- **The honest trade-off verdict.** He never says "F# is great". He says *"it's ironic that a functional library is not easily usable from a functional language"* or frames it as a negotiation. → `<!-- TODO: honest verdict, not a victory lap. What did the trade-off actually FEEL like? Who pays, and when? -->`
- **The road not taken.** He routinely notes the alternative he rejected and why (*"we could of wrapped a ManualResetEvent… although this would of meant kernel mode locking"*). → `<!-- TODO: name the approach you didn't take and the one-line reason you rejected it -->`
- **The parenthetical aside.** He drops humour and caveats in parens, often italicised: *(as usual)*, *(which incidentally contains F# notes)*. → `<!-- TODO: an aside lives well here — a parenthetical caveat or a dry joke -->`
- **The wish-list.** He says what he wishes existed (*"I think such a type should have been exposed from the core libraries"*). → `<!-- TODO: is there a language feature, crate, or API you wish existed here? say so -->`

Do NOT prompt him to explain F# basics, pattern matching, immutability, or what async is. His audience knows functional programming.

### 6. Code block placeholders

Fenced blocks with the right language tag (`fsharp`, `csharp`, `python`, `rust`, `js`, `cpp`) and a one-line comment for what goes there. He typically shows code, then narrates *under* it in prose ("Lets quickly go through the example…"), occasionally re-explaining ("If you read through the code again now it probably makes more sense"). For comparison posts, scaffold both language blocks. Example:

```fsharp
// TODO: the target shape — the type signature you WANT before you build it
```

For images he uses Hugo shortcodes: `{{< figure src="..." >}}`. Many old image hosts are dead (googleusercontent, dropbox, flickr) — if scaffolding a figure, use a local `/img/...` path placeholder and add `<!-- TODO: host this image locally -->`.

### 7. Closing beat

A short `##` section, never titled "Conclusion". Real closers riff on the title or topic. Here Dave:
- Ties back to the opening quote/joke if he used one (comparison and deep-dive posts usually callback).
- Gives the honest verdict, not a cheerleading line.
- Signs off. His sign-off is **near-universal**: `Until next time!` or `Until next time...` (sometimes `Until next time ...`). Retrospectives sometimes close warmer (`Thank you!` / `7sharp9.`). Include the sign-off line literally.

TODO reminding him to keep it to roughly one paragraph.

### 8. References (link-reference style)

Dave uses numbered link references collected at the bottom, and inline `[text][1]` in the body:

```markdown
[1]: <url>
[2]: <url>
```

Scaffold this block if the topic touches external libraries, papers, prior posts, or docs. Cross-link his own related posts where relevant.

---

## Series awareness

If the topic fits an existing series, set `series: ["..."]` and shape it as a part-N post: short callback to the prior part, no re-introduction of the premise, forward reference at the end. Known series and their `series` slugs:

| Series | slug | About |
|---|---|---|
| Sockets & Bockets | `socketsandbockets` | high-performance F# socket server |
| Pipeline Processing | `pipelineprocessing` | TPL/async pipelines |
| Dataflow Agents | `dataflowagents` | F# agents / dataflow |
| Back to the Primitive | `backtotheprimitive` | async synchronisation primitives |
| F# in the ML era | `F# in the ML era` | week-by-week ML module, Python vs F# |

For **F# in the ML era** specifically: this is the active series and follows a fixed template — premise recap is in a "What this series is" section, then "The dataset" / "The problem", then **Python idiom first**, then **F# translation**, then "Where each language earns its shape" (multiple threads, pick a few, NO declared winner in early posts), then "What's next" teasing the next week. Mirror the pandas draft's structure if the topic is this series. This is also the one series where thin DRAFT prose (clearly marked `<!-- DRAFT: edit in your own voice -->`) is acceptable.

If the topic is a NEW series, say so in the note and suggest a slug.

---

## What NOT to generate

- No "In this post we will learn…" / "Let's dive in!" openers. He states what he did or found. (One old post used "In this post we are going…" but the modern voice doesn't.)
- No motivational filler, no "F# is amazing!" as a verdict, no exclamation-laden hype paragraphs.
- No em dashes as a stylistic tic in the prose you draft (the TODOs can use them; the DRAFT prose should not — start a new sentence or use a comma).
- No generic section names. No "Introduction", "Background", "Conclusion", "Summary".
- No explaining functional-programming basics to the reader.
- No `categories`, no TOML frontmatter, no Jekyll fields (`author`/`layout`/`type`/`comments`/`slug`/`status`/`wordpress_id`).
- Do not write the full post body. Scaffold and prompt. (ML-series DRAFT prose is the only exception, and even there keep it thin.)
- Do not invent profiling numbers, overload counts, or "this cost me an hour" specifics — leave those as TODOs for Dave to fill with real figures.

---

After generating the skeleton, add a short note (outside the markdown file), 3-4 sentences: the **shape** you classified the post as and why, what you assumed about its scope (standalone vs series, code-heavy vs personal), and one open question (e.g. "Do you have a profiling number for the verdict?", "Is this part of Back to the Primitive or standalone?", "What's the cultural reference you'd want in the title?").
