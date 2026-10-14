<!-- solve-problem:ecbb0cdf-14f3-446b-b3ee-713b547e7fe7 -->
# Efficient selection of fee dimensions

A [Tangle Solve problem](https://solve.tangle.tools/problems/ecbb0cdf-14f3-446b-b3ee-713b547e7fe7).

## What is the problem?

Multidimensional fee markets (as in Ethereum EIP-4844-style designs) charge transactions separately for resources (computation, bandwidth, storage, state growth), each with its own floating price updated by a target-usage rule. A blockchain must commit to a fixed, small set of fee dimensions k (e.g., 1, 2, or 3), but the underlying set of "resources" transactions actually consume may be high-dimensional and time-varying — formalized as an evolving resource-capacity region: in each time step t, the feasible set of transaction bundles is a convex region C_t ⊆ R^m (m resources, m ≫ k).

The problem: choose a k-dimensional linear map (equivalently, a set of k aggregated resource dimensions, i.e., a k×m nonnegative matrix W) such that pricing transactions by W·x within a k-dimensional fee market approximately preserves welfare/revenue achievable with full m-dimensional pricing, across the evolution of C_t. Precisely:

> Given a sequence (or distribution) of capacity regions {C_t} in R^m and k < m, find W ∈ R^{k×m}_{≥0} maximizing the worst-case (over t) ratio of achievable performance under k-dimensional pricing to m-dimensional pricing, in polynomial time, with a provable multiplicative approximation guarantee (e.g., an α-approximation to the optimal W).

Ambiguities to resolve: the exact objective (worst-case vs. average over t; welfare vs. revenue vs. congestion control), and whether W must be an incidence matrix (each fee dimension is a subset of resources — "selection" in the strict sense) or arbitrary nonnegative combinations ("low-dimensional approximation of production polytopes," per the census).

## Why does it matter?

Every additional fee dimension costs implementation complexity, state overhead, and governance risk, so chain designers need a principled answer to "how many dimensions, and which ones?" A solution with guarantees would let EIP-style fee proposals be justified rigorously rather than by simulation, and would extend the 2025 characterization (that one dimension suffices/when it fails) to the practically relevant 2–4 dimensional regime. If it stays open, dimension choices remain ad hoc, risking either wasted complexity (too many dimensions) or unpriced congestion externalities (too few).

## What's already known?

- Lavee et al. (2025) characterize when a single fee dimension suffices: they give conditions under which one-dimensional pricing approximates multidimensional pricing well, and prove that selecting the optimal set of dimensions (for general m, k) is NP-complete. (Verify exact statement; the census record is terse.)
- The problem is listed as a research target *after* that 2025 work, so no nontrivial approximation algorithm with provable guarantees is known for general k ≥ 2 dimension selection under evolving regions.
- Related literature: low-dimensional approximation of production possibility polytopes (economics/operations research) gives techniques for projecting polytopes onto few dimensions; submodular-maximization and column-subset-selection techniques (e.g., sparse PCA, CUR decompositions) are natural analogues when the objective has suitable structure (verify whether the objective is submodular in the selected dimension set).

## What would count as solving it?

Any of the following, in increasing strength:

1. **Positive result:** a polynomial-time algorithm outputting W (or a subset of k dimensions) with a proved α-approximation guarantee for the stated objective, for general m, k, and evolving regions {C_t} — or even for the static, single-shot version (fixed C).
2. **Matching hardness:** proof that no poly-time algorithm achieves better than β-approximation (e.g., via reduction from densest-k-subgraph or sparse PCA hardness), establishing an approximation threshold.
3. **Structured regimes:** exact or FPTAS results for restricted capacity regions (e.g., separable concave production functions, polymatroids, axis-aligned boxes) or small k with evolving regions via online learning with provable regret.

A referee would want: formal objective, algorithm, proof of the guarantee, and comparison to the NP-completeness baseline from Lavee et al.

## How to start

1. Read Lavee et al. (2025), arXiv:2504.15438, end to end — especially the NP-completeness reduction (what problem it reduces from tells you which hardness assumptions to leverage) and the one-dimensional characterization (what objective/region model they use; adopt it for comparability).
2. Formalize the static problem first: fixed polytope C, subset-selection objective. Check whether the objective is monotone submodular in the chosen dimension set; if so, the (1−1/e)-greedy guarantee follows immediately for the subset variant.
3. Look at sparse linear dimensionality-reduction literature (sparse PCA, feature selection for linear regression, CUR/skeleton approximations of polytopes) for algorithmic templates.
4. Main obstacle: the evolving aspect — an W good for C_t may be poor for C_{t'}; consider online competitive analysis or robust (worst-case over a family) formulations as the extension once the static case is settled.

## Key references

- Lavee, R. et al. (2025), *Does Your Blockchain Need Multidimensional Transaction Fees?*, arXiv:2504.15438. https://arxiv.org/abs/2504.15438 — Characterizes when one-dimensional fees approximate multidimensional ones and proves NP-completeness of optimal dimension selection; the direct predecessor of this problem. (Verify author list and precise theorem statements.)
- No other specific citations are recorded in the census; the "low-dimensional approximation of production polytopes" pointer is a literature area, not a specific paper — search under that phrase and "production possibility frontier approximation."

## Warnings

- The census record is terse; the exact objective (welfare? revenue? congestion?) and region model are not pinned down, and any solution must first fix these and note the choice.
- "Selection" may mean strict subset-of-dimensions or weighted aggregation — these have very different complexity; state which you solve.
- Do not assume the problem is submodular without proof; capacity-region interactions can be highly non-submodular (this may be the source of NP-completeness).
- Ethereum-specific fee market details (EIP-1559-style target/limit controllers) matter for the dynamic model; a purely static polytope result may be seen as insufficiently answering the "evolving" clause.

---
*`BCGT-000069` · flagged for review*

## Acceptance guidance

Any of the following, in increasing strength:

1. **Positive result:** a polynomial-time algorithm outputting W (or a subset of k dimensions) with a proved α-approximation guarantee for the stated objective, for general m, k, and evolving regions {C_t} — or even for the static, single-shot version (fixed C).
2. **Matching hardness:** proof that no poly-time algorithm achieves better than β-approximation (e.g., via reduction from densest-k-subgraph or sparse PCA hardness), establishing an approximation threshold.
3. **Structured regimes:** exact or FPTAS results for restricted capacity regions (e.g., separable concave production functions, polymatroids, axis-aligned boxes) or small k with evolving regions via online learning with provable regret.

A referee would want: formal objective, algorithm, proof of the guarantee, and comparison to the NP-completeness baseline from Lavee et al.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/ecbb0cdf-14f3-446b-b3ee-713b547e7fe7?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000069.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000069)
