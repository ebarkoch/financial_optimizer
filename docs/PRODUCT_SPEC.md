# Product specification

Status: draft for owner review. Created October 4, 2026. No application has been
implemented. Confirmed requirements and proposed defaults are distinguished in
`DECISIONS.md`.

## Purpose

Help an investor answer: "Under my assumptions, does selling some current
investments and buying alternatives leave me with more money after taxes than
holding, and how sensitive is that answer to future returns?"

The product is a decision-support calculator. It explains outcomes and tradeoffs;
it does not place trades or claim to know the best future investment.

## Confirmed requirements

- A simple, intuitive interface supporting multiple existing positions, multiple
  proposed sales, and multiple replacement investments.
- Long-term capital gains as the default in v1, with detailed tax assumptions later.
- A fair after-tax comparison of holding and proposed reallocations.
- Bear, base, and bull return assumptions.
- Probability of reallocation outperforming holding, with Monte Carlo simulation
  and confidence intervals eventually supported.
- Break-even future return required to overcome the cost of selling.
- A financial engine separate from the UI, with transparent, auditable calculations.

## Proposed v1 scope

Use one taxable portfolio in USD, with manually entered values and assumptions.
The US tax context is a proposal to review, not a confirmed fact about the owner.
Use one user-entered combined long-term gains tax rate; do not determine rates from
income, filing status, or tax brackets. Require a rate or explicit confirmation of
0%; do not silently default to a potentially incorrect percentage.

Compare the entire entered portfolio against one user-defined proposal at a time.
Each proposal can sell any fraction of several holdings and allocate all net
proceeds across several destinations. Existing shares that are not sold remain in
the comparison. Later versions can save and compare several alternative proposals.

Use a common horizon greater than one year so newly bought assets can also fit the
long-term assumption. Project annual price appreciation, excluding dividends and
interest, with no interim trading or rebalancing. This first model fits growth
assets; income-producing investments need a later cash-flow model. ETFs can be
modeled only with this distribution limitation made visible.

The main result is estimated spendable value after hypothetical liquidation of
both portfolios at the horizon. Also show their values before that final tax and
show the proposal's immediate tax separately. Liquidation is a comparison
convention, not an instruction to sell at that date.

## Simple user workflow

1. Enter current holdings: name or ticker, current total market value, and adjusted
   total cost basis (the amount used to calculate a taxable gain). Each entry
   represents a homogeneous holding or lot; distinct lots can have the same ticker.
2. Choose how much of each entry to sell, from 0% to 100%. Explain that partial
   sales use that entry's proportional basis rather than selecting tax lots.
3. Add replacement investments and allocation percentages totaling 100% of the
   proceeds remaining after the estimated sale tax.
4. Enter the horizon, the combined gains rate, and annual bear/base/bull returns
   for every asset that appears in either portfolio. Share the same return
   assumption for the same asset in both portfolios.
5. Review a comparison table with holding, proposal, and dollar/percentage
   difference for each scenario. Show immediate tax, net investable proceeds,
   final tax estimates, and the break-even replacement return.
6. Optionally assign probabilities to the three scenarios. Show a weighted
   probability of outperforming only when valid probabilities are provided.
   Without them, show "Probability not estimated" and which scenarios win.
7. Expand "How this was calculated" to inspect position-level values, basis,
   realized gains and losses, portfolio tax totals, formulas, and assumptions.

The first screen should explain the question being answered and use understandable
labels. Keep the primary inputs and results prominent; place detailed calculation
tables and limitations in an expandable section. Keep essential assumptions such
as omitted dividend taxes and the loss policy visible near the results.

## Result semantics

- A positive difference means the proposal has a higher after-tax terminal value
  under that scenario, not that the app recommends the trade.
- Percentage advantage is the difference divided by holding's after-tax value;
  when holding's value is zero, show the percentage as unavailable.
- Break-even is an equivalent annual return for the replacement basket, with the
  horizon, original holdings' scenario returns, and taxes held fixed. It is not a
  forecast or an allocation recommendation.
- Scenario probability is conditional on user-assigned weights over three
  possible joint outcomes. Equal weights are not an automatic default.
- A weighted average value, if shown, is a weighted average of after-tax scenario
  results; do not calculate it by averaging returns first.

## Later stages

After review, implement and verify the deterministic engine before the UI. Add
scenario weights with the initial calculator. Add Monte Carlo in a later stage:
joint return distributions, correlations, reproducible trials, outcome percentiles,
probability of outperforming, and an interval measuring simulation-estimate
uncertainty. The underlying input assumptions remain visible.

Later tax detail can include tax lots, short-term rates, federal/state components,
NIIT, bracket effects, loss carryforwards, wash-sale rules, tax-payment timing,
and distinct current/future rates. Later investment detail can include distributions,
fees, contributions, withdrawals, cash, and rebalancing. Decide the stack and storage
approach only after the model is reviewed.

## Outside v1

Brokerage connections, live quotes, automatic trade execution, automated portfolio
search, tax-return preparation, retirement accounts, leverage, options, multi-currency
portfolios, and claims that scenarios or simulations predict actual market odds.

## Acceptance criteria for later implementation

- Two or more holdings can be partially or fully sold into two or more destinations;
  unselected holdings remain, and the same initial portfolio funds both strategies.
- Tax is reserved from sale proceeds, replacement basis equals new purchase cost,
  and both strategies use the same horizon and final-liquidation convention.
- All three scenarios show auditable after-tax comparisons and break-even results.
- Probabilities are optional, explicitly labeled, and never inferred from scenario
  labels. Missing or invalid weights cannot produce an outperformance percentage.
- Inputs are validated with useful messages; errors never produce plausible-looking
  results. Follow the domains and unsupported-case rules in `FINANCIAL_MODEL.md`.
- The engine can run and be tested without a browser. Its results reproduce the
  documented examples and expose every relevant assumption.
- Monte Carlo and confidence intervals are identified as future functionality;
  v1 does not display fabricated simulation outputs.

## Owner review

Review the proposed tax jurisdiction, liquidation convention, price-only returns,
loss policy, and scope of the first probability estimate before authorizing code.
The outstanding choices are collected in `DECISIONS.md`.
