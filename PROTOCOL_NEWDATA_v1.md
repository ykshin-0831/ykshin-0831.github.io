# AI Guard: new-data test of a breach-clustering hypothesis (2010–2015)

**Version:** 1.0, 2026-11-17 (Asia/Seoul)

**Status:** Written before any 2007–2015 price was downloaded.
- The hypothesis was designed on realized dates 2018-04-20 to 2022-12-29 only.
- No statistic of this hypothesis was computed on 2023–2026 data.
- The protocol counts as preregistered only if it is committed to a public repository before the notebook is run in REAL mode. The notebook records the commit SHA.

## 1. Question

Why do the 99% VaR breaches of the current AI Guard engine (filtered historical simulation, FHS) cluster in time?

Earlier results with the same 300 portfolios:
- FHS breach clustering (Christoffersen, Monte Carlo null with 1,000,000 draws):
  - 7.0% of portfolios in 2018–22
  - 48.3% in 2023–26
- A pooled LightGBM with market-stress features reduced clustering (46% to 17%). It raised quantile loss by 3.0% and the Kupiec rejection share (7% to 12%).

## 2. Hypothesis: stock-specific shock persistence

A breach on a day when the market did not fall sharply raises the chance of a breach on the next day. A breach on a day of a market-wide drop does not.

### Definitions

- **Breach.** For portfolio p and trading day t+1, the breach indicator is b_{p,t+1} = 1[r_{p,t+1} < q_{p,t}].
  - r is the portfolio log return.
  - q_{p,t} is the FHS 1% quantile made at the close of day t: EWMA volatility (λ = 0.94, seeded with the sample variance of the first 60 returns) times the 1% quantile of the last 500 standardized returns r_k/σ_{k−1}.
- **Market shock day.** Day t is a market shock day, S_t = 1, if z_{m,t} = r_{m,t} / σ_{m,t−1} < −2.
  - r_m is the KOSPI 200 log return.
  - σ_m is its EWMA volatility (λ = 0.94, same seeding).
- **Pairs.** A pair is (p, t), where t and t+1 are consecutive trading days and both lie in the evaluation window. Each pair falls into one of three cells:
  - **I:** b_{p,t} = 1 and S_t = 0 (breach without a market shock)
  - **M:** b_{p,t} = 1 and S_t = 1 (breach on a market shock day)
  - **N:** b_{p,t} = 0 and S_t = 0 (no breach and no shock)
- **Statistics.** P_X is the share of pairs in cell X with b_{p,t+1} = 1. Then R = P_I / P_N and D = P_I − P_M.

### Design-period values

300 original portfolios, 1,154 as-of days, 39 market shock days.

| Quantity | Value | 95% date-block interval |
|---|---|---|
| P_I (next-day breach after a breach without a market shock) | 3.14% (2,099 pairs) | |
| P_M (after a breach on a market shock day) | 0.50% (2,220 pairs) | |
| P_N (after no breach, no shock) | 1.18% (332,401 pairs) | |
| R = P_I / P_N | 2.66 | 1.09 to 4.45 |
| D = P_I − P_M | 2.65 pp | 0.64 to 5.29 pp |

Supporting patterns in the design period:
- **Gradient.** The next-day breach rate after an own breach rises with the market's move on the breach day:

  | Market z on the breach day | Next-day breach rate | Own breaches |
  |---|---|---|
  | ≤ −2 | 0.50% | 2,220 |
  | −2 to −1 | 2.25% | 1,200 |
  | −1 to 0 | 3.33% | 630 |
  | > 0 | 6.69% | 269 |

- **Portfolio size.** R falls as the number of holdings rises: 3.31 (3 stocks), 2.34 (6), 1.97 (13).
- **COVID window.** Excluding as-of dates 2020-02-20 to 2020-04-30, P_I is 2.75% vs P_N 1.08%.
- **By year.** P_I > P_N in 2018, 2019, 2020 and 2021, but not in 2022 (0.85% vs 1.37%).

### Complete list of analyses run on the design period

This list makes the number of looks visible.

1. Overall P(b_{t+1} | b_t).
2. The four cells at shock thresholds −1.5, −2, −2.5 and −3.
3. Date-block bootstrap of the cell ratios at −2.
4. Concentration of next-day breaches by date, and exclusion of the COVID window.
5. The cells by calendar year.
6. Next-day breach rate by market-z bin.
7. Breach rates 1 to 5 days after a breach.
   - Not stable beyond one day, so only the next day is used.
