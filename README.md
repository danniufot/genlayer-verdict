# Verdict — GenLayer Intelligent Escrow & Bounty Adjudicator

Verdict is an Intelligent Contract built on GenLayer that enables escrowed jobs and bounties to be resolved using natural-language requirements, public web evidence, and AI-validator consensus.

## What it does

A funder creates a job with:

* A natural-language rubric
* A resolution deadline
* An escrowed GEN stake

A worker then submits:

* A deliverable URL
* An evidence URL

After the deadline, Verdict retrieves the submitted web content and asks GenLayer's non-deterministic execution layer to determine whether the deliverable satisfies the rubric.

The proposed decision is independently validated before the contract finalizes the outcome.

The supported outcomes are:

* `pass`
* `fail`
* `insufficient_evidence`

If the result is `pass`, the escrowed stake is transferred to the worker.

If the result is `fail` or `insufficient_evidence`, the stake is returned to the funder.

## Why GenLayer?

Traditional smart contracts are well suited to deterministic rules, but they cannot natively determine whether a real-world deliverable satisfies a natural-language specification.

Verdict uses GenLayer's Intelligent Contract model for exactly this type of problem:

1. Read external web content.
2. Interpret a natural-language rubric.
3. Produce a structured judgment.
4. Independently validate the proposed judgment.
5. Reach a consensus-backed result.
6. Execute an onchain financial consequence.

This makes the contract useful for bounty completion, milestone verification, grants, escrow, content verification, and other situations where the outcome depends on interpreting evidence.

## GenLayer Features Demonstrated

Verdict demonstrates:

* Intelligent Contracts
* Non-deterministic web access
* LLM-based reasoning
* Structured JSON outputs
* Validator-side verification
* Optimistic consensus
* Persistent contract state
* GEN escrow
* Native value transfers
* Finalized consensus transactions

## Contract Lifecycle

```text
OPEN
  |
  | fund()
  v
FUNDED
  |
  | submit()
  v
SUBMITTED
  |
  | deadline passes
  v
resolve()
  |
  | GenLayer web + LLM evaluation
  v
PASS / FAIL / INSUFFICIENT_EVIDENCE
  |
  +---- PASS ----------------> worker receives stake
  |
  +---- FAIL ----------------> funder receives stake
  |
  +---- INSUFFICIENT --------> funder receives stake
```

## Deployed Contract

**Network:** GenLayer Studio / Studionet

**Contract address:**

`0x20EA0A6e9Df705dF368077d5e4FEE8eb1660a21f`

The deployed contract was tested through the complete lifecycle:

1. Contract deployment
2. Funding with GEN
3. Worker submission
4. Deadline enforcement
5. Web evidence retrieval
6. LLM adjudication
7. Validator verification
8. Consensus finalization
9. Escrow settlement

The final resolution successfully finalized with the contract reaching the successful verdict state.

## Example Rubric

The deployed demonstration used the following rubric:

> The submitted page must be a public GitHub README containing an Install section and at least one code example.

The contract evaluated the submitted public GitHub README against this requirement.

## Security / Design Notes

Retrieved web pages are treated as evidence rather than instructions.

The adjudication prompts explicitly instruct the model not to follow instructions contained inside retrieved webpages.

The contract also uses a restricted verdict enum rather than allowing arbitrary natural-language output to directly control settlement.

If evidence is insufficient, the contract fails closed by treating the result as unsuccessful rather than paying the worker.

## Repository

Source code and deployment documentation are available in this repository.

## Future Improvements

Potential future versions could include:

* Multiple evidence sources
* Explicit dispute periods
* Appeal mechanisms
* Multiple workers
* Partial milestone payments
* More sophisticated rubric schemas
* A frontend for creating and resolving bounties
* Reusable bounty templates
* Onchain reputation for workers and funders

## Author

Built as a GenLayer Builder contribution demonstrating practical Intelligent Contract adjudication.
