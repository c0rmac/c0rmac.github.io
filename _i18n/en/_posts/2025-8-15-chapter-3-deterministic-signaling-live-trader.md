---
title: 'Harnessing Deterministic Signals through Stochastic Sampling: A State-Based Approach to Algorithmic Trading'
date: 2025-8-15
permalink: /posts/2025/8/15/chapter-3-deterministic-signaling-live-trader/
tags:
  - algorithmic trading
  - quantitative finance
  - machine learning
  - stochastic modeling
  - cryptocurrency
  - portfolio optimization
  - risk management
  - simulation
  - backtesting
  - python
  - kelly criterion
  - sharpe ratio
  - state-based model
  - k-means clustering
---

> **Abstract:** This article details the architecture and methodology of an automated, quantitative trading system designed for cryptocurrency markets. The system's core is a machine learning strategy that leverages unsupervised clustering to identify distinct market patterns and applies cluster-specific models to generate daily trade signals. Trade execution is managed through a sophisticated two-phase lifecycle, incorporating a momentum-based entry confirmation and dynamic in-trade risk management. The software is designed with a dual-mode operational framework, allowing for both live trading and robust, parallelized backtesting on historical data. This document outlines the system's modular components, the intricacies of the trading and capital management strategies, and the implementation of its operational modes.

<style>
    /* This is the container that will allow scrolling */
    #diagram-container {
       width: 100%;
        max-height: 700px;
        /* This is the key property: it adds scrollbars ONLY if needed */
        overflow: auto; 
    }
        
    /* Add some padding around the diagram itself */
    .mermaid {
	    width: 200%;
        padding: 20px;
        box-sizing: border-box;
    }
</style>

## 1. System Architecture

The software is built on a modular, event-driven architecture that separates concerns into distinct, interacting components. The `LiveTrader` class serves as the central orchestrator, managing the overall trading lifecycle, budget, and state transitions.

The primary components are:

* **Predictor**: The "brain" of the system, responsible for analyzing market data and generating a list of nominated assets for potential trading.

* **Strategy Agents (`PreTrade`, `ActiveTrade`)**: These classes define the logic for each stage of a trade's lifecycle, from initial nomination to final sale. They implement the base `TradingAgent` interface.

* **Budget Manager**: A sophisticated class that compartmentalizes and tracks capital across various states: unallocated (`main`), nominated (`pre_trade`), active, and designated for reinvestment (`divestment`, `held_for_reinvestment`).

* **API Executor**: An abstraction layer responsible for executing buy and sell orders, whether in a simulated environment or with a live exchange API.

* **Operational Entrypoints**: The system has two primary modes of execution:

  * `main.py`: The entry point for live or paper trading, which uses a scheduler to run prediction and data-fetching tasks at set intervals.

  * `test_trader.py`: The entry point for backtesting, which simulates the entire process against historical data to evaluate strategy performance.

## 2. The Trading Strategy

The core strategy is a multi-stage process designed to identify high-probability setups and manage risk dynamically once a position is open.

### 2.1. Signal Generation (Predictor)

The signal generation process, encapsulated within the `Predictor` class, is executed once per day at 23:55, as defined in `main.py`. It leverages pre-trained machine learning models.

1. **Clustering for Market Regimes**: The system uses an unsupervised learning model (k-means) to classify the current market state into a predefined cluster. This is based on various features derived from candlestick data. The assumption is that different market patterns (e.g., high volatility, low volatility, trending) require different predictive approaches.

2. **Cluster-Specific Prediction**: For each identified cluster, a dedicated predictive model is loaded. When new data is processed, it is first assigned to a cluster, and then the corresponding model for that cluster is invoked to determine if a "buy" signal should be issued. This tailored approach allows the strategy to adapt to changing market conditions.

### 2.2. Trade Execution Logic

A signal from the `Predictor` does not trigger an immediate market buy. Instead, it initiates a two-phase execution and management process.

#### 2.2.1. Phase 1: Pre-Trade Confirmation (`PreTrade` Class)

Once an asset is nominated, it enters a "pre-trade" state for a maximum of 24 hours. This phase acts as a filter to confirm market momentum before committing capital.

* **Breakout Trigger**: A price window is established around the price at the time of nomination, defined by a `limit_price` (e.g., initial price * 1.005) and a `stop_price` (e.g., initial price * 0.92).

* **Buy Execution**: A "buy" action is only triggered if the price breaks out of this window in either direction. This ensures the asset is showing significant momentum, reducing the risk of entering a stagnant market.

* **Expiration**: If the price does not break the window within the 24-hour period, the pre-trade nomination expires, and the allocated capital is returned to a holding pool.

#### 2.2.2. Phase 2: Active Trade Management (`ActiveTrade` Class)

Upon a successful buy, the asset transitions to an "active trade" state, where a dynamic risk management strategy is applied.