8. The cells by portfolio size.
9. Idiosyncratic-breach persistence by tercile of the breach's own z and by tercile of cross-sectional dispersion over σ.
   - No pattern, so not used.
10. The uplift multiplier grid in section 4.

**Rejected candidate.** "A market shock raises next-day breach risk for portfolios that did not breach."
- In the full design period: 3.63% vs 1.18%.
- This came almost entirely from two dates (2020-03-09 and 2020-03-18). Outside the COVID window it was 1.17% vs 1.08%.
- It is therefore not tested.

## 3. New data

### Windows

- **Evaluation window:** realized dates from 2010-01-04 to the last KRX trading day of 2015.
- **Download window:** from 2007-06-01, which leaves at least 560 trading days of warm-up.
- **Why this window:**
  - No AI Guard analysis has evaluated forecasts in it.
  - The earliest evaluated forecasts were KODEX 200 from 2016-04-15 and portfolios from 2018-04-20.
  - The 2016–2018 returns were used only as warm-up inputs.

### Market index and calendar

- **KOSPI 200 source:** FinanceDataReader `KS200`. If that fails, pykrx index `1028`, then a user file of KRX closes.
- **Trading calendar:** the dates on which KOSPI 200 has a close.
- **Calendar check:** every full calendar year from 2008 to 2015 must have between 240 and 255 trading days, otherwise the run stops.
- **No market index means no test.** The run stops; KODEX 200 is not substituted.

### Stocks and price sources

- **Candidate stocks:** the 33 tickers of the frozen file, in its column order.
- **Primary price source:** Yahoo Finance adjusted close (`yfinance`, `auto_adjust=True`), the same source as the frozen file.
- **Overlap check for each ticker:**
  - Compare its downloaded log returns on the frozen file's dates from 2016-01-06 to 2018-04-19 with the frozen file.
  - The source passes if at least 97% of those dates match within 1e-4. A wrong ticker, a date shift or an unadjusted split gives far lower agreement. A source without dividend adjustment differs only on a few ex-dividend days per year and passes; the source of each ticker is recorded.
  - If Yahoo is empty or fails, FinanceDataReader is tried with the same check.

### Inclusion rules

A ticker is excluded at the first rule it fails. Every exclusion and its reason are recorded.

1. A source passes the overlap check.
2. The ticker has a price on or before 2007-06-15.
3. On the calendar from 2007-06-01 to the end of 2015:
   - at most 1% of days are missing, and
   - no run of missing days is longer than 3.

   Missing days are counted from the ticker's first price and take the previous close, which gives a zero return. The common return series starts at the latest first price among the included tickers, which is no later than 2007-06-15.
4. No daily simple return lies beyond the KRX price limit by more than 0.5 pp. The limit is ±15% before 2015-06-15 and ±30% from then. A larger move means a data error or an unadjusted corporate event.
5. No run of 5 or more identical consecutive closes.

At least 13 tickers must pass, otherwise the run stops.

### Portfolios

- 300 new portfolios built with the original procedure from the included tickers, in the frozen column order:
  - generator `numpy.random.default_rng(7)`
  - for each size 3, 6 and 13: 100 calls of `rng.choice(U, n, replace=False)`, then one call `rng.dirichlet(0.5·1, size=100)`
- The notebook checks that this generator reproduces the frozen 300 portfolios when it is given the original 33 tickers.
- Portfolio return is the weighted sum of constituent log returns, the earlier convention.

## 4. Models

- **Primary: FHS λ = 0.94**, as defined above. This is the current engine.
- **Secondary models:**
  - **FHS λ = 0.85:** the clustering-study candidate.
  - **FHS-U:** FHS with a post-breach uplift.
    - If the FHS breach indicator b_{p,t} = 1 and S_t = 0, the quantile for t+1 is multiplied by k = 1.45. Otherwise it is unchanged.
    - The trigger is the unadjusted FHS breach, known at the close of t.
    - k was chosen on 2018–22 as the value in {1.00, 1.05, …, 2.00} with the lowest pooled pinball loss: −0.18% vs FHS, with 0.61% of rows flagged.
  - **LightGBM BASE and STRESS:** trained once on the original 300 portfolios.
    - Training rows: realized dates 2018-04-20 to 2022-12-29.
    - Frozen settings: depth 2, learning rate 0.03, other settings as in protocol v1.2 of the pooled study.
    - They are not refitted or tuned on the new data. Features are computed on the new portfolios the same way.
  - **FHS at 97.5%:** α = 0.025, same volatility and window.

## 5. Primary test and decision

