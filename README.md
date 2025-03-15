# Accelerated Index Tracking

## Overview
This project addresses the problem of constructing a tracking portfolio that closely replicates the returns of a given index while selecting a subset of stocks from a larger universe. The challenge is to efficiently determine the optimal subset of stocks while maintaining computational feasibility.

## Problem Formulation
We aim to solve the following optimization problem:

$\[ \min f(x) = \frac{1}{2N} \sum_{k=1}^{N} (r_k(x) - \bar{r}_k)^2 \]$

subject to:
- $\( \sum_{j=1}^{n} x_j = 1 \)$
- $\( x_j \geq 0, \quad \forall j \in \{1, \dots, n\} \)$
- $\( x_j = 0 \)$ for all $\( j \notin I \)$

where each $\( r_k(x) \)$ represents the portfolio return at time step $\( k \)$, and $\( \bar{r}_k \)$ is the index return.

## Solution Approaches
Three different methods can be considered:

### 1. Fixed Stock Selection
A predefined set \( I \) of stocks is chosen, and the problem is solved using standard nonlinear solvers. Stocks are selected using binary indicators:
$\[ b_j = \begin{cases} 1, & \text{if stock } j \text{ is selected} \\ 0, & \text{otherwise} \end{cases} \]$

and constraints:
$\[ 0 \leq x_j \leq b_j, \quad \forall j \in \{1, 2, \dots, n\} \]$

This method is simple but may yield suboptimal tracking results.

### 2. Mixed-Integer Optimization
To improve selection, we introduce binary decision variables $\( b_j \)$, solving a mixed-integer optimization problem with the cardinality constraint:
$\[ \sum_{j=1}^{n} b_j \leq J \]$
where $\( J \)$ is a pre-specified number of selected stocks.

While effective for small-scale problems $(\( n \approx 100 \))$, this approach becomes computationally infeasible for large datasets.

### 3. Regularized Model (Big Data Approach)
To handle large-scale problems, we introduce a regularization term that penalizes the number of selected stocks:

$\[ \min f(x) = \frac{1}{2N} \sum_{k=1}^{N} (r_k(x) - \bar{r}_k)^2 + \lambda * \sum |x_j| \]$

where $\( \lambda > 0 \)$ controls the sparsity of the solution.

The solution $\( \hat{x} \)$ provides an indication of which stocks to use:
$\[ b_j = \begin{cases} 1, & \text{if } \hat{x}_j > 0 \\ 0, & \text{if } \hat{x}_j = 0 \end{cases} \]$

Using these values, problem (2) is solved again with variable upper bounds to refine the final tracking portfolio.

## Implementation Details
The implementation includes:
- **Optimization Solver**: Nonlinear solvers (e.g., SciPy, Gurobi) for the fixed and regularized models.
- **Mixed-Integer Programming**: Solved using Gurobi for small-scale cases.
- **Sparse Selection Heuristics**: Efficient feature selection methods for large-scale stock filtering.

## Advantages
- **Scalability**: Regularized formulation allows handling large \( n \) efficiently.
- **Automated Selection**: Reduces the need for manual stock picking.

## Getting Started
### Requirements
- Python 3.8+
- NumPy, SciPy
- Gurobi (for mixed-integer optimization)


## Future Improvements
- Parallel computing for large-scale optimizations.



