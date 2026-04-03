# 0/1 Knapsack Visualizer — Interactive Teaching Tool

**Course:** BCS 309 — Algorithms I | **Instructor:** Dr. Arash Kermani | **Student:** Sarah Alzaidi (20220002575)

> **Live Demo:** [https://saraammarhusham.github.io/knapsack-visualizer/](https://saraammarhusham.github.io/knapsack-visualizer/)
> **Repository:** [https://github.com/saraammarhusham/knapsack-visualizer](https://github.com/saraammarhusham/knapsack-visualizer)

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Algorithm Coverage](#algorithm-coverage)
3. [Complexity Analysis (CLO-1)](#complexity-analysis-clo-1)
4. [Algorithm Application & Tool Correctness (CLO-2)](#algorithm-application--tool-correctness-clo-2)
5. [Design Paradigm & Correctness (CLO-3)](#design-paradigm--correctness-clo-3)
6. [Site Structure & Features](#site-structure--features)
7. [How to Use the Visualizer](#how-to-use-the-visualizer)
8. [Worked Examples](#worked-examples)
9. [Comparison of All Three Approaches](#comparison-of-all-three-approaches)
10. [References](#references)

---

## Project Overview

This tool is an interactive, educational web application for understanding the **0/1 Knapsack Problem** — one of the most important optimization problems in computer science. It teaches three algorithmic approaches (Brute Force, Dynamic Programming, and Greedy), demonstrates why Dynamic Programming is the correct and efficient choice for this problem, and visualizes the DP table construction step by step.

### What is the 0/1 Knapsack Problem?

Given a set of items, each with a **weight** and a **value**, and a knapsack with a fixed **capacity W**, choose a subset of items to **maximize total value** without exceeding capacity.

The "0/1" constraint means each item is either taken in full (1) or not taken at all (0) — no fractional amounts.

**Formal definition:**

- **Input:** n items with weights w₁, w₂, …, wₙ and values v₁, v₂, …, vₙ; knapsack capacity W
- **Output:** A subset S ⊆ {1, …, n} such that Σᵢ∈S wᵢ ≤ W and Σᵢ∈S vᵢ is maximized

**Real-world applications:**
- Project budgeting (selecting projects within a budget)
- Cargo loading (which boxes fit within a truck's weight limit)
- Feature selection in software development (maximizing features within a time constraint)
- Portfolio investment (selecting assets within a fixed total cost)

---

## Algorithm Coverage

This project covers **three algorithmic approaches** to the 0/1 Knapsack problem, fulfilling the requirement to address Dynamic Programming, Brute Force, and Greedy (for Fractional Knapsack):

| Approach | Paradigm | Time Complexity | Space | Optimal for 0/1? |
|---|---|---|---|---|
| Brute Force | Exhaustive Search | O(2ⁿ) | O(n) | Yes |
| Dynamic Programming | DP / Bottom-Up Table | O(n·W) | O(n·W) | Yes |
| Greedy | Greedy (ratio-based) | O(n log n) | O(n) | **No** |

---

## Complexity Analysis (CLO-1)

### 1A & 1B — Time and Space Complexity with Full Derivation

#### Dynamic Programming — O(n·W) Time, O(n·W) Space

**Derivation:**

The DP solution builds a 2D table `K[i][w]` of size `(n+1) × (W+1)`.

- The outer loop iterates over `i = 1 to n` → **n iterations**
- The inner loop iterates over `w = 0 to W` → **W+1 iterations**
- Each cell performs a constant-time comparison: `max(K[i-1][w], vᵢ + K[i-1][w-wᵢ])`

**Total work = n × (W+1) = O(n·W)**

There is **no early exit** — all three cases (best, average, worst) require filling the entire table:

| Case | Complexity | When It Occurs |
|---|---|---|
| Best Case | O(n·W) | All item weights > W (copy row without include branch) |
| Average Case | O(n·W) | Mixed item weights; some fit, some don't |
| Worst Case | O(n·W) | All items weight = 1, W is large; maximum include/exclude comparisons |

**Space Complexity:** The table stores (n+1)×(W+1) integer values → **O(n·W)**. This can be reduced to **O(W)** using a 1D rolling array if only the final answer (not the traceback) is needed.

#### Brute Force — O(2ⁿ) Time, O(n) Space

**Derivation:**

With n items, there are **2ⁿ possible subsets** (each item is either included or excluded — a binary decision tree with n levels).

- For each of the 2ⁿ subsets: compute total weight and value → O(n) work per subset
- **Total: O(n · 2ⁿ)** = **O(2ⁿ)** asymptotically

**Space:** Only the current subset needs to be tracked → O(n) stack/recursion depth.

At n=20: ~1,000,000 subsets. At n=30: ~1,000,000,000 subsets. Completely impractical beyond n ≈ 20.

#### Greedy — O(n log n) Time, O(n) Space

**Derivation:**

1. Compute value-to-weight ratio vᵢ/wᵢ for each item → O(n)
2. Sort items by ratio in descending order → **O(n log n)** (dominates)
3. Iterate through sorted list, greedily adding items that fit → O(n)

**Total: O(n log n)** (sorting dominates)

**Space:** O(n) for the sorted array.

**Why Greedy Fails for 0/1 Knapsack:**

Greedy picks the highest ratio item first, but this can block combinations of items that together yield a higher total value. Example: W=4, items = {(w=3,v=4), (w=2,v=3), (w=2,v=3)}. Greedy picks item 1 (ratio 1.33), leaving only w=1 unused — value=4. Optimal picks items 2+3 — value=6. Greedy is **only correct for Fractional Knapsack** where items can be split.

#### Recurrence Relation

The DP recurrence is:

```
K[i][w] = 0                                         if i = 0 or w = 0
K[i][w] = K[i-1][w]                                 if wᵢ > w  (item too heavy — exclude)
K[i][w] = max(K[i-1][w],  vᵢ + K[i-1][w - wᵢ])    if wᵢ ≤ w  (choose best of include/exclude)
```

**Base cases:** `K[0][w] = 0` for all w (no items → no value); `K[i][0] = 0` for all i (zero capacity → no value).

**Answer:** `K[n][W]`

#### Why DP Beats Brute Force

Both are **correct** (always find the optimal answer). DP wins on efficiency by exploiting two key properties:

1. **Optimal Substructure:** The optimal solution to `K[i][w]` depends only on optimal solutions to `K[i-1][w]` and `K[i-1][w-wᵢ]`. The global optimum is safely built from smaller optimal sub-answers.

2. **Overlapping Subproblems:** A naive recursive approach recomputes the same sub-answers across multiple branches. DP stores each answer in the table and retrieves it in O(1), replacing exponential re-exploration with a single O(nW) pass.

> **Key insight:** Brute force explores all 2ⁿ subsets. DP compresses this into an (n+1)×(W+1) table because many branches in the decision tree ask the same sub-questions — and DP answers each question only once.

> **Common mistake:** DP is often confused with Divide & Conquer. The critical difference: D&C subproblems are **independent** (e.g., merge sort). DP subproblems **overlap** — the same `K[i][w]` is needed by multiple parent cells.

---

## Algorithm Application & Tool Correctness (CLO-2)

### 2A — Correct Application on Multiple Inputs (Including Edge Cases)

The tool supports all of the following input types, which can be entered manually or loaded via presets:

**Standard inputs:**
- Classic: 4 items, W=8 → demonstrates standard DP table construction and traceback
- Abdul Bari: 3 items (w=2,v=6 | w=2,v=10 | w=3,v=12), W=5 → canonical textbook example yielding value=22 by selecting items 2 and 3

**Edge cases demonstrated:**

| Preset | Items | W | Purpose |
|---|---|---|---|
| Greedy Fails | 3 items | 4 | Demonstrates greedy giving suboptimal answer |
| Worst Case | 6 items | 10 | All items fit at all capacities; maximum DP work per cell |
| Tight Fit | 5 items | 12 | Items barely fit; tests boundary conditions |
| Large | 8 items | 20 | Larger input demonstrating scalability |

**When to use each algorithm:**
- Brute Force: Only for n ≤ 15, or as a correctness baseline to verify other algorithms
- Dynamic Programming: Standard approach when W is reasonably bounded — correct and efficient
- Greedy: Only correct for **Fractional Knapsack** (where partial items are allowed); never use for 0/1 Knapsack

**Preconditions:** All weights and values must be positive integers. W must be a positive integer.

### 2B — Tool Correctness & Completeness

The visualizer correctly implements the 0/1 Knapsack DP algorithm with no bugs or misleading displays:

- **DP Table Construction:** The full (n+1)×(W+1) table is built bottom-up. Each cell is computed using the correct recurrence, and the computation is displayed in real time.
- **Traceback:** After the table is filled, the traceback procedure correctly walks from `K[n][W]` back to `K[0][0]`, identifying exactly which items were selected.
- **Live Statistics Panel:** Tracks Lookups, Comparisons, Cells Filled, and Best Value in real time as the animation progresses.
- **Recurrence Walkthrough:** Clicking any filled cell shows the exact include/exclude decision made for that cell, with the values substituted.
- **Pseudocode Panel:** Displays the DP pseudocode with the currently executing line highlighted, mapping abstract code to concrete execution.
- **Step History:** A log of every cell filled, showing Item #, capacity w, value K[i][w], and the Include/Exclude decision made.
- **Item Statistics Bar:** Displays selected items, total value, and total weight vs capacity after traceback.

### 2C — Pedagogical Value & Interactivity

The tool is fully interactive with rich educational annotations:

**Execution Controls:**
- **Build (⚙):** Initializes the table from current input and prepares the animation
- **Step (▷):** Advances one cell at a time — the learner sees exactly which cell is being computed and why
- **Play (▶):** Auto-animates through the full table fill
- **Pause (⏸):** Freezes execution at any point for closer inspection
- **Reset (↻):** Returns to initial state for re-running with different inputs
- **Speed Control:** Adjustable playback speed (multiplier)

**Custom Input:**
- Users can add/remove items manually (name, weight, value)
- Knapsack capacity W is freely adjustable
- Six preset examples load instantly for comparison

**Educational Annotations:**
- Complexity display (O(nW) shown prominently, updated with actual n and W values)
- Status indicator (IDLE / BUILDING / TRACEBACK / DONE)
- Color-coded table legend: Include (active), Exclude (active), Compare (active), Included, Traceback, Filled
- Step Explanation panel: plain-language description of every decision made
- Recurrence Walkthrough panel: clicking any cell shows the mathematical formula with substituted values
- Pseudocode highlighting: the line currently executing is highlighted
- Growth Curve chart (Comparison tab): interactive chart showing O(n·W) vs O(2ⁿ) divergence with adjustable n and W
- Sorting & Searching accordion: explains how sorting drives Greedy and how DP uses array lookup

---

## Design Paradigm & Correctness (CLO-3)

### 3A — Problem Definition & Algorithm Design Description

**Problem Input:** n items, each with integer weight wᵢ > 0 and integer value vᵢ > 0; integer capacity W > 0

**Problem Output:** A subset S ⊆ {1, …, n} maximizing Σᵢ∈S vᵢ subject to Σᵢ∈S wᵢ ≤ W

**Constraints/Objective:** Binary inclusion (0 or 1 per item); capacity is a hard upper bound; maximize total value

**Algorithm Design — Why This Structure:**

The DP table structure arises directly from the problem's subproblem structure. Define `K[i][w]` = "best value using the first i items with capacity w." This is the natural sub-question that must be answered to build up to the full answer `K[n][W]`.

- **Why rows represent items:** We process items one by one. Once we decide about item i, we only need the row for items 0..i-1 to make future decisions — a clean sequential dependency.
- **Why columns represent capacities:** Capacity is the binding constraint. By storing answers for every possible remaining capacity (0 to W), we avoid recomputing what's achievable at each capacity sub-level.
- **Why bottom-up:** We fill base cases first (row 0 and column 0 = 0), then build upward. Each cell depends only on the row above it — so left-to-right, top-to-bottom is a valid fill order.
- **Why this data representation:** A 2D array gives O(1) lookup for previously computed values, which is what enables the polynomial time bound.

### 3B — Design Paradigm & Correctness Argument

**Design Paradigm: Dynamic Programming**

DP applies here because of two structural properties of the problem:

**1. Optimal Substructure**

The optimal solution to `K[i][w]` is built from optimal sub-solutions:
- If item i is **not included** in the optimal solution for capacity w with i items, then the remaining items must be optimal for capacity w with i-1 items → `K[i-1][w]`
- If item i **is included** in the optimal solution, then the remaining items must be optimal for capacity `w - wᵢ` with i-1 items → `vᵢ + K[i-1][w - wᵢ]`

Taking `max` of these two cases is safe — no better option exists because we have enumerated all possibilities for item i.

**Formal inductive proof sketch:**
- Base: `K[0][w] = 0` (no items → value is 0, trivially optimal)
- Inductive step: Assume `K[i-1][·]` holds all optimal values for i-1 items. Then `K[i][w] = max(K[i-1][w], vᵢ + K[i-1][w-wᵢ])` is the maximum value achievable, since item i is either in or out and both options use optimal sub-answers by the inductive hypothesis.

**2. Overlapping Subproblems**

Without memoization, recursive descent from `K[n][W]` recomputes the same `K[i][w]` values exponentially many times. DP computes each cell exactly once and stores it → O(nW) total.

**What breaks if assumptions are violated:**
- If items had **negative values**, the recurrence still holds — the exclude option would be preferred
- If **fractional items** were allowed, the greedy approach (sort by v/w ratio, take fractions) becomes optimal and DP is unnecessary overhead
- If **item counts > 1** (unbounded knapsack), the recurrence changes: `K[i][w] = max(K[i-1][w], vᵢ + K[i][w-wᵢ])` — note the second term references row i, not i-1

**Why not Divide & Conquer?** D&C requires independent subproblems. If we split the item set in half and solve each half independently, the optimal combination of both halves is not guaranteed to equal the globally optimal solution — the knapsack capacities interleave across both halves. The subproblems are not independent.

**Why not Greedy?** Greedy requires the greedy choice property: a locally optimal choice is always safe. For 0/1 Knapsack, this fails — taking the highest ratio item can leave a "gap" in the remaining capacity that no single remaining item fills well, while two smaller items would have combined better. The greedy choice is not always globally safe.

### 3C — Design Depth: Variations & Comparisons

**Variation 1 — Space Optimization to O(W):**

Since each row of the DP table depends only on the row directly above it, we can maintain a single 1D array and update it in reverse (right to left):

```
for i = 1 to n:
    for w = W downto wᵢ:
        K[w] = max(K[w], vᵢ + K[w - wᵢ])
```

Reverse iteration prevents using the same item twice (which would give the unbounded knapsack). This reduces space to O(W) while keeping O(nW) time.

**Variation 2 — Unbounded Knapsack:**

If each item can be taken multiple times, change the recurrence from `K[i-1][w-wᵢ]` to `K[i][w-wᵢ]` — iterating the inner loop forward instead of backward. This allows the same item to be included multiple times.

**Variation 3 — What if W is very large?**

When W is extremely large (e.g., W = 10⁹), O(nW) becomes infeasible. In this case, alternative approaches are used:
- **Meet-in-the-middle:** Split items into two halves, enumerate all 2^(n/2) subsets for each half, then combine → O(2^(n/2) · n)
- **FPTAS (Fully Polynomial-Time Approximation Scheme):** Scale and round item values to reduce the effective capacity

**Comparison — DP vs Brute Force vs Greedy:**

| Property | Brute Force | Dynamic Programming | Greedy |
|---|---|---|---|
| Correctness for 0/1 | ✓ Always optimal | ✓ Always optimal | ✗ Can fail |
| Time | O(2ⁿ) — exponential | O(n·W) — pseudo-polynomial | O(n log n) — fast |
| Space | O(n) | O(n·W) or O(W) | O(n) |
| Practical limit | n ≤ 15–20 | W ≤ ~10⁶ | Any n |
| When to use | Tiny inputs / verification | Standard choice | Fractional Knapsack only |

**NP-Completeness note:** The 0/1 Knapsack decision problem (is there a subset with value ≥ V and weight ≤ W?) is NP-complete. The DP solution is **pseudo-polynomial** — O(nW) is polynomial in n and W, but W can be exponential in the number of bits used to represent it. This is why there is no known polynomial-time algorithm for 0/1 Knapsack in the strong sense.

---

## Site Structure & Features

The website is organized into five sections with a recommended learning order:

```
Home → Examples → Methods → Visualizer → Comparison → Resources
```

### Home
- Definition of optimization problems and the 0/1 Knapsack problem
- Motivation with real-world applications
- Guided learning path with recommended order of exploration

### Examples Tab
- Complete step-by-step walkthrough of the **Abdul Bari** example (3 items, W=5)
- Full DP table construction explained row-by-row
- Traceback procedure shown explicitly
- **Brute Force Decision Tree** visualization for a 4-item example showing all 2⁴ = 16 leaf nodes

### Methods Tab
- Side-by-side explanation of all three approaches
- Complexity cards with Time, Space, Optimal, and Practical ratings
- Expandable sections:
  - **Optimal Substructure & Overlapping Subproblems** — why DP works
  - **The Include/Exclude Recurrence** — full recurrence with base cases
  - **Sorting & Searching in Knapsack** — how sorting drives Greedy, how DP uses array lookup

### Visualizer Tab (Core Interactive Feature)
- **Items Input Table:** Add/remove items with name, weight, value
- **Capacity Control:** Set W freely
- **6 Preset Examples:** Load instantly for comparison
- **Playback Controls:** Build / Step / Play / Pause / Reset + Speed control
- **Live Statistics:** Lookups, Comparisons, Cells Filled, Best Value — update in real time
- **DP Table K[i][w]:** Color-coded cells with live animation
- **Table Legend:** Color key for Include/Exclude/Traceback states
- **Item Statistics Bar:** Selected items, total value, total weight vs capacity
- **Step Explanation Panel:** Plain-language explanation of every cell decision
- **Recurrence Walkthrough Panel:** Click any cell → see formula with substituted values
- **Pseudocode Panel:** Synchronized code highlighting
- **Step History Log:** Full record of every cell filled with item, capacity, value, and decision

### Comparison Tab
- **Approach Comparison Table:** All three algorithms side by side
- **DP Case Analysis:** Best/Average/Worst case explanation with interactive presets
- **Growth Curve Chart:** Interactive O(nW) vs O(2ⁿ) chart with adjustable W and n
- **Live Operation Count:** Updates as you step through the Visualizer
- **Key Differences Summary:** Bullet-point pedagogical summary
- **Formal Recurrence & Correctness Proof:** Expandable inductive proof

### Resources Tab
- Recommended lecture (Abdul Bari — Dynamic Programming)
- Background reading links

---

## How to Use the Visualizer

**Step 1 — Load an example or enter your own input**

Either click one of the six preset buttons (Classic, Greedy Fails, Worst Case, Tight Fit, Abdul Bari, Large), or manually type items into the input table and set capacity W.

**Step 2 — Build the table**

Click **⚙ Build**. The table dimensions are set based on your input. The status changes from IDLE → BUILDING.

**Step 3 — Step through or play**

- Click **▷ Step** once per cell to advance manually — read the Step Explanation panel after each step to understand the decision made
- Click **▶ Play** to auto-animate — use **⏸ Pause** to freeze at any moment
- Adjust speed with the multiplier control

**Step 4 — Explore the completed table**

- Click any filled cell in the DP table → the **Recurrence Walkthrough** panel shows the exact formula and values for that cell
- The **Step History** log at the bottom records every decision — click any row to jump back to that state
- After traceback completes, the **Item Statistics Bar** shows which items were selected

**Step 5 — Compare with Greedy**

Load the **Greedy Fails** preset. Observe that greedy (sorting by v/w ratio) would pick item 1, leaving insufficient capacity for the optimal combination of items 2+3. The DP solution finds the correct optimum.

---

## Worked Examples

### Example 1 — Abdul Bari (Canonical DP Example)

**Items:** i1(w=2, v=6), i2(w=2, v=10), i3(w=3, v=12) | **Capacity W=5**

| | w=0 | w=1 | w=2 | w=3 | w=4 | w=5 |
|---|---|---|---|---|---|---|
| i=0 (no items) | 0 | 0 | 0 | 0 | 0 | 0 |
| i=1 (w=2,v=6) | 0 | 0 | 6 | 6 | 6 | 6 |
| i=2 (w=2,v=10) | 0 | 0 | 10 | 10 | 16 | 16 |
| i=3 (w=3,v=12) | 0 | 0 | 10 | 12 | 16 | **22** |

**Traceback from K[3][5]=22:**
- K[3][5]=22 ≠ K[2][5]=16 → **Item 3 included** → move to K[2][5-3]=K[2][2]
- K[2][2]=10 ≠ K[1][2]=6 → **Item 2 included** → move to K[1][2-2]=K[1][0]
- K[1][0]=0 = K[0][0]=0 → **Item 1 excluded**

**Result:** Items {i2, i3} selected. Total value = 22. Total weight = 5/5. ✓

### Example 2 — Greedy Fails (Edge Case)

**Items:** A(w=3,v=4), B(w=2,v=3), C(w=2,v=3) | **Capacity W=4**

Greedy sort by ratio: A=1.33, B=1.50, C=1.50 → greedy order: B, C, A

Greedy picks B (w=2, fits) → then C (w=2, total w=4, fits) → value=6 ✓ (happens to work here)

Try: A(w=3,v=5), B(w=2,v=3), C(w=2,v=3) | **Capacity W=4**

Greedy picks A (ratio=1.67, fits w=3) → B doesn't fit (w=2, total=5>4) → **Greedy value=5**

DP picks B+C (w=2+2=4 ≤ 4, value=3+3=6) → **DP value=6** ✓

This is why greedy cannot guarantee optimality for 0/1 Knapsack.

---

## Comparison of All Three Approaches

### At a Glance

```
Brute Force:  Enumerate all 2ⁿ subsets → correct but exponential → only n ≤ 20
DP:           Fill (n+1)×(W+1) table bottom-up → correct and O(nW) → standard choice
Greedy:       Sort by v/w, pick greedily → fast but incorrect for 0/1 → Fractional only
```

### Why DP Is the Right Tool

The 0/1 Knapsack problem has **both** properties that justify DP:
1. **Optimal Substructure** — the globally optimal solution is composed of locally optimal sub-solutions
2. **Overlapping Subproblems** — the same sub-problems recur across different branches

When both properties hold, DP compresses exponential recursion into polynomial table-filling. This is the formal justification for choosing DP over Brute Force or Greedy.

---

## References

- Abdul Bari — Dynamic Programming: 0/1 Knapsack Problem (YouTube lecture — linked in Resources tab)
- CLRS — *Introduction to Algorithms*, Chapter 15 (Dynamic Programming)
- Kleinberg & Tardos — *Algorithm Design*, Chapter 6 (Dynamic Programming)
- GitHub Pages deployment: [https://saraammarhusham.github.io/knapsack-visualizer/](https://saraammarhusham.github.io/knapsack-visualizer/)
- Source code: [https://github.com/saraammarhusham/knapsack-visualizer](https://github.com/saraammarhusham/knapsack-visualizer)
