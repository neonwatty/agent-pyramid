# Handoff Prompt

Use this prompt to continue in a fresh Codex project rooted at this repository.

```text
We are starting work in the `agent-pyramid` repo.

Context:
- Agent Pyramid is an experimental sibling project inspired by Agent Conveyor.
- The goal is to explore multi-level manager/worker/judge hierarchies for visible Codex app sessions.
- We intentionally kept this separate from Agent Conveyor so we can iterate without destabilizing its existing single manager/worker ledger and Dispatch model.
- The first prototype should use visible Codex app sessions for auditability.
- The database ledger should be the authority for coordination: topology, current task, allowed routes, inboxes, receipts, self-context, and completion state.
- Direct Codex app thread messages are allowed for bootstrap/wakeup, but durable instructions and reports should go through Pyramid commands/inboxes.
- Topology matters: Pyramid must explicitly encode who can talk to whom. Parent-child routes are allowed by default; sibling/cross-branch routes require explicit edges and should otherwise be blocked with receipts.
- Every node must be able to call `pyramid context --self --json` and get its role, task, parent, children, allowed message routes, authority, and expected outputs.
- The local visualization server is a first-class control/audit surface, not decoration.

Current repo contents:
- `README.md` describes the concept and proposed CLI.
- `docs/design-seed.md` captures the first architecture seed.
- `prototype/pyramid-console.html` is a self-contained interactive mockup.

Near-term goal:
Turn this design seed into a concrete implementation plan for the first vertical slice:

root manager
  -> speaker-manager
      -> route-scout
      -> proof-worker
  -> boundary-judge
      -> receipt-packager

The first slice should prove:
- six visible Codex app nodes can be registered;
- parent-child topology is represented in the ledger;
- a denied worker-to-worker message is blocked and recorded;
- every node has a working self-context JSON command;
- a local web visualization reads the ledger and shows the hierarchy, selected-node context, allowed/blocked routes, and receipts.

Please inspect the repo, then propose the smallest implementation plan. Prefer a conservative TypeScript/Node CLI plus SQLite ledger unless local repo evidence suggests another better starting point.
```

