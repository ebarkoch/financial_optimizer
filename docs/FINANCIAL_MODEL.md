# Financial model

Status: proposed model v1 for review, October 4, 2026. This specifies calculations
for future implementation; no engine exists yet. All numerical examples are synthetic.

## Comparison contract and assumptions

Start both strategies with exactly the same entered portfolio. Holding retains all
positions. The proposal sells the specified fractions now, reserves the estimated
tax from proceeds, immediately invests the remainder, and retains unsold holdings.
Value both at the same horizon and estimate tax on full liquidation at that horizon.
Immediate tax is paid from the proposal's starting wealth; do not subtract it again
from its final value or fund it from an unmodeled external account.

Proposed v1 assumptions:

- One US-style taxable portfolio in USD; all sales qualify as long-term.
- A horizon `h > 1` years, and a single user-specified combined tax rate `0 <= t < 1`
  applied now and at the horizon. No bracket calculation or automatic rate lookup.
- Each entry has known nonnegative adjusted basis and positive market value.
  Partial sales use proportional basis within that entry. Different lots should be
  separate entries; proportional allocation across mixed lots is only an approximation.
- Annual rates describe price appreciation excluding distributions, after any fund
  expense drag already reflected in prices. No dividends, interest, new contributions,
  withdrawals, transaction fees, intermediate sales, or rebalancing are modeled.
- Gains and losses are netted within the proposed sale batch now, and separately
  across the fully liquidated portfolio at the horizon. Negative net gains create
  zero tax, not a refund. Loss carryforwards and ordinary-income deductions have no
  modeled value; losses unused now do not offset future taxes.
- Taxes are reserved immediately even though actual payment dates can differ.
  Rates do not change by scenario, income, jurisdiction, or holding period.
- Loss sales that involve wash-sale conditions are unsupported. The workflow must
  flag a loss sale and require confirmation that no disallowed loss is being used;
  it cannot infer substantially identical investments or outside-account trades.

The loss policy may overstate taxes when excluded losses have value. Other omissions
can change results in either direction. Display these assumptions with results.

## Inputs and identities

Use stable position IDs, asset IDs, and proposal IDs. Several position entries may
refer to one asset, but shared assets use the same scenario return in both strategies.
An existing entry and a new purchase of the same asset retain distinct cost bases.

| Symbol | Meaning |
| --- | --- |
| `V_i` | Current market value of existing entry `i` |
| `B_i` | Adjusted cost basis of that whole entry |
| `s_i` | Fraction sold now, from 0 to 1 |
| `w_j` | Share of net proceeds allocated to destination `j` |
| `r_a,k` | Annual price return for asset `a` in scenario `k` |
| `h` | Common investment horizon in years |
| `t` | Combined assumed long-term capital gains tax rate |
| `p_k` | Optional probability of joint scenario `k` |

Percentages displayed to the user are converted to decimals at the input boundary:
5% is `0.05`, not `5`. Bear/base/bull describe three joint portfolio outcomes;
all assets use the same scenario index in a comparison.

## Sell now and allocate

For each entry:

```text
sale_proceeds_i = s_i * V_i
sold_basis_i = s_i * B_i
realized_gain_i = sale_proceeds_i - sold_basis_i
retained_value_i = (1 - s_i) * V_i
retained_basis_i = (1 - s_i) * B_i
```

Net across the whole simultaneous sale batch before applying tax:

```text
S = sum(sale_proceeds_i)
G_now = sum(realized_gain_i)
T_now = t * max(G_now, 0)
A = S - T_now
new_value_j = w_j * A
new_basis_j = new_value_j
sum(w_j) = 1
```

`A` is net investable proceeds. Per-entry gains are audit details; do not independently
tax each gain and then sum, since this would ignore same-batch losses. Do not subtract
sold basis from cash proceeds: basis is a tax input, not another payment.

Conservation checks:

```text
sum(retained_value_i) + A + T_now = sum(V_i)
sum(new_value_j) = A
retained_basis_i + sold_basis_i = B_i
```

If no positions are sold, allow a holding-equivalent comparison without destinations;
break-even is unavailable because no capital was reallocated.

## Grow and liquidate at the horizon

For a starting value `x` with annual price return `r`:

```text
growth(x, r, h) = x * (1 + r)^h
```

