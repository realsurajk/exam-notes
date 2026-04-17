# NP-Complete Patterns — ECE 406 Exam Reference

Every NP question is one of 5 types. Know the template for each.

---

## The Core Framework

**NP:** a problem where a YES solution can be verified in polynomial time.
**NP-hard:** every problem in NP reduces to it (at least as hard as all of NP).
**NP-complete:** NP-hard AND in NP.

To prove X is NP-complete, you need exactly two things:
1. X is in NP — give a certificate and poly-time verifier
2. X is NP-hard — show known-NP-complete ≤_p X (reduce FROM known hard problem TO X)

The arrow direction matters:
- A ≤_p B means "A reduces to B" — B is at least as hard as A
- To prove B is NP-hard: find NP-complete A and show A ≤_p B

---

## Type 1 — Certificate Question ("Is X in NP?")

**Template:**
(a) State YES or NO
(b) Certificate: what is the proposed solution? (usually a small object)
(c) Verifier: what do you check? What is the runtime?

**The certificate must be:**
- Polynomial size in the input
- Verifiable in polynomial time

**Example: Clique**
Given graph G = (V, E) and integer k, does G contain a clique of size k?

Certificate: a set S of k vertices.
Verification:
1. Check |S| = k — O(1)
2. For every pair (u, v) in S, check edge (u,v) ∈ E — O(k²) = O(V²)
Total: polynomial. YES, Clique ∈ NP.

**Example: Independent Set**
Certificate: a set S of k vertices.
Verification:
1. Check |S| ≥ k — O(1)
2. For every pair (u, v) in S, check (u,v) ∉ E — O(k²)
Total: polynomial. YES, Independent Set ∈ NP.

**Example: Vertex Cover**
Certificate: a set S of k vertices.
Verification:
1. Check |S| ≤ k — O(1)
2. For every edge (u, v) ∈ E, check u ∈ S or v ∈ S — O(E)
Total: polynomial. YES, Vertex Cover ∈ NP.

**Example: Set Cover**
Certificate: a selection of at most k sets.
Verification:
1. Check count ≤ k — O(1)
2. Check union of selected sets = B — O(nm)
Total: polynomial. YES, Set Cover ∈ NP.

**Example: Hamiltonian Path**
Certificate: an ordering of all n vertices.
Verification:
1. Check all n vertices appear exactly once — O(n)
2. Check consecutive pairs are connected by edges — O(n)
Total: polynomial. YES, Hamiltonian Path ∈ NP.

**Example: Subset Sum**
Certificate: the selected subset of numbers.
Verification:
1. Check all selected numbers are from the input — O(n)
2. Check their sum = target T — O(n)
Total: polynomial. YES, Subset Sum ∈ NP.

---

## Type 2 — Reduction Question ("Show A ≤_p B")

**Template:**
(1) Construction: given an instance of A, describe how to build an instance of B
(2) → direction: A has YES solution → B has YES solution
(3) ← direction: B has YES solution → A has YES solution

