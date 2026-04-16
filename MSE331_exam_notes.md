# MSE 331 Exam Notes

---

## 1. LP Formulation

**Standard form:**

$$\text{maximize } \mathbf{c}^\top \mathbf{x} \quad \text{subject to } A\mathbf{x} \leq \mathbf{b},\ \mathbf{x} \geq 0$$

**Four conversions to standard form:**
1. $\min \to \max$: negate objective — $\min\ c^\top x = \max\ {-c^\top x}$
2. $\geq\ \to\ \leq$: negate both sides — $ax \geq b \Rightarrow -ax \leq -b$
3. $=\ \to$ two inequalities: $ax = b \Rightarrow ax \leq b$ and $-ax \leq -b$
4. Free variable $x$: replace with $x = x^+ - x^-$, where $x^+, x^- \geq 0$ **(do this FIRST)**

---

## 2. Simplex Method

**Setup:**
- $\leq$ constraint $\to$ add slack $s$ (initial BFS uses $s$)
- $\geq$ constraint $\to$ subtract excess $e$, add artificial $a$ (Big M)
- $=$ constraint $\to$ add artificial $a$ only (Big M)

**Big M:**
- MAX objective: add $-Ma$ for each artificial $a$
- Fix Row 0 **before** iterating: $\text{Row 0} \leftarrow \text{Row 0} - M \times (\text{artificial's row})$ for each artificial
- If artificial never leaves basis $\to$ LP infeasible

**Each iteration:**
1. Entering variable: most negative Row 0 coefficient
2. Departing variable: min ratio test $= \min\left\{\frac{\text{RHS}_i}{\text{entry}_{i}} : \text{entry}_i > 0\right\}$
3. Pivot: divide pivot row by pivot element, eliminate from all other rows via ERO
4. Report BFS: list all basic variables and values
5. Stop when all Row 0 $\geq 0$

**Variable substitution:**
- $x \leq 0$: substitute $x' = -x$ (so $x' \geq 0$), replace $x$ with $-x'$ everywhere

**Multiple optimal solutions:** at least one non-basic variable has Row 0 $= 0$

**Reading from optimal tableau:**
- Basic variables: read RHS column
- Non-basic variables: $= 0$
- $z^* =$ RHS of Row 0
- Shadow prices: $y_i^* =$ Row 0 coefficient of $s_i$ (slack of constraint $i$)
- Reduced cost of $x_j =$ Row 0 coefficient of $x_j$

---

## 3. LP Duality

**Primal (Max):**

$$\max\ \mathbf{c}^\top \mathbf{x} \quad \text{s.t.}\ A\mathbf{x} \leq \mathbf{b},\ \mathbf{x} \geq 0$$

**Dual (Min):**

$$\min\ \mathbf{b}^\top \mathbf{y} \quad \text{s.t.}\ A^\top \mathbf{y} \geq \mathbf{c},\ \mathbf{y} \geq 0$$

**Construction rules:**
- One dual variable $y_i$ per primal constraint
- One dual constraint per primal variable $x_j$
- Objective $\leftrightarrow$ RHS swap; $\max \leftrightarrow \min$ flip; $\leq\ \leftrightarrow\ \geq$ flip
- Column rule: dual constraint for $x_j$ = read **column $j$** of $A$, multiply by $\mathbf{y}$, set $\geq c_j$
- Dimensions: primal ($n$ vars, $m$ constraints) $\to$ dual ($m$ vars, $n$ constraints)

**Duality theorems:**

1. **Weak duality:** $\mathbf{c}^\top \mathbf{x} \leq \mathbf{b}^\top \mathbf{y}$ for any feasible $\mathbf{x}, \mathbf{y}$
2. **Strong duality:** at optimality, $\mathbf{c}^\top \mathbf{x}^* = \mathbf{b}^\top \mathbf{y}^*$
3. **Fundamental theorem:**

| Primal | Dual |
|---|---|
| Optimal | Optimal (equal values) |
| Unbounded | Infeasible |
| Infeasible | Unbounded or infeasible |

**Complementary Slackness (CS):**

$$x_j^* > 0 \Rightarrow \text{dual constraint } j \text{ is binding (=)}$$
$$y_i^* > 0 \Rightarrow \text{primal constraint } i \text{ is binding (slack = 0)}$$

Use CS to find missing values: basic primal variable $\Rightarrow$ dual constraint tight $\Rightarrow$ solve for $y_i$; then use binding primal constraint to solve for $x_j$.

**Optimality conditions (all three must hold):**
1. Primal feasibility: $A\mathbf{x}^* \leq \mathbf{b},\ \mathbf{x}^* \geq 0$
2. Dual feasibility: $A^\top \mathbf{y}^* \geq \mathbf{c},\ \mathbf{y}^* \geq 0$
3. Complementary slackness

---

## 4. Sensitivity Analysis

**Shadow price of constraint $i$** $= y_i^* =$ Row 0 coefficient of slack $s_i$

$$z^*_{\text{new}} = z^*_{\text{old}} + y_i^* \cdot \Delta b_i$$

- Non-binding constraint $\Rightarrow y_i^* = 0$
- For $\geq$ constraint: $y_i^* = -(\text{Row 0 of excess } e_i)$

**Range of feasibility** (how much $b_i$ can change):

Let $b_i \to b_i + \delta$. For each constraint row $k$:

$$\text{RHS}_k + \delta \cdot (\text{column of } s_i \text{ in row } k) \geq 0$$

Solve for $\delta$ range. Shadow price valid only within this range.

**Reduced cost of $x_j$** $=$ Row 0 coefficient of $x_j$
- Basic variable $\Rightarrow$ reduced cost $= 0$
- Non-basic variable $\Rightarrow$ objective coefficient must improve by this amount before $x_j$ enters basis
- For MAX: positive reduced cost $=$ not worth producing

**Range of optimality** (how much $c_j$ can change):
- Basic $x_j$: let $c_j \to c_j + \delta$, update Row 0 via ERO, require all Row 0 $\geq 0$
- Non-basic $x_j$: just require updated Row 0 entry $\geq 0$

**100% rule (simultaneous changes):**

$$r_j = \frac{|\Delta c_j|}{I_j \text{ or } D_j} \quad \text{(use } I_j \text{ if increasing, } D_j \text{ if decreasing)}$$

$$\sum_j r_j \leq 100\% \Rightarrow \text{current basis remains optimal}$$

**Updating $z^*$ within allowable range:**
- $c_i$ changes by $\delta$ (basic variable): $z^*_{\text{new}} = z^*_{\text{old}} + \delta \cdot x_i^*$
- Non-basic variable: $z^*$ unchanged (since $x_j^* = 0$)

---

## 5. Integer Programming (IP)

**Formulation rules:**
- Always declare: $x_{ij} \in \{0,1\}$ explicitly
- Define ALL indices (e.g., $i=1$ Marie, $i=2$ Jack, $i=3$ Nancy)
- Assignment: $\sum_{i} x_{ij} = 1 \quad \forall j$ (each task done by exactly one person)

**Conditional constraint "if A then B":**

$$x_B \geq x_A$$

If $x_A = 1$ forces $x_B \geq 1$, i.e., $x_B = 1$. If $x_A = 0$, no restriction.

**OR constraint "at least one of A or B must hold":**

Introduce $y \in \{0,1\}$:

$$\text{Constraint A: } \ldots \leq k + My \qquad \text{(relaxed when } y=1\text{)}$$
$$\text{Constraint B: } \ldots \geq k - M(1-y) \quad \text{(relaxed when } y=0\text{)}$$

**Key mistake:** $x_A + x_B \geq 2$ forces **both** (not "at least one")

---

## 6. Branch and Bound

**For IP problems where variables must be integers.**

**Algorithm:**
1. Solve LP relaxation (ignore integrality) $\to$ upper bound for MAX
2. If all-integer $\to$ optimal
3. Pick fractional variable $x_j = f$, branch:
   - Left: add $x_j \leq \lfloor f \rfloor$
   - Right: add $x_j \geq \lceil f \rceil$
4. Solve each child LP relaxation
5. Prune if: infeasible, LP value $\leq$ current best integer, or LP is all-integer

**Pruning rules:**

| Condition | Action |
|---|---|
| LP infeasible | Prune |
| LP value $\leq$ current best (MAX) | Prune — can't improve |
| LP solution all-integer | Update best, prune |

**Key fact:** LP relaxation value $\geq$ IP optimal (for MAX). Gap closes as you branch.

---

## 7. Network Models

### Shortest Path (IP Formulation)

$$\min\ Z = \sum_{(i,j)} c_{ij} x_{ij}$$

$$\text{s.t.} \quad \sum_j x_{sj} = 1 \quad \text{(origin leaves once)}$$

$$\sum_i x_{it} = 1 \quad \text{(destination entered once)}$$

$$\sum_i x_{ik} = \sum_j x_{kj} \quad \forall k \neq s,t \quad \text{(flow conservation)}$$

$$x_{ij} \in \{0,1\}$$

### Maximum Flow

$$\max\ v$$

$$\text{s.t.} \quad v = \sum_j x_{sj} \quad \text{(source outflow)}$$

$$v = \sum_i x_{it} \quad \text{(sink inflow)}$$

$$\sum_i x_{ik} = \sum_j x_{kj} \quad \forall k \neq s,t \quad \text{(flow conservation)}$$

$$x_{ij} \leq c_{ij} \quad \forall (i,j) \quad \text{(capacity)}$$

$$x_{ij} \geq 0$$

**Max Flow = Min Cut theorem:**
- **Cut:** partition nodes into $S$ (source side) and $T$ (sink side)
- **Cut capacity** $= \sum_{i \in S,\ j \in T} c_{ij}$ (forward arcs only, ignore $T \to S$)
- $\text{Max flow} = \min \text{ cut capacity}$

### Minimum Spanning Tree (MST) — Prim's Algorithm

1. Start at any node (mark connected)
2. Add cheapest edge from connected set to any unconnected node
3. Never add edge if both endpoints already connected (creates cycle)
4. Repeat until all $n$ nodes connected ($n-1$ edges total)

### Critical Path Method (CPM)

**Forward pass:**

$$ES(\text{first}) = 0, \quad ES(j) = \max_{i \in \text{pred}(j)} EF(i), \quad EF(j) = ES(j) + d_j$$

$$\text{Project duration} = \max_j EF(j)$$

**Backward pass:**

$$LF(\text{last}) = \text{project duration}, \quad LF(i) = \min_{j \in \text{succ}(i)} LS(j), \quad LS(i) = LF(i) - d_i$$

**Float:**

$$\text{Float} = LS - ES = LF - EF$$

**Critical path:** all activities with $\text{Float} = 0$. Any delay propagates directly to project end.

---

## Key Mistakes to Avoid

1. Never skip $x_{ij} \in \{0,1\}$ declaration in IP
2. $\mathbf{b}$ and $A$ in dual come from **original LP**, not optimal tableau
3. Shadow price only valid within range of feasibility
4. One negative Row 0 entry $\Rightarrow$ tableau NOT optimal
5. Primal unbounded $\Rightarrow$ dual **infeasible** (not unbounded)
6. Non-binding constraint $\Rightarrow$ shadow price $= 0$
7. Reduced cost $= 0$ for all basic variables
8. Cut capacity: count **forward arcs only** ($S \to T$), ignore backward
9. CS: $x_j^* > 0 \Rightarrow$ dual constraint $j$ binding; $y_i^* > 0 \Rightarrow$ primal constraint $i$ binding
10. Always report $z^*$ alongside $\mathbf{x}^*$
