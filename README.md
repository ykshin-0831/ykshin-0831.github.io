<div align="center">

# Yeong Kyeong Shin

**Statistical Modeling · Machine Learning · Financial Risk**

Lab Researcher, Artificial Intelligence & Business Model Laboratory, Kyung Hee University

[![Portfolio](https://img.shields.io/badge/Portfolio-ykshin--0831.github.io-0A3D62?style=flat-square)](https://ykshin-0831.github.io/)
[![ORCID](https://img.shields.io/badge/ORCID-0009--0008--9079--6642-A6CE39?style=flat-square&logo=orcid&logoColor=white)](https://orcid.org/0009-0008-9079-6642)
[![SSRN](https://img.shields.io/badge/SSRN-Preprint-1F4E79?style=flat-square)](https://ssrn.com/abstract=7682931)
[![Email](https://img.shields.io/badge/Email-yk__shin%40khu.ac.kr-555555?style=flat-square)](mailto:yk_shin@khu.ac.kr)

</div>

---

## About

I study whether the skill that financial risk models report holds up under fair evaluation. My work combines exact metric decompositions, simulation-based null distributions, preregistered and split-sample designs, and multiple-testing control, applied to crash early warning, portfolio risk engines, and textual analysis of corporate disclosures.

- **Education:** B.A. in International Studies, Kyung Hee University (2026)
- **Code:** developed and maintained in Google Drive (Colab) over the course of each project; the public repositories hold versions tidied and re-run from a clean state before publication.

## Research Interests

- Forecast evaluation and validation of financial risk models
- Early-warning systems for market crashes
- Textual analysis of corporate risk disclosure
- Reproducible and preregistered empirical research

## Working Papers

1. **How Much of Crash Early Warning Is Early? Separating Pre-Onset Warning from In-Progress Detection**<br>
   **Yeong Kyeong Shin** (sole author) · Under review at *Journal of Forecasting*, 2026<br>
   Decomposes ROC-AUC into pre-onset and in-progress components and shows that most measured crash-warning skill reflects detection of declines already under way.<br>
   [Project page](https://ykshin-0831.github.io/crash-early-warning.html) · [Preprint (SSRN)](https://ssrn.com/abstract=7682931) · [Code](https://github.com/ykshin-0831/crash-ews-preonset)

2. **Risk Disclosure Quality and Stock Liquidity: Evidence from Embedding-Based Textual Analysis of Korean Unstructured Corporate Filings**<br>
   **Yeong Kyeong Shin**, Jangho Gil · Under external peer review at *Asia-Pacific Journal of Accounting & Economics*, 2026<br>
   Measures the breadth, renewal, and firm specificity of risk disclosures in 32,314 Korean annual reports using sentence embeddings, and relates them to stock liquidity and distress outcomes.<br>
   [Project page](https://ykshin-0831.github.io/risk-disclosure-nlp.html)

3. **MD&A Narrative Composition and Corporate Distress: Boilerplate Displacement of Firm-Specific Risk Disclosure in Korean Listed Firms**<br>
   **Yeong Kyeong Shin**, Jangho Gil · Manuscript in preparation

## Research Projects

### AI Guard — Validating a Portfolio Risk Engine
*AI & Business Model Laboratory, Kyung Hee University · Team Lead (6 members) · Sep 2025 – Present*

Statistical validation of a prototype engine that reports one-day 99% VaR and CVaR for Korean equity portfolios. I led the team building the prototype, designed and ran the validation, independently re-implemented the estimators and backtests, and led the breach-clustering study.

- Compared Gaussian, Student-t, historical simulation, and filtered historical simulation (FHS) models on 300 random portfolios using Kupiec, Christoffersen, and dynamic quantile tests.
- FHS was selected on 2018–22 data under a rule fixed in advance (Kupiec rejections: 4% of portfolios vs. 88% for the original Gaussian model). On 2023–26, which an earlier whole-period backtest had already covered, it was rejected for 0% (Gaussian: 71%).
- Limitation: breaches still cluster. In 2023–26 the Christoffersen test flagged 48% of portfolios, almost entirely due to two double-crash episodes (August 2024 and March 2026); neither faster volatility nor market-stress features removed the clustering without losing accuracy.
  
[Project page](https://ykshin-0831.github.io/ai-guard.html)

### Bitcoin Drawdown Early Warning
*FBA Quant Research Group · 3 members · Dec 2025 – Present*

Tested whether a Fourier-based instability signal from one-minute Binance data warns of sustained 4% Bitcoin drawdowns better than volatility alone. I proposed the core signal, rebuilt the system as a tested package, and ran its out-of-sample validation.

- Out of sample, the frozen spectral rule caught 17 of 66 episodes versus 14 for an equal-budget volatility rule (McNemar *p* = 0.58).
- The signal behaves mainly as a volatility measure; this finding motivated the sole-authored paper on pre-onset warning.

[Project page](https://ykshin-0831.github.io/btc-early-warning.html)

## Methods & Tools

| Area | Methods and tools |
| --- | --- |
| Programming | Python (pandas, NumPy, scikit-learn, pytest), R, Stata, MATLAB, SQL, Git |
| Statistics & Econometrics | Panel fixed-effects models, HAR volatility forecasting, QLIKE and Clark–West tests, bootstrap / permutation / surrogate tests, Benjamini–Hochberg FDR, OSF preregistration |
| Machine Learning & NLP | XGBoost, logistic regression, sentence embeddings (KoSimCSE, BGE-M3), Korean text processing (KSS, OKT) |
| Quantitative Finance | VaR / CVaR backtesting, CAViaR, factor models, Monte Carlo simulation, derivatives pricing |
| Data | OpenDART, Korea Exchange, Binance minute data, Bloomberg |

## About This Repository

This repository contains the source (HTML, CSS, and static assets) of my portfolio website, deployed with GitHub Pages. Research code is maintained in separate repositories where publicly available (see **Code** under About).

<sub>Last updated: November 2026</sub>
