{
 "cells": [
  {
   "cell_type": "markdown",
   "id": "e9ed220c-8f83-4fc7-96f9-ba3a0fd603a6",
   "metadata": {},
   "source": [
    "# Smart Order Router — Cont & Kukanov Backtest\n",
    "\n",
    "This project implements and evaluates a Smart Order Router (SOR) using the **static cost model** from *Cont & Kukanov (2013)*. The router splits a 5,000-share buy order across multiple venues by minimizing the expected total cost, considering price, liquidity, queue risk, and penalties.\n",
    "\n",
    "## Code Structure\n",
    "\n",
    "- `allocate(...)`: Implements the exact pseudocode from `allocator_pseudocode.txt`. Performs a brute-force grid search over discrete share splits to minimize total cost.\n",
    "- `compute_cost(...)`: Calculates execution cost including:\n",
    "  - Ask price + fee for executed shares\n",
    "  - Rebate for unexecuted shares\n",
    "  - Underfill and overfill penalties\n",
    "  - Queue risk penalty\n",
    "- `backtest(...)`: Replays a time-ordered stream of market snapshots, rolling forward any unfilled quantity until the order is completed or the file ends.\n",
    "- `backtest_with_logging(...)`: Adds per-snapshot execution logs and allocator failure tracking.\n",
    "- `twap_baseline_fill_all(...)` / `vwap_baseline_fill_all(...)`: Enforce full 5,000-share fills for fair comparison.\n",
    "\n",
    "## Parameter Search\n",
    "We conducted a grid search over:\n",
    "- `lambda_over`: [0.1, 1.0, 10.0]\n",
    "- `lambda_under`: [0.1, 1.0, 10.0]\n",
    "- `theta_queue`: [0.001, 0.01, 0.1]\n",
    "\n",
    "**Best-performing configuration**:\n",
    "```json\n",
    "{\n",
    "  \"lambda_over\": 0.1,\n",
    "  \"lambda_under\": 0.1,\n",
    "  \"theta_queue\": 0.001\n",
    "}\n",
    "\n",
    "Results Summary:\n",
    "All strategies operate over the same 9-minute window (13:36:32–13:45:14 UTC on August 1, 2024). Below is the total cost and average price:\n",
    "\n",
    "Strategy\tAvg Price\tTotal Cost\tShares Filled\tSavings (bps)\n",
    "SOR (CK)\t222.75\t    1,113,750\t5,000\t             —\n",
    "Best Ask\t222.83\t    1,114,152\t5,000\t           +3.61\n",
    "TWAP\t    223.10\t    444,862\t    1,994 (partial)\t   +15.71\n",
    "VWAP\t    222.83\t   1,114,152\t5,000\t           +3.61\n",
    "\n",
    "Plot Files:\n",
    "results.png: Cumulative cost over time\n",
    "\n",
    "\n",
    "Realism Improvement Idea:\n",
    "In real markets, displayed size does not guarantee immediate execution. To improve realism:\n",
    "Model partial fills probabilistically based on queue position and latency. For example, treat execution probability as a decreasing function of queue length or prior volume."
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3 (ipykernel)",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.11.7"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
