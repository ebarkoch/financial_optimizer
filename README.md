# Portfolio Optimizer

Answer: **Would selling these investments and buying others leave me better off after taxes?**

## Current status

Updated October 10, 2026. The owner selected a simpler Basic interface and requested
this documentation update. The repository is documentation-only: no application,
selected stack, dependencies, or runnable commands exist yet. Interactive chat
mockups are design references, not an implemented or validated application.
This task updates the handoff; it does not implement or deploy the app.

## Basic version

- Sell one or more arbitrary stocks: ticker, amount to sell ($), and **Net gain/loss ($)**.
- Buy one or more stocks: amount in dollars or percentage of available after-tax cash.
- Set an investment horizon and expected annual return for each stock.
- Enter two tax assumptions: **Federal tax rate (%)** and **State/local tax rate (%)**.
- Assume long-term sales; show separate tax components and their combined effect.
- Compare immediate taxes, investable cash, break-even return, and after-tax ending values.
- Use one responsive web interface for mobile and desktop.

No full-portfolio import is required. Basic uses dollars and percentages only;
shares, quotes, manual stock prices, future price targets, imports, tax profiles,
bear/base/bull scenarios, probability simulations, and automatic optimization belong
to later stages. A ticker is an identifier, not a dependency on a market-data service.

## Start here

1. [Current status and handoff](docs/STATUS.md)
2. [Product specification](docs/PRODUCT_SPEC.md)
3. [Financial model](docs/FINANCIAL_MODEL.md)
4. [Decisions and history](docs/DECISIONS.md)

[AGENTS.md](AGENTS.md) supplies Codex working instructions. Read these files instead
of relying on another chat's memory. Keep the calculation engine separate from
the UI so Advanced features can be added later.

The financial model documents explicit simplifications, including a traded-slice
comparison, taxes reserved from proceeds, and hypothetical final liquidation.
These are calculation conventions, not a complete tax-return model.

Repository: [ebarkoch/financial_optimizer](https://github.com/ebarkoch/financial_optimizer).
The known Mac checkout is `/Users/eyal/Desktop/financial_optimizer`; the October 10
update targets GitHub and does not establish whether that checkout is synchronized.
The repository is public: use synthetic examples, never personal financial data.
