---
title: 'Improving the Odds: A Heuristic Method for Selecting Crypto Breakouts'
date: 2025-8-15'
permalink: /posts/2025/8/15/chapter-2-improving-odds/
tags:
   - machine learning
   - cryptocurrency
   - algorithmic trading
   - quantitative finance
   - heuristic
   - selection model
   - pattern recognition
   - ensemble learning
   - stochastic process
   - breakout trading
   - xgboost
---

> **Abstract:** This document details a proprietary algorithm designed to predict significant upward price movements, or "breakouts," in crypto-assets. The methodology is built on a multi-stage process that transforms raw market data into a powerful selection heuristic. The algorithm leverages historical price action, market sentiment, and a novel, stability-focused machine learning approach to identify and leverage a more favorable, informed subset of the crypto-asset universe for potential trading opportunities.

## Introduction

In the volatile world of crypto-assets, identifying true breakout opportunities is a significant challenge. This document outlines a unique algorithm developed to meet this challenge. The core objective is to move beyond simple price prediction and instead identify high-probability breakout events, defined by both the magnitude of the price increase and the quality of the rally.

## Methodology and Rationale

A critical aspect of this algorithm is its novel approach to model training, which intentionally deviates from standard time-series forecasting practices. This section provides the motivation and justification for this design.

### The "Bag of Events" Hypothesis

Conventional wisdom dictates that shuffling time-series data is poor practice, as it can lead to data leakage and unreliable models. However, this algorithm deliberately reframes the problem from **time-series forecasting** to **pattern recognition**.

The core hypothesis is that the combination of features signaling a high-quality breakout is a **recurrent, time-independent pattern**. In other words, the specific signature of sentiment, volume, and price action that precedes a breakout is fundamentally the same whether it occurred last year or last week. The goal is not to predict the price on *day T+1* based on *day T*, but to classify whether the pattern on *day T* belongs to the class of "patterns that lead to a breakout."

By shuffling the data within a cross-validation fold, we force the model to learn the intrinsic properties of the breakout pattern itself, divorced from its specific place in history. This prevents the model from "cheating" by learning simple, time-based correlations and compels it to become a more generalized and robust pattern-recognition engine.

### Purpose as a Selection Heuristic

It is critical to acknowledge that predicting specific breakout events with high accuracy is exceptionally difficult. Therefore, this algorithm is not designed to be an infallible predictor. Rather, its intended purpose is to serve as a powerful heuristic, or a method of "better guessing." When faced with a large universe of potential crypto-assets to invest in on any given day, the model's signals are designed to significantly improve the selection process. The core value proposition is that using this model to filter and select assets will yield superior results over time compared to choosing them at random.

### Formalizing the Hypothesis: A Stochastic Process Framework

The central hypothesis of this work can be formally described by comparing the projected revenue streams from two distinct investment strategies. Let $V(t)$ represent the value of a portfolio at time $t$, where $t$ is a continuous variable. The evolution of $V(t)$ is a stochastic process whose behavior is governed by the underlying investment strategy.

Due to the inherently random nature of crypto-asset prices, the instantaneous return of a portfolio is a random variable. The goal is to show that our selection heuristic systematically biases the probability distribution of these returns in our favor.

**Process 1: The Baseline Strategy (Uniform Random Selection)**

This process models the revenue projection of a naive investment strategy where the portfolio is rebalanced at discrete intervals (e.g., daily). At each rebalancing point, an asset $c$ is selected from the universe $C$ with uniform probability. The portfolio's value, $V^{\text{random}}(t)$, evolves as a continuous-time stochastic process whose growth is driven by the returns of these randomly selected assets. Let $r^{\text{random}}$ be the random variable for the return over a single rebalancing period.

**Process 2: The Heuristic Strategy (Informed Selection)**

This process models the revenue projection of the strategy guided by our algorithm, also rebalanced at discrete intervals. At each rebalancing point, the algorithm identifies a subset of assets $C_H(t) \subseteq C$ that meet the selection heuristic $H$. An asset $c$ is then chosen from this informed subset. The portfolio's value, $V^{\text{heuristic}}(t)$, evolves as a different continuous-time stochastic process, driven by the returns of the algorithm's selected assets. Let $r^{\text{heuristic}}$ be the random variable for the return over a single rebalancing period under this strategy.

