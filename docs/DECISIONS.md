# Decisions and review log

Created October 4, 2026. "Confirmed" means explicitly requested by the owner;
"Proposed" means a draft choice awaiting review. Do not treat a proposal as approval.

## Initial inspection

- Local folder: `/Users/eyal/Desktop/financial_optimizer` existed and was empty,
  including no `.git` directory. It was not a clone or initialized Git repository.
- GitHub: [ebarkoch/financial_optimizer](https://github.com/ebarkoch/financial_optimizer)
  exists and is public. The authenticated contents API reported "This repository
  is empty" and the branches endpoint returned no branches. Metadata names `main`
  as the default branch, but there is no actual branch or commit yet.
- No existing application, dependencies, tests, README, or project instructions
  were found. No stack or license was established.
- This task creates five local documentation files. It does not initialize Git,
  commit, push, install dependencies, or build an application.

## Confirmed decisions

| ID | Decision | Reason |
| --- | --- | --- |
| D01 | Review product and financial logic before implementation | Owner's requested first task |
| D02 | Keep the UI simple and explain development explicitly | Owner is new to software development |
| D03 | Support several holdings, sales, and destinations | Required portfolio workflow |
| D04 | Assume long-term gains in v1; detailed tax inputs later | Reduce initial input complexity |
| D05 | Compare after-tax outcomes with bear/base/bull assumptions | Required decision support |
| D06 | Include probability of outperforming and break-even return | Required outputs |
| D07 | Add Monte Carlo and confidence intervals in a later stage | Requested future uncertainty features |
| D08 | Separate the engine from the UI and expose calculations | Transparent and auditable behavior |

## Proposed defaults for review

| ID | Proposal | Reason and tradeoff |
| --- | --- | --- |
| P01 | US-style taxable portfolio, USD, manually entered combined rate | Simple initial context; jurisdiction is not confirmed and tax brackets are omitted |
| P02 | Require an entered rate or explicit 0% | Long-term treatment does not imply one correct tax percentage |
| P03 | Pay sale tax from proceeds; liquidate both portfolios at a shared horizon | Equal funding and spendable-value comparison; actual tax dates differ |
| P04 | Require a horizon greater than one year | Replacement assets should fit the long-term assumption too |
| P05 | Homogeneous position/lot entries with proportional partial-sale basis | Simple inputs; no automatic tax-lot selection |
| P06 | Price appreciation only; no distributions or interim activity | Avoid unmodeled distribution taxes and basis changes; limits investment coverage |
| P07 | Net same-batch gains/losses, floor tax at zero, omit carryforwards | Handles multiple sales while keeping the tax model small; discarded losses may have real value |
| P08 | Exclude wash-sale-affected losses and flag loss sales | Loss netting is unreliable when a loss is disallowed |
| P09 | One proposal at a time; allocate 100% of net proceeds | Clear first workflow; saved side-by-side proposals and cash can come later |
| P10 | Optional user-weighted scenario probability in v1 | Provides a transparent probability conditional on three assumed outcomes; not a simulation estimate |
| P11 | Full-portfolio break-even as an equivalent replacement-basket CAGR | Supports several destinations and retains portfolio-level tax offsets |
| P12 | Deterministic engine first, small UI second, Monte Carlo later | Review and validate the financial logic before expanding functionality |

## Open review questions

1. Is US taxation in USD the right starting context, and are these taxable accounts?
2. Should the primary comparison be spendable value after final liquidation, as
   proposed, or is a different horizon-tax convention needed?
3. Is price appreciation without dividend/interest cash flows useful for the intended
   investments, or should distributions enter the first model?
4. Are proportional basis entries and the simplified loss policy sufficient, or are
   tax-lot selection and carryforwards essential immediately?
5. Is a clearly labeled user-weighted scenario probability useful in v1, or should
   probability remain unavailable until Monte Carlo is implemented?
6. After model review: which platform, language/framework, storage approach, and
   numerical representation should be used? Consider a browser-first interface
   and local calculations, but no stack has been selected.

These questions do not prevent the requested documentation from being completed.
Resolve those affecting the first model before coding its dependent behavior.

## Planned checkpoints

1. Owner reviews these documents; update and approve the assumptions.
2. Connect the local folder to the empty GitHub repository and decide the initial
   source-control workflow when authorized. A commit is a saved local snapshot;
   a push publishes those snapshots to GitHub. Neither has happened in this task.
3. Choose a stack and implement the independent deterministic engine with tests
   derived from the model's examples and invariants.
4. Add a minimal input/results UI and verify complete example workflows.
5. Design and review Monte Carlo assumptions before implementing simulation.

## Updating this log

Add dated entries with decision ID, status, choice, reasoning, and affected files.
Mark proposals approved only after owner review. Preserve superseded choices with
a reference to their replacement rather than erasing the history.

## Source-control follow-up — October 4, 2026

The owner requested cloning the repository into the local folder, adding a macOS
`.gitignore`, and creating and pushing the initial commit. The clone preserved the
five existing documents and connected `origin` to the GitHub repository on `main`.
The initial snapshot includes those documents and `.gitignore`. This source-control
authorization does not approve the proposed financial assumptions or authorize
application implementation; the documentation-review checkpoint remains in place.
