## What it does

`wayfinder` carries an effort too big for one agent [session](https://www.aihero.dev/ai-coding-dictionary/session) as a **mission** on your issue tracker. A mission is a single issue that names a working outcome, the **destination**, and says how anyone can see that it works. Its child tickets are the decisions and the building that get there.

A mission is done when the destination is seen working, not when it is fully specified. A specification written long before the code it rests on is never quite right: building teaches what grilling cannot, and the codebase and the stack keep moving underneath the plan. So wayfinder plans a few steps ahead, in detail that fades with distance, gets the nearest step built, and then replans from what the building revealed and from what changed around it. It works like model predictive control: plan over a short horizon, act on the first step, measure, plan again.

## When to reach for it

You invoke this by typing `/wayfinder`; the [agent](https://www.aihero.dev/ai-coding-dictionary/agent) won't reach for it on its own.

It is the heaviest flow in the set, so the trigger is narrow. The effort must be larger than one agent session can hold, and the route to the destination must be unclear. The split is session count: `/grill-with-docs` for work you can settle and build from one conversation, `/wayfinder` for work that will take many sessions of deciding and building.

| What you have in front of you | What to run |
| --- | --- |
| A well-scoped feature you can settle in one sitting | [grill-me](https://aihero.dev/skills-grill-me), or [grill-with-docs](https://aihero.dev/skills-grill-with-docs) when there is a codebase |
| A greenfield project, or a build spanning many sessions, with the route still unclear | `/wayfinder` |
| A thread where the deciding is already done | [to-spec](https://aihero.dev/skills-to-spec) and [to-tickets](https://aihero.dev/skills-to-tickets): no mission needed |
| One step of a mission whose decisions are settled and which is too big for one build ticket | [to-spec](https://aihero.dev/skills-to-spec) or [to-tickets](https://aihero.dev/skills-to-tickets) for that step, with the resulting tickets attached to the mission |
| An existing session that has already grown too big | say "hand off to `/wayfinder`" ([handoff](https://aihero.dev/skills-handoff) bridges into a mission as well as out of one) |

Greenfield is not a requirement. People use wayfinder routinely on legacy and half-built codebases, and it can be more useful there, because much of the fog is "what is already true here" rather than "what should we do".

## Prerequisites

The mission and its tickets live on the repo's issue tracker, so wayfinder needs the tracker setup from [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills). That step writes a "Wayfinding operations" section. The section describes how to express the mission, its child tickets, blocking edges, priority and frontier queries on GitHub, GitLab, or local markdown. A repo that dispatches work to agents can add a "Wayfinding execution" section saying how build tickets reach its build path. Wayfinder finds that doc through the pointer in your `CLAUDE.md` / `AGENTS.md`, not at a fixed path. With no tracker configured, it falls back to local markdown files.

The tracker does real work. Its native blocking links show the frontier in the tracker's own UI. On a tracker without native dependency links (a self-hosted Gitea, say), wayfinder infers blockers from the mission text. That works, but you must supervise it more closely.

## The mission, the fog, and the frontier

The **mission** is a single issue labelled `wayfinder:map`, and its tickets, decision and build alike, are its child issues. Child membership matters beyond tidiness: a repo's dispatcher can rank and cap a mission's work together because the tickets sit under it. The mission is an **index, not a store**. A decision lives in its ticket and a landing in its change; the mission gives a one-line gist and a link. A session loads the mission at low resolution and opens individual tickets when it needs them.

The mission body has these sections:

- **Destination.** The working outcome, and how anyone can see that it works: the observation, demo or check that closes the mission. You name it first, because it fixes the scope you measure every ticket against.
- **Notes.** Domain, skills every session should consult, standing preferences.
- **Priority.** The mission's rank against your other missions, in whatever form the tracker doc uses, or "Parked" with a reason.
- **Decisions so far.** One line per closed decision ticket, with a link.
- **Landed and learned.** One line per landed piece of building: what works now, and what building it taught. This is the section that keeps the mission honest about reality.
- **Not yet specified.** This is the **fog of war**: work and questions you can tell are coming but that are too far off to plan in detail. Fog graduates into tickets when the planning horizon reaches it, not when you could phrase it sharply.
- **Out of scope.** Work beyond the destination. It stays closed and never graduates.

The **frontier** is the set of open, unblocked, unclaimed tickets that are ready to work. A session claims a ticket by assigning it to itself before it does any work. Sessions refer to tickets by name, never by a bare `#42`, because a wall of issue numbers is hard to read in narration.

## The planning horizon

Planning ahead is good when it makes the near work less risky. What goes wrong is planning far-off work in excruciating detail, and then treating the plan as fixed. A mission keeps three bands, with detail fading with distance:

- **Ready.** The next build step or two. Their decisions are settled, and each build ticket is a contract written against the code as it stands now. Building can start.
- **Planned.** The few steps after that, written as tickets when seeing them makes the near steps safer or clearer: the order of work, the seams the near steps must leave room for, decisions worth settling early. A planned ticket is an outline and a working assumption. It is refreshed against the code and marked ready when it reaches the front.
- **Fog.** Everything further out, loose in **Not yet specified**. Planning it in detail now is wasted effort, because it may be irrelevant by the time building gets there.

So there are two kinds of ticket ahead of the building: ready ones you can start, and planned ones you expect to rewrite.

Decisions and plans are working assumptions, and replanning is the normal way a mission moves. Revisit them after every landing, for what the building revealed, and whenever the surroundings change: other work landing in the codebase, a dependency or tool in the stack changing, another mission's decision. When one is wrong, reopen or supersede the decision, rewrite or close the planned tickets it fed, and record why in **Landed and learned**. A mission that never replans is probably not learning.

## The ticket types

Every ticket carries a `wayfinder:<type>` label. Each ticket is either **[HITL](https://www.aihero.dev/ai-coding-dictionary/human-in-the-loop)** (worked with a human who speaks for themselves) or **[AFK](https://www.aihero.dev/ai-coding-dictionary/afk)** (driven by agents alone). A HITL ticket resolves only through the live exchange. An agent that answers its own [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) questions has broken it.

| Type | Mode | Reach for it when | Resolved by |
| --- | --- | --- | --- |
| `build` | AFK | The decisions and code a slice of the outcome rests on exist. | The repo's build path (an agent session, or a dispatched crew and a pull request), followed by a learn pass |
| `grilling` | HITL | The question can be settled by talking it through. | [grilling](https://aihero.dev/skills-grilling) plus [domain-modeling](https://aihero.dev/skills-domain-modeling), in a fresh session |
| `prototype` | HITL | "How should this look" or "how should this behave": a question talking cannot settle. A spike that settles it by trying beats a decision argued from priors. | [prototype](https://aihero.dev/skills-prototype), with a link from the ticket to the built artifact |
| `research` | AFK | A fact outside the working directory is blocking a decision. | A [research](https://aihero.dev/skills-research) [subagent](https://www.aihero.dev/ai-coding-dictionary/subagent), run in parallel on a `research/<name>` branch |
| `task` | Either | Nothing to decide or build, but manual work blocks a step, such as provisioning access or moving data so you can see its shape. | The agent alone where it can, otherwise a precise checklist for the human |

A ready build ticket's body is the contract your build path expects: what the slice delivers, how it is checked, and what is out of scope. A planned one is an outline of the same. A decision ticket's body is the question, and the build step that waits on it.

At most one HITL ticket per session. AFK tickets (research and build) can run in parallel.

## Advancing a mission

Each session on an existing mission picks its move in this order:

1. **Learn and replan.** If a build ticket has landed, or the surroundings have changed, since the last learn pass, do that first: record what works and what was learned, check what changed elsewhere in the codebase and the stack, reopen or supersede the decisions those things contradict, rewrite or close the planned tickets they fed, and re-extend the horizon from the fog.
2. **Start building.** If a ready build ticket is unblocked and unclaimed, hand it to the build path.
3. **Decide what building waits on.** Otherwise resolve the decision ticket that unblocks the nearest build step, then write the build ticket it unblocked.
4. **Refresh or extend the horizon.** If nothing is ready, refresh the nearest planned ticket against the code and mark it ready. If nothing is planned and the destination isn't yet seen working, turn the nearest fog into tickets.

The mission closes when its destination check is seen working, with the observation recorded on the mission.

## Common questions

**How is this different from `/grill-with-docs`? Which should I start with?**
Session count, not project size. If you can hold the whole thing in one conversation, grilling is cheaper and better, and a mission is slower and denser for that case. Wayfinder only makes sense if the work does not fit into a single session.

**When it asks for the "destination", does it mean the end of this session or the end of everything?**
The end of the whole mission: a working outcome, and how you would see that it works. "A spec is written" is not a destination; "the import runs nightly against production data and the dashboard shows it" is.

**Where did "the map is cleared, now run `/to-spec`" go?**
Earlier versions of wayfinder planned only. Every ticket was a decision, the map closed when nothing was left to decide, and the build happened afterwards in a separate chain. In practice that specified work long before its prerequisites existed, and once the map closed, nothing tracked the building or fed what it taught back into the decisions. A mission keeps decisions and building on one board. You can still use [to-spec](https://aihero.dev/skills-to-spec) or [to-tickets](https://aihero.dev/skills-to-tickets) to slice one step that turns out bigger than a single build ticket; attach the results to the mission.

**I charted 27 tickets, and by the time I got to the thirteenth, the rest no longer made sense.**
This is the failure the planning horizon exists to prevent. Charting creates ready tickets for the first step and planned outlines for the few after it, not detailed tickets for all 27. If you find yourself writing detailed tickets for work many steps away, put it in **Not yet specified** instead. And when the building does invalidate a planned ticket, that is the plan working: rewrite or close it. [Prototype](https://www.aihero.dev/ai-coding-dictionary/prototyping) aggressively: cheap concrete artifacts expose uncertainty before a build depends on it.

**Can I work several tickets in parallel?**
AFK tickets, yes: research subagents and build tickets on independent slices. HITL tickets, one at a time is the safer default. If you work two grilling tickets at once, one session can ask you a question you just answered in the other, because the sessions share no [context](https://www.aihero.dev/ai-coding-dictionary/context). A prototype ticket's choice between variations is yours, not the agent's.

**Do I have to use GitHub Issues?**
No. Any issue tracker works. GitHub has the best support, because its native sub-issues and blocking relationships make the frontier visible without opening the mission. On a tracker with no native blocking, wayfinder infers the dependency graph from text, and you must correct it by hand. Local markdown puts the artifacts in your repo, which is not recommended, because material stored in the repo tends to persist by accident.

**The grilling is exhausting. Every question is three paragraphs long.**
The verbosity looks like a property of the current set of [models](https://www.aihero.dev/ai-coding-dictionary/model) rather than of the skill. Try a lower [reasoning effort](https://www.aihero.dev/ai-coding-dictionary/effort), and a plain-language instruction in your global `CLAUDE.md`. Grilling to the planning horizon, rather than to the end of the effort, also keeps sessions shorter.

**A decision I already closed turned out to be wrong. Do I edit the old ticket or make a new one?**
Reopen it or supersede it with a new decision ticket, and say so in **Landed and learned**. Agents tend to design around a bad decision instead of challenging it, so tell wayfinder plainly what the building showed.

**Where did `decision-mapping` go?**
It is this skill, renamed to `wayfinder` in v1.1. The mission's label is still `wayfinder:map`, so existing maps carry over: add a **Landed and learned** section, rewrite the destination as a working outcome, and attach the build work as children.

## It's working if

- The destination names a working outcome and how to see it, before a single ticket exists.
- A few steps are ready, a few more are planned in outline, and the rest is fog, not detailed tickets for the whole effort.
- Build tickets are children of the mission, and a landing is followed by a line in **Landed and learned**.
- **Not yet specified** holds work whose prerequisites haven't been built, and fog graduates when a landing reaches it.
- Decisions and planned tickets get revised after landings, and after changes elsewhere in the codebase or the stack.
- The mission closes on an observation of the outcome working, not on a finished spec.
- When the opening breadth-first grill finds no fog at all, the skill stops and tells you the effort is small enough to skip the mission.

## Where it fits

`wayfinder` is a **situational on-ramp**, not the default starting point. Most work still starts on the grill-led idea → ship chain. You use wayfinder when the idea is too big to hold in one session, and it carries the effort through to the outcome rather than handing off at a spec.

Most of the work happens in other skills that wayfinder schedules. [grilling](https://aihero.dev/skills-grilling) and [domain-modeling](https://aihero.dev/skills-domain-modeling) resolve decision tickets, [prototype](https://aihero.dev/skills-prototype) resolves the ones talk cannot settle, [research](https://aihero.dev/skills-research) runs as a subagent so its reading stays out of your session, and [implement](https://aihero.dev/skills-implement) or the repo's dispatcher carries build tickets. [handoff](https://aihero.dev/skills-handoff) moves work in and out: into a mission from a conversation that grew too big, and out of one when a side quest appears mid-session. For anything else, [ask-matt](https://aihero.dev/skills-ask-matt) routes over the whole set.
