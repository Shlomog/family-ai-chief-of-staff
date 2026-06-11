# How I Built a Family AI Chief of Staff (with Claude Code)

*By [Shlomo Garbi](https://www.linkedin.com/in/shlomog/) · read it as a
[web page](https://shlomog.github.io/family-ai-chief-of-staff/)*

I have an assistant named **Nano** — after [NanoClaw](https://nanoclaw.dev), the technology
she runs on (more on it below). The simplest way to describe her: *a second brain that never
forgets*. She remembers everything across every conversation: the kids' schedules,
what my partner asked me to handle, which doctor said what, who owes whom, the thing I
mentioned a month ago — so I never have to re-explain myself.

And she belongs to the whole house, not just me. She lives in our **family group chat**, so my
partner and I talk to the *same* assistant — one shared brain for the household instead of two
people each trying to hold it all. My partner hands her one thing, I hand her another, and it
all lands in one place; nothing slips through the gap between us. That's the *family* in family
chief of staff — it isn't an add-on, it's half the reason I built her.

And she *acts* on it. I say "book the 1pm pickleball" and it's done. She texts vendors, adds to
our calendars, and runs in the background — a morning brief, birthday watches, school-email
alerts, a running list of my partner's asks so nothing slips (I call it my nag-list). When I
need something looked up, she finds it live. She'll even pick up the phone and call — in a real
voice — to book a table or confirm an appointment.

Here's why it matters. I've got a demanding job, three kids, and a lot moving at once. What
wears me down isn't the big decisions — it's the *forty small things* I'm supposed to remember.
Nano carries that load so I show up for the things that count. It's not magic; it's really good
memory plus a system that quietly runs in the background. But it genuinely changed how I move
through my week.

None of it is a demo — it's my actual daily setup, living in our family WhatsApp thread. People
keep asking how it works, so here's the whole build, from an empty machine to this, using
[NanoClaw](https://nanoclaw.dev) and Claude Code. **Every step is broken
down to the smallest action**, and you never type a command — you just talk, in plain English,
to the two things below. (The personal bits are swapped for placeholders.)

> **What you'll need:** a Mac or Linux machine (Windows works via WSL2) and about an hour.
> [Claude Code](https://claude.ai/download) — a normal Mac app — does the actual engineering;
> you'll mostly be *talking to it*, no terminal required.

> ⚡ **Shortcut: this guide is also a prompt.** Paste the whole thing into Claude Code and say
> *"build this for me."* It will work through every step it can on its own and pause only when
> it genuinely needs you — sign-ins, one QR scan, a few choices. Expect about six pauses.

**Two things you'll talk to.** This trips people up, so I'll flag it up front:

- **→ Claude Code** — the engineer on your computer. You talk to it to *build and change* the
  system (install integrations, rename the trigger). You never memorize commands — you
  describe what you want and it maps that to the right tool under the hood.
- **→ Nano** — your assistant, living in your chat app. You talk to it to set *how it
  behaves* day to day (rules, reminders, tasks).

Both are just plain English. I'll mark every instruction with **→ Claude Code** or **→ Nano**
so it's always clear which one you're talking to.

**Contents**

1. [A day with Nano](#0-a-day-with-nano)
2. [What NanoClaw is, and why I trust it](#1-what-nanoclaw-is--and-why-i-trust-it)
3. [Install it — step by step](#2-install-it--step-by-step)
4. [Meet your agent — the one rule that matters](#3-meet-your-agent--the-one-rule-that-matters)
5. [Give it a memory (the wiki)](#4-give-it-a-memory-the-wiki)
6. [Put it in WhatsApp — and move the family in](#5-put-it-in-whatsapp--and-move-the-family-in)
7. [Let it read your email](#6-let-it-read-your-email)
8. [Let it run your calendar](#7-let-it-run-your-calendar)
9. [Make it proactive — the recipes](#8-make-it-proactive--the-recipes)
10. [The next layer — phone, Notion, images](#9-the-next-layer--phone-notion-images)
11. [Living with it — privacy & upkeep](#10-living-with-it--privacy--upkeep)

---

## 0. A day with Nano

To set expectations, here's a normal Tuesday in our house — watch how the same brain serves
both of us:

- **7:15am** — The brief lands: the whole family's day in one message — my 9:30 call, my
  partner's late shift, swim at 4 (my partner's on pickup), the permission slip due Friday,
  the gas bill autopay tomorrow.
- **9:10am** — An email from the school arrives. Nano reads it and posts in the family chat:
  *"Half-day Friday — neither of your calendars has the afternoon covered. Want me to ask the
  sitter?"*
- **12:40pm** — My partner, in the family chat: "We're out of the oat milk the kids like —
  and someone needs to grab the dry cleaning." The milk goes on the grocery list; the dry
  cleaning lands on *my* open list, and Nano will nudge me each morning until I say done.
- **3:30pm** — I message Nano in my DM: "Book the 1pm pickleball Saturday." Nano: *"Done —
  but the family calendar has you on birthday-party duty 12–2 Saturday; your partner added it
  yesterday. Book 3pm instead?"* A conflict I didn't know existed — caught because both of us
  feed the same brain.
- **6:40pm** — In the family chat: *"You're both free Wednesday evening and the sitter's
  around — book the usual place for 7?"* My partner replies 👍. Nano calls the restaurant.

None of this is magic. It's four ingredients — **memory**, the **channels** our family
already talks in, a few **tools** (email, calendar, phone), and **proactivity** (it acts
without being asked). The trick that makes it a *family* chief of staff: both of us feed the
same brain, so what my partner tells it on Monday saves me on Saturday. Let's build each
piece.

---

## 1. What NanoClaw is — and why I trust it

I wouldn't wire a black box into my family's logistics. The reason I use NanoClaw is that I
can actually understand it:

- **It's small.** One host process and a handful of files. If I want to know how something
  works, I ask Claude Code to walk me through that file.
- **It doesn't reinvent the agent.** The big win: NanoClaw doesn't build its own AI layer at
  all. The hard parts — the model, the reasoning, the tool-running — are outsourced to
  Anthropic's official [Claude Agent SDK](https://platform.claude.com/docs/en/api/agent-sdk/overview),
  the same engine that powers Claude Code. NanoClaw keeps only the plumbing it actually
  needs: routing messages, containers, memory.
- **No special hardware.** It runs on the Mac or Linux machine you already own — no Mac mini,
  no cloud server to rent. Leave a machine on and it keeps working around the clock; a spare
  laptop or an old desktop is plenty.
- **Agents run in containers, not behind permission prompts.** Each assistant runs inside
  its own Linux container and can only see the folders I explicitly mount. "Let it run bash"
  is safe because bash runs *in the box*, not on my laptop.
- **Credentials never enter the container.** API keys and OAuth tokens live in a local
  vault (**OneCLI**). When the agent makes an API call, the vault injects the real
  credential at the last moment. The container never holds a usable secret — so even in a
  worst case, there's nothing to steal from inside it.

That's the trade I wanted: capable enough to act for me, contained enough that I sleep fine.
Repo and docs: [github.com/qwibitai/nanoclaw](https://github.com/qwibitai/nanoclaw) ·
[docs.nanoclaw.dev](https://docs.nanoclaw.dev).

---

## 2. Install it — step by step

On a Mac, Claude Code is just an app — you never open a terminal. You install it, point it at
an empty folder, and *ask it to do the rest*: it picks and installs the right container
runtime for your machine, clones NanoClaw, and runs the setup. You don't need to know what any
of that means.

> **Legend.** Only two places to act: **In your browser** (one download — the app itself) and
> **→ Claude Code** (everything else, by just asking). On a Mac there's no terminal step at
> all.

**Step 1 — Install the Claude Code app.** On a Mac, Claude Code is a normal desktop app — you
open it like Notes or Slack, no terminal involved. Download it from
[claude.ai/download](https://claude.ai/download) and install. Everything below, you hand to the
app. *(Linux works too, via the Claude Code CLI; on Windows, enable WSL2 first —
`wsl --install` in an Administrator PowerShell — and run it there.)*

**Step 2 — Ask Claude Code to set up your machine.** Make an empty folder, open Claude Code
inside it, and tell it:

> **→ Claude Code:** *"Set up NanoClaw on this machine. Check my system and install whatever
> it needs — including the right container runtime for how much disk space I have — then clone
> the repo and start the guided setup."*

It checks your OS and free space, then installs and configures a **container runtime** — the
sandbox each agent runs in. There's more than one option: full **Docker Desktop** on a roomy
machine, or a lighter runtime (**Colima**, **OrbStack**, or macOS's native **Apple
Container**) when space is tight. **You don't pick — Claude Code does**, based on your actual
machine. It also installs Node and pnpm if they're missing and clones NanoClaw — no terminal
typing from you.

> **Why not just "install Docker"?** Docker Desktop is heavy. On my machine there wasn't room
> for it, so Claude Code set me up with a lighter runtime instead — same result, a fraction of
> the footprint. That's exactly why you let it decide rather than follow a fixed command.

**Step 3 — Answer the guided setup.** The final piece is a short, interactive Q&A, and it
happens right in the Claude Code app — no separate terminal window. Claude Code runs the setup
(`bash nanoclaw.sh` under the hood) and you answer its questions there. Three things:
1. **How to sign in to Claude** — a Claude Pro/Max subscription (paste a token) or an
   Anthropic API key.
2. **Which folders the agent may see** — keep this minimal to start; widen later.
3. **Your first channel** — Telegram, Discord, WhatsApp, or a local CLI. If you just want to
   see it work, pick **CLI**; we'll wire WhatsApp properly in step 5 of this guide.

**Step 4 — Wait for the first build.** It downloads and assembles the agent image — **a few
minutes**. When it's done you'll see a success summary, and NanoClaw installs itself as a
background service that starts on login and restarts itself if it crashes.

**Step 5 — If anything trips,** you don't debug it yourself — that's the point of building on
Claude Code. Just tell it:

> **→ Claude Code:** *"Something isn't working — check the logs and figure out why."*

(Logs live in `logs/nanoclaw.log`.)

✅ **You now have a running assistant.** Everything from here is *adding capabilities* — and
each one is the same move: just ask.

---

## 3. Meet your agent — the one rule that matters

When setup finishes you get a welcome message from your assistant. By default the trigger
word is `@Andy`. I renamed mine. Here's *how*, because it's the most important habit in this
whole guide — I opened Claude Code and said:

> **→ Claude Code:** *"Change the trigger word to @Nano."*

That's it — I didn't run a command, I just **asked**. NanoClaw has no settings screen, and
there are no commands to memorize: you describe what you want and Claude Code builds it,
mapping your request to the right tool under the hood. Shorter replies? Tell it. A custom
greeting when you say "good morning"? Tell it. The change sticks.

Hold onto this, because every step below is one of the same two moves: **→ Claude Code** to
build and change the system, and **→ Nano** to set how it behaves day to day. Both are plain
English.

---

## 4. Give it a memory (the wiki)

A chatbot forgets you between messages. But a household runs on a thousand small facts — who's
lactose intolerant, which day swim moved to, where the spare key is, what you're saving the
good wine for — and usually *one person* ends up carrying them all. **Memory is the
difference**, so it's the first thing I add.

**Step 1 — Turn on the wiki.**

> **→ Claude Code:** *"Give my assistant a persistent wiki memory — one it reads before
> replying and writes to after."*

It scaffolds a plain-Markdown knowledge base the agent reads before answering and writes to
after learning something. The structure:
```text
wiki/
  index.md          ← table of contents
  log.md            ← timestamped changelog of everything learned
  people/           ← one file per person (partner, kids, the sitter, the plumber…)
  places/           ← restaurants, the pediatrician, schools, the gym
  projects/         ← anything ongoing (the kitchen reno, the summer trip)
  topics/           ← patterns and preferences that don't fit above
```

**Step 2 — Teach it something.** Now you talk to the assistant itself:

> **→ Nano:** *"My partner is Sam — lactose intolerant, birthday March 14, works late
> Thursdays."*

It writes a `people/sam.md` file that reads like plain notes:
```markdown
---
name: Sam
type: person
relationship: partner
last_verified: 2026-06-01
---

# Sam
- Lactose intolerant — no dairy.
- Birthday: March 14.
- Works late on Thursdays.
```

And here's what makes it a *household* brain rather than mine: once Nano is in the family chat
(next section), my partner feeds the same files. When swim moves to Thursdays and my partner
mentions it there, the kid's page updates — nobody has to repeat it to me, it's simply correct
in tomorrow's brief. One memory, written from both sides. I stopped being the single point of
failure for family facts.

**Two rules make the memory trustworthy** — and they fix the #1 failure mode of AI
assistants:
1. **No "saved" without an actual save.** The agent is forbidden from saying *"Got it,
   noted!"* unless it really wrote the file in that same turn. There's even a host-side
   backstop that catches a fake "I saved that" and makes it do the write for real.
2. **Every write is logged**, which is what makes the memory searchable across time ("when
   did we last talk about the leaky faucet?").

On top of that, the wiki quietly tidies itself — a nightly reflection that catches things it
should have saved, plus weekly cleanup and digest passes you never see.

**Step 3 (optional) — Browse its brain.** Point [Obsidian](https://obsidian.md) at the
`wiki/` folder and you can read and edit the whole thing as a normal notes vault.

---

## 5. Put it in WhatsApp — and move the family in

Everything so far is a private assistant. This section is where it becomes the *family* chief
of staff — the whole point of the build. You move Nano into the group chat your household
already runs on, and from then on both of you talk to the same brain: no special app, no "ask
me to ask it." For us that's WhatsApp.

**Step 1 — Decide which number Nano uses.** You can link it to *your own* WhatsApp (it
prefixes its messages with its name) or give it a **dedicated number** (a cheap second SIM or
eSIM). I went dedicated, so Nano is its own participant my partner can talk to directly.

**Step 2 — Install WhatsApp.**

> **→ Claude Code:** *"Connect my assistant to WhatsApp."*

It installs the adapter, then asks how you want to authenticate. Choose **QR code in
browser**.

**Step 3 — Link the phone.** A browser window opens showing a QR code. On the phone that
holds Nano's number:
1. Open **WhatsApp → Settings → Linked Devices → Link a Device**.
2. Scan the QR code in the browser.
3. Wait for **"Authenticated!"**. (The QR expires in ~60 seconds — if you miss it, Claude
   Code re-runs it.)

**Step 4 — Wire your own DM first.**

> **→ Claude Code:** *"Set up my own WhatsApp DM with the assistant."*

It asks your phone number, your name, and the assistant's name (I said **Nano**), then sends
you a welcome DM. Reply to it to confirm the loop works.

**Step 5 — Create the family group.** On your phone, make a normal WhatsApp group containing
**you, your partner, and Nano's number**.

**Step 6 — Wire that group.**

> **→ Claude Code:** *"Wire this new WhatsApp group to my assistant."*

It lists your WhatsApp groups. Pick the new one and make two choices it walks you through:
- **Who can talk to it?** Set unknown-sender policy to **request approval** — if a stranger
  is ever added to the chat, Nano checks with *you* before engaging. (Nobody hijacks your
  assistant just by being added to a thread.)
- **One brain or two?** Pick the **isolation level**:
  - **Shared agent** (what I use) — the same assistant powers your private DM *and* the
    family group, so it carries one memory across both.
  - **Separate agents** — fully private, no shared memory. Use this for, say, a work channel
    that should never see home context.

**Step 7 — Add the privacy rule.** Because mine is a shared brain, I told the assistant:

> **→ Nano:** *"In the family group, never surface anything from my private DM unless I bring
> it up there first."*

One sentence, and that's the guardrail.

**Step 8 — Test the shared brain.** Have your partner ask Nano for something in the group —
then bring it up yourself an hour later. Same assistant, same memory: what one of you tells
it, the other benefits from. That's the moment it stops being *your* assistant and becomes the
household's. 🎉 (The same one-ask pattern works for Telegram, Discord, Slack, iMessage,
Signal, and more — just tell Claude Code which you want.)

---

## 6. Let it read your email

A chief of staff that can't see your inbox is flying blind — and half of what runs a household
arrives by email: school announcements, camp forms, appointment reminders, bills.

**Step 1 — Add the Gmail tool.**

> **→ Claude Code:** *"Add Gmail so it can read, search, and draft my email."*

**Step 2 — Connect Gmail to the vault.** When Claude Code asks, open your browser to the
OneCLI dashboard and connect Google:
1. Go to **http://127.0.0.1:10254**.
2. **Apps → Gmail → Connect.**
3. Sign in with the Google account you want the agent to act as, and approve.

This is the *only* place your Google login happens. The token stays in the vault; the
container only ever sees a placeholder, and the vault swaps in the real token at request
time. Your email is readable by your assistant without your credentials living anywhere an
agent could leak them.

**Step 3 — Let it finish.** Claude Code wires the Gmail tool into your agent, rebuilds the
container image (~1 minute), and restarts the service. Just confirm when it prompts you.

**Step 4 — Test it.**

> **→ Nano:** *"List my Gmail labels."* — or — *"What's in my inbox today that actually needs
> me?"*

**Step 5 — Make it a habit.**

> **→ Nano:** *"Every morning, triage my inbox — surface only what needs me, draft replies
> for the obvious ones, and never send anything without showing me first."*
>
> **→ Nano:** *"Anything from the school is for both of us — flag it in the family chat, not
> just to me."*

That second rule is how the 9:10am moment in the timeline happens: the email arrives in *my*
inbox, but the household hears about it together.

---

## 7. Let it run your calendar

**Step 1 — Add the calendar tool.**

> **→ Claude Code:** *"Add my Google Calendar."*

**Step 2 — Connect Google Calendar** the same way: **http://127.0.0.1:10254 → Apps → Google
Calendar → Connect → sign in.** It supports multiple calendars and multiple accounts (handy
when you and your partner keep separate ones).

**Step 3 — Let it wire and rebuild** (same as email — confirm when prompted), then test:

> **→ Nano:** *"What's on my calendar Thursday?"*

**Step 4 — Add the rules that make it a chief of staff.** Two I rely on:

> **→ Nano:** *"When I say 'add it to our calendar,' invite my partner too."*
>
> **→ Nano:** *"Never tell me what day of the week something is from memory — always check the
> calendar first."*

That second rule quietly prevents a confident "that's a Thursday" from sending you to the
dentist on the wrong day. And because one assistant sees both calendars, it catches
cross-partner conflicts — the Saturday clash in the timeline was caught exactly this way: my
partner's plan, my booking, one brain holding both.

---

## 8. Make it proactive — the recipes

Everything so far is *reactive* — it answers when asked. The chief-of-staff feeling comes
from the assistant acting **before** you ask. That's three things working together:

- **Scheduled tasks** = the **when** (a brief at 7:30am).
- **Prose rules** in its memory = the **how, what, and tone**.
- **The wiki** = the **what it already knows** (who the sitter is, where "the usual place"
  is).

You never touch cron syntax — you describe the behavior and it sets up the schedule. A few
things happen under the hood: schedules run in *your* timezone; some tasks are silent by
design (a weekly digest written to the wiki) while reminders message you; and a task can run
a tiny check first and *only* wake the assistant if there's something worth saying — so it
can watch something hourly without spamming you on quiet days.

Each recipe below is **one sentence.** Some I sent from my DM; the family ones went straight
in the group chat, where my partner sees them, amends them, and adds their own. I've added
what each sets up so you can see the gears.

**Recipe 1 — The morning brief**

> **→ Nano:** *"Every morning at 7:30, brief me: both our calendars, the kids' day, any email
> that needs one of us, and what's still open on my list. Keep it short."*

→ Creates a daily 7:30am task that pulls both calendars + email + open items into one tight
digest of the household's day — who's where, who's on pickup, what's due. You're waiting on
it, so it always messages you.

**Recipe 2 — The nag-list**

> **→ Nano:** *"When my partner asks me to handle something in the family chat, track it.
> Remind me each morning what's still open, until I say done."*

→ Every "can you deal with…" becomes a tracked item the moment it's said — nobody transcribing
chores into an app. It rides the morning brief until it's closed. Nothing my partner asks me
falls through the cracks again — this one recipe has saved me more grief than the rest
combined.

**Recipe 3 — Date night**

> **→ Nano:** *"Each week, check if my partner and I both have a free evening and the sitter's
> around. If so, suggest a night and offer to book somewhere we like."*

→ Creates a weekly check that cross-references both calendars and sitter availability, pulls
"places we like" from the wiki, and proposes a plan. Say yes and it books — by phone (step 9).

**Recipe 4 — Weeknight dinners**

> **→ Nano:** *"On Sundays, plan the week's dinners around what we've got going on, and build
> a grocery list from what we usually buy."*

→ Reads the calendar (busy nights get easy meals), respects dietary notes from the wiki (no
dairy for Sam), and keeps a list your partner can add to from the group chat all week.

**Recipe 5 — The summer-vacation project**

This one shows real chief-of-staff behavior, because it's not a single task — it's a
**project** run over weeks.

> **→ Nano:** *"We're planning a week away this summer. Track it as a project: keep our
> options, what's booked, what's still open. Check in with me when something needs a
> decision."*

→ Creates a `wiki/projects/summer-trip.md` page (dates, budget, candidate spots, a "booked vs
open" list), uses the **calendar** to hold tentative dates, watches **email** for
confirmations, and uses the **phone** to call a place that won't book online. Periodic
check-ins surface the next decision instead of letting the whole thing rot in your head.

**Recipe 6 — Birthday & activity watch**

> **→ Nano:** *"Watch for birthdays and anniversaries and give me a week's notice, and remind
> me about the kids' recurring activities."*

→ A daily 9am pass over the wiki's `people/` files surfaces what's coming up. Birthdays stop
sneaking up on you.

**Managing tasks** is conversational too: *"list my scheduled tasks," "pause the morning
brief," "move the dinner planning to Saturday."*

---

## 9. The next layer — phone, Notion, images

Once the core is humming, these are the upgrades I reach for.

### Outbound phone calls

The jaw-dropper: Nano can pick up the phone.

**Step 1 — Add calling.**

> **→ Claude Code:** *"Give it the ability to make outbound phone calls."*

**Step 2 — Connect the calling provider** ([Bland AI](https://bland.ai)) when prompted — sign
up, grab an API key, and the vault stores it. It's **outbound only**, recording is **off by
default**, and there's a **per-day call cap** so it can't run wild.

**Step 3 — Set your guardrails.**

> **→ Nano:** *"Keep calls to 3/day max, and always confirm with me before any call that
> commits us to something."*

**Step 4 — Use it.**

> **→ Nano:** *"Call the Italian place on Main and book a table for two, Friday at 7. Text me
> the confirmation."*

(I personally run calls through [Vapi](https://vapi.ai) instead of Bland — same idea,
different voice provider — but the built-in calling integration is the clean, supported path,
so start there.)

### Notion as a tool

If you live in Notion, give the assistant access to it. There's no one-step integration for
it, but the **pattern** is the same one Gmail and Calendar use — and it works for *any*
MCP-compatible tool:

> **→ Claude Code:** *"Add Notion as a tool for this agent."*

It will install the Notion MCP server, wire it to a placeholder credential, and have you
connect Notion in the vault — so the **real** token is injected at request time, exactly like
email and calendar, never sitting in the container. It's more hands-on than the one-ask
integrations above, but the principle — *stub in the box, real secret injected by the vault* —
never changes.

### Images and media

The assistant can also generate images (a birthday-invite graphic, a quick mockup). This one
is the most bespoke — a small skill plus a hosted image API routed through the vault — so I'll
leave it at the pattern level: *capability + API behind OneCLI.* Describe it to Claude Code
and build it together.

---

## 10. Living with it — privacy & upkeep

Wiring an AI into your family's life is only smart if you've thought about the edges. The
posture I run:

- **Isolation by default** — each agent is a container that sees only what I mount. Nothing
  crosses between groups unless I wire it to.
- **Secrets stay out of reach** — every credential (Gmail, Calendar, phone, Notion) is
  injected by the vault at request time; the container never holds a usable key.
- **Unknown senders are gated** — *request approval* means a stranger added to a group can't
  get my assistant to do anything until I say so.
- **Private stays private** — my shared-brain assistant has a standing rule never to leak
  private-DM context into the family group; non-family groups are privacy-locked.
- **Some things are a hard no** — I told it to flatly refuse credential-recon asks (password
  resets, 2FA codes, full card numbers), however they're phrased.

**Upkeep is light.** The service restarts itself if it crashes and starts on its own when you
log in. When I want to check on it — or restart it, or change anything — I just ask Claude
Code. It knows where everything lives, so there's nothing to maintain by hand:

> **→ Claude Code:** *"Restart NanoClaw, and tell me if anything looks off in the logs."*

---

## Make it your household's

That's the whole thing: a shared memory, the chat your family already runs on, a few tools,
and the nerve to let it act on its own. Start with the install and the wiki, move it into the
family chat, then add one proactive recipe and live with it for a week — you'll know within
days which piece your house wants next.

And you're never locked into my choices. Don't like how it phrases the brief? Tell it. Want
it to nag you about flossing? Tell it. It's *your family's* chief of staff — built on a
codebase small enough that you, and Claude Code, can bend it into the exact shape of your
life together.

Go build one. → [github.com/qwibitai/nanoclaw](https://github.com/qwibitai/nanoclaw)
