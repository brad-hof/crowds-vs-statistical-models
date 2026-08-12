# The Wisdom of Crowds vs. Statistical Models
### Measuring Market Efficiency in NBA Outcome Predictions

Comparing closing-line probabilities from the Kalshi prediction market website against a classical ELO rating model across the full 2025–26 NBA regular season.


## Table of Contents
1. [Overview](#overview)
2. [Process](#process)
4. [Limitations](#limitations)
5. [Improvements](#improvements)
6. [Tech Stack](#tech-stack)
7. [Contributions](#contributions)
8. [Disclaimer](#disclaimer)
9. [References](#references)


## Overview
Prediction markets aggregate dispersed public and private information into prices that function as real-time probabilistic forecasts. This project tests a core claim of market efficiency: **do closing prices on Kalshi fully incorporate the statistical signals contained in team performance data, or do systematic biases remain that a transparent statistical model can exploit?**

We paired official results for every 2025–26 NBA regular-season game with the corresponding Kalshi binary-contract closing prices, implemented a sequential ELO baseline with home-court adjustment, and evaluated both forecasting systems with proper scoring rules and calibration diagnostics.

**Headline result:** The prediction market was meaningfully better calibrated than the ELO model (Brier score **0.194** vs **0.213** on 1,229 games). When the two disagreed, the market was more often correct. A simple performance-based model did not uncover reliable edges.


## Process
1. **Data acquisition**  
   - Loaded the complete 2025–26 regular-season schedule and final scores from Basketball-Reference.  
   - Queried the Kalshi Trades API for the last trade price immediately before tip-off on each game’s “home team wins” contract. Because the public API retains only ~61 days of history, the season was split into two retrieval windows and later concatenated.

2. **ETL & alignment**  
   - Standardized team names to Kalshi ticker codes, constructed consistent GameIDs and timestamps, derived a winner label, and merged market prices with results.  
   - After cleaning, 1,229 games remained for analysis (a handful of early-season or data-gap rows were dropped).

3. **Statistical baseline**  
   - Implemented a classic sequential ELO system: all teams start at 1500, K-factor = 20, fixed home-court bonus of +75 ELO points (≈60.7 % implied win probability for equal teams).  
   - Ratings and pre-game win probabilities were updated game-by-game in chronological order.

4. **Evaluation**  
   - Primary metric: Brier score (mean squared error of probabilistic forecasts).  
   - Secondary diagnostics: reliability diagrams (calibration curves) and an edge-distribution histogram (model probability − market probability).


## Limitations
- Our data was from a single season only so any conclusions may not generalize across different competitive eras or injury climates.
- Closing price taken from the single nearest trade rather than a volume-weighted or order-book snapshot.
- Deliberately simple ELO baseline; richer feature sets were left for future work.


## Improvements
- Expand to 5–10 seasons (and include postseason) to test stability of calibration and edge patterns.
- Richer market data: ingest the full order book or multiple trades in a short window before tip-off for more robust closing prices.
- Stronger statistical / ML baselines: replace the pure ELO with models that include rest days, travel distance, injury reports, pace, and advanced box-score features (logistic regression, gradient boosting, or simple neural nets).


## Tech Stack
| Category          | Tools                                      |
|-------------------|--------------------------------------------|
| Language          | Python 3.13                                |
| Data & Analysis   | pandas, numpy                              |
| Visualization     | matplotlib                                 |
| API Access        | requests (Kalshi Trading API)              |
| Environment       | Jupyter Notebook                           |
| Data Sources      | Basketball-Reference, Kalshi               |


## Contributions
- Bradley Hofmeister: NBA data cleaning, Kalshi API integration, ETL pipeline, primary author of Part 1 of notebook
- Dominick Devarti: ELO implementation, Brier & calibration analysis, visualization, primary author of Part 2 of notebook

## Disclaimer
This repository is an educational research project focused on forecasting calibration and market-efficiency questions. It is not betting advice, financial advice, or a recommendation to trade on any prediction market. Prediction-market contracts involve risk of capital loss. Always treat market prices as probabilistic estimates, never as certainties.

## References
- Basketball-Reference – 2025–26 NBA Schedule and Results
- Kalshi Trading API Documentation
- Classic ELO rating system (Elo, 1978) and subsequent sports adaptations