**The Core Assertions**

The fundamental hypothesis can be stated in two ways.

**1. Geometric Growth Rate**

The first assertion is that the heuristic strategy will generate a higher compound growth rate than the baseline strategy. This is captured by comparing the expected one-step log returns:

$$
E[\ln(1 + r^{\text{heuristic}})] > E[\ln(1 + r^{\text{random}})]
$$

This formulation relates directly to the median, or typical, long-term outcome of a compounding strategy.

**2. Arithmetic Average**

The second assertion is that the long-term expected portfolio value of the heuristic strategy will be greater than that of the baseline.

$$
\lim_{t \to \infty} E[V^{\text{heuristic}}(t)] > \lim_{t \to \infty} E[V^{\text{random}}(t)]
$$

While this assertion compares the long-term average portfolio values, it should be approached with some caution. The expected value (the arithmetic mean) of a compounding process can be heavily skewed by rare, extreme positive outcomes, which can make it a deceptive measure of a strategy's typical performance. A quantitative investigation of these hypotheses will be the subject of a future article.

### Justification Through Advanced Concepts

This methodology is supported by established, advanced concepts in machine learning and financial econometrics:

1. **Robustness Through Extreme Ensembling**: The "Grand Tournament" is a massive application of ensemble learning. By training thousands of models on different shuffles of the data and—most importantly—selecting for **minimum variance**, the algorithm actively filters for models that are robust to the "noise" of a shuffled context. Models that rely on temporal sequence will fail this stress test by exhibiting high performance variance. This is a sophisticated method for managing the bias-variance tradeoff, prioritizing stability and reliability above all else.

2. **Addressing Non-Stationarity**: Financial markets are notoriously non-stationary, meaning their statistical properties change over time. A model that learns a sequence in one market regime (e.g., a bull market) will likely fail in another. The **Market Regime Clustering** (Stage 3) is a crucial prerequisite that groups the data into more homogenous, "pseudo-stationary" periods. Shuffling events *within* a single market regime is therefore more justifiable and allows the algorithm to train an expert model for each distinct market type.

3. **Academic Alignment**: While the exact method is proprietary, its principles align with the work of leading researchers like **Dr. Marcos López de Prado**, who has been highly critical of applying standard machine learning techniques naively to finance. The focus on robust cross-validation, feature importance, and preventing data leakage is central to modern financial machine learning, and this algorithm represents a powerful, heuristic approach to achieving those goals.

## Algorithmic Framework and Workflow

The algorithm operates through a sequential, multi-stage pipeline. Each stage is designed to progressively refine the data and build a foundation for the final predictive model.

### Stage 1: Data Structuring and Feature Foundation

The foundational step of the algorithm is to structure raw time-series data into a format rich with predictive potential. The motivation here is to create a comprehensive snapshot of the market at each time step.

1. **Candlestick Aggregation**: To reduce noise and summarize price action, raw data is first converted into a candlestick representation (Open, High, Low, Close) at a daily interval. This provides a clear, standardized view of market activity.

2. **Contextual Data Integration**: Each candlestick is enriched with crucial contextual data:

   * **Volume**: The total trading volume, indicating the conviction behind price movements.

   * **Fear & Greed Index**: The mode of the market sentiment index during the period, offering a direct measure of investor psychology.

3. **Future Peak Identification (`log_returns`)**: To train a predictive model, we must first define what we are trying to predict. The algorithm looks forward from each candlestick over a set window (e.g., 4 days) to identify the absolute highest price reached. The logarithmic return between the current candle's closing price and this future peak is calculated. This value, `log_returns`, quantifies the magnitude of the best possible future rally.

4. **Breakout Quality Analysis (`log_returns_min_prior_to_max`)**: Not all rallies are created equal. A key innovation of this algorithm is its ability to assess the *quality* of a breakout. It's crucial to distinguish a clean, powerful rally from a volatile one that experienced a sharp dip first. To do this, the algorithm calculates the most significant dip the price took *on its way* to reaching the future peak. The log return between the current price and this low point is stored. A value close to zero indicates a high-quality, low-volatility breakout, which is the primary target for prediction.

### Stage 2: Advanced Feature Engineering

With a solid data foundation, the next stage is to engineer features that capture historical trends and define the precise prediction target.

