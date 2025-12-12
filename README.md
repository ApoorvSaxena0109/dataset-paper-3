# Social Media-Driven Stock Manipulation and Tail Risk

A comprehensive research methodology and implementation for studying pump-and-dump episodes in small-cap US equities using freely available, web-scrapeable data.

## Research Questions

1. Can joint price-volume-social media anomalies identify pump-and-dump-like episodes in small-cap US equities?
2. What is the magnitude and distribution of tail losses for investors entering during these episodes?
3. Do these episodes generate volatility spillovers or remain self-contained?

## Methodology Overview

This research uses **only freely available, web-scrapeable data**:
- Yahoo Finance (prices, volume, message boards)
- SEC EDGAR (filings, enforcement releases)
- Public news archives

**NOT used:** Twitter/X API, Reddit API, Bloomberg/Refinitiv/CRSP

## Repository Structure

```
notebooks/
├── 01_Universe_Construction_SEC_Scraping.ipynb   # Build ticker universe & SEC labels
├── 02_Yahoo_Finance_Market_Data.ipynb            # Collect OHLCV data & baselines
├── 03_Yahoo_Message_Board_Scraping.ipynb         # Scrape social media data
├── 04_Episode_Detection.ipynb                    # Identify joint price-volume-social events
├── 05_Feature_Engineering_Classification.ipynb   # Train pump classifier & generate PLS
└── 06_Tail_Risk_Analysis.ipynb                   # VaR, ES, spillovers, regressions
```

## Notebook Workflow

### Notebook 1: Universe Construction & SEC Enforcement
- Scrape SEC litigation releases for pump-and-dump enforcement cases
- Build ticker universe from enforcement + known meme stocks + volatile small-caps
- Create ground truth labels (confirmed manipulation)

### Notebook 2: Yahoo Finance Market Data
- Collect daily OHLCV data using yfinance (no API key required)
- Compute 60-day rolling baselines (mean, std, percentiles)
- Flag candidate price-volume spike events

### Notebook 3: Yahoo Message Board Scraping
- Scrape Yahoo Finance community discussions
- Aggregate to daily message counts per ticker
- Compute message z-scores and burst flags
- Classify promotional vs. informational content

### Notebook 4: Episode Detection
- Merge price-volume and social data
- Identify joint events: price spike + volume spike + social burst
- Cluster consecutive events into distinct episodes
- Assign SEC enforcement labels
- Run placebo tests (time shuffle)

### Notebook 5: Feature Engineering & Classification
- Engineer market features (reversals, drawdowns)
- Engineer social features (promo share, user concentration)
- Train Random Forest classifier
- Generate Pump Likelihood Scores (PLS) for each episode

### Notebook 6: Tail Risk Analysis
- Compute Value at Risk (VaR) and Expected Shortfall (ES)
- Compare high-PLS vs low-PLS portfolios
- Run regression analysis (tail loss ~ PLS + social features)
- Analyze spillover effects

## Episode Detection Criteria

An episode is flagged when ALL conditions hold:

| Condition | Threshold | Rationale |
|-----------|-----------|-----------|
| Return z-score | > 3.0 | Extreme positive return |
| Volume | > 95th percentile | Unusual trading activity |
| Social burst | z-score > 3.0 within ±1 day | Coordinated social activity |
| Price | < $10 | Penny stock filter |

## Key Outputs

- **Pump Likelihood Score (PLS)**: Continuous 0-1 measure of manipulation likelihood
- **Episode-level dataset**: Event windows with pre/post metrics
- **Tail risk metrics**: VaR(95%), VaR(99%), ES(95%), ES(99%)
- **Regression coefficients**: Explaining tail losses with social features

## Dependencies

```bash
pip install yfinance pandas numpy scipy scikit-learn statsmodels beautifulsoup4 requests selenium tqdm pyarrow matplotlib seaborn
```

All dependencies are free and open-source. No API keys required.

## Ethical Considerations

1. **ToS Compliance**: Respect robots.txt and rate limits
2. **Privacy**: Usernames hashed before analysis
3. **No Trading Advice**: Academic research only
4. **Balanced Reporting**: Acknowledge limitations

## Limitations

| Limitation | Mitigation |
|------------|------------|
| Yahoo boards have lower volume than Twitter/Reddit | Focus on relative spikes, not absolute volume |
| Cannot observe Telegram/Discord coordination | Infer from account concentration patterns |
| SEC enforcement is tip of iceberg | Use PLS as continuous proxy |
| Small labeled sample | Simple models, confidence intervals |
| No intraday data | Daily data, timing imprecision noted |

## Sample Period

January 2019 - December 2025

## Citation

If using this methodology, please cite appropriately.

## License

MIT License - See LICENSE file for details.
