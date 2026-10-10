# Financial model

Updated October 10, 2026. Model specification: `basic-0.2`.
This specifies future implementation, not a running engine or a complete tax model.
All examples and rates below are synthetic, not current statutory rates.

## Scope and conventions

Compare only the amounts in the sell rows: Hold retains those investments;
Switch sells them now, reserves tax, buys replacements, and retains unallocated cash.
No full portfolio or shares are required. Omitted holdings may affect actual tax
netting; the result is explicitly a traded-slice comparison.

Both strategies are liquidated hypothetically at horizon h. Long-term treatment,
h > 1 years, USD, and constant manually entered federal and state/local rates
apply. Immediate tax comes from proceeds, never unmodeled outside cash.
Annual returns are price appreciation only. Exclude distributions, fees, additions,
withdrawals, interim trading, and rebalancing. Cash earns 0%.
These are transparent implementation defaults; do not describe them as tax law.

Net gains/losses across the entered sale batch now and separately at final liquidation.
Negative net gains create zero tax, not a refund. No loss carryforwards or
ordinary-income deduction value. Disallowed losses and wash-sale cases require
Advanced modeling; do not treat known disallowed losses as available offsets.

## Inputs

| Symbol | Meaning |
| --- | --- |
| S_i | Amount to sell ($) in row i |
| G_i | Entered Net gain/loss ($) for exactly that sale |
| B_i = S_i - G_i | Inferred adjusted basis for the sale slice |
| f, s | Federal and state/local decimal tax assumptions |
| t = f + s | Combined simplified rate |
| h | Horizon in years, greater than 1 |
| r_a | Expected annual price return for asset a |
| D_j or w_j | Buy dollars or fraction of total after-tax proceeds |

Use stable row IDs, canonical asset IDs, and shared per-asset returns. Duplicate
ticker rows are allowed but are not independent return assumptions.
Convert entered percentages to decimals once. Gains are signed dollars, not a rate.
Editing S_i does not authorize scaling G_i; retain it and require reconfirmation.

## Immediate sale and cash allocation

```text
S = sum(S_i)
G = sum(G_i)
taxable_gain_now = max(G, 0)
T_f = f * taxable_gain_now
T_s = s * taxable_gain_now
T_now = T_f + T_s
A = S - T_now

P_j = D_j              for a dollar buy
P_j = w_j * A          for a percentage buy
P = sum(P_j)
K = A - P             unallocated cash
new_basis_j = P_j
```

Net first, then apply rates; never independently tax each positive row.
Basis is not subtracted from cash. Require K >= 0 within numerical tolerance.
Do not normalize purchases unless the user invokes an explicit action.
"Use all available cash" scales positive allocations proportionally to A; when
all are zero it can split equally, visibly. Preserve each row's chosen unit.
Changing A updates percentage rows; fixed dollar rows remain fixed.

Conservation: `P + K + T_now = S`.
A partial holding sale is already represented by its own S_i and G_i; no whole
holding or automatic proportional cost basis calculation is needed.

## Growth and final taxes

```text
growth(x, r, h) = x * (1 + r)^h

H_gross = sum(growth(S_i, r_asset(i), h))
B_H = sum(B_i)
H_gain = max(H_gross - B_H, 0)
H_tax_f = f * H_gain
H_tax_s = s * H_gain
H_net = H_gross - H_tax_f - H_tax_s

R_gross = sum(growth(P_j, r_asset(j), h))
R_gain = max(R_gross - P, 0)
R_tax_f = f * R_gain
R_tax_s = s * R_gain
R_net = R_gross - R_tax_f - R_tax_s + K

delta = R_net - H_net
advantage = delta / H_net     only if H_net > 0
```

Cash principal K is not a gain. New purchases have new basis P; do not carry old
sold basis into them. T_now is already removed from investment capital: never
subtract it a second time at the horizon. Show both final tax components in the
audit output. The same f and s apply now and later in Basic.

## Break-even

Find an equivalent common annual return r_star on the invested replacement amount
P that matches H_net after final tax, while K stays cash at 0%. It is a basket
threshold, not a prediction for each buy.

Define `net(W,B) = W - t * max(W-B,0)` and `Q = H_net - K`.

For P > 0:

```text
W_required = Q                       if 0 <= Q <= P
W_required = (Q - t*P) / (1-t)       if Q > P
r_star = (W_required/P)^(1/h) - 1
```

