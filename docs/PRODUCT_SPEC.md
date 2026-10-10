# Product specification

Updated October 10, 2026. Current target: **Basic trade comparison**.
The owner simplified the earlier full-portfolio, scenario-first proposal.
No application code has been implemented in this repository.

## Core question

"Would selling these investments and buying alternatives leave me better off
after taxes, and how much extra return would justify switching?"

## Basic workflow

1. Add one or more sell rows.
   - Ticker: arbitrary stock/ETF identifier; no quote lookup required.
   - **Amount to sell ($)**: proceeds from the shares under consideration.
   - **Net gain/loss ($)**: signed gain or loss included in that sale amount.
     Hint: "For the shares you're selling." Positive is a gain; negative is a loss.
   - Do not request the whole holding or original cost as additional fields.
2. Add one or more buy rows.
   - Ticker and amount, with a Dollars / % of available cash selector.
   - Percentages refer to total after-tax proceeds, not remaining cash in row order.
   - Show unallocated cash; offer "Use all available cash" as an explicit action.
   - Allow a cash remainder; do not silently scale purchases to fit.
3. Enter years and one expected annual return (%) per unique asset.
   - Same ticker shares a return assumption across sell and buy rows.
   - No scenarios or price targets on the Basic screen.
4. Enter **Federal tax rate (%)** and **State/local tax rate (%)**.
   - Add the two for the simplified model; show components separately.
   - Long-term sales assumed. Explain that any applicable federal surcharge must
     already be included in the entered federal assumption; no automatic add-on.
   - State/local is an all-in assumption for that component, not a tax lookup.
   - Require both entries or explicit zero; save assumptions locally when implemented.
5. Review taxes now, available cash, break-even return, and hold-versus-switch outcomes.

Changing a sell amount after entering a gain marks that gain "Confirm for updated
sale amount." Do not assume proportional lots or silently recalculate it.
Changing a gain or tax rate changes available cash; percentage buys update, fixed
dollar buys stay fixed. Show and block overfunded results until the user adjusts.

## Results and labels

- Immediate federal tax, state/local tax, and total estimated tax.
- Sale proceeds, available to invest, allocated funds, and cash remainder.
- Headline: required annual return on the new investment basket to break even.
- When useful, show extra annual percentage points versus the holding basket's
  equivalent annual return; do not call a percentage-point difference "percent".
- After-tax ending value: Hold, Switch, and switching advantage/disadvantage.
- Show dollar and percentage advantage; percentage is unavailable when Hold is zero.
- Detailed arithmetic and assumptions are expandable.

The primary comparison assumes liquidation of both strategies at the same horizon.
The entered sale slice funds both strategies; unchanged holdings are not required.
Do not label it a whole-portfolio projection. Outside holdings can change loss
offsets and actual taxes, so cancellation of their tax effect is not assumed.
Results are conditional on assumptions, not trade recommendations.

## Responsive interface

One responsive web app and one calculation engine for desktop and mobile.
Desktop: inputs on the left, results on the right.
Mobile: stacked trade cards with readable labels and accessible numeric inputs;
a compact result summary may link to complete results without covering controls.
Basic/Advanced is a feature choice on either device, not separate applications.
No native iOS application or PWA installation is required for the first release.

## Basic model conventions

See FINANCIAL_MODEL.md for exact formulas. Defaults for implementation:
USD; taxes paid from sale proceeds; constant entered rates now and at liquidation;
horizon greater than one year; price appreciation only; 0% cash return; no fees,
distributions, external funding, or interim trading; same-batch netting with no tax
refund or value for unused losses. These defaults are documented design choices,
not a claim that the owner approved every edge-case tax treatment.
If a declared wash-sale/disallowed-loss situation applies, Basic does not model it.
No exhaustive tax questionnaire belongs on the main screen.

## Advanced / later, not required for Basic

- Shares and position-level cost basis; brokerage CSV imports and individual tax lots.
- Live/latest available quotes with source and timestamp, arbitrary manual prices,
  locked overrides, and future price targets converted to returns.
- Detailed federal/state/local estimates using income, filing status, jurisdiction,
  holding periods, NIIT, brackets, carryforwards, and current/future tax rates.
- Whole-portfolio holdings and retained positions, distributions and cash-flow models.
- Bear/base/bull sensitivity comparisons, then paired correlated simulations,
  outcome ranges, calibrated probabilities, and allocation/tax-lot optimization.
- Saved comparisons, syncing between devices, and optional PWA installation.

The owner still wants these capabilities eventually; deferral is not removal.
Never display fabricated probabilities or imply three scenarios are confidence intervals.

## Acceptance criteria

- A one-sell/one-buy comparison needs no shares, prices, whole holdings, or import.
- Multiple arbitrary sell/buy rows work; duplicate assets share assumptions.
- Negative gains are accepted, and net sale gain is taxed once at each entered rate.
- Dollars and percentage purchases can coexist with an explicit cash remainder.
- Changing a fixed dollar input does not silently change another fixed dollar input.
- Federal and state/local estimates sum to total tax.
- Both final values include the same hypothetical liquidation convention.
- Break-even residual and funding conservation pass the model's checks.
- Invalid or unreconfirmed inputs never yield an apparently finalized result.
- All Basic actions remain usable on phone and desktop.
- No Advanced-only feature is needed to run the Basic calculation.
