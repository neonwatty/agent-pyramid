# Agent Instructions

## Project Posture

This repo is experimental. Optimize for clear contracts, auditable behavior, and small vertical slices over breadth.

## Safety

- Do not print secrets, tokens, cookies, auth storage contents, private keys, or raw environment values.
- Treat Codex app thread ids/titles as operational metadata, but do not claim transcript evidence unless it was actually inspected.
- Keep direct app-thread messages separate from durable ledger receipts.

## Design Priorities

- The ledger is the authority for topology, routing, task state, receipts, and completion.
- The Codex app transcript is reviewable evidence, not the source of routing truth.
- Every manager, worker, scout, or judge needs a self-context command.
- Topology must answer: who can talk to whom, for what message types, and under whose authority?
- Visualization should read from the same ledger state agents use.

## Verification

When changing CLI, database, routing, or visualization behavior, add focused tests or a runnable smoke path. Record commands run and known limitations in final handoffs.

