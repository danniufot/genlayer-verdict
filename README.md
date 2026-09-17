# Verdict — Intelligent Escrow & Bounty Adjudicator

A GenLayer Intelligent Contract that escrows a bounty between a funder and
a worker, and uses an LLM-based multi-validator consensus to adjudicate
whether the submitted deliverable satisfies a funder-defined rubric —
then automatically routes the payout.

## What it does

1. A **funder** deploys the contract with a `rubric` (the pass/fail
   criteria) and a `duration_seconds` window.
2. The funder calls `fund()`, sending GEN into escrow and starting the
   countdown to the deadline.
3. A **worker** calls `submit()` with a `deliverable_url` and an
   `evidence_url` before the deadline. Once accepted, the submission is
   locked — no further submissions are possible.
4. After the deadline, anyone can call `resolve()`. This:
   - Fetches the deliverable and evidence content.
   - Has the leader validator ask an LLM to judge the content against
     the rubric, returning `pass`, `fail`, or `insufficient_evidence`.
   - Has every other validator **independently** re-fetch the same
     content and **independently** ask the LLM for its own verdict —
     without seeing the leader's answer.
   - Only reaches consensus if a validator's verdict, payout recipient,
     and payout amount all **exactly match** the leader's. This
     prevents two validators from reaching opposite settlements on the
     same job — a validator either agrees fully or rejects the leader.
   - Automatically transfers the escrowed stake: to the **worker** on
     `pass`, or back to the **funder** on `fail` / `insufficient_evidence`.
5. If the deadline passes with **no submission**, the funder can call
   `refund()` to reclaim the stake.

## Why it's structured this way

An earlier version of this contract failed GenVM lint because it used
`gl.nondet.web.get(url)` to fetch page content, which returns an empty
body on GenVM and isn't recognized as a valid call inside the
consensus path. This version uses `gl.nondet.web.render(url,
mode="text")` instead, which returns decoded text directly and is the
documented, reliable pattern for nondet web access.

The contract also binds validators to the **exact payout outcome**
(verdict + recipient + amount) rather than asking them to approve
whether the leader's verdict was merely "reasonable" — which is what
guarantees conflicting settlements can never both be accepted.

## Deploying

Deploy via [GenLayer Studio](https://studio.genlayer.com/run-debug)
with two constructor arguments:

- `rubric` (str) — the pass/fail criteria for the deliverable, at
  least 10 characters.
- `duration_seconds` (u256) — how long the job stays open for
  submission before it can be resolved or refunded.

## Methods

| Method | Type | Description |
|---|---|---|
| `fund()` | payable write | Funder deposits the stake and starts the deadline. |
| `submit(deliverable_url, evidence_url)` | write | Worker submits their work before the deadline. Locks after first accepted call. |
| `resolve()` | write | Callable after the deadline once a submission exists. Runs LLM adjudication + validator consensus and pays out. |
| `refund()` | write | Callable after the deadline if no submission was made. Returns the stake to the funder. |
| `get_status`, `get_verdict`, `get_rubric`, `get_stake`, `get_deadline`, `get_funder`, `get_worker`, `get_deliverable_url`, `get_evidence_url`, `get_payout_to`, `get_payout_amount` | view | Read current contract state. |

## Status

Built for the GenLayer Builder Program. Tested end-to-end in GenLayer
Studio: `fund` → `submit` → `resolve` reaches full validator consensus
(FINALIZED) with the payout correctly routed based on the verdict.
