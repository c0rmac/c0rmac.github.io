---
title: 'Identifying Market Accumulation Patterns with K-Means Clustering'
date: 2025-8-15
permalink: /posts/2025/8/15/chapter-1-cluster-buster/
tags:
  - machine learning
  - data science
  - quantitative finance
  - algorithmic trading
  - financial modeling
  - k-means clustering
  - unsupervised learning
  - market sentiment analysis
  - fear and greed index
  - python
  - scikit-learn
  - cluster analysis
  - market accumulation
  - trading strategy
  - risk management
  - pattern recognition
---

> **Abstract:** This paper details the "ClusterBuster" algorithm, a methodology for identifying potentially profitable market conditions using unsupervised machine learning. The approach leverages K-Means clustering to group historical financial data, with features primarily derived from rolling modes of the "Fear and Greed Index" over various lookback periods. Each resulting cluster is evaluated using custom performance metrics: the "breakthrough ratio" to quantify upside potential and the "loss ratio" to assess downside risk. The core of the algorithm is an optimization loop that systematically tests different numbers of clusters to identify a model that maximizes the proportion of high-performing, or "breakthrough," clusters. The final output is an optimized clustering model that effectively isolates historical data patterns correlated with significant positive returns, providing a systematic approach to identifying trading opportunities.

## Introduction

The ClusterBuster script uses the K-Means algorithm to identify optimal market conditions by clustering financial data. It analyzes historical "Fear and Greed Index" data to find patterns that historically precede significant price increases.

---

## 1. Data Preprocessing (`datapreprocess.py`)

The `generate_lookback` function enriches the data by creating features from past "Fear and Greed Index" values. It calculates the rolling mode of the index over various lookback periods (e.g., 0-60 days), creating a set of features known as the `fear_greed_trail`.

---

## 2. Output Definitions

The `run` function produces a list called `training_analysis`. Each item in the list is a dictionary that holds the complete results for a specific `k` value. The key fields and metrics used in the analysis are described below.

### Analysis Definitions

| Key                                                     | Description                                                                                             |
| ------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `k`                                                     | The number of clusters used for this analysis run.                                                      |
| `proportion_train_breakthrough_ratio_above_threshold`   | The proportion of clusters that had a "Breakthrough Ratio" above the defined threshold. This is the primary optimization metric. |
| `proportion_train_loss_ratio_above_threshold`           | The proportion of clusters that had a "Loss Ratio" above the defined threshold.                         |
| `max_train_loss_ratio`                                  | The highest "Loss Ratio" found among all clusters for this `k`.                                           |
| `cluster_sizes`                                         | A list containing the number of members in each cluster.                                                |
| `kmeans`                                                | The fitted `KMeans` model object from scikit-learn.                                                     |
| `scaler`                                                | The `StandardScaler` object used to scale the data.                                                     |
| `train_df`                                              | The complete DataFrame with the cluster assignments for this `k`.                                         |

### Metric Definitions

| Key                  | Description                                                                                                |
| -------------------- | ---------------------------------------------------------------------------------------------------------- |
| `breakthrough_ratio` | The ratio of trades that exceeded a profit threshold to those that didn't. A higher value indicates greater upside potential. |
| `loss_ratio`         | The ratio of trades that exceeded a loss threshold to those that didn't. A higher value indicates greater downside risk. |

---

## 3. Core Logic (`clusterbuster.py`)

The main script finds the best clustering model by performing the following steps:

### Finding and Scoring Clusters

* **`find_clusters`**: This function prepares the data for clustering.
    1.  **Data Split**: It divides the data into a `learning_df` (for training on data with high returns) and an `assignment_df` (for the remaining data).
    2.  **K-Means Training**: It trains a `KMeans` model on the scaled `learning_df` for a given number of clusters, `k`.
    3.  **Cluster Assignment**: It uses the trained model to assign all data points from both dataframes into clusters.
* **`score_clusters`**: This function evaluates each cluster's performance.
    * **Breakthrough Ratio**: Measures upside potential by calculating the ratio of positive to negative outcomes.
    * **Loss Ratio**: Measures downside risk.

### Optimization Loop (`run`)

This is the main function that finds the optimal number of clusters (`k`).

1. It iterates through a range of `k` values (e.g., 2 to 30), running the clustering and scoring process for each.

2. It filters out models with too many small, insignificant clusters.

3. It selects the best model by identifying the one with the highest proportion of "breakthrough" clusters. If there's a tie, it chooses the model with the higher `k` value to favor a more detailed clustering solution.

---

## 4. Results

### Example Training Results

Below is a table showing a detailed breakdown for a single run where `k=6`. It displays the performance metrics for each of the six clusters (0-5) on both the training and testing datasets.

| Metric                   | Cluster 0 | Cluster 1 | Cluster 2 | Cluster 3 | Cluster 4 | Cluster 5 |
| ------------------------ | --------- | --------- | --------- | --------- | --------- | --------- |
| `train_size`             | 65.00     | 57.00     | 24.00     | 154.00    | 64.00     | 31.00     |
| `train_breakthrough_ratio` | 0.12      | 0.33      | 0.14      | 0.31      | 0.31      | 0.35      |
| `train_loss_ratio`       | 6.22      | 6.13      | 11.00     | 3.67      | 4.82      | 9.33      |
| `test_size`              | 1.00      | 47.00     | 21.00     | 17.00     | 0.00      | 50.00     |
| `test_breakthrough_ratio`| 0.00      | 0.21      | 0.00      | 0.00      | inf       | 0.11      |
| `test_loss_ratio`        | inf       | 6.83      | 6.00      | 2.40      | inf       | 3.55      |

---

## Conclusion

The ClusterBuster algorithm systematically identifies profitable trading setups. By combining feature engineering with K-Means clustering and a custom scoring system, it pinpoints historical patterns with high profit potential. The output is the optimal clustering model for separating these opportunities from other market data.