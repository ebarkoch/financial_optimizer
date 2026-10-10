# Working instructions

## Project and current stage

Build toward a simple tax-aware "Hold or switch?" comparison. The owner is new to
software development: use plain language and avoid unnecessary follow-up questions.
The October 10, 2026 scope supersedes the original scenario-first v1 proposal.

This task authorizes a documentation update, not application implementation,
dependency installation, scaffolding, or deployment. The next implementation task
should follow the current Basic scope rather than reopen decisions already made.
No stack is established in this repository. An independent Python engine was
discussed previously; it is a direction, not an installed implementation.

## Read first

- `docs/STATUS.md`: current stage and next step.
- `docs/PRODUCT_SPEC.md`: interface, scope, and acceptance criteria.
- `docs/FINANCIAL_MODEL.md`: calculation contract and worked examples.
- `docs/DECISIONS.md`: confirmed choices, defaults, and superseded history.
- `README.md`: overview.

Explicit owner instructions take precedence. Keep all affected documents consistent.
Do not interpret old entries in the decision history as current requirements.

## Basic scope guardrails

- Basic inputs use dollars and percentages only, plus ticker identifiers and years.
- Sell fields: ticker, amount to sell ($), **Net gain/loss ($)** for that exact sale.
- Do not ask for both net gain and cost basis. Infer basis as sale amount minus gain.
- Changing a sale amount does not justify silently scaling the entered gain.
  Preserve it and request reconfirmation before treating results as final.
- Buy fields: ticker, dollars or percentage of available after-tax proceeds.
- Separate editable federal and state/local rates. No automatic tax profile or
  bracket calculation in Basic. Long-term is an explicit assumption.
- No mandatory shares, stock prices, total holdings, purchase dates, or broker imports.
- Expected annual returns are user assumptions, not fetched analyst forecasts.
- No bear/base/bull grid, probability, confidence interval, or optimizer in Basic.
- Support arbitrary tickers without requiring a quote-provider match.
- Use one responsive web interface; Basic/Advanced is independent of screen size.

## Development rules for subsequent implementation

- Separate financial calculations from UI, storage, framework, and network.
  Use explicit inputs, structured results, model version, and an audit trail.
- Use stable row IDs and canonical asset IDs; ticker is not a row ID. Shared
  assets use the same return assumption in both strategies.
- Compare the entered sale slice. Do not claim a whole-portfolio tax estimate or
  fabricate unseen holdings. Advanced can model retained positions and tax lots.
- Follow the documented aggregate gain/loss netting, new purchase basis, cash,
  and final-liquidation rules. Never double-subtract immediate taxes.
- Keep assumptions visible but compact. Use synthetic examples. Tax and return
  defaults must not masquerade as personalized estimates.
- No automatic recommendations, trade execution, undisclosed normalization,
  or hidden changes to user-entered dollar trades.
- Choose precise numerical handling and documented tolerances; reject invalid
  or nonfinite inputs. Round presentation, not intermediate calculations.
- Test meaningful financial behavior: examples, conservation, tax components,
  loss netting, zero taxes, negative returns, mixed allocation units, cash,
  break-even substitution, and changed sale/gain consistency.
- In later simulations, distinguish outcome percentiles from confidence intervals;
  record seed, distributions, correlations, trial count, and model version.
- Keep credentials, real holdings, personal tax data, and local exports out of this
  public repository. Preserve unrelated files and inspect changes before writing.
- Commit, push, publish, deploy, or add external services only when requested.
  Owner authorization for this documentation update does not authorize deployment.

## Handoff

Start each session by reading status and checking the actual repository and working
tree. Do not assume the Mac checkout reflects a GitHub edit. Before finishing,
update status with completed work, verification, limitations, and next step; append
decisions with their reasons. Document-only checks are not application tests.
