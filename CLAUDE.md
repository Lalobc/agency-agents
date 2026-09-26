# Agent Routing Policy

This repository ships 279 specialized subagents in `.claude/agents/`, invokable
through the Agent tool by their display name (derived from each file's
frontmatter, not its filename). This section governs how Claude Code decides
when and which of them to use. It applies to this session's own behavior, not
to application/source code.

## 1. Simple tasks — handle directly

Answer simple questions, small edits, one-file fixes, and straightforward
lookups yourself, in the main session. Do not invoke a subagent just because
one exists for the topic. A subagent is a delegation with real overhead
(fresh context, no memory of this conversation) — only pay for it when it
buys something: parallelism, protecting context, or specialized judgment the
main session doesn't already have.

## 2. Complex tasks — analyze, then route

For anything nontrivial:

1. Read the request and identify the domain(s) it actually touches.
2. Break it into subtasks only when the subtasks are genuinely separable.
3. Match each subtask to the **narrowest specialized agent** whose
   description fits, by reading actual agent descriptions/frontmatter in
   `.claude/agents/` — not by keyword-matching a fixed category list.
4. Prefer a specialized agent over `general-purpose` whenever a clear domain
   match exists. Fall back to `general-purpose` only for open-ended
   multi-area research/search with no clean specialist match.
5. Do not ask which agent to use — pick the best match yourself. Only ask the
   user when the request is genuinely ambiguous about *what* is wanted (not
   about *which agent* should do it).

## 3. Orchestration

- For large or multidisciplinary efforts (touching 4+ distinct domains, or
  needing a coordinated multi-phase plan), invoke **Agents Orchestrator**
  first to help decide the specialist lineup and sequencing.
- The main Claude session always stays in control: it invokes the chosen
  specialists directly, collects their output, and integrates the results.
  Agents Orchestrator advises on the plan; it does not chain to other agents
  on its own.
- Do not build agent chains (agent A invoking agent B invoking agent C).
  Keep delegation one level deep from the main session.

## 4. Parallel work

- Run independent specialist subtasks in parallel (single message, multiple
  Agent calls) when their outputs don't depend on each other.
- Never parallelize subtasks where one needs another's output first — run
  those sequentially.

## 5. Routing priorities

Route to the most appropriate installed specialist for the domain in play —
examples include frontend/web UI, backend/APIs, databases, mobile,
architecture, debugging, testing/QA, cybersecurity, auth/permissions,
DevOps/infrastructure, AI/ML, data/statistics, UI/UX design, product
management, business strategy, finance, marketing, sales, research,
legal/compliance, and writing/content.

This list is illustrative, not exhaustive or hard-coded. With 279 agents
covering far more ground (e.g. game dev, geospatial/GIS, China-market
platforms, industry-specific ops), always check actual agent descriptions
for a closer match before defaulting to a broad category agent.

## 6. Planning-only requests

A "planning-only" request is a plan, design, roadmap, or recommendation where
no code, config, schema, or content is being produced yet.

- Classify every candidate agent into exactly one of three buckets before
  routing:
  1. **Planning agents needed now** — agents whose judgment shapes the plan
     itself (architecture, product tradeoffs, sequencing). These are the
     only ones that should normally be invoked for a planning-only request.
  2. **Implementation agents that may be needed later** — agents who would
     write the code, schema, content, or config once building starts.
     Mention them in the plan as "who would build this," but do not invoke
     them to do planning work.
  3. **Review agents that run only after implementation** — agents whose job
     is to check finished work (see §8). Never invoke these during planning;
     note in the plan that a review pass will happen later.
- Default to **at most 3 specialists** total for a planning-only request.
  Prefer **one lead architecture/product agent plus up to two specialists**
  — not one specialist per domain mentioned in the request.
- Do not assign an implementation specialist just because their domain
  appears somewhere in the future project. A specialist earns a seat only if
  their expertise is necessary to produce *this* plan, not because their
  phase will eventually exist.
- The following domains are normally **deferred until their phase actually
  begins**, and should not be pulled into a planning-only request by default
  just because the project will eventually need them: database
  optimization, testing/QA, DevOps, data visualization implementation, and
  final/security/reality-check review passes. Pull one in early only if the
  current planning task specifically hinges on its expertise (e.g. the plan
  itself is about choosing a database engine).
- Larger planning teams (more than 3) are allowed only when the current task
  itself genuinely spans multiple independent specialties that cannot be
  reasoned about by a single lead agent (e.g. reconciling conflicting
  regulatory, security, and architectural constraints simultaneously at the
  planning stage). Scale up deliberately, not by default.
- When presenting a routing decision for a planning-only request, show the
  three buckets explicitly (planning now / implementation later / review
  after implementation) rather than a single flat team list.

## 7. Quality control

For significant software changes (i.e. once implementation is under way, not
during planning):

- Run an appropriate testing/QA agent after implementation.
- Bring in a security specialist whenever the task touches authentication,
  secrets, permissions, user data, payments, APIs, or other sensitive
  systems.
- Use **Reality Checker** as a final, independent review pass for important
  or complex deliverables before calling the work done.

## 8. Overlapping agents

When multiple agents could plausibly cover a task:

- Choose the single narrowest specialist that matches.
- Use more than one only when each contributes a genuinely different
  perspective (e.g. a security review alongside a QA review are different
  lenses; two general backend agents doing the same review are not).

## 9. Agent-count limits

- Don't invoke agents just because they're available.
- Default to the smallest effective team — most tasks need **0–3
  specialists**; planning-only requests follow the stricter cap in §6.
- Reserve larger teams for tasks whose scope clearly justifies it (e.g. a
  multi-service feature spanning frontend, backend, database, and security).

## 10. Transparency

Whenever a subagent is used, briefly tell the user:

- which agent(s) were selected, and
- what each one is responsible for.

Never require the user to name agents manually — routing is automatic.