1. **Sentiment Trajectory**: To understand the momentum of market psychology, the algorithm generates features based on the historical trajectory of the Fear & GreG Index. By creating rolling snapshots of sentiment over various lookback periods, the model gains insight into whether the market is moving towards greed or fear.

2. **Defining the Prediction Target (`y`)**: The ultimate goal is to predict a very specific event: a strong, high-quality breakout. The target variable, `y`, is therefore defined with two conditions:

   * The rally's magnitude (`log_returns`) must be greater than a significant threshold (e.g., a 15% gain, or `np.log(1.15)`).

   * The rally's quality (`log_returns_min_prior_to_max`) must be high, meaning the price did not dip more than a small amount (e.g., 10%, or `np.log(0.9)`) on its way to the peak.

This dual condition ensures the algorithm focuses exclusively on identifying the most desirable breakout opportunities.

### Stage 3: Market Regime Clustering

A core hypothesis of this methodology is that a "one-size-fits-all" model is suboptimal for financial markets. Different market conditions (e.g., a bull market, a a bear market, sideways consolidation) have unique dynamics. To address this, the algorithm employs K-Means clustering to automatically segment the historical data into distinct market regimes. This allows for the training of specialized models, each an expert in a particular type of market behavior, leading to a more nuanced and effective overall system.

### Stage 4: Stability-Focused Model Generation

The final stage is the generation of the predictive models. The primary motivation in this stage is to build a system that is not just accurate, but exceptionally **reliable and stable**. A model that performs well on historical data but is erratic on new data is useless. Therefore, the algorithm employs a rigorous, tournament-style selection process.

#### The Search for Optimal Models (`k_splits`)

1. **The Grand Tournament**: The algorithm initiates a massive competition, running 5,000 independent training sessions in parallel. This large-scale simulation is designed to explore a vast solution space to find the most robust models.

2. **The Training Regimen**: Each of the 5,000 sessions uses **9-Fold Stratified Cross-Validation**. In this technique, the data is split into 9 representative parts. The model is trained on 8 parts and tested on 1, in 9 successive rounds. This ensures the model is evaluated against all parts of the data, preventing it from overfitting to a specific subset.

3. **Identifying the Champions via Stability**: After the tournament, the winner is not chosen based on the single highest accuracy score. Instead, the algorithm seeks **consistency**. For each of the 5,000 sessions, it calculates the standard deviation of the model's performance across its 9 validation folds. A low standard deviation proves that the model is stable and performs reliably on different data subsets. The algorithm selects the group of models that exhibited the absolute lowest standard deviation.

4. **Assembling the Final Team**: The individual models from these champion sessions are collected. This final, elite group (`k_splits`) represents the most reliable and consistent predictors discovered during the entire process.

## Final Output and Future Work

The output of the algorithm described in this paper is a set of highly-vetted, specialized prediction models for each crypto-asset. The next logical step, which will be the focus of a subsequent article, is to deploy these models within a live trading framework to test the core hypotheses.

### Implementation of Predictors

In a live environment, the system will perform the following steps for each asset in the universe on a daily basis:

1. **Data Ingestion**: Acquire the latest market data (price, volume, sentiment).

2. **Feature Engineering**: Apply the exact same feature generation pipeline as used in training to the new data point.

3. **Regime Identification**: Use the pre-trained clustering model to determine the current market regime for the asset.

4. **Signal Generation**: Load the specific prediction model trained for that asset and that regime. The model will then process the new features and output a binary signal (0 or 1), indicating whether the conditions for a potential breakout have been met.

### The Heuristic Trading Algorithm

The signals generated by the predictors will feed into a systematic trading algorithm. The design of this algorithm will be straightforward, focusing on executing the heuristic strategy:

1. **Daily Scan**: At the start of each trading period, the algorithm will run the prediction pipeline for every asset in the universe.

2. **Portfolio Construction**: It will identify the subset of assets, $C_H(t)$, for which the model generated a positive signal.

3. **Execution**: The algorithm will then construct a portfolio based on this subset. For instance, a simple implementation would be to create an equally-weighted portfolio of all assets in $C_H(t)$, rebalancing daily.

The performance of this trading algorithm, when backtested on historical data and potentially deployed in a live market, will serve as the ultimate validation of the methodology outlined in this paper. The detailed results of these tests will be presented in our next article.