If Q < 0, cash alone exceeds Hold: no equality exists for r >= -100%.
If Q = 0, r_star = -100%.
If P = 0, report break-even unavailable; still show the all-cash comparison.
If there are no sales, report "Nothing to compare" instead of fabricated zeros.

Substitute r_star back into `net(P*(1+r_star)^h,P)+K` and verify H_net.
Cumulative threshold is `(1+r_star)^h-1`.
Equivalent original basket return is `r_hold=(H_gross/S)^(1/h)-1`.
Optional extra annual return is `100*(r_star-r_hold)` percentage points.
It can be negative; do not floor it at zero.

The actual replacement basket's equivalent annual return is
`(R_gross/P)^(1/h)-1` for P > 0. Never average annual rates to obtain a multi-year
basket CAGR. Keep the break-even result independent of the user's forecast for
the replacement assets; changing those forecasts changes the advantage, not the hurdle.

## Worked examples and acceptance fixtures

### 1. One sell and one buy

Inputs: sell $20,000; net gain $10,000; federal 20%; state/local 10%;
5 years; Hold return 10%; replacement return 13%; invest all proceeds.

- Inferred basis: $10,000.
- Federal tax now: $2,000; state/local: $1,000; total: $3,000.
- New investment and new basis: $17,000; cash: $0.
- Hold gross: $32,210.20; final taxable gain: $22,210.20.
- Hold final taxes: $4,442.04 federal + $2,221.02 state/local.
- Hold net: $25,547.14.
- Switch gross: $31,321.40 (rounded); final taxable gain: $14,321.40 (rounded).
- Switch net: $27,024.98 (rounded); switching advantage: $1,477.84 (rounded).
- Break-even is about 11.4338% annually; extra return about 1.4338 percentage points.
  Use unrounded arithmetic for verification.

### 2. Multiple sells and loss netting

Sell A for $50,000 with $30,000 gain and B for $20,000 with -$10,000 loss.
Federal 15%, state/local 5%: batch gain $20,000; federal tax $3,000;
state/local $1,000; available cash $66,000.
Buy C for 60% ($39,600), D for $20,000, leave $6,400 cash.
Conservation: $39,600 + $20,000 + $6,400 + $4,000 = $70,000.
Inferred sold bases are $20,000 and $30,000.

### Other required behavior

- With zero rates and identical assets/returns/allocation, Hold equals Switch.
- Selling and rebuying the same appreciated asset can underperform holding due
  to immediate tax; do not force equality when taxes are positive.
- Net losses yield zero immediate tax and no assumed refund/carryforward.
- Negative returns including -100% are handled without negative asset values.
- A changed dollar buy stays fixed when taxes change; a percentage buy changes.
- An all-cash proposal is valid; its investment break-even is unavailable.
- Too much buying, nonfinite values, and gain greater than sale amount are invalid.
- A changed sale amount with unconfirmed gain cannot yield finalized results.
- Editing f or s updates the corresponding component and total consistently.

## Numerical policy

Require finite values, S_i > 0, G_i <= S_i, B_i >= 0; loss magnitude is not bounded
by sale proceeds. Require f >= 0, s >= 0, f+s < 1, h > 1, r >= -1,
D_j >= 0 and 0 <= w_j <= 1. Validate total funding after resolving mixed units.
Missing inputs are not zero. Zero tax must be explicitly entered.

Use full internal precision and documented absolute/relative tolerances.
Suggested money residual tolerance: max($0.000001, 1e-10 * value scale).
Do not silently fund an over-allocation; any tolerance adjustment must be auditable.
Choose a consistent nearest-cent display rule and compute deltas before rounding.
Reject numerical overflow and return explicit unavailable statuses for break-even
edge cases. Results expose model version, inputs, inferred bases, component taxes,
funding, growth, and assumptions.

## Advanced boundary

Detailed tax brackets, NIIT applicability, state/local rules, carryforwards,
tax lots, short-term treatment, and distributions require a different tax/cash-flow
policy. Basic just sums the two entered rates; it does not infer deductions,
surcharges, or applicability from personal facts. No year-specific statutory rates
are embedded here.

Later simulations must pair both strategies under the same joint market outcome
and preserve correlations. Outcome percentiles differ from sampling confidence
intervals; subjective scenarios alone establish neither. Simulation and probability
outputs are deferred entirely from Basic.
