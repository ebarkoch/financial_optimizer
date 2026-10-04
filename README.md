# Portfolio Optimizer

A planned app for comparing holding current investments with selling and
reallocating them, accounting for capital-gains taxes and uncertain future returns.
Calculations will be transparent and independent of the interface.

**Status:** documentation draft for review. No application, installation steps,
or runnable commands exist yet.

## Start here

1. Read [Current status and session handoff](docs/STATUS.md) to see where work stands.
2. Read [Product specification](docs/PRODUCT_SPEC.md) for scope and the intended workflow.
3. Read [Financial model](docs/FINANCIAL_MODEL.md) for assumptions, formulas, and worked examples.
4. Review [Decisions and open questions](docs/DECISIONS.md) before authorizing implementation.

[AGENTS.md](AGENTS.md) gives future development agents the project rules.
When switching sessions, give the next session access to the latest repository
files and ask it to read these instructions and the status document. Update status
and decisions before handing off; commit and push updates when authorized.

The proposed first version handles multiple holdings, partial sales, multiple
replacement investments, long-term gains assumptions, and bear/base/bull comparisons.
It reports after-tax outcomes and an equivalent break-even replacement return.
Optional user-assigned scenario weights give a conditional probability of
outperforming; Monte Carlo and confidence intervals are planned for later.

Review the proposed tax context, final-liquidation convention, price-only returns,
and loss treatment first. After approval, the next development step is to choose
the stack and build a tested calculation engine, followed by a simple interface.

Repository: [ebarkoch/financial_optimizer](https://github.com/ebarkoch/financial_optimizer).
The local folder was empty and was not a Git checkout at initial inspection on
October 4, 2026. It is now connected to the repository; this initial snapshot
contains the project documents and a macOS `.gitignore`.
The repository is public: use synthetic examples and keep real financial data out
of source control.
