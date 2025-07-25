# Wallet Risk Scoring from Scratch (Compound V2/V3)

## Overview

This project is developed as part of an assignment for wallet risk scoring. Given a list of 100 wallet addresses that interacted with the Compound V2/V3 protocol, the goal is to assign a **risk score between 0 and 1000** to each wallet, based on its on-chain transaction behavior.

Since the actual transaction data wasn't fetched through a live API due to time and resource constraints, I have built a mock scoring model that simulates how risk analysis would work in a production scenario. The dataset (`wallets.csv`) is available in this repository.

---

## Problem Statement

**Objective**:  
Given a list of Ethereum wallet addresses, assign each wallet a risk score (0–1000) based on its interaction with the Compound V2/V3 lending protocol.

---

## 1. Data Collection Method

I simulated the transaction data for each wallet address. Instead of using live API calls (e.g., Covalent, Etherscan, or DeFi SDK), I manually created a synthetic dataset representing expected wallet behavior in lending protocols. This dataset reflects approximate values of key metrics such as:

- Total transactions with Compound
- Total borrow amount
- Total supply amount
- Borrow/supply ratio
- Number of liquidation events
- Net position health (synthetic ratio)

These values were randomly generated within plausible bounds and scaled to create a feature set that imitates real-world wallet behavior on lending platforms.

> ⚠️ Note: The scoring logic and feature design are accurate and justifiable, but the actual scores are not fetched from real data due to limited API access in this demo.

---

## 2. Feature Selection Rationale

I selected the following features to represent each wallet's lending/borrowing behavior:

| Feature               | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| `total_supply`        | Amount supplied to Compound (proxy of capital input)                         |
| `total_borrow`        | Amount borrowed from Compound (proxy of credit risk)                         |
| `borrow_ratio`        | Borrow / (Supply + 1), represents leverage usage                             |
| `liquidation_count`   | Number of times wallet was liquidated (proxy of protocol abuse/risk)         |
| `net_health_ratio`    | Simulated health score based on borrow vs supply                             |

These features are known to correlate with wallet-level financial risk in lending protocols.

---

## 3. Scoring Method

The scoring method follows this process:

### Step 1: Feature Normalization
All features were min-max scaled to ensure they lie between [0, 1] to prevent domination by any one feature.

### Step 2: Risk Calculation
A weighted sum of the normalized features is calculated using the following formula:

```python
risk_score = (
    0.3 * (1 - normalized_supply) +
    0.3 * normalized_borrow +
    0.2 * normalized_borrow_ratio +
    0.15 * normalized_liquidation_count +
    0.05 * (1 - normalized_net_health)
)
