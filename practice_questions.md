# Practice Questions — ECE 406 & MSE 331

One question per topic. Answer is directly below each question.

---

## ECE 406

---

**Q1 — Asymptotic Notation**

Prove or disprove: f(n) = 3n² + 5n + 2 is O(n²).
Use the formal definition of Big-O (find explicit c and n₀).

**A1**

Choose c = 10, n₀ = 1. For all n ≥ 1:
3n² + 5n + 2 ≤ 3n² + 5n² + 2n² = 10n².
So 3n² + 5n + 2 ≤ 10n² for all n ≥ 1. Yes, f(n) = O(n²). ✓

---

**Q2 — Strong Induction**

Prove by strong induction that every integer n ≥ 2 can be written as a product of primes.

**A2**

Base case: n = 2 is prime, so it is trivially a product of primes.
Inductive hypothesis: assume every integer 2 ≤ k < n can be written as a product of primes.
Inductive step: if n is prime, done. If n is composite, n = a × b with 2 ≤ a, b < n. By IH, a and b are each products of primes, so n = (product of primes) × (product of primes) = product of primes.

---

**Q3 — Modular Arithmetic**

Compute 7¹³⁰ mod 11 using modular exponentiation.
Show each step of the recursive halving.

**A3**

7¹³⁰ mod 11. Use Fermat: 7¹⁰ ≡ 1 (mod 11). 130 = 13×10 + 0. So 7¹³⁰ = (7¹⁰)¹³ ≡ 1¹³ = 1 (mod 11).

Or via recursive halving:
- 7² = 49 ≡ 5 (mod 11)
- 7⁴ ≡ 5² = 25 ≡ 3 (mod 11)
- 7⁸ ≡ 3² = 9 (mod 11)
- 7¹⁶ ≡ 9² = 81 ≡ 4 (mod 11)
- 7³² ≡ 4² = 16 ≡ 5 (mod 11)
- 7⁶⁴ ≡ 5² = 25 ≡ 3 (mod 11)
- 7¹²⁸ ≡ 3² = 9 (mod 11)
- 7¹³⁰ = 7¹²⁸ × 7² ≡ 9 × 5 = 45 ≡ 1 (mod 11) ✓

---

**Q4 — Extended Euclidean / Multiplicative Inverse**

Find the multiplicative inverse of 5 mod 13.
Use the extended Euclidean algorithm. Show gcd(13, 5) = 1 and find x, y such that 13x + 5y = 1.

**A4**

Extended Euclidean on gcd(13, 5):
- 13 = 2×5 + 3
- 5 = 1×3 + 2
- 3 = 1×2 + 1
- 2 = 2×1 + 0

Back-substitute:
- 1 = 3 − 1×2
- 1 = 3 − 1×(5 − 1×3) = 2×3 − 5
- 1 = 2×(13 − 2×5) − 5 = 2×13 − 5×5

So 5×(−5) ≡ 1 (mod 13) → inverse of 5 = −5 ≡ 8 (mod 13).

Verify: 5×8 = 40 = 3×13 + 1 ≡ 1 (mod 13) ✓

---

**Q5 — RSA**

p = 5, q = 11, e = 3.
(a) Compute N and φ(N).
(b) Find d (the private key).
(c) Encrypt the message m = 4.
(d) Decrypt it back to verify.

**A5**

(a) N = 5×11 = 55. φ(N) = (5−1)(11−1) = 40.
(b) d = inverse of 3 mod 40. Extended Euclidean: 3×27 = 81 = 2×40 + 1 → d = 27.
(c) Encrypt: c = 4³ mod 55 = 64 mod 55 = 9.
(d) Decrypt: m = 9²⁷ mod 55. Using modexp: result = 4. ✓

---

**Q6 — Recurrence Building**

Write the recurrence T(n) for this function. Then apply the Master Theorem to find the closed-form runtime.

```python
def f(arr, n):
    if n <= 1:
        return arr[0]
    for i in range(n * n):       # loop A
        print(arr[i % n])
    f(arr, n // 2)
    f(arr, n // 2)
    f(arr, n // 2)
    for i in range(n):           # loop B
        print(i)
```

**A6**

Loop A: O(n²). Loop B: O(n). Three recursive calls on n/2.
T(n) = 3T(n/2) + n²

Master Theorem: a=3, b=2, c = log₂3 ≈ 1.585. f(n) = n². Since 2 > 1.585 → Case 3 → T(n) = Θ(n²).

---

