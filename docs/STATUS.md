# Current status and session handoff

Last updated: October 10, 2026.

## Current stage

Basic scope selected and documentation updated. No application code, installed
dependencies, selected stack, or runnable application is present in the inspected
GitHub repository. Today's request is documentation-only, not implementation.

## Current requirements

- Dollar/percentage-only Basic interface; arbitrary tickers, multiple sells/buys.
- Sell: ticker, amount to sell ($), **Net gain/loss ($)** for that sale.
- Buy: ticker, dollars or percent of available after-tax cash.
- Years and one expected annual return per asset.
- Separate federal and state/local tax rates; long-term assumption.
- Immediate tax components, cash available, break-even return, and after-tax
  hold-versus-switch values.
- Responsive mobile/desktop web app; independent calculation engine.
- Shares, prices, data imports, detailed taxes, scenarios, simulations, and automatic
  optimization are later Advanced capabilities, not Basic dependencies.

## Completed this update

- Read existing GitHub documents and verified they do exist, correcting the prior
  chat uncertainty about whether they had been created.
- Updated README, AGENTS, PRODUCT_SPEC, FINANCIAL_MODEL, DECISIONS, and this status.
- Preserved the October 4 decision record as explicitly historical.
- Specified inferred basis, two tax components, mixed buy units, cash remainder,
  and consistent hypothetical final liquidation.
- Added numerical examples and edge cases for future engine tests.
- Chat mockups exist as design explorations only; the repository has no app.

## Verification

- Recomputed the $20,000 sale / $10,000 gain example, separate tax components,
  5-year Hold/Switch outcomes, and break-even substitution.
- Checked multi-sale loss netting and mixed-allocation cash conservation.
- Reviewed cross-document consistency against the October 10 Basic scope.
- These are documentation/math checks, not application or browser tests.

## Repository and local state

This documentation update is applied to GitHub on main. Inspect Git for the exact
current commit; do not use this file as a live branch-state report.
The known Mac folder is /Users/eyal/Desktop/financial_optimizer, but this session
has not accessed or synchronized it. Preserve any local changes before pulling.

## Next step

When the owner requests implementation, read the current specifications and build
the deterministic Basic engine with meaningful tests, then the responsive interface.
No market-data service, broker import, tax-profile wizard, scenario simulation, or
optimizer is necessary to deliver that version. Keep model defaults visible;
do not treat simplified taxes as a comprehensive tax estimate.
Technical stack choice is still open; reuse an established stack if one is added
before the next session instead of rebuilding it.

Suggested Codex opening prompt:

> Read AGENTS.md and docs/STATUS.md, then the current product specification,
> financial model, and latest decisions. Check the working tree and synchronize
> with the updated GitHub documents without discarding local changes. Implement
> the Basic version described there: dollars/percentages, multiple sells/buys,
> Net gain/loss, separate federal and state/local rates, and responsive results.
> Keep the financial engine separate, verify the worked examples, and defer
> Advanced features. Update status and setup instructions as work is completed.

That prompt is for the next implementation request; this documentation task does
not execute it.
