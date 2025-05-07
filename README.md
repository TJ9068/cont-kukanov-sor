# Smart Order Router — Cont & Kukanov Backtest

This project implements and evaluates a Smart Order Router (SOR) using the **static cost model** from *Cont & Kukanov (2013)*. The router splits a 5,000-share buy order across multiple venues by minimizing the expected total cost, considering price, liquidity, queue risk, and penalties.

## 📁 Code Structure

- `allocate(...)`: Implements the pseudocode from `allocator_pseudocode.txt`. Performs brute-force search over share splits.
- `compute_cost(...)`: Calculates:
  - Execution cost = ask + fee
  - Rebate for unfilled shares
  - Underfill/overfill penalties
  - Queue-risk penalty
- `backtest(...)`: Replays snapshots, tracks remaining shares, fills, and total cost.
- `backtest_with_logging(...)`: Adds per-snapshot diagnostics.
- `twap_baseline_fill_all(...)` / `vwap_baseline_fill_all(...)`: Ensure full 5,000-share fill for comparison.

## 🔎 Parameter Search

Grid search was run over:

- `lambda_over`: [0.1, 1.0, 10.0]
- `lambda_under`: [0.1, 1.0, 10.0]
- `theta_queue`: [0.001, 0.01, 0.1]

### ✅ Best Parameters
```json
{
  "lambda_over": 0.1,
  "lambda_under": 0.1,
  "theta_queue": 0.001
}
