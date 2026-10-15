---
name: wayfinder
description: Carry a huge chunk of work (more than one agent session can hold) as a mission on your issue tracker. The mission names a working outcome and tracks the decisions and the build that reach it, deciding each step just before it is needed and learning from every landing. It is done when the outcome is seen working.
disable-model-invocation: true
---

A loose idea has arrived, too big for one agent session, and wrapped in fog: the way from here to the **destination** isn't visible yet. This skill carries that effort as a **mission**: one issue on the repo's tracker that names a working outcome, and whose child tickets are the decisions and the build that get there. It is done when the outcome is seen working. A complete specification is never the finish line, because specifications are never quite right until something is built against them.

Building teaches what grilling can't. The mission therefore decides only what the next piece of building needs, builds it, and then folds what the building revealed back into the mission before deciding the next step.

## Refer by name

Every mission and ticket is an issue, so it has a **name**: its title. In everything the human reads (narration, the mission's sections), refer to it by that name, never by a bare id, number, or slug. A wall of `#42, #43, #44` is illegible; names read at a glance. The id and URL ride _inside_ the name, as its link.

## The Mission

The mission is a single issue on this repo's issue tracker, labelled `wayfinder:map`. It is the canonical artifact, and every ticket that serves it, decision or build, is its child issue. Child membership is what lets the repo's dispatcher rank and cap the mission's work together (see the tracker doc).

The mission is an **index**, not a store. It gists each decision and each landing, and links to the ticket or change that holds the detail. Each fact lives in exactly one place.

**Where the mission, its child tickets, blocking, priority and frontier queries physically live is tracker-specific.** The issue tracker should have been provided to you. If not, tell the user to run `/setup-matt-pocock-skills`. Consult the tracker doc's "Wayfinding operations" section for how _this_ repo expresses them, and its "Wayfinding execution" section for how build tickets are dispatched. If no tracker has been provided, default to the local-markdown tracker.

### The mission body

The whole mission at low resolution, loaded once per session. Open tickets are **not** listed: they are open child issues, found by query.

```markdown
## Destination

<the working outcome, in one or two lines, and how anyone can see that it works: the observation, demo or check that closes this mission.>

## Notes

<domain; skills every session should consult; standing preferences for this effort>

## Priority

<the mission's rank against other missions, expressed the way the tracker doc says (for example Value, Class of service, Crew limit), with one line of reason. "Parked: <reason>" when the mission is not being advanced.>

## Decisions so far

<!-- one line per closed decision ticket: enough to judge relevance; zoom the link for detail -->

- [<decision ticket title>](link): <one-line gist of the answer>

## Landed and learned

<!-- one line per landed piece of building: what now works, and what building it taught -->

- [<build ticket or change title>](link): <what works now>. Learned: <what turned out differently, or "as expected">

## Not yet specified

<!-- see "Fog of war": known-to-be-coming work and questions whose prerequisites don't exist yet -->

## Out of scope

<!-- see "Out of scope": work ruled beyond the destination; closed, never graduates -->
```

### Tickets

Each ticket is a **child issue** of the mission; the tracker's issue id is its identity. It carries a `wayfinder:<type>` label (see [Ticket Types](#ticket-types)).

- A **decision** ticket's body is the question, sized to one agent session:

  ```markdown
  ## Question

  <the decision or investigation this ticket resolves, and the build step that waits on it>
  ```

- A **build** ticket's body is the contract the repo's dispatch expects: what the next piece of building delivers, how it is checked, and what is out of scope. It is written from the decisions it rests on and the code as it stands now.

A session **claims** a ticket by assigning it to the dev driving the mission, **first**, before any work, so concurrent sessions skip it. That assignee _is_ the claim: an open, unassigned ticket is unclaimed.

Blocking uses the tracker's **native** dependency relationship, so the tracker's own UI shows what's takeable without opening the mission. Only a tracker that lacks native blocking falls back to a body convention. A ticket is **unblocked** when every ticket blocking it is closed; the **frontier** is the open, unblocked, unclaimed children.

Answers and outcomes are recorded on resolution (see [Advance the mission](#advance-the-mission)). Assets created while resolving a ticket are linked from the issue, not pasted in.

## Ticket Types

Every ticket is either **HITL** (human in the loop, worked _with_ a human who speaks for themselves) or **AFK**, driven by agents alone. A HITL ticket only resolves through that live exchange; the agent asks, and the human answers.

- **Build** (AFK): A slice of the outcome itself, delivered the way the repo builds things (for example a dispatched crew and a change request). It resolves when the slice has landed, and its landing is followed by a learn pass.
- **Research** (AFK): Reading documentation, third-party APIs, or local resources to surface a fact a decision waits on. Resolved by a subagent that calls the Skill tool with "research". Use when knowledge outside the current working directory is required.
- **Prototype** (HITL): A cheap, rough, concrete artifact to react to (an outline, a stub, UI or logic code), made by calling the Skill tool with "prototype". Use when "how should it look" or "how should it behave" is the key question. A spike that settles a question by trying it beats a decision argued from priors.
- **Grilling** (HITL): Conversation. Call the Skill tool twice, for "grilling" and "domain-modeling".
- **Task** (HITL or AFK): Manual work a decision or a build step waits on (provisioning access, signing up for a service, moving data so its shape can be seen). The agent drives it alone where it can; otherwise it hands the human a precise checklist. The answer records what was done and the facts later tickets depend on.

## Just in time

A ticket earns its place by serving the **next** piece of building. Write a decision ticket when a build step is waiting on that answer; write a build ticket when the decisions and the code it rests on exist. Everything further ahead stays in **Not yet specified**, however sharply it could be phrased today, because the building in between will change the question.

Keep the frontier short: the next build step or two, and the decisions they wait on. When a grill reaches past that horizon, record the thought in **Not yet specified** and return to the next step.

Decisions are working assumptions. When building shows one is wrong, reopen it or supersede it with a new decision ticket, and say so in **Landed and learned**.

## Fog of war

The mission is _deliberately_ incomplete. Beyond the frontier lies the **fog of war**: work and questions you can tell are coming, written down loosely in **Not yet specified**. That section is everything in scope that the next build step doesn't need yet. Write as loosely or as fully as the view allows; it signposts where the effort is headed for collaborators.

A patch of fog **graduates** into tickets when the building reaches it: when a landing makes its prerequisites real, or when the next build step turns out to wait on it. One patch may graduate into several tickets, or none.

**Not yet specified** excludes what's already decided (Decisions so far), what's landed (Landed and learned), what's already a live ticket, and what's out of scope.

## Out of scope

The destination fixes the scope, so work beyond it is **out of scope**: it isn't fog, and it doesn't belong in **Not yet specified**. It gets its own **Out of scope** section: work consciously ruled out of _this_ mission. Out-of-scope work returns only if the destination is redrawn, and then as a fresh mission.

When an existing ticket turns out to sit past the destination, **close it** and leave one line in **Out of scope**: the gist, why, and the link.

## Invocation

Two modes. Resolve at most one HITL ticket per session; AFK tickets (research, build) may run in parallel.

### Chart the mission

User invokes with a loose idea.

1. **Name the destination.** Call the Skill tool twice, for "grilling" and "domain-modeling", to pin down the working outcome and how anyone will see that it works. The destination fixes the scope, so it's settled first.
2. **Find the first step.** Grill breadth-first just far enough to see the whole space and the **first piece of building** that can start now, together with the decisions it waits on. **If no fog remains** (the journey is small enough for one session), you don't need a mission. Stop and ask the user how they'd like to proceed.
3. **Create the mission** (label `wayfinder:map`): Destination, Notes and Priority filled in; Decisions so far and Landed and learned empty; the rest of the space sketched loosely into **Not yet specified**.
4. **Create only the first step's tickets** as child issues: the first build ticket (or its precursor research, prototype or task), plus the decision tickets it waits on. Wire blocking edges in a **second pass**, once the issues have ids.
5. **Fire the research subagents** for any `research` ticket just created, capturing findings on a throwaway `research/<name>` branch with a context pointer from the ticket.
6. Stop. Charting is one session's work; it resolves no HITL ticket.

### Advance the mission

User invokes with a mission (URL or number). A ticket is **optional**: without one, you pick the next move.

1. Load the **mission**: the low-res view, not every ticket body.
2. Choose the move, in this order:
   1. **Learn from a landing.** If a build ticket has landed since its last learn pass, do that pass first (step 5).
   2. **Start building.** If a build ticket is unblocked and unclaimed, hand it to the repo's build path (the tracker doc's "Wayfinding execution"), claiming it.
   3. **Decide what building waits on.** Otherwise take the frontier decision ticket that unblocks the nearest build step, and claim it.
   4. **Graduate the next step.** If the frontier is empty and the destination isn't yet seen working, graduate the fog the next build step needs into tickets.
3. Resolve a decision ticket. **Zoom as needed**: fetch the full body of related or closed tickets on demand; call the Skill tool for whichever skills `## Notes` names. If in doubt, call the Skill tool twice, for "grilling" and "domain-modeling". Post the answer as a **resolution comment**, **close** the issue, and append a pointer to **Decisions so far**.
4. Write the build ticket the answer unblocked, if its other prerequisites exist, from the decisions and the code as it is now.
5. **Learn pass**, after each landing:
   - Append the landing to **Landed and learned**: what works now, and what building it taught.
   - Revisit earlier decisions the building contradicted: reopen or supersede them.
   - Graduate fog that the landing made real into tickets, and clear it from **Not yet specified**.
   - Thin anything ticketed ahead of its prerequisites back into fog.
6. **Close the mission** when the destination's check is seen working: record the observation (link, demo, or run) on the mission, then close it.

The user may run unblocked tickets in parallel, so expect other sessions to be editing the tracker concurrently.