**Rules:**
- Construction must run in polynomial time
- Both directions must be proven
- The constructed B instance must be valid (satisfies B's input format)

---

### Reduction 1: Independent Set ≤_p Vertex Cover

**Key insight:** S is an independent set iff V \ S is a vertex cover.

Construction: given (G, k) for Independent Set, output (G, |V| − k) for Vertex Cover. Same graph.

→: if S is independent set of size k, then V \ S is a vertex cover of size |V| − k.
Proof: take any edge (u, v). Since S is independent, not both u,v ∈ S. So at least one of u,v ∈ V\S. V\S covers every edge. ✓

←: if C is vertex cover of size |V| − k, then V \ C is an independent set of size k.
Proof: take any two vertices u, v ∈ V\C. If (u,v) were an edge, then C would not cover it (neither endpoint in C) — contradiction. So no edge exists within V\C. Independent set of size |V| − (|V|−k) = k. ✓

---

### Reduction 2: Vertex Cover ≤_p Set Cover

Construction: given (G=(V,E), k) for Vertex Cover:
- Universe B = E (edges are elements to cover)
- For each vertex v: set Nv = {all edges incident to v}
- Same k

→: vertex cover S → sets {Nv : v ∈ S} cover all edges.
Each edge (u,w): either u ∈ S (edge in Nu) or w ∈ S (edge in Nw). ✓

←: set cover {Nv1,...,Nvk} → vertices {v1,...,vk} form a vertex cover.
Each edge (u,w) is in some Nvi → vi is an endpoint of (u,w) → edge covered. ✓

---

### Reduction 3: Dominating Set ≤_p Set Cover

Construction: given (G=(V,E), k) for Dominating Set:
- Universe B = V (vertices are elements to cover)
- For each vertex v: set Nv = {v} ∪ {all neighbors of v}  (include v itself)
- Same k

→: dominating set S → {Nv : v ∈ S} covers all vertices.
Each u ∈ S: u ∈ Nu ✓. Each u ∉ S: has neighbor v ∈ S, so u ∈ Nv ✓.

←: set cover {Nv1,...,Nvk} → {v1,...,vk} is a dominating set.
Each u not in the set: u is covered by some Nvi, so u = vi (contradiction) or u is a neighbor of vi. So u has vi as a neighbor in S. ✓

Key difference from Vertex Cover reduction: include v in Nv because a vertex dominates itself.

---

### Reduction 4: 3-SAT ≤_p Independent Set (classic)

Construction: given 3-SAT formula with clauses C1,...,Cm each with 3 literals:
- For each clause Ci = (a ∨ b ∨ c): create a triangle of 3 nodes (one per literal)
- Add conflict edges: connect xi in one triangle to ¬xi in any other triangle
- k = m (one node per clause)

→: satisfying assignment → independent set of size m.
From each clause, pick a literal that is true. Those m nodes form an independent set: no two within the same triangle (we pick one per triangle), no conflict edges (we never pick both xi and ¬xi). ✓

←: independent set of size m → satisfying assignment.
Exactly one node per triangle (since triangles have 3 nodes and k=m). Set each selected literal to true. No conflicts → consistent assignment. Every clause has its selected literal true → formula satisfied. ✓

---

### Reduction 5: Independent Set ≤_p Clique

Construction: given (G, k) for Independent Set, output (G', k) for Clique where G' is the complement of G (flip all edges/non-edges).

→: independent set S of size k in G → clique of size k in G'.
No edges in G within S → all edges present in G' within S → clique. ✓

←: clique S of size k in G' → independent set of size k in G.
All edges in G' within S → no edges in G within S → independent set. ✓

---

## Type 3 — ILP Formulation ("Reduce X to ILP")

**Template:**
1. Define binary variables (one per decision, xi ∈ {0,1})
2. Write objective (usually sum of xi)
3. Write constraints that encode the problem's rules
4. Prove → direction (solution to X → ILP solution)
5. Prove ← direction (ILP solution → solution to X)

**Standard constraint patterns:**

| Condition | Constraint |
|---|---|
| Select at most k | sum xi ≤ k |
| Select at least k | sum xi ≥ k (or −sum xi ≤ −k) |
| Select exactly k | sum xi = k |
| If u selected, v must be | xv ≥ xu |
| u and v cannot both be selected | xu + xv ≤ 1 |
| At least one of u, v must be selected | xu + xv ≥ 1 |
| Every edge (u,v) must be covered | xu + xv ≥ 1 |
| Vertex v is in dominating set or a neighbor is | xv + sum neighbors xw ≥ 1 |

**Example: Independent Set as ILP**

Variables: xv ∈ {0,1} for each v (1 = in independent set).

Max sum xv
s.t. xu + xv ≤ 1   for every edge (u,v)    [no two adjacent selected]
     xv ≤ 1         for each v
     xv ≥ 0, integer

→: IS S → set xv=1 if v ∈ S. No edge has both endpoints in S → xu+xv ≤ 1 ✓.
←: ILP solution → S={v:xv=1}. xu+xv ≤ 1 for all edges → no two adjacent → independent set ✓.

**Example: Vertex Cover as ILP**

Variables: xv ∈ {0,1} for each v (1 = in cover).

Min sum xv
s.t. xu + xv ≥ 1   for every edge (u,v)    [every edge covered]
     sum xv ≤ k
     xv ≤ 1, xv ≥ 0 integer

**Example: Dominating Set as ILP**

Variables: xv ∈ {0,1} for each v.

Min sum xv
s.t. xv + sum_{(v,w)∈E} xw ≥ 1   for each v    [v is dominated]
     sum xv ≤ k
     xv ≤ 1, xv ≥ 0 integer

Convert ≥ to ≤ for standard ILP (Ax ≤ b):
-xv - sum xw ≤ -1   for each v

---

## Type 4 — What Does a Reduction Establish?

**The logic:**

Assume problem X is NP-complete.

| What you show | What you can conclude |
|---|---|
| A ∈ NP + X ≤_p A | A is NP-complete |
| A ≤_p X | A is "no harder than" X (A ∈ NP if X ∈ NP) |
| X ≤_p A | A is NP-hard (A is at least as hard as X) |
| A ∈ NP + X ≤_p A | A is NP-complete |

**Common trick question:** if you show A ≤_p X (wrong direction), you cannot conclude A is NP-complete. You only showed A is easier than or equal to X.

**Chaining reductions:**
If X ≤_p A and A ≤_p B, then X ≤_p B (transitivity).
So if X is NP-complete: X ≤_p A ≤_p B → B is NP-hard.

---

## Type 5 — Greedy Approximation Algorithm

When the problem asks for a polynomial-time algorithm that doesn't have to be optimal.

**Template:**
1. State the greedy choice (what you pick at each step)
2. Argue it runs in polynomial time
3. Argue it produces a valid solution (not necessarily optimal)

**Example: Greedy Vertex Cover**

```
C = {}
while there exists uncovered edge (u, v):
    add both u and v to C
    remove all edges incident to u or v
return C
```

Produces a valid cover (loop terminates only when all edges covered).
Runtime: O(V + E). At most E/2 iterations.
Approximation ratio: 2 (at most twice optimal — each selected pair must include at least one optimal vertex).

**Example: Greedy Set Cover / Dominating Set**

```
S = {}
uncovered = all elements (or all vertices)
while uncovered is not empty:
    pick set Si (or vertex v) that covers the most uncovered elements
    add Si (or v) to S
    remove covered elements from uncovered
return S
```

Runtime: O(n * iterations). At most n iterations → O(n²) or O(V(V+E)).
Approximation ratio: O(log n) — won't achieve optimal but reasonable in practice.

---

## Quick Reference — NP Problem Vocabulary

| Problem | Certificate | Key relationship |
|---|---|---|
| Independent Set | set S of k vertices, no two adjacent | IS ≤_p Clique (complement), IS ↔ VC (complement) |
| Vertex Cover | set S of k vertices, every edge has endpoint in S | VC ≤_p Set Cover |
| Clique | set S of k vertices, all pairs connected | Clique ≤_p IS (complement) |
| Set Cover | k sets whose union = universe | VC ≤_p SC, DS ≤_p SC |
| Dominating Set | k vertices, every other vertex has neighbor in S | DS ≤_p SC, DS ≤_p ILP |
| Hamiltonian Path | ordering visiting all vertices once | HP ≤_p TSP |
| 3-SAT | truth assignment satisfying all clauses | 3-SAT ≤_p IS (classic starting point) |
| Subset Sum | subset summing to target T | SS ≤_p Knapsack |

---

## The 4 Questions to Ask for Any NP Problem

1. Is this a certificate question, a reduction, an ILP, or a greedy?
2. If certificate: what object proves YES, and how fast can you verify it?
3. If reduction A ≤_p B: what is the construction? Does it work in both directions?
4. If ILP: one binary variable per decision. What constraints enforce the problem's rules?