For each scenario `k`:

```text
hold_gross_k = sum(growth(V_i, r_asset(i),k, h))
hold_basis = sum(B_i)
hold_tax_k = t * max(hold_gross_k - hold_basis, 0)
hold_net_k = hold_gross_k - hold_tax_k

proposal_gross_k = sum(growth(retained_value_i, r_asset(i),k, h))
                 + sum(growth(new_value_j, r_asset(j),k, h))
proposal_basis = sum(retained_basis_i) + A
proposal_tax_k = t * max(proposal_gross_k - proposal_basis, 0)
proposal_net_k = proposal_gross_k - proposal_tax_k

delta_k = proposal_net_k - hold_net_k
advantage_k = delta_k / hold_net_k   [only if hold_net_k > 0]
```

The original basis survives in unsold holdings; newly purchased investments receive
their actual purchase cost as basis. Taxes now and at the horizon concern distinct
realizations. Do not tax previously realized gains again by carrying sold basis
into new purchases.

For a terminal gross value `W` and basis `B`, define:

```text
net(W, B) = W - t * max(W - B, 0)
```

Because the entire portfolio is liquidated together, net terminal gains are taxed
at portfolio level. Retained holdings appear in both strategies, but they can affect
terminal loss offsets; include them in a break-even calculation too.

## Break-even replacement return

For each scenario, hold that scenario's original-asset returns, tax rate, horizon,
sales, and allocations fixed. Find the common annual price return `r_star` for the
replacement basket that makes the full proposal equal to holding after tax. This is
an equivalent basket CAGR (compound annual growth rate); it does not assert that
each replacement asset will earn the same return.

Let `C` be the gross horizon value of retained holdings and `B_P` the proposal's
total terminal basis. For `A > 0`, solve:

```text
net(C + A * (1 + r_star)^h, B_P) = hold_net_k
```

A closed-form inverse is available under the proposed flat-rate policy. Let
`H = hold_net_k`. First find the total gross proposal value required:

```text
W_required = H                         if H <= B_P
W_required = (H - t * B_P) / (1 - t)   if H > B_P
Y_required = W_required - C

r_star = (Y_required / A)^(1 / h) - 1  if Y_required >= 0
```

If `Y_required = 0`, the threshold is -100%. If `Y_required < 0`, even a total loss
of replacement capital leaves the proposal ahead: report "Already exceeds holding
at -100%; no equality in the allowed range." Do not take a root of a negative value.
If `A = 0`, report break-even as unavailable.

Report annual and cumulative thresholds, and verify equality by substituting the
calculated threshold into the full comparison. For actual differing destination
returns, the basket's equivalent annual return is:

```text
basket_return_k = (sum(w_j * (1 + r_asset(j),k)^h))^(1 / h) - 1
```

It is generally not the weighted average of annual rates. Compare this equivalent
rate with `r_star` only under the same model and scenario. An immediate tax-cost
percentage alone is not a fair break-even comparison: it ignores growth of the
holdings and the different final tax bases.

## Worked examples for review and later tests

Example 1: one holding worth $100,000 with $40,000 basis; sell it all, reserve tax
at an illustrative 20%, and buy one replacement. The horizon is five years.

| Item | Hold at 5% annually | Reallocate at 5% annually |
| --- | ---: | ---: |
| Immediate tax | $0.00 | $12,000.00 |
| Starting invested value | $100,000.00 | $88,000.00 |
| Horizon gross value | $127,628.16 | $112,312.78 |
| Horizon basis | $40,000.00 | $88,000.00 |
| Horizon tax | $17,525.63 | $4,862.56 |
| Horizon after-tax value | $110,102.53 | $107,450.22 |

The proposal finishes $2,652.30 behind. Its break-even replacement return is about
5.61271% per year, or 31.39563% cumulatively over five years. At that return, its
after-tax value equals $110,102.525 before display rounding.
The table rounds dollars to the nearest cent, with half-cent ties rounded up.
Calculate differences before rounding; subtracting displayed totals can differ by
one cent from the displayed difference.

