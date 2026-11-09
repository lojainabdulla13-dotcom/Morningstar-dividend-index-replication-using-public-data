# Replicating the Morningstar UK Dividend Yield Focus Index (June 2026)

This project reconstructs the June 2026 reconstitution of the Morningstar UK Dividend Yield Focus Index (25 stocks, effective 19 June 2026, market data as of 29 May 2026) using only publicly available data. Morningstar's proprietary Distance to Default score is replaced by a naïve Merton distance-to-default proxy (Bharath and Shumway, 2008).

**Result:** the replication matches 7 of Morningstar's 25 constituents, about 30% of the index by weight. Most of the remaining gap comes from Morningstar's proprietary moat and distance-to-default screens. The write-up explains the method and results in full.

## How it works

The notebook `index_selection_final.ipynb` runs top to bottom:

1. **Universe:** point-in-time London Stock Exchange issuers as of 30 May 2026, filtered to Main Market UK companies with an eligible ordinary share line, REITs removed.
2. **Data:** trailing-12-month dividend yields, liquidity and balance-sheet debt from Yahoo Finance (via `yfinance`).
3. **Screens:** dividend paid in the last 12 months, $1m liquidity for new additions, and a distance-to-default proxy ranked within ICB industry.
4. **Selection:** ranked by yield, keeping existing constituents ranked within the top 33.
5. **Comparison:** against Morningstar's published constituents, in three runs (baseline, plus the Merton proxy, plus the fund rule).

## Reproducibility

All inputs are frozen and checked against a SHA-256 manifest before the pipeline runs, so a rerun gives identical results. Re-downloading from Yahoo Finance may shift yields slightly, because Yahoo revises historical data.

## Setup

```bash
pip install -r requirements.txt
jupyter notebook index_selection_final.ipynb
```

Then run all cells (Restart & Run All).

## Data not included

Third-party files are not redistributed here. To run the notebook, place them in `data/raw/`:

| File | Source |
| --- | --- |
| `lse_may_2026.xlsx` | London Stock Exchange issuer list, archived 30 May 2026 |
| `Instrument list.xlsx` | London Stock Exchange instrument list |
| Morningstar rulebook and reconstitution report | Morningstar Indexes website (used for rules and validation only) |

The frozen Yahoo Finance downloads are available on request.

`data/manual/` holds hand-built inputs, such as companies delisted after May 2026, and is included.

## Limitations

The main limitation is that Morningstar's moat ratings and Distance to Default scores are proprietary. Other approximations (the REIT proxy, special dividends included by Yahoo, the fund rule) are listed with their measured effects in the write-up.
