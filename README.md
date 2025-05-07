# Smart Order Router — Cont & Kukanov Backtest

This project implements and evaluates a Smart Order Router (SOR) using the **static cost model** from *Cont & Kukanov (2013)*. The router splits a 5,000-share buy order across multiple venues by minimizing the expected total cost, considering price, liquidity, queue risk, and penalties.

## Code Structure

- `allocate(...)`: Implements the pseudocode from `allocator_pseudocode.txt`. Performs brute-force search over share splits.
- `compute_cost(...)`: Calculates:
  - Execution cost = ask + fee
  - Rebate for unfilled shares
  - Underfill/overfill penalties
  - Queue-risk penalty
- `backtest(...)`: Replays snapshots, tracks remaining shares, fills, and total cost.
- `backtest_with_logging(...)`: Adds per-snapshot diagnostics.
- `twap_baseline_fill_all(...)` / `vwap_baseline_fill_all(...)`: Ensure full 5,000-share fill for comparison.

## Parameter Search

Grid search was run over:

- `lambda_over`: [0.1, 1.0, 10.0]
- `lambda_under`: [0.1, 1.0, 10.0]
- `theta_queue`: [0.001, 0.01, 0.1]

### Best Parameters
```json
{
  "lambda_over": 0.1,
  "lambda_under": 0.1,
  "theta_queue": 0.001
}
```
## Results Summary

All strategies were run over the same 9-minute window: **13:36:32–13:45:14 UTC** on August 1, 2024.

| Strategy   | Avg Price | Total Cost | Shares Filled    | Savings (bps) |
|------------|-----------|------------|------------------|----------------|
| **SOR (CK)** | 222.75    | 1,113,750   | 5,000              | —            |
| Best Ask   | 222.83    | 1,114,152   | 5,000              | +3.61          |
| TWAP       | 223.10    | 444,862     | 1,994 (partial)    | +15.71         |
| VWAP       | 222.83    | 1,114,152   | 5,000              | +3.61          |

## Plot Files

- `results.png`: Cumulative cost over time (shares filled vs cost)
- `results_avg_price.png`: Average fill price as a function of fill progression

  ## Suggested Realism Improvement

In real markets, **displayed size does not guarantee immediate execution**. The actual outcome depends on queue position, latency, and hidden order flow.

To improve realism:
> Model **partial fills probabilistically** based on queue depth or latency-adjusted execution risk. For example, assign a decreasing execution probability to deeper queue positions or introduce a random fill/no-fill event based on current order book pressure.
