# Working instructions

## Project and current stage

Portfolio Optimizer helps a user compare holding existing investments with selling
some holdings and reallocating the proceeds, accounting for taxes and uncertain
future returns. The owner is new to software development: explain unfamiliar
terms, decisions, verification results, and the next review step in plain language.

The project is at the documentation-review stage. The initial task authorizes
these context documents only. Stop before application implementation; wait for
the owner to review the product and financial model and authorize the next stage.
Do not select a technology stack, install dependencies, or create scaffolding as
part of this initial stage.

## Sources of project context

- `docs/PRODUCT_SPEC.md`: user needs, scope, workflow, and acceptance criteria.
- `docs/FINANCIAL_MODEL.md`: assumptions, formulas, examples, and model limitations.
- `docs/DECISIONS.md`: confirmed requirements, proposed decisions, and open questions.
- `README.md`: starting point and current project status.

Read these before making related changes. Explicit owner instructions take
precedence. If the documents conflict, explain and resolve the conflict rather
than silently inventing a financial rule. Record approved changes in the decision
log and keep the specification and model consistent.

## Development rules for later authorized work

- Keep the calculation engine independent of the UI, framework, network, and
  storage. Give it explicit inputs and return structured results and an audit trail.
- Put financial formulas in the engine; the UI collects inputs and presents results.
- Prefer small, reviewable increments. Explain the intended outcome before a
  meaningful change and summarize what changed and how it was checked afterward.
- Do not add automatic recommendations, trade execution, or hidden assumptions.
- Keep assumptions editable and visible. Label examples as synthetic; never
  present an illustrative tax rate or return as a personalized estimate.
- Preserve multiple positions, partial sales, multiple destinations, and unchanged
  holdings in the data model. Use stable IDs rather than tickers as record identities.
- Keep scenario probabilities distinct from simulation probabilities. Never infer
  either from the names bear, base, and bull or fabricate a confidence interval.
- Use precise numeric handling and documented tolerances. Round for display only;
  do not round intermediate financial calculations or silently clamp invalid inputs.
- Include model version and assumptions in results. Later simulations must also
  include their seed, distributions, correlations, and trial count.
- Test meaningful financial behavior: worked examples, conservation of proceeds,
  basis tracking, tax netting, partial sales, identical portfolios, zero tax,
  negative returns, break-even residuals, and scenario probability semantics.
- Keep real portfolio data, account details, credentials, and local exports out of
  source control. This GitHub repository is public. Use synthetic fixtures.
- Do not commit, push, publish, deploy, or add external services unless requested.
  Preserve unrelated user changes and inspect the working tree before editing.

## Review and handoff

For financial changes, update the relevant formulas, worked examples, limitations,
and decisions together. Report verification honestly: documentation checks are
not application tests, and a scenario model is not an empirical market forecast.
Surface unresolved choices that materially affect the answer before implementing
dependent behavior.