**Q7 — Master Theorem**

For each recurrence, identify the case and give the closed-form runtime:

(a) T(n) = 4T(n/2) + n²
(b) T(n) = 3T(n/3) + n
(c) T(n) = 8T(n/2) + n²

**A7**

(a) T(n) = 4T(n/2) + n². c = log₂4 = 2. f(n) = n² = n^c → Case 2 → Θ(n² log n).
(b) T(n) = 3T(n/3) + n. c = log₃3 = 1. f(n) = n = n^c → Case 2 → Θ(n log n).
(c) T(n) = 8T(n/2) + n². c = log₂8 = 3. f(n) = n² < n³ → Case 1 → Θ(n³).

---

**Q8 — Graph Algorithm Pattern: Super-Source**

You have a weighted directed graph G = (V, E) with non-negative edge weights.
Some nodes are marked blue. For every node v, find the shortest distance from v to the nearest blue node.
Your algorithm must run in O((V+E) log V).

Write: (1) one-sentence idea, (2) construction + pseudocode, (3) runtime justification.

**A8**

Idea: reverse all edges, add a virtual super-source connected to all blue nodes with weight 0, run one Dijkstra from the super-source.

Construction of G':
- Reverse all edges in G (so paths toward blue nodes become paths away from super-source)
- Add node s
- For each blue node u: add edge (s, u, weight 0)

