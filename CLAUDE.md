# CLAUDE.md

Guidance for Claude Code when it automatically routes work in this repo to the specialist agents defined here.

## Delegation rules

These rules govern automatic agent selection/routing. They apply on top of, and take precedence over, generic "use the best specialist" instincts.

### Planning-only requests

- Default to **at most 3 specialists** total for a planning-only request (a plan, design, or recommendation with no code/config/content being produced yet).
- Prefer **one lead architecture/product agent plus up to two specialists** — not one specialist per domain mentioned in the request.
- Do not assign an implementation specialist just because their domain appears somewhere in the future project. A specialist earns a seat only if their expertise is necessary to produce *this* plan, not because their phase will eventually exist.
- Larger teams (more than 3) are allowed only when the current task itself genuinely spans multiple independent specialties that cannot be reasoned about by a single lead agent (e.g., a plan that requires reconciling conflicting regulatory, security, and architectural constraints simultaneously). Scale up deliberately, not by default.
- Keep the smallest effective team for the task in front of you.

### Three categories of agents

When deciding who to route to, classify every candidate agent into exactly one of these buckets for the current request:

1. **Planning agents needed now** — agents whose judgment shapes the plan itself (architecture, product tradeoffs, sequencing). These are the only ones that should normally be invoked for a planning-only request.
2. **Implementation agents that may be needed later** — agents who would write the code, schema, content, or config once building starts. Mention them in the plan as "who would build this," but do not invoke them to do planning work.
3. **Review agents that run only after implementation** — agents whose job is to check finished work. Never invoke these during planning; note in the plan that a review pass will happen later.

### Deferred-by-default domains

The following are normally **deferred until their phase actually begins**, and should not be pulled into a planning-only request by default just because the project will eventually need them:

- Database optimization
- Testing / QA
- DevOps
- Data visualization implementation
- Final/security/reality-check review passes

Only pull one of these in early if the current planning task specifically hinges on its expertise (e.g., the plan itself is about choosing a database engine).

### Applying this to output

When presenting a routing decision, distinguish the three categories explicitly (planning now / implementation later / review after implementation) rather than listing a single flat team. This keeps the "why now" reasoning visible and prevents scope creep from future phases leaking into the current team selection.