- **Data:** FHS λ = 0.94, threshold −2, the new 300 portfolios, the 2010–2015 evaluation window.
- **Bootstrap:**
  - circular blocks of 20 consecutive as-of dates
  - all portfolios kept together on each date
  - 4,999 replicates, seed 20261117
  - percentile intervals
  - a replicate in which a needed cell is empty counts as a failure of that test
- **H1 passes** if the 2.5th percentile of R across replicates exceeds 1.
- **H2 passes** if the 2.5th percentile of D across replicates exceeds 0.
- **Verdict:**
  - "supported" if H1 and H2 both pass
  - "partly supported" if only H1 passes
  - "not supported" otherwise

The two tests form one claim (an intersection–union rule), so no multiplicity correction is applied. No other number changes the verdict.

## 6. Secondary analyses

These are reported with the predictions written here, but none of them changes the verdict. Per-portfolio tests use the following methods.
- **Christoffersen p-value:** (1 + #{null ≥ LR}) / (N + 1), with a null of 200,000 independent Bernoulli(α) sequences of the window's length. Seeds are 99 (α = 1%) and 98 (α = 2.5%). Rejection at p < 0.05.
- **Kupiec:** χ²(1), p < 0.05.

The analyses:

1. **Gradient.** Next-day breach rate after an own breach, by the market z bins of section 2. Prediction: it rises from the lowest bin to the highest.
2. **Size.** R for 3-, 6- and 13-stock portfolios. Prediction: R for 3 stocks is above R for 13 stocks.
3. **Threshold sensitivity.** R and D at shock thresholds −1.5 and −2.5.
4. **Remedy (FHS-U vs FHS).** Report:
   - Christoffersen rejection share
   - Kupiec rejection share
   - breach rate
   - mean VaR
   - pooled pinball difference, with a 95% interval from 20-day date blocks (1,999 replicates, seed 11)
   - P(breach at t+1 | cell I)

   Predictions: lower clustering share than FHS, a Kupiec share no more than 2 pp higher, and pinball no worse.
5. **Replication of earlier results.**
   - For FHS λ = 0.94, FHS λ = 0.85, LGBM BASE and LGBM STRESS, report:
     - Christoffersen and Kupiec rejection shares
     - breach rate
     - mean VaR
     - pinball loss
   - Also report STRESS − BASE pinball with an interval.
   - Predictions from 2023–26:
     - FHS λ = 0.85 clusters less than λ = 0.94
     - STRESS clusters less than BASE
     - STRESS pinball is above BASE
6. **FHS 97.5%.** Breach rate with an interval, and the Kupiec and Christoffersen rejection shares.
7. **The hypothesis on 2023–26** with the original 300 portfolios.
   - Same rules and seeds.
   - This period was used for other analyses but not for this hypothesis, so it is reported as a second, weaker replication.
8. **Breach rate with a 95% interval for every model.**
   - Daily mean over portfolios, 20-day blocks, 1,999 replicates, seed 11.

## 7. Deviations

- If any data rule stops the run, the stop and its reason are reported.
- Any change after a REAL download becomes version 1.1 with its reasons. Both versions' results are reported.
- Results are reported whatever they show.

## 8. Known caveats

- **Survivorship.** The candidates are today's large caps, so stocks that later failed are missing.
- **Smaller universe and new portfolios.** The new data has fewer stocks, and the portfolios are new random draws, not the original 300.
- **Different market.** 2010–2015 differs from 2018–2026: a different price limit before mid-2015 and a range-bound market.
- **Power.** In the design period the lower bound of R was only 1.09. A true effect of that size can fail to clear the bar on about 1,490 new days, so "not supported" is weak evidence against the hypothesis, while "supported" is the informative outcome.
- **Dependence.** Portfolios share stocks and dates. Pooled rates can rest on a few episodes. The bootstrap resamples dates to account for this, but the evidence is still a handful of stress events.

## 9. Reproducibility checks in the notebook

- The design-period values in section 2 are recomputed from the frozen file. A mismatch above rounding stops the run.
- The frozen LightGBM predictions on 2023–26 are compared with the pooled run's checksum: mean q −0.04841481085 (BASE), −0.04755177431 (STRESS). A mismatch gives a warning.
- Two kinds of look-ahead check:
  - Changing all returns after a cutoff inside the evaluation window must leave every forecast made at or before the cutoff unchanged.
  - Changing a portfolio's return on day t+1 must not change its cell at t.

## Revision history

- v1.0 (2026-11-17): first version, before any 2007–2015 download.
