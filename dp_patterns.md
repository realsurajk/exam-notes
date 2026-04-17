# DP Patterns — ECE 406 Exam Reference

Every DP question follows the same structure:
(a) Subproblem definition + how solution is extracted
(b) Recurrence
(c) Base cases
(d) Evaluation order + runtime

---

## Pattern 1 — 1D Prefix DP

**Canonical problem:** Longest Increasing Subsequence (LIS)
Given array A[1..n], find the length of the longest strictly increasing subsequence.

**(a) Subproblem**

dp[i] = length of the longest increasing subsequence that ends at index i.

Solution: max over all i of dp[i].

**(b) Recurrence**

dp[i] = 1 + max{ dp[j] : j < i and A[j] < A[i] }

(If no valid j exists, dp[i] = 1.)

**(c) Base cases**

dp[i] = 1 for all i. Every single element is a subsequence of length 1.

**(d) Evaluation order + runtime**

Fill left to right (i = 1 to n). Each dp[i] only looks at j < i — already computed.
Runtime: O(n²). Space: O(n).

**When to use this shape:** problems where you're building something ending at position i, and you scan all previous positions for a valid predecessor.
Other examples: longest non-decreasing subsequence, max sum subarray ending at i.

---

## Pattern 2 — Interval DP

**Canonical problem:** Minimum deletions to make a string a palindrome.
Given string S[1..n], find the minimum characters to remove so S becomes a palindrome.

**(a) Subproblem**

dp[i][j] = minimum deletions to make S[i..j] a palindrome.

Solution: dp[1][n].

**(b) Recurrence**

If S[i] = S[j]:  dp[i][j] = dp[i+1][j-1]
If S[i] ≠ S[j]:  dp[i][j] = 1 + min( dp[i+1][j],  dp[i][j-1] )

Intuition: if the ends match, no deletion needed for them — solve the inside. If they don't match, delete one end (cost 1) and solve the smaller problem.

**(c) Base cases**

dp[i][i] = 0        (single character is already a palindrome)
dp[i][i+1] = 0  if S[i] = S[i+1],  else 1

**(d) Evaluation order + runtime**

Fill by increasing interval length (length 1, then 2, ..., then n). dp[i][j] depends on dp[i+1][j-1], dp[i+1][j], dp[i][j-1] — all shorter intervals, already filled.
Runtime: O(n²). Space: O(n²).

**When to use this shape:** problems on substrings or subarrays where you shrink from both ends. Other examples: optimal matrix chain multiplication, minimum cost polygon triangulation, balloon burst.

---

## Pattern 3 — Two-Sequence DP

**Canonical problem:** Longest Common Subsequence (LCS)
Given strings A[1..m] and B[1..n], find the length of their longest common subsequence.

**(a) Subproblem**

dp[i][j] = length of LCS of A[1..i] and B[1..j].

Solution: dp[m][n].

**(b) Recurrence**

If A[i] = B[j]:  dp[i][j] = dp[i-1][j-1] + 1
If A[i] ≠ B[j]:  dp[i][j] = max( dp[i-1][j],  dp[i][j-1] )

Intuition: if last characters match, use both and recurse on the rest. If not, skip one of them (whichever gives better result).

**(c) Base cases**

dp[0][j] = 0  for all j    (empty prefix of A has no common subsequence)
dp[i][0] = 0  for all i    (empty prefix of B has no common subsequence)

**(d) Evaluation order + runtime**

Fill row by row, left to right. dp[i][j] depends on dp[i-1][j-1], dp[i-1][j], dp[i][j-1] — all previously filled.
Runtime: O(mn). Space: O(mn) (can reduce to O(n) with rolling array).

**When to use this shape:** problems involving two sequences simultaneously. Other examples: edit distance, sequence alignment.

---

## Pattern 4 — Resource / Knapsack DP

**Canonical problem:** 0/1 Knapsack
Given n items each with weight wi and value vi, and capacity W. Maximize total value without exceeding capacity. Each item used at most once.

**(a) Subproblem**

dp[i][w] = maximum value achievable using only items 1..i with total weight at most w.

Solution: dp[n][W].

**(b) Recurrence**

If wi > w (item too heavy):  dp[i][w] = dp[i-1][w]
If wi <= w:  dp[i][w] = max( dp[i-1][w],   vi + dp[i-1][w - wi] )
                                skip item i    include item i

**(c) Base cases**

dp[0][w] = 0  for all w    (no items, no value)
dp[i][0] = 0  for all i    (no capacity, no value)

**(d) Evaluation order + runtime**

Fill row by row (i = 1 to n), left to right (w = 0 to W). dp[i][w] only depends on row i-1.
Runtime: O(nW). Space: O(nW).

Note: O(nW) is pseudo-polynomial — W is the numeric value of capacity, not the number of bits. Not polynomial in the input size.

**When to use this shape:** problems where you decide to include/exclude items and track a budget/capacity. Other examples: coin change (unbounded), subset sum, scheduling with deadlines.

---

## Pattern 5 — Subset DP (Set Cover / Skill Cover)

**Canonical problem:** Minimum cost team (Q4 from 2022 final)
n applicants each with skill set Si and cost ci. Cover all m skills at minimum cost.

**(a) Subproblem**

K(R_bar) = minimum cost to cover all skills in the set R_bar, using any subset of the n applicants.

Solution: K(R)  where R is the full skill set.

**(b) Recurrence**

Pick any skill r in R_bar. Someone must cover r — try all applicants i with r in Si:

K(R_bar) = min over all i where r in Si  of  { ci + K(R_bar \ Si) }

Progress: R_bar \ Si is a strict subset of R_bar (since r is removed).

**(c) Base cases**

K(empty set) = 0    (no skills left, no cost)
If R_bar is non-empty but no applicant covers any skill in R_bar: K(R_bar) = infinity (infeasible)

**(d) Evaluation order + runtime**

Fill subsets in increasing order of size. Each subset R_bar depends only on strictly smaller subsets.
Represent subsets as bitmasks for efficient set operations.

Runtime: O(n * 2^m).  Not polynomial — exponential in m (number of skills/elements).

**When to use this shape:** problems where the state is "which elements have been covered/visited." Other examples: TSP (see Pattern 6), minimum vertex cover.

---

## Pattern 6 — Subset DP (TSP / Hamiltonian Path)

**Canonical problem:** Travelling Salesman Problem (TSP)
Given n cities with costs cost(u, v). Find minimum cost tour that visits every city exactly once and returns to start.

**(a) Subproblem**

dp[S][v] = minimum cost path that starts at city 0, visits exactly the cities in set S, and ends at city v.
(v must be in S, and 0 must be in S.)

Solution: min over all v ≠ 0 of { dp[V][v] + cost(v, 0) }
where V is the set of all cities.

**(b) Recurrence**

dp[S][v] = min over all u in S \ {v}  of  { dp[S \ {v}][u] + cost(u, v) }

Intuition: the second-to-last city was some u in S. Remove v from S, solve for reaching u, then pay cost(u, v).

**(c) Base cases**

dp[{0}][0] = 0    (at start city, only city 0 visited, zero cost)
dp[S][v] = infinity if v not in S or 0 not in S

**(d) Evaluation order + runtime**

Fill by increasing |S|. dp[S][v] depends on subsets of size |S|-1.

Runtime: O(2^n * n²). Space: O(2^n * n). Not polynomial.

**When to use this shape:** any problem requiring visiting all nodes of a graph exactly once with minimum cost.

---

## Pattern 7 — Grid DP

**Canonical problem:** Minimum cost path in a grid.
Given m x n grid where each cell has a cost. Find the minimum cost path from top-left (1,1) to bottom-right (m,n). You can only move right or down.

**(a) Subproblem**

dp[i][j] = minimum cost to reach cell (i, j) from (1, 1).

Solution: dp[m][n].

**(b) Recurrence**

dp[i][j] = cost[i][j] + min( dp[i-1][j],  dp[i][j-1] )
                              from above    from left

**(c) Base cases**

dp[1][1] = cost[1][1]
dp[1][j] = dp[1][j-1] + cost[1][j]    (top row — can only come from left)
dp[i][1] = dp[i-1][1] + cost[i][1]    (left column — can only come from above)

**(d) Evaluation order + runtime**

Fill row by row, left to right. dp[i][j] depends only on cells above and to the left.
Runtime: O(mn). Space: O(mn).

**When to use this shape:** any problem on a 2D grid with restricted movement. Other examples: number of paths, longest path in a DAG grid.

---

## Quick Reference — Which Pattern to Use

| Problem type | Pattern | State shape | Runtime |
|---|---|---|---|
| Subsequence ending at i | 1D Prefix | dp[i] | O(n²) |
| Substring S[i..j] | Interval | dp[i][j] | O(n²) |
| Two strings A[i], B[j] | Two-sequence | dp[i][j] | O(mn) |
| Items + budget/capacity | Resource | dp[i][w] | O(nW) |
| Cover a set of elements | Subset | dp[bitmask] | O(n * 2^m) |
| Visit all nodes | Subset (TSP) | dp[S][v] | O(2^n * n²) |
| Grid movement | Grid | dp[i][j] | O(mn) |

## The 4 Questions to Ask for Any DP Problem

1. What does dp[...] represent? (one sentence, precise)
2. How do I express dp[...] in terms of smaller subproblems? (the recurrence)
3. What are the smallest subproblems I can answer directly? (base cases)
4. In what order do I fill the table, and what is the total runtime? (subproblems x work per subproblem)