Example 2: sell half of a $100,000 entry with $40,000 basis and all of a $20,000
entry with $30,000 basis. Proceeds are $70,000. Realized gains are $30,000 and
-$10,000, so batch net gain is $20,000 and tax at 20% is $4,000. Allocate the
$66,000 remainder 60%/40%: $39,600 and $26,400, each with the same new basis.
The retained first entry has $50,000 value and $20,000 basis. Its value plus new
investments plus reserved tax equals the original $120,000 portfolio value.

## Probability in v1

Bear/base/bull alone are not probabilities. Optional nonnegative user weights must
sum to 1; no implicit equal weights or automatic normalization. Calculate:

```text
P_scenario(outperform) = sum(p_k * indicator(delta_k > epsilon_money))
P_scenario(tie) = sum(p_k * indicator(abs(delta_k) <= epsilon_money))
P_scenario(underperform) = sum(p_k * indicator(delta_k < -epsilon_money))
weighted_delta = sum(p_k * delta_k)
```

Use a documented computational tie tolerance, initially proposed as $0.000001;
classify before rounding displayed dollars. A displayed $0.00 can therefore have
a small positive or negative underlying difference. The three probabilities sum
to 1. For example, weights 25%/50%/25% and a proposal that wins only in the bull
scenario give a 25% scenario-weighted probability. No statistical confidence
interval follows from three subjective scenario weights.

## Monte Carlo extension, deferred

Use paired trials: both strategies see the same joint market realization, and the
same asset has the same return path in both. Model correlations across assets;
independently sampling holding and proposal would distort the difference. Bear/base/
bull inputs alone do not identify a joint distribution, volatility, or correlation.

Record distributions, calibration source, time steps, correlations, trial count,
seed, tax policy, and model version. Compute taxes and terminal differences within
each trial before aggregating. Estimate probability as winning trials divided by
total trials, with the same tie rule as v1. Specify and test the sampling model
before interpreting any result.

Keep outcome percentiles (a range of modeled future values) separate from a
confidence interval for the estimated probability (finite-sample uncertainty).
A later design may use a Wilson binomial interval for the latter when its sampling
assumptions apply. Neither measures whether the return model is correct.

## Validation and numerical policy

- Reject nonfinite numbers, empty portfolios, negative basis, nonpositive initial
  entry values, sale fractions outside `[0, 1]`, and unknown IDs or missing returns.
- Require finite `h > 1`, `0 <= t < 1`, and finite annual returns `r >= -1`.
  A -100% return gives zero horizon value; more than -100% is unsupported.
- Require nonnegative destination weights totaling 1 whenever `A > 0`; reject
  unfunded purchases. Validate optional scenario weights as a complete set.
- Require bear <= base <= bull for each asset in this ordered three-scenario model;
  do not silently reorder inputs. This is a scenario-design convention, not a claim
  that every real-world asset rises or falls together.
- Proposed sum tolerance is `1e-8` for decimal weights; disclose any adjustment
  within that tolerance. Reject larger discrepancies rather than normalizing them.
- Keep full precision internally and round only presentation; proposed dollar
  display uses nearest-cent rounding with half-cent ties rounded up. Before
  implementation, choose a numeric approach, overflow checks, and documented
  absolute/relative tolerances for conservation and break-even residuals.
- Return explicit statuses for unavailable or out-of-domain break-even results.
  Never replace missing data with zero or silently change a financial assumption.
- The future engine must expose inputs, intermediate values, model version, and
  all assumptions along with totals; audit outputs must reconcile to displayed values.

## Tax references and model boundaries

The IRS describes gain/loss as sale proceeds versus adjusted basis, generally treats
holdings longer than a year as long-term, and documents capital-loss netting and
carryovers. V1 deliberately omits several of those rules. See
[IRS Topic 409: Capital gains and losses](https://www.irs.gov/taxtopics/tc409),
reviewed October 4, 2026. Do not copy that page's year-specific brackets into this
flat-rate model.

NIIT can apply depending on income; it is not an automatic surcharge for everyone.
See [IRS Topic 559: Net investment income tax](https://www.irs.gov/taxtopics/tc559),
reviewed October 4, 2026. V1 only uses the user's combined assumption and does not
compute NIIT applicability. Wash-sale and other investment-income rules require
separate treatment; see [IRS Publication 550](https://www.irs.gov/publications/p550).

These references explain the proposed US context. The model's flat rate, tax
timing, and omitted cash flows are product simplifications, not a complete
representation of a person's tax liability.
