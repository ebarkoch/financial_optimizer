# Current status and session handoff

Last updated: October 4, 2026.

## Current stage

Product and financial-model review. No application code, selected technology stack,
installed dependencies, or runnable application exists. The owner has authorized
project documentation and source-control setup, but has not approved the proposed
financial assumptions or authorized application implementation.

## Completed

- Inspected the initially empty local folder and GitHub repository.
- Created `AGENTS.md`, `README.md`, and the product specification, financial model,
  and decision log in `docs/`.
- Cloned the repository into the local folder while preserving the documents.
- Added a macOS `.gitignore`.
- Published initial commit `8fbb33e` on `main`: "Initial project documentation and
  macOS gitignore".
- Added this handoff document and references from the project instructions and README.

## Work in progress and publication state

The owner authorized committing and pushing the handoff-document update to `main`.
This snapshot includes `docs/STATUS.md` and updates to `AGENTS.md`, `README.md`, and
`docs/DECISIONS.md`. Check Git for the current commit, working tree, and synchronization
with `origin/main` rather than treating this document as a live Git status report.
No implementation work is in progress; the next task is product and model review.

## Verification

- The initial documentation's worked-example arithmetic was checked, including
  partial-sale proceeds conservation, allocations, and break-even equality.
- The macOS ignore rules were checked without hiding the project documents.
- The initial push succeeded, and the local tree was clean afterward.
- These are documentation and repository checks; no application test suite exists.

## Review needed and blockers

No technical blocker is currently known. The next development stage requires owner
review of the proposals in `DECISIONS.md`, especially the US/USD tax context,
final-liquidation comparison, price-only returns, proportional basis, simplified
loss treatment, and user-weighted scenario probability before Monte Carlo.

## Next step

Review `PRODUCT_SPEC.md` and `FINANCIAL_MODEL.md` with the owner. Record approved
choices in `DECISIONS.md` and update the affected specifications. Wait for explicit
implementation authorization before choosing a stack or writing application code.

## Starting the next session

Provide access to `/Users/eyal/Desktop/financial_optimizer` or the latest files in
[ebarkoch/financial_optimizer](https://github.com/ebarkoch/financial_optimizer).
An uploaded copy is a snapshot; replace it when the repository documents change.

Suggested opening prompt:

> Read and follow AGENTS.md, then read docs/STATUS.md. Check the current branch,
> commit, and working tree if Git is available. Review the product specification,
> financial model, and decisions relevant to this task. Summarize the current stage,
> unresolved choices, and next step before proceeding. Product and financial logic
> are still awaiting review; application implementation has not been authorized.

Before handing off again, refresh this snapshot and record any decisions in the
decision log. Update publication state after an authorized commit or push.
