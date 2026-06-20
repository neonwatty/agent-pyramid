# Agent Pyramid

Experimental hierarchy, topology, and visualization layer for coordinating visible Codex app agents.

The idea starts from Agent Conveyor's durable ledger model, but moves experimentation into a separate repo so we can explore multi-level manager/worker hierarchies without destabilizing conveyor itself.

## Core Idea

Agent Pyramid generalizes a single manager/worker binding into a hierarchy:

```text
operator
  -> root manager
      -> child manager
          -> worker
          -> worker
      -> independent judge
          -> receipt worker
```

Every node should be a visible Codex app session, while the database ledger remains the authority for coordination. Direct app-thread messages may bootstrap or wake a session, but durable instructions, status reports, completion claims, and escalations should flow through Pyramid commands/inboxes so they leave receipts.

## First Principles

- **Visible sessions for audit.** Start with Codex app sessions so a human can inspect the actual manager/worker/judge threads.
- **Ledger as authority.** The database defines identity, current task, topology, routing, receipts, and completion state.
- **Topology is explicit.** The hierarchy must encode who may talk to whom. Parent-child routes are allowed by policy; sibling or cross-branch communication requires an explicit edge.
- **Self-context is mandatory.** Every manager or worker needs a command that explains who it is, what task it owns, where it sits in the hierarchy, and what actions it may take.
- **Visualization is a control surface.** The web UI should show live hierarchy, policy routes, blocked routes, selected-node context, receipts, and replayable evidence.

## Prototype

Open the static mockup:

```bash
npm run preview
```

Then visit:

```text
http://127.0.0.1:61777/pyramid-console.html
```

The prototype is self-contained HTML in `prototype/pyramid-console.html`.

## Proposed CLI Shape

Early commands should stay conservative:

```bash
pyramid run create <objective> --json
pyramid node register --role manager|worker|judge|scout --codex-app-thread-id <id> --json
pyramid topology allow <source> <target> --message-type report|nudge|review --json
pyramid topology deny <source> <target> --reason <reason> --json
pyramid context --self --json
pyramid inbox --consume-next --json
pyramid report --status <status> --json
pyramid visualize --port 61777
```

## Repo Status

This repo currently contains the product/design seed and interactive visualization mockup. It does not yet implement the database, CLI, Codex app binding, or routing engine.

See:

- `docs/design-seed.md`
- `docs/handoff-prompt.md`
- `prototype/pyramid-console.html`

