# Agent Pyramid Design Seed

## Objective

Create an experimental sibling project to Agent Conveyor that explores multi-level agent coordination for visible Codex app sessions.

The first milestone is not full autonomy. It is an auditable hierarchy where each node knows its role, task, parent, children, allowed routes, and evidence obligations.

## Why Separate From Agent Conveyor

Agent Conveyor already has useful primitives: sessions, tasks, bindings, Dispatch, inboxes, telemetry, loop evidence, and replay.

Pyramid should begin outside that codebase because the model is more experimental:

- managers may manage managers;
- one run may contain multiple active task branches;
- communication routes are graph/topology based;
- visualization is central, not auxiliary;
- every node needs a self-context contract.

The long-term path can still be reuse, extraction, or backporting once the hierarchy model proves itself.

## Starting Assumptions

- The first prototype should use **visible Codex app sessions**, not a pure ledger simulation.
- The database ledger is the coordination authority.
- The Codex app transcript is human-reviewable evidence, not the routing source of truth.
- Direct thread messages are allowed for bootstrap/wakeup, but durable work should use Pyramid inbox/report commands.
- Topology policy must be enforced by the ledger and Dispatch-like routing layer.

## Core Data Model

Suggested tables or logical records:

### `pyramid_runs`

- `id`
- `objective`
- `state`
- `root_node_id`
- `created_at`
- `updated_at`
- `metadata_json`

### `pyramid_nodes`

- `id`
- `run_id`
- `role`: `operator`, `manager`, `worker`, `judge`, `scout`
- `label`
- `parent_node_id`
- `task_id`
- `codex_app_thread_id`
- `codex_app_thread_title`
- `state`
- `authority_json`
- `self_context_json`

### `pyramid_edges`

- `id`
- `run_id`
- `source_node_id`
- `target_node_id`
- `relation`: `supervises`, `reports_to`, `reviews`, `collaborates`
- `allowed_message_types_json`
- `state`
- `policy_json`

### `pyramid_messages`

- `id`
- `run_id`
- `source_node_id`
- `target_node_id`
- `message_type`
- `state`
- `payload_json`
- `receipt_json`
- `created_at`
- `consumed_at`

### `pyramid_receipts`

- `id`
- `run_id`
- `node_id`
- `task_id`
- `receipt_type`
- `summary`
- `artifact_path`
- `payload_json`
- `created_at`

## Self-Context Contract

Every node should be able to call:

```bash
pyramid context --self --json
```

Example:

```json
{
  "node": "speaker-manager",
  "role": "manager",
  "parent": "atlas-root",
  "children": ["route-scout", "proof-worker"],
  "task": "Coordinate signed-in speaker workflow verification.",
  "may_message": ["parent", "children"],
  "may_delegate": true,
  "may_finish": false,
  "expected_outputs": ["child_status_rollup", "evidence_review"],
  "blocked_escalation_target": "atlas-root"
}
```

## Visualization Server

The visualization should be a small local web server, probably served by the Pyramid CLI.

The UI should provide:

- a live hierarchy map;
- node status: active, waiting, blocked, done;
- allowed routes and denied routes;
- selected-node self-context;
- current task and authority limits;
- inbox/read status;
- receipt and evidence summaries;
- replay timeline.

The first mockup is in `prototype/pyramid-console.html`.

## Prototype Slice

Target first real slice:

```text
root manager thread
  -> speaker-manager thread
      -> route-scout worker thread
      -> proof-worker thread
  -> boundary-judge thread
      -> receipt-packager worker thread
```

Acceptance for the slice:

- each node is registered with a Codex app thread id/title;
- topology allows only parent-child messages by default;
- a worker-to-worker message is blocked and recorded as a policy receipt;
- `pyramid context --self --json` returns correct context for every node;
- the visualization shows six nodes, allowed edges, the blocked edge, selected-node details, and receipts.

## Open Design Questions

- Should Pyramid vendor Agent Conveyor internals, import them as a package, or start with a minimal independent ledger?
- Should a manager be represented as both a worker of its parent and a manager of its children, or as one node with dual authority?
- How much should the visualization server mutate state versus only observe it?
- What should be the minimum durable receipt for a Codex app thread wakeup?
- How should completion rollups be validated against child receipts?