* **Profit Target**: A static profit-taking limit is set upon purchase (e.g., purchase price * 1.52).

* **Dynamic Stop-Loss**: The system employs a trailing stop-loss to protect capital and lock in profits. The initial stop-loss is set at a fixed percentage below the purchase price (e.g., 98.3%). However, as the trade becomes profitable (e.g., price exceeds 110% of purchase price), the stop-loss is dynamically adjusted upwards to trail the current price, securing gains while giving the trade room to grow.

* **Capital Recycling (Divestment)**: When a trade is sold at a loss, the proceeds are not returned to the main budget. Instead, they are moved to a `divestment` pool. The system then seeks to reinvest this capital into other *active* trades that are showing early signs of profitability, effectively increasing the position size of the current cycle's winners.

### 2.3 Trade Lifecycle Flowchart

The following diagram illustrates the complete process managed by the `LiveTrader`, which is driven by two distinct, scheduled triggers.

<div id="diagram-container">
    <pre class="mermaid">
graph TD
    subgraph "Start of Day"
        A["Daily Prediction @ 23:55"] --> B{Cycle Active?}
        B -- No --> C[Start New Cycle: Process Predictions & Allocate Capital]
        B -- Yes --> D((Ignore Prediction))
    end

    subgraph "Ongoing Operations (Triggered every 5 mins)"
        C --> F
        E["Price Update @ 5 min intervals"] --> F[For each active asset...]

        subgraph "Pre-Trade Asset Logic"
            F --> G{State is Pre-Trade?}
            G -- Yes --> H{Expired?}
            H -- Yes --> I[Expire Asset: Move Capital to Holding Pool]
            H -- No --> J{Price Breakout?}
            J -- Yes --> K[BUY Asset: Transition to Active Trade]
            J -- No --> L((Hold))
        end

        subgraph "Active-Trade Asset Logic"
            G -- No --> M{State is Active Trade?}
            M -- Yes --> N{"Sell Signal? (Profit/Loss)"}
            N -- Yes --> O[SELL Asset]
            O --> P{Profit or Loss?}
            P -- Profit --> Q[Move Proceeds to Main Budget]
            P -- Loss --> R[Move Proceeds to Divestment Pool]
            N -- No --> S((Hold))
        end

        subgraph "Post-Tick Processing"
            I --> T{All Assets Closed?}
            K --> T
            L --> T
            Q --> T
            R --> T
            S --> T
            T -- No --> U[Process Divestment Pool]
            U --> V{Capital to Reinvest?}
            V -- Yes --> W[Reinvest into eligible Active Trades]
            V -- No --> X((Wait for next tick))
            W --> X
        end
    end

    subgraph "End of Cycle"
        T -- Yes --> Y[End Cycle: Calculate P/L]
        Y --> Z{Activate Dry Run for Next Cycle?}
        Z -- Yes --> AA((Reset for Dry Run Cycle))
        Z -- No --> AB((Reset for Live Cycle))
    end

    style A fill:#cde4f9,stroke:#333,stroke-width:2px
    style E fill:#d2f5d2,stroke:#333,stroke-width:2px
    style Y fill:#cde4f9,stroke:#333,stroke-width:2px
    </pre>
</div>

## 3. Capital and Risk Management

The `LiveTrader` class implements a robust framework for managing capital and mitigating risk through operational modes.

### 3.1. Budget Allocation (`Budget` Class)

The `Budget` class provides granular control over the trading capital by dividing it into distinct pools. Capital flows between these pools based on trade events:

* `main`: Liquid capital available for allocation.

* `pre_trade_allocations`: Capital reserved for nominated assets awaiting a buy trigger.

* `active_trade_allocations`: Capital deployed in live positions.

* `divestment`: Proceeds from losing trades, held for strategic reinvestment into potentially profitable active trades.

* `held_for_reinvestment`: Capital from expired pre-trade nominations, which can be redeployed in the next trading cycle.

### 3.2. Dry-Run and Live Modes

The system can operate in a "dry-run" (paper trading) mode as a risk-control measure.

* **Automatic Dry-Run Activation**: The system automatically enters dry-run mode after a configurable number of consecutive losing cycles (`dry_run_loss_threshold`). This halts real-capital deployment during unfavorable market periods.

* **Profit-Triggered Conversion to Live**: A key innovation is the ability to convert an entire dry-run cycle to live trading mid-cycle. If any single paper trade in a dry-run cycle reaches a significant profit threshold (`dry_run_to_live_threshold`), the system can be configured to immediately deploy real capital across all active paper positions, aiming to capture a highly favorable market-wide move.

## 4. Implementation and Operation

### 4.1. Live Trading (`main.py`)

The live execution script orchestrates the system for real-time operation.

* **Scheduler**: A `Runner` object schedules two primary tasks: fetching price data from the `DataStream` every 5 minutes and running the `PredictionTask` daily at 23:55.

