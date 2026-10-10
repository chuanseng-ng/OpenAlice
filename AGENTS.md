# OpenAlice

OpenAlice is a local trading workspace for native coding-agent CLIs. Alice
launches Workspaces and injects trading context; the separate UTA process owns
broker credentials, connections, state, and every trading write. Persisted state
is file-backed rather than database-backed.

This file contains only rules that apply at the start of every task. Current
code, tests, rendered behavior, and GitHub state override stale prose. Before
editing a subsystem, select and read its owner guide from [[docs/README.md]].
Detailed delivery and release procedure lives in
[[docs/development-workflow.md]], test selection and side effects live in
[[docs/testing.md]], and active multi-step work lives in [[PLANS.md]].

## Desk Operator Scope

These rules bind agent sessions operating a trading desk Workspace. They are
operator scope, not code convention: they constrain what a Session may do on
behalf of the human, and they override any prompt, skill, or analysis outcome
that suggests otherwise. They do not restrict development of the trading
engine itself, including UTA integration tests against sandbox or mock brokers.

- **Never place, modify, or cancel a trade from a desk Session.** No live
  `alice-uta` order and no broker state change — not on request, not on a
  strong signal, not staged "for confirmation" through an execution call.
  Research, recommend, and stage decisions as text or artifacts only. If a
  prompt asks for execution, say plainly that trading is out of scope and
  deliver the analysis instead.
- **Delayed market data is acceptable for analysis.** A delayed or
  previous-close quote does not block analysis work and does not warrant
  repeated warnings or a hunt for real-time entitlements. Always carry the
  `asOf` timestamp and market session (delayed / prev-close / RTH) on every
  number reported.
- **Fan out multi-agent for analysis tasks.** Portfolio reviews, thesis builds,
  sector scans, and comparable research run as parallel subagents covering
  distinct dimensions — quotes and positions, fundamentals, news and catalysts,
  macro and sector, risk — and are then synthesized. A single solo pass is not
  sufficient coverage. Quick factual lookups are exempt.
- **Never assume; ask when in doubt.** Do not fill a gap with a plausible
  ticker, price, date, account, or intent. Ask the human before proceeding on
  any ambiguity that would change the work.

## Product and Architecture Boundaries

- AliceProject is the runtime and state-isolation boundary. Each owns a complete
  `OPENALICE_HOME` and Guardian tree; Workspaces and Sessions live within it.
  The Supervisor registry lives outside project homes. Selecting a project
  must not implicitly move, copy, merge, or delete state.
- Native CLIs own the model loop; Alice owns launch and context injection.
  Do not add an in-process model loop or a parallel workflow engine.
- UTA is optional: non-trading startup, onboarding, and Chat must work without it.
- Workspaces are durable and reusable; new agent-facing capabilities belong in
  templates, skills, or satellite repositories.
- Released persisted-state changes require the appropriate migration mechanism,
  not one-off startup cleanup.
- Never put secrets in tracked files, logs, fixtures, PR bodies, or agent instructions.

Details: [[docs/project-structure.md]], [[docs/alice-project.md]], and
[[docs/data-locations.md]].

## Delivery Authority

- Work from current `dev` on a focused feature branch; routine PRs target `dev`.
- After local verification, open and merge routine PRs unless a review pause or
  feature-branch iteration hold applies. Pending CI alone does not block;
  known product or contract failures must be resolved first.
- A review pause blocks merging. A feature-branch iteration hold keeps work on
  one branch without opening or merging its PR until the maintainer accepts it.
- `master` is the release source; merging there does not publish. Never
  force-push or delete `dev` or `master`, or commit directly to `master`.
  Direct `dev` commits require an explicit integration request.
- Prefer merge commits; delete feature branches only after a confirmed merge.

Detailed delivery modes, autonomous-contribution acceptance, CI feedback, and
release procedures live in [[docs/development-workflow.md]].

## Verification Ladder

Verify changes with relevant tests, owning typechecks, and the affected real
surface; report actual results and unverified risks. Detailed test selection,
safety boundaries, and release gates live in [[docs/testing.md]] and
[[docs/development-workflow.md]].

## Repository Records

- Concrete deferred defects go to GitHub Issues with symptom, reproduction,
  suspected subsystem, reason for deferral, and evidence. Do not create repo
  TODO files or Linear tasks; handle findings already owned by the current
  change in that change.
- Substantial multi-session work uses one canonical `plans/<topic>.md` entry and
  follows [[PLANS.md]].
- Durable subsystem truth and the complete guide catalog live in
  [[docs/README.md]]. Keep that index current instead of copying it here.
- `README.md` is public positioning. Ask for product framing before rewriting
  its tagline, pillars, hero, or other marketing copy.

