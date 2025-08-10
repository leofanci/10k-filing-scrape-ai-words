### README — Returns ~ Word Counts on 10-K Filings

- **Author**: Fancello Leonardo Livio (leonardofanc@g.ucla.edu)
- **Goal**: Scrape SEC 10‑K filings for selected tickers (2019–2023), count occurrences of AI/ESG terms, merge with prices, compute 6‑month returns, and run OLS/panel regressions with controls.

### Data
- **Inputs**
  - `tickers.csv`: one ticker per line
  - `prices.csv`: columns include `ticker`, `date`, `prc` (daily prices)
- **Intermediates**
  - `url_data.csv`: URLs for 10‑K text files (auto-created)
- **Outputs**
  - `final.csv`: merged dataset with counts, returns, and metadata

### Pipeline
1. Map tickers → CIK/name via `sec_cik_mapper.StockMapper`.
2. Fetch recent 10‑K result pages via `edgar.Company(...).get_10Ks(...)`, extract URLs, filter to 2019–2023, convert `-index.htm` to `.txt`.
3. Count terms in filings:
   - Words: `artificial intelligence`, `Artificial Intelligence`, `Artificial intelligence`, `AI`, `ESG`
   - Combined feature: `artificial_intelligence` = sum of three case variants
4. Scrape filing `date_scraped` and `form_type`; filter out `10-K/A`, fix a few missing dates manually.
5. Merge with `prices.csv`, compute 6‑month returns per filing date.
6. Save `final.csv`, visualize, and run OLS and panel (pooled and FE) regressions with industry/year controls.

### How to run
- Fresh run (full scrape; slow due to SEC rate limits):
  - Run cells top-to-bottom. The function `get_10Ks` sleeps 10s per company; expect long runtime.
- Fast rerun (skip scraping):
  - Ensure `url_data.csv` exists; start from the reload section to proceed with counting/analysis.

### Dependencies (install if needed)
```bash
pip install pandas numpy matplotlib seaborn requests statsmodels linearmodels bs4 sec-cik-mapper edgar
```

### Notes
- Respect SEC EDGAR fair-use: randomized `User-Agent` and sleep are included.
- Ticker normalization included (e.g., preferred series consolidated for `JPM`, `GS`).
- The 6‑month return uses the first available date ≥ filing date + 6 months; falls back to last available date if missing.

### Files produced
- `url_data.csv`: reproducible link list for filings.
- `final.csv`: analysis-ready dataset used by the regression and visualization sections.