Run Dijkstra(G', s). Return dist[v] for all v ∈ V.

Runtime: O((V+E) log V). One Dijkstra on a graph with V+1 nodes and E+|blue| edges.

---

**Q9 — Graph Algorithm Pattern: Layered Graph**

You have a weighted directed graph G = (V, E). Some edges are marked toll edges.
Find the shortest path from s to t using at most 2 toll edges.

Write: (1) one-sentence idea, (2) construction + pseudocode, (3) runtime.

**A9**

Idea: build 3-layer graph where layer i = "used exactly i toll edges so far." Toll edges advance the layer. Run Dijkstra from s₀. Answer = min(dist[t₀], dist[t₁], dist[t₂]).

Construction of G':
- Create v₀, v₁, v₂ for every v
- For each edge (u,v,w):
  - If toll: add (u₀,v₁,w), (u₁,v₂,w) [no edge from layer 2 — cap at 2]
  - If not toll: add (u₀,v₀,w), (u₁,v₁,w), (u₂,v₂,w)

Run Dijkstra(G', s₀). Return min(dist[t₀], dist[t₁], dist[t₂]).

Runtime: O((V+E) log V). Graph has 3V nodes, 3E edges.

---

**Q10 — Dynamic Programming: Subproblem Design**

You have an array A[1..n] of integers. Find the length of the longest subsequence that is first non-decreasing then non-increasing (a "mountain" subsequence). A single element counts.

Example: A = [1, 3, 5, 4, 2] → answer is 5 (the whole array).

Define the subproblem, write the recurrence, state base cases, evaluation order, and runtime.

**A10**

Define two tables:
- inc[i] = length of longest non-decreasing subsequence ending at index i
- dec[i] = length of longest non-increasing subsequence starting at index i

Mountain length through i = inc[i] + dec[i] − 1.

Recurrences:
- inc[i] = 1 + max{inc[j] : j < i, A[j] ≤ A[i]}, default 1
- dec[i] = 1 + max{dec[j] : j > i, A[j] ≤ A[i]}, default 1

Base cases: inc[i] = 1 if no valid j; dec[i] = 1 if no valid j.
Evaluation order: inc left to right; dec right to left.
Answer: max over all i of (inc[i] + dec[i] − 1).

Runtime: O(n²).

---

**Q11 — DP: Interval**

Given a string S[1..n], find the minimum number of characters to remove to make S a palindrome.

Define the subproblem and write the recurrence. (Hint: think about what happens when S[i] = S[j] vs S[i] ≠ S[j].)

**A11**

dp[i][j] = minimum deletions to make S[i..j] a palindrome.

Recurrence:
- If S[i] = S[j]: dp[i][j] = dp[i+1][j−1]
- If S[i] ≠ S[j]: dp[i][j] = 1 + min(dp[i+1][j], dp[i][j−1])

Base cases: dp[i][i] = 0, dp[i][i+1] = 0 if S[i]=S[i+1] else 1.
Order: increasing length. Answer: dp[1][n]. Runtime: O(n²).

---

**Q12 — LP Standard Form**

Convert to standard form (define A, b, c):

```
minimize    x₁ + 3x₂ − x₃
subject to  x₁ + x₂      ≥ 4
            x₂ + x₃      = 6
            x₁ ≥ 0,  x₂ ≥ 0,  x₃ unrestricted
```

**A12**

Step 1 — substitute x₃ = x₃⁺ − x₃⁻ (free variable, do this first):
Step 2 — min to max: negate objective.
Step 3 — flip C1 (≥ to ≤): −x₁−x₂ ≤ −4
Step 4 — C2 equality: x₂+x₃⁺−x₃⁻ ≤ 6 and −x₂−x₃⁺+x₃⁻ ≤ −6

Max Z = −x₁ − 3x₂ + x₃⁺ − x₃⁻
s.t.  −x₁ − x₂                    ≤ −4
       x₂  + x₃⁺ − x₃⁻            ≤  6
      −x₂  − x₃⁺ + x₃⁻            ≤ −6
      x₁, x₂, x₃⁺, x₃⁻           ≥  0

c = [−1, −3, 1, −1], b = [−4, 6, −6]
A = [[−1,−1, 0, 0], [0, 1, 1,−1], [0,−1,−1, 1]]

---

**Q13 — LP Duality**

Write the dual of:

```
maximize    2x₁ + x₂ + 4x₃
subject to  x₁ + x₂        ≤ 5
            2x₁      + x₃  ≤ 3
                 x₂  + x₃  ≤ 4
            x₁, x₂, x₃    ≥ 0
```

**A13**

Min W = 5y₁ + 3y₂ + 4y₃
s.t.  y₁ + 2y₂        ≥ 2   (x₁ column)
      y₁       + y₃   ≥ 1   (x₂ column)
            y₂ + y₃   ≥ 4   (x₃ column)
      y₁, y₂, y₃     ≥ 0

---

**Q14 — LP Reformulation**

Convert to a valid LP (define variables, write linear constraints and objective):

```
minimize    |x₁ − x₂| + max{x₁ + x₂,  2x₁}
subject to  x₁ + 2x₂ ≥ 5
            x₁, x₂   ≥ 0
```

**A14**

Introduce t₁ for |x₁−x₂| and t₂ for max{x₁+x₂, 2x₁}:

Min t₁ + t₂
s.t.  t₁ ≥  (x₁ − x₂)
      t₁ ≥ −(x₁ − x₂)
      t₂ ≥ x₁ + x₂
      t₂ ≥ 2x₁
      x₁ + 2x₂ ≥ 5
      x₁, x₂, t₁, t₂ ≥ 0

---

**Q15 — LP Feasibility and Boundedness**

For each LP, state whether it is infeasible, unbounded, or has an optimal solution. Justify briefly.

(a) Max x₁ + x₂  s.t.  x₁ + x₂ ≤ 6,  x₁ − x₂ ≤ 2,  x₁,x₂ ≥ 0
(b) Max x₁ − x₂  s.t.  x₁ + x₂ ≤ 5,  x₁ + x₂ ≥ 7,  x₁,x₂ ≥ 0
(c) Max x₁ + x₂  s.t.  x₁ − x₂ ≤ 3,  x₁,x₂ ≥ 0

**A15**

(a) Feasible (x=0 works). Bounded (x₁+x₂≤6 caps both). Has optimal solution.
(b) Infeasible. x₁+x₂≤5 and x₁+x₂≥7 cannot both hold simultaneously.
(c) Feasible (x=0 works). Unbounded: try x₁=t, x₂=t — constraint: t−t=0≤3 ✓, objective 2t→∞.

---

**Q16 — NP: Certificate**

Is the following problem in NP? If yes, describe the certificate and explain how to verify it in polynomial time.

Problem (Clique): Given a graph G = (V, E) and integer k, does G contain a clique of size k? (A clique is a set of k vertices where every pair is connected by an edge.)

**A16**

Yes, Clique is in NP.
Certificate: a set S of k vertices.
Verification: check |S|=k, then check every pair (u,v) in S has edge (u,v) in E. This takes O(k²) time — polynomial. ✓

---

**Q17 — NP Reduction to Set Cover**

Show that Vertex Cover reduces to Set Cover.

Vertex Cover: Given graph G = (V, E), integer k. Does a set S ⊆ V with |S| ≤ k exist such that every edge has at least one endpoint in S?

Write: (1) construction, (2) correctness → direction, (3) correctness ← direction.

**A17**

Construction: given G=(V,E), k:
- Universe B = E (edges are elements to cover)
- For each vertex v: create set Nᵥ = {all edges incident to v}
- Same k

→ direction: if vertex cover S exists, sets {Nᵥ : v∈S} cover all edges. Each edge (u,w) has u∈S or w∈S, so it appears in Nᵤ or Nw. ✓

← direction: if k sets {Nv₁,...,Nvₖ} cover all edges, then {v₁,...,vₖ} is a vertex cover. Each edge (u,w) is in some Nvᵢ → vᵢ is an endpoint of (u,w) → edge covered. ✓

---

**Q18 — ILP Reduction**

Formulate the Independent Set problem as an ILP.

Independent Set: Given G = (V, E), integer k. Does a set S ⊆ V with |S| ≥ k exist such that no two vertices in S share an edge?

Define variables, write the objective and constraints. Then prove correctness in both directions.

**A18**

Variables: xᵥ ∈ {0,1} for each vertex v (1 = in independent set).

Max Σxᵥ  (or enforce Σxᵥ ≥ k as a constraint)
s.t.  xᵤ + xᵥ ≤ 1   for each edge (u,v)    [can't select both endpoints]
      xᵥ ∈ {0,1}

→ direction: independent set S of size k → set xᵥ=1 if v∈S. No edge has both endpoints in S → xᵤ+xᵥ≤1 ✓. Σxᵥ=k ✓.
← direction: ILP solution → S={v:xᵥ=1}. xᵤ+xᵥ≤1 for all edges → no two adjacent vertices both in S → independent set. Σxᵥ≥k → |S|≥k ✓.

---

---

## MSE 331

---

**Q19 — LP Formulation**

A factory makes two products, X and Y. Each unit of X requires 2 hours of machining and 1 hour of assembly. Each unit of Y requires 1 hour of machining and 3 hours of assembly. Available: 100 machining hours and 90 assembly hours per week. Profit: $5 per X, $4 per Y. Formulate as an LP. Define variables, state objective, write all constraints including non-negativity.

**A19**

Let x₁ = units of X per week, x₂ = units of Y per week.

Max Z = 5x₁ + 4x₂
s.t.  2x₁ +  x₂ ≤ 100   (machining hours)
       x₁ + 3x₂ ≤  90   (assembly hours)
      x₁, x₂    ≥   0

---

**Q20 — Simplex Method (with Big M)**

Solve using the simplex method. Show every tableau, entering/departing variable, min ratio test, and BFS at each iteration.

```
Max Z = 3x₁ + 2x₂
s.t.   x₁ + x₂  ≥ 3
      2x₁ + x₂  ≤ 8
      x₁, x₂    ≥ 0
```

**A20**

Standard form: C1 is ≥, subtract excess e₁, add artificial a₁. C2 is ≤, add slack s₂.
Max Z = 3x₁ + 2x₂ − Ma₁

Fix Row 0: Row 0 ← Row 0 + M × (Row a₁).

Initial tableau after fix:

| Basis | x₁    | x₂    | e₁ | a₁ | s₂ | RHS |
|-------|-------|-------|----|----|-----|-----|
| Z     | 3−M   | 2−M   | M  | 0  | 0   | −3M |
| a₁    | 1     | 1     | −1 | 1  | 0   | 3   |
| s₂    | 2     | 1     | 0  | 0  | 1   | 8   |

Entering: x₁ (most negative = 3−M, since M is large). Departing: a₁ (ratio 3/1=3 < 8/2=4).

After pivot, a₁ leaves, x₁ enters. Iteration 1 BFS: x₁=3, s₂=2, Z=9. Check Row 0 — all coefficients ≥ 0 (x₂ coefficient = 2−M+... verify all positive given large M).

Optimal: x₁=3, x₂=0, Z=9. (C1 binding: 3=3 ✓, C2: 6≤8 ✓.)

---

**Q21 — Reading the Optimal Tableau**

Given this optimal simplex tableau for a MAX LP:

|       | x₁  | x₂  | x₃  | s₁  | s₂  | s₃  | RHS  |
|-------|-----|-----|-----|-----|-----|-----|------|
| Z     | 0   | 3   | 0   | 5   | 0   | 2   | 80   |
| s₂    | 0   | 1   | 0   | 2   | 1   | −1  | 6    |
| x₁    | 1   | 2   | 0   | −1  | 0   | 1   | 4    |
| x₃    | 0   | 1   | 1   | 0   | 0   | 3   | 10   |

Answer:
(a) What are x₁*, x₂*, x₃*, z*?
(b) What are the shadow prices of constraints 1, 2, 3?
(c) Which constraints are binding?
(d) What is the reduced cost of x₂? What does it mean?
(e) If b₁ increases by 3, what is the new z*?

**A21**

(a) Basic: x₁=4, x₃=10, s₂=6. Non-basic: x₂=0, s₁=0, s₃=0. z*=80.
(b) Shadow prices: y₁=5 (Row 0 of s₁), y₂=0 (s₂ is basic → non-binding), y₃=2 (Row 0 of s₃).
(c) Binding: C1 (s₁=0), C3 (s₃=0). Non-binding: C2 (s₂=6).
(d) Reduced cost of x₂ = 3. Means x₂'s objective coefficient must increase by 3 before it's worth producing.
(e) y₁=5, Δb₁=3. z*_new = 80 + 5×3 = 95.

---

**Q22 — Range of Feasibility**

Using the tableau from Q21, find the range of feasibility for constraint 1 (slack s₁). What does this range mean?

**A22**

s₁ column in constraint rows: [2, −1, 0].
Let b₁ change by δ:
- s₂ row: 6 + 2δ ≥ 0 → δ ≥ −3
- x₁ row: 4 + (−1)δ ≥ 0 → δ ≤ 4
- x₃ row: 10 + 0δ ≥ 0 → always

Range: −3 ≤ δ ≤ 4. Shadow price y₁=5 valid while b₁ ∈ [b₁−3, b₁+4].
Meaning: the current basis and shadow price remain valid as long as b₁ stays in this range.

---

**Q23 — Range of Optimality**

Using the tableau from Q21, find the range of optimality for x₁'s objective coefficient (currently the coefficient of x₁ in the original objective). How does z* change if c₁ increases by 2?

**A23**

x₁ is basic. Let c₁ → c₁ + δ. Update Row 0 by subtracting δ × (x₁ row) from Row 0:
- x₂: 3 − δ(2) ≥ 0 → δ ≤ 3/2
- s₁: 5 − δ(−1) = 5+δ ≥ 0 → δ ≥ −5
- s₃: 2 − δ(1) ≥ 0 → δ ≤ 2

Most restrictive: −5 ≤ δ ≤ 3/2. If c₁ increases by 2 (δ=2): outside range, basis changes — must re-solve.

Within range: z*_new = z*_old + δ × x₁* = 80 + δ × 4.

---

**Q24 — 100% Rule**

The allowable ranges for x₁ and x₃ are:
- x₁: objective coefficient ∈ [2, 7], currently 4
- x₃: objective coefficient ∈ [3, ∞), currently 5

Simultaneously: c₁ changes from 4 to 6, and c₃ changes from 5 to 4.
Does the current basis remain optimal? Show your work using the 100% rule.

**A24**

c₁: 4→6, change = +2. Allowable increase = 7−4 = 3. r₁ = 2/3 = 66.7%.
c₃: 5→4, change = −1. Allowable decrease = 5−3 = 2. r₃ = 1/2 = 50%.
Sum = 66.7% + 50% = 116.7% > 100%. Current basis may NOT remain optimal — must re-solve.

---

**Q25 — LP Duality (MSE 331 style)**

Write the dual of:

```
Max Z = 4x₁ + 6x₂ + 5x₃
s.t.   x₁ + 2x₂ + x₃  ≤ 10
       x₁ +  x₂       ≤ 6
      2x₁       + x₃  ≤ 8
      x₁, x₂, x₃     ≥ 0
```

Then state the dual optimal value if the primal optimal is z* = 36.

**A25**

Min W = 10y₁ + 6y₂ + 8y₃
s.t.  y₁ + y₂ + 2y₃ ≥ 4   (x₁ column)
      2y₁ + y₂       ≥ 6   (x₂ column)
       y₁      + y₃  ≥ 5   (x₃ column)
      y₁, y₂, y₃    ≥ 0

By strong duality: dual optimal W* = z* = 36.

---

**Q26 — Complementary Slackness**

Primal LP:
```
Max Z = 5x₁ + 4x₂
s.t.  6x₁ + 4x₂ ≤ 24   (C1)
       x₁ + 2x₂ ≤ 6    (C2)
      x₁, x₂ ≥ 0
```

Optimal primal: x₁* = 3, x₂* = 3/2, s₁* = 0, s₂* = 0, z* = 21.
Dual variables: y₁* = ?, y₂* = ?

Use complementary slackness and strong duality to find y₁* and y₂*.

**A26**

Both s₁=0 and s₂=0 → C1 and C2 are binding → y₁, y₂ can both be non-zero.

CS1: x₁*=3>0 → dual constraint for x₁ is binding: 6y₁ + y₂ = 5
CS1: x₂*=3/2>0 → dual constraint for x₂ is binding: 4y₁ + 2y₂ = 4 → 2y₁ + y₂ = 2

Subtract: 4y₁ = 3 → y₁ = 3/4. Then y₂ = 2 − 2(3/4) = 1/2.

Verify strong duality: W* = 24(3/4) + 6(1/2) = 18 + 3 = 21 = z* ✓.
y₁* = 3/4, y₂* = 1/2.

---

**Q27 — IP Formulation**

Four workers (A, B, C, D) must be assigned to four jobs (1, 2, 3, 4), one worker per job. The cost matrix is:

|   | Job 1 | Job 2 | Job 3 | Job 4 |
|---|-------|-------|-------|-------|
| A | 9     | 2     | 7     | 8     |
| B | 6     | 4     | 3     | 7     |
| C | 5     | 8     | 1     | 8     |
| D | 7     | 6     | 9     | 4     |

(a) Formulate as an IP. Define variables, state objective, write all constraints, declare binary variables.
(b) Add a constraint: if worker A does job 1, then worker B must do job 2.
(c) Add a constraint: worker C or worker D must do job 3 (at least one of them).

**A27**

(a) Let xᵢⱼ = 1 if worker i does job j, 0 otherwise.
i = 1(A), 2(B), 3(C), 4(D). j = 1,2,3,4.

Min Z = 9x₁₁ + 2x₁₂ + 7x₁₃ + 8x₁₄ + 6x₂₁ + 4x₂₂ + 3x₂₃ + 7x₂₄
      + 5x₃₁ + 8x₃₂ + x₃₃ + 8x₃₄ + 7x₄₁ + 6x₄₂ + 9x₄₃ + 4x₄₄

s.t.  Σᵢ xᵢⱼ = 1  ∀j = 1,...,4   (each job done by one worker)
      Σⱼ xᵢⱼ = 1  ∀i = 1,...,4   (each worker does one job)
      xᵢⱼ ∈ {0,1}

(b) If A does job 1, B must do job 2: x₂₂ ≥ x₁₁
(c) C or D must do job 3: x₃₃ + x₄₃ ≥ 1

---

**Q28 — Branch and Bound**

Solve using branch and bound. Draw the tree. At each node, state the LP relaxation result and the reason for your action (branch/prune/update best).

```
Max Z = 5x₁ + 4x₂
s.t.  6x₁ + 4x₂ ≤ 24
       x₁ + 2x₂ ≤ 6
      x₁, x₂ ≥ 0, integers
```

**A28**

Node 0 — LP relaxation: x₁=3, x₂=1.5 (fractional), Z=21. Branch on x₂.

Node 1 (x₂≤1): LP gives x₁=10/3, x₂=1, Z=52/3≈17.3. x₁ fractional. Branch on x₁.
  Node 1L (x₁≤3): x₁=3, x₂=1, Z=19. Integer! Update best = 19.
  Node 1R (x₁≥4): 6(4)+4(1)=28>24. Infeasible. Prune.

Node 2 (x₂≥2): x₁+2(2)≤6 → x₁≤2. 6(2)+4(2)=20≤24 ✓. x₁=2, x₂=2, Z=18. Integer! Z=18 < best=19. Prune (can't improve).

Optimal: x₁=3, x₂=1, Z=19.

---

**Q29 — Maximum Flow**

(a) Formulate the maximum flow LP for this network (source = A, sink = F):

```
A→B: 4    A→C: 3
B→D: 2    B→C: 1    B→E: 3
C→E: 2    C→D: 1
D→F: 3    E→F: 4
```

(b) Find the max flow using the min cut theorem. Try at least 3 cuts and identify the minimum.

**A29**

(a) Variables: xᵢⱼ = flow on each arc, v = total flow.
Max v
s.t. v = x_AB + x_AC                     (source A outflow)
     v = x_DF + x_EF                     (sink F inflow)
     x_AB = x_BD + x_BC + x_BE          (flow conservation B)
     x_AC + x_BC = x_CE + x_CD          (flow conservation C)
     x_BD + x_CD = x_DF                 (flow conservation D)
     x_BE + x_CE = x_EF                 (flow conservation E)
     All xᵢⱼ ≤ capacity, xᵢⱼ ≥ 0

(b) Cuts (forward arcs only, S→T):
- {A} vs {B,C,D,E,F}: A→B(4) + A→C(3) = 7
- {A,B,C,D,E} vs {F}: D→F(3) + E→F(4) = 7
- {A,B,C} vs {D,E,F}: B→D(2) + B→E(3) + C→D(1) + C→E(2) = 8
- {A,C} vs {B,D,E,F}: A→B(4) + C→D(1) + C→E(2) = 7 (ignore B→C since T→S)

Min cut = 7 → Max flow = 7.

---

**Q30 — Critical Path Method (CPM)**

| Activity | Duration | Predecessors |
|----------|----------|--------------|
| A        | 4        | —            |
| B        | 3        | —            |
| C        | 2        | A            |
| D        | 6        | B            |
| E        | 3        | B            |
| F        | 4        | C, D         |
| G        | 2        | E, F         |

(a) Perform the forward pass — find ES and EF for each activity.
(b) Perform the backward pass — find LS and LF for each activity.
(c) Find the float for each activity.
(d) Identify the critical path and project duration.
(e) Activity D is delayed by 2 days. How does this affect the project?

**A30**

Forward pass:
- A: ES=0, EF=4
- B: ES=0, EF=3
- C: ES=EF(A)=4, EF=6
- D: ES=EF(B)=3, EF=9
- E: ES=EF(B)=3, EF=6
- F: ES=max(EF(C),EF(D))=max(6,9)=9, EF=13
- G: ES=max(EF(E),EF(F))=max(6,13)=13, EF=15

Project duration = 15.

Backward pass:
- G: LF=15, LS=13
- F: LF=LS(G)=13, LS=9
- E: LF=LS(G)=13, LS=10
- D: LF=LS(F)=9, LS=3
- C: LF=LS(F)=9, LS=7
- B: LF=min(LS(D),LS(E))=min(3,10)=3, LS=0
- A: LF=LS(C)=7, LS=3

Float (LS − ES):
- A: 3−0=3. Not critical.
- B: 0−0=0. Critical.
- C: 7−4=3. Not critical.
- D: 3−3=0. Critical.
- E: 10−3=7. Not critical.
- F: 9−9=0. Critical.
- G: 13−13=0. Critical.

Critical path: B → D → F → G. Duration = 3+6+4+2 = 15. ✓

(e) D delayed by 2: D's float=0 → project delayed by 2 days. New duration = 17.

---

**Q31 — Graphical LP**

Consider the LP:

```
Min z = −x₁ − 3x₂
s.t.   x₁ +  x₂ ≤ 6   (C1)
       x₁        ≤ 4   (C2)
              x₂ ≤ 5   (C3)
      x₁, x₂    ≥ 0
```

(a) Draw the feasible region. Label each constraint line and shade the feasible region.
(b) Find the optimal solution using the graphical method. State x₁*, x₂*, z*.
(c) Which constraints are binding at the optimal? Which are non-binding?
(d) Write a different objective function for which the LP has multiple optimal solutions. Justify.
(e) Write an additional constraint that makes the LP infeasible. Justify.
(f) Now add constraint C4: x₁ + x₂ ≥ 5 to the original problem. Find the new optimal solution and z*.

**A31**

(a) Feasible region: bounded polygon with vertices (0,0), (4,0), (4,2), (1,5), (0,5).
    C1: x₁+x₂=6 line (binding at (4,2) and (1,5)). C2: x₁=4 (vertical). C3: x₂=5 (horizontal).

(b) Check all vertices (minimize z = −x₁ − 3x₂):
- (0,0): z=0
- (4,0): z=−4
- (4,2): z=−4−6=−10
- (1,5): z=−1−15=−16
- (0,5): z=0−15=−15

Optimal: x₁*=1, x₂*=5, z*=−16.

(c) At (1,5): C1: 1+5=6=6 binding ✓. C3: x₂=5=5 binding ✓. C2: x₁=1<4 non-binding.

(d) Multiple optima: need objective parallel to a binding edge. C1 has slope −1.
    Use objective z = −x₁ − x₂ (slope −1, parallel to C1 edge).
    At (1,5): z=−6. At (4,2): z=−6. Entire C1 edge between these points is optimal.

(e) Add x₁ + x₂ ≥ 7. All feasible points satisfy x₁+x₂≤6 (C1), so no point satisfies both. Infeasible.

(f) Add C4: x₁+x₂≥5. Original optimal (1,5): 1+5=6≥5 ✓. C4 is already satisfied — optimal unchanged.
    x₁*=1, x₂*=5, z*=−16.

---

**Q32 — Minimum Spanning Tree**

Find the minimum spanning tree for this network using Prim's algorithm. Start at node 1. Show each step — which node you add and why.

```
Edges:
1-2: 6     1-3: 1     1-4: 5
2-3: 5     2-5: 3
3-4: 5     3-5: 6     3-6: 4
4-6: 2
5-6: 6
```

State the total MST cost and list the edges included.

**A32**

| Step | Connected set | Cheapest edge to new node | Add |
|------|--------------|---------------------------|-----|
| 1 | {1} | 1-3:1, 1-4:5, 1-2:6 | 1-3 (cost 1) |
| 2 | {1,3} | 3-6:4, 3-4:5, 3-2:5, 1-4:5, 1-2:6 | 3-6 (cost 4) |
| 3 | {1,3,6} | 6-4:2, 3-4:5, 3-2:5, 1-4:5, 1-2:6 | 6-4 (cost 2) |
| 4 | {1,3,4,6} | 3-2:5 (cheapest to unconnected node 2) | 3-2 (cost 5) |
| 5 | {1,2,3,4,6} | 2-5:3 | 2-5 (cost 3) |

MST edges: 1-3, 3-6, 6-4, 3-2, 2-5. Total cost = 1+4+2+5+3 = 15.

---

**Q33 — Multiple Optimal Solutions and Degeneracy**

Given this optimal simplex tableau for a MAX LP:

| Basis | x₁ | x₂ | x₃ | s₁ | s₂ | RHS |
|-------|----|----|----|----|-----|-----|
| Z     | 0  | 0  | 4  | 3  | 0   | 24  |
| x₁    | 1  | 2  | 1  | 1  | 0   | 4   |
| s₂    | 0  | 1  | 2  | 0  | 1   | 0   |

(a) What is the optimal solution? What is z*?
(b) Is the solution degenerate? Explain what degeneracy means.
(c) Does this LP have multiple optimal solutions? How can you tell from the tableau? If yes, find a second optimal solution.

**A33**

(a) Basic: x₁=4, s₂=0. Non-basic: x₂=0, x₃=0, s₁=0. z*=24.
Full solution: x₁=4, x₂=0, x₃=0, z*=24.

(b) Yes, degenerate. s₂ is a basic variable with value 0. Degeneracy means a basic variable equals zero — more constraints are binding than necessary at this vertex. It can cause cycling in simplex and means multiple optimal bases may represent the same vertex.

(c) Yes, multiple optimal solutions. x₂ is non-basic with Row 0 coefficient = 0. In a MAX problem, a non-basic variable with Row 0 = 0 can enter the basis without changing z.

Pivot x₂ in (entering), s₂ out (min ratio: 0/1=0 vs 4/2=2 → s₂ row): degenerate pivot, x₂ enters with value 0. Same point but different basis.

For truly different point: look if x₃ also has Row 0=0 — it doesn't (coefficient=4). So the only second basis corresponds to x₁=4, x₂=0 (same vertex, different representation). No distinct second optimal vertex exists here.

---

**Q34 — Primal-Dual Relationship**

For each scenario, use the fundamental theorem of LP to determine what you can conclude about the other problem. Justify each answer.

(a) You solve the primal MAX LP and find it is unbounded. What can you say about the dual?
(b) You solve the dual MIN LP and find it is infeasible. What can you say about the primal?
(c) You find a primal feasible solution with Z = 40 and a dual feasible solution with W = 40. What can you conclude?
(d) You find a primal feasible solution with Z = 30 and a dual feasible solution with W = 45. What can you conclude?

**A34**

(a) Primal unbounded → dual is infeasible. By weak duality, any dual feasible solution would bound the primal — but no such bound exists, so the dual feasible region must be empty.

(b) Dual infeasible → primal is unbounded OR infeasible. Cannot determine which without more info. (Asymmetric case: dual infeasible does not guarantee primal unbounded alone.)

(c) Z=40=W=40. By weak duality Z≤W always. Z=W means the gap is zero → by strong duality, both are optimal. No further improvement possible for either.

(d) Z=30 < W=45. Weak duality is satisfied (Z≤W ✓). But this only confirms both are feasible — neither is confirmed optimal. The primal may still improve toward 45, and the dual may still decrease toward 30.