* **Status Server**: A lightweight HTTP server is launched in a separate thread to provide real-time status updates on the trader's performance, budget, and active positions.

### 4.2. Backtesting Framework (`test_trader.py`)

The backtesting script provides a powerful framework for strategy validation.

* **Historical Data Simulation**: The script loads historical price data from pickle files and iterates through it, simulating the price tick-by-tick flow of a live environment.

* **Parallel Execution**: To achieve a statistically significant evaluation, the script leverages `joblib` and `tqdm` to run hundreds of independent simulations in parallel over the same historical dataset.

* **Performance Analysis**: At the conclusion of the runs, it aggregates the results and generates histograms of final capital distribution. This provides a deep understanding of the strategy's expected return, risk profile, and consistency.

## 5. Potential Issues and Future Improvements

While the system's architecture is robust, several design choices present opportunities for refinement and could pose risks in a live environment.

### 5.1. Dynamic Risk Parameters

* **Issue**: The current system uses static, "one-size-fits-all" risk parameters (e.g., `limit_returns = 1.52`, `stop_returns = 0.983`). This is rarely optimal, as different assets and market conditions have different volatility profiles.

* **A Potential Improvement**: The parameters could be made dynamic. The ideal objective would be to learn a function $\theta^*(X_t, a)$ that maps the current market state and asset characteristics to an optimal set of risk parameters. This function would be the solution to the optimization problem, which is a formulation of the well-known Kelly Criterion (Kelly, 1956):

  $$\theta^*(X_t, a) = \arg\max_{\theta} \mathbb{E} \left[ \log(1 + R_a(\theta)) \,|\, X_t, a \right]$$

  where $R_a(\theta)$ is the return of a single trade on asset $a$ using parameters $\theta$.

  **Relaxation**: A practical relaxation is to optimize a proxy metric over historical data. For each market cluster `c`, we can find the parameters $\theta_c^*$ that maximize the Sharpe Ratio (Sharpe, 1966) of simulated historical returns:

  $$\theta_c^* = \arg\max_{\theta} \frac{\mathbb{E}[\text{SimulateReturns}(D_c, \theta)]}{\sqrt{\text{Var}[\text{SimulateReturns}(D_c, \theta)]}}$$

### 5.2. Managing Prediction Randomness

* **Issue**: After the model generates a list of nominated coins, the system randomly selects a subset to trade. This introduces a significant element of luck, meaning the performance of any single cycle can have a very high variance.

* **A Potential Improvement**: One possible improvement is to run an ensemble of $K$ independent `LiveTrader` instances in parallel. The total portfolio return would be the sample mean of all instances:

  $$\bar{R}_{K,t} = \frac{1}{K} \sum_{i=1}^{K} R_{i,t}$$

  By the Law of Large Numbers, as $K$ increases, this sample mean converges to the true expected return of the strategy, and its variance is reduced by a factor of $K$. This ensemble approach (Dietterich, 2000) could transform high variance into a predictable, stable return.

### 5.3. Risk-Based Position Sizing

* **Issue**: The system divides capital equally among all selected trades, treating all trades as having equal potential and risk.

* **A Potential Improvement**: A position sizing model could be implemented. For example, the capital allocated to a trade could be inversely proportional to the asset's recent volatility. This would mean taking smaller positions on riskier assets and larger positions on more stable ones, leading to a more balanced risk profile across the portfolio.

## 6. Next Steps: Empirical Evaluation

Having detailed the theoretical framework, architecture, and potential improvements of the trading system, the subsequent article in this series will be dedicated to a rigorous empirical evaluation. We will conduct a comprehensive backtest of the model as described, running the simulation on historical data from the first six months of 2025 to assess its real-world performance and validate the strategies discussed herein.

**References**

* Dietterich, T. G. (2000). Ensemble Methods in Machine Learning. *Multiple Classifier Systems*, 1-15.

* Kelly, J. L. (1956). A New Interpretation of Information Rate. *Bell System Technical Journal, 35*(4), 917-926.

* MacLean, L. C., Thorp, E. O., & Ziemba, W. T. (Eds.). (2011). *The Kelly capital growth investment criterion: Theory and practice*. World Scientific.

* Rotando, L. M., & Thorp, E. O. (1992). The Kelly criterion and the stock market. *The American Mathematical Monthly, 99*(10), 922-931.

* Sharpe, W. F. (1966). Mutual Fund Performance. *The Journal of Business, 39*(1), 119-138.

* Thorp, E. O. (2006). The Kelly Criterion in Blackjack, Sports Betting, and the Stock Market. In *Handbook of asset and liability management* (Vol. 1, pp. 385-428). Elsevier.

<script type="module">
    import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';
    mermaid.initialize({ startOnLoad: true });
</script>