# ECE 406 Final Exam — Question Types & Tricks

---

## 1. Graph Algorithm Design (Modified BFS/Dijkstra)

**Pattern 1 — Super-source (all-to-one)**
- Trigger: "for every node v, find shortest distance to nearest X"
- Trick: add virtual node s, connect s→every X with weight 0, run Dijkstra from s
- Runtime: O((V+E) log V)

**Pattern 2 — Node removal (avoid certain nodes)**
- Trigger: "shortest path that never passes through nodes in set F"
- Trick: delete F and incident edges, run standard BFS/Dijkstra on remaining graph
- Runtime: O((V+E) log V)

**Pattern 3 — Layered graph (count constraint)**
- Trigger: "at most k of X nodes/edges on path"
- Trick: k+1 layers; layer i = "used i of X so far"; constraint-triggering edge crosses layer i→i+1; last layer blocks further crossings
- Runtime: O((V+E) log V) — graph multiplied by (k+1)
- Answer: min(dist[t][0], dist[t][1], ..., dist[t][k])

**Pseudocode structure (Approach 1 — exam safe):**
```
Build G': [describe layer construction]
Run Dijkstra(G', s_0)
Return [answer from appropriate layer]
Runtime: O((V+E) log V)
```

---

## 2. Recurrence Building

**Formula:** T(n) = (#recursive calls) × T(subproblem size) + (cost of all non-recursive work)

**Rule:** scan function top to bottom — ALL loops count regardless of position relative to recursive calls

**Common mistake:** forgetting loops that appear AFTER a recursive call — they still add to f(n)

**Base cases:** count how far back recurrence reaches; need that many base cases
- Recurrence uses i-1 → need i=0
- Recurrence uses i-2 → need i=0 and i=1

---

## 3. Master Theorem

T(n) = aT(n/b) + f(n) → compute c = log_b(a)

| Case | Condition | Answer |
|------|-----------|--------|
| 1 | f(n) grows slower than n^c | T(n) = Θ(n^c) |
| 2 | f(n) grows same rate as n^c | T(n) = Θ(n^c · log n) |
| 3 | f(n) grows faster than n^c | T(n) = Θ(f(n)) |

"Slower/faster" = by a polynomial factor (n^ε difference)

---

## 4. DP Subproblem Design

**Step 1 — identify shape:**
| Input | Shape | When |
|-------|-------|------|
| One sequence, left-right | dp[i] | decisions left to right |
| One sequence, symmetric | dp[i][j] | interval/palindrome problems |
| Two sequences | dp[i][j] | comparing prefixes |
| Items + resource | dp[i][c] | capacity/count constraint |
| Sequence + state | dp[i][state] | need to track direction/flag |

**Step 2 — ask "what was the last decision?"**
Each choice for the last element = one case in recurrence

**Step 3 — check: does dp[i] give enough info?**
If not, add a dimension

**Runtime = (number of subproblems) × (work per subproblem)**
- Fixed comparisons per cell → O(1) per cell
- Loop over previous positions → O(n) per cell
- Loop over splits in range → O(n) per cell

**Common patterns:**
- LIS: dp[i] = LIS ending at i → O(n²)
- Knapsack: dp[i][c] → O(nW)
- LCS: dp[i][j] → O(nm)
- Interval DP (balloon burst, palindrome): dp[i][j], O(n) per cell → O(n³)
- No-adjacent: skip branch uses i-1 (NOT i-2), take branch uses i-2

---

## 5. LP Standard Form

Standard form: maximize c^T x, subject to Ax ≤ b, x ≥ 0

**Four conversions:**
1. min → max: negate objective
2. ≥ → ≤: negate both sides
3. = → two inequalities (≤ and ≥, then negate second)
4. Free variable x: replace with x⁺ - x⁻, both ≥ 0 (adds one variable)

**Do free variable substitution FIRST — affects all constraints and objective**

---

## 6. LP Duality

Primal: maximize c^T x, Ax ≤ b, x ≥ 0
Dual:   minimize b^T y, A^T y ≥ c, y ≥ 0

**Rules:**
- One dual variable per primal constraint
- One dual constraint per primal variable
- Objective ↔ RHS swap; max ↔ min flip; ≤ ↔ ≥ flip
- Primal (n vars, m constraints) → Dual (m vars, n constraints)

**Column rule:** dual constraint for xⱼ = read COLUMN j of A, multiply by y, set ≥ cⱼ

**Duality theorems:**
- Weak: c^T x ≤ b^T y for any feasible x, y
- Strong: optimal values are equal

---

## 7. LP Reformulation Tricks

**min max{f₁, f₂, ..., fₖ}:**
```
minimize t
s.t. t ≥ f₁, t ≥ f₂, ..., t ≥ fₖ
     (original constraints)
```

**max min{f₁, f₂, ..., fₖ}:**
```
maximize t
s.t. t ≤ f₁, t ≤ f₂, ..., t ≤ fₖ
```

**minimize |x|:**
```
minimize t
s.t. t ≥ x, t ≥ -x
```
Note: need BOTH constraints (t ≥ x alone insufficient when x < 0)

**|ax + b| ≤ c:** expand to -c ≤ ax+b ≤ c (two constraints)

---

## 8. LP Feasibility & Boundedness

**Three states:** infeasible / unbounded / has optimal

**Check feasibility:** find one point satisfying all constraints (try x=0)

**Check boundedness:** find parametric direction — let variable = t, adjust others to stay feasible, check if objective → ±∞

**Strong duality table:**
| Primal | Dual |
|--------|------|
| Has optimal | Has optimal (equal) |
| Unbounded | Infeasible |
| Infeasible | Unbounded OR infeasible |

---

## 9. NP: Is X in NP?

**Template:**
"Certificate = [the proposed solution]. Verify by [checking the property], which takes O(...) time."

The certificate is always the proposed YES-witness. Verification checks it's valid.

---

## 10. NP Reductions

**To show A ≤_p B (A reduces to B):**

**Step 1 — find the analogy:**
- Write YES condition for both A and B
- "What needs to be satisfied in A?" → universe/elements in B (or constraints in ILP)
- "What satisfies it?" → sets in B (or variables in ILP)

**Step 2 — construction:**
- Map A-input to B-input in polynomial time

**Step 3 — two-direction correctness:**
```
(→) Assume A-solution exists
    → apply construction mapping
    → show B-solution is valid

(←) Assume B-solution exists
    → apply reverse mapping
    → show A-solution is valid
```

**What it establishes:**
- A ≤_p B + B in NP → B is NP-complete (if A is NP-complete)
- Reduction direction: A → B means B is at least as hard as A

**Common reductions:**
- Vertex-Cover → Set-Cover: universe=edges, sets=incident edges per vertex
- Dominating-Set → Set-Cover: universe=vertices, sets=vertex+neighbors
- Graph problem → ILP: 0/1 variable per vertex, constraint per requirement

**ILP constraint patterns:**
| Requirement | Constraint |
|-------------|------------|
| u or v selected | -xᵤ - xᵥ ≤ -1 |
| not both u,v selected | xᵤ + xᵥ ≤ 1 |
| select at most k | Σxᵥ ≤ k |
| select at least k | -Σxᵥ ≤ -k |
| binary variable | xᵥ ≤ 1 |

---

## 11. True/False Traps

- Quicksort worst case: O(n²), NOT O(n log n)
- Dual of dual = primal: TRUE
- Branch and bound: finds optimal (if run to completion): TRUE
- P ∩ NP ≠ ∅ → P = NP: FALSE (P ⊆ NP so P ∩ NP = P ≠ ∅ always)
- Undirected graph, no cycles, |E|=|V|-1 → tree: TRUE (connected + acyclic + right edge count)
- Edge weights 1 or 2, shortest path in O(V+E): TRUE (split weight-2 edges into two weight-1 edges → BFS)

---

## Algorithm Answer Template (Every Time)

1. **One-sentence idea**
2. **Pseudocode** (with construction if modified algorithm)
3. **Runtime with justification**
