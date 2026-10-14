<!-- solve-problem:31e5addf-3d1a-490d-a7b3-7c79ad7222d0 -->
# Retargeting under coordinated coin hopping

A [Tangle Solve problem](https://solve.tangle.tools/problems/31e5addf-3d1a-490d-a7b3-7c79ad7222d0).

## What is the problem?

**Setting.** Two or more proof-of-work chains, $i = 1, \dots, n$, each running a difficulty adjustment algorithm (DAA): at each retarget period, chain $i$ sets difficulty $D_i(t+1) = f_i(D_i(t), \text{timestamps of recent blocks})$. The intended equilibrium is $D_i \approx \alpha_i H$ where $\alpha_i$ is the fraction of total hashrate $\ H$ honestly mining chain $i$, so each chain hits its target inter-block time.

**Perturbation.** Miners can **hop**: they choose $\alpha_i(t)$ dynamically, based on stale/delayed observations of relative profitability (a miner on chain $j$ may learn $D_i$ only after a lag $\tau$), possibly in a coordinated way (a large pool moving hash mass together), and possibly subject to switching costs $c(\alpha(t), \alpha(t-1))$.

**Problem.** Formalize the coupled system
$$D_i(t+1) = f_i(D_i(t), \alpha(t-\tau_i)), \quad \alpha(t) \in \arg\max_{\text{hash allocation}} \sum_i \alpha_i(t) r_i(D(t-\tau), \cdot) - c(\cdot)$$
and determine conditions (on the DAA update rules $f_i$, the lag $\tau$, and switching costs $c$) under which the coupled dynamics converge to the fair equilibrium $D_i = \alpha_i H$ — or prove they must oscillate/diverge for broad classes of DAAs. Deliver: (a) a stability/instability theorem for the coupled switched system, and (b) ideally a DAA or retarget coordination rule (e.g., cross-chain difficulty smoothing, timestamp-robust estimators) restoring stability under bounded coordination and delay.

Ambiguity to resolve first: whether miners are modeled as best-response (instant rationality with delay) or as replicator/myopic dynamics; both are defensible — state your choice.

## Why does it matter?

Coin hopping between BTC/BCH/BSV-style shared-PoW chains is empirically documented and degrades the smaller chain's block regularity, fee markets, and security budget (fewer honest miners online when difficulty is high — a cheap attack window). A stability theorem would tell DAA designers (Monero, BCH, and any merge-mined family) exactly which retarget rules survive adversarial coordination, and would underpin safe multi-chain protocol design where hash power is a shared, mobile resource.

## What's already known?

- Single-chain results: simple DAAs (e.g., Bitcoin's 2016-block window, ASERT/cw144) can be driven into oscillation by timestamp manipulation and hashrate drops; Ilie et al. (2021) analyze breakdown conditions for one chain's controller.
- Incentive attacks: the (verify) Doger–Ulukus line analyzes miners exploiting retarget lag for excess reward — the one-hop, one-chain version of this problem.
- Control theory: DAAs are essentially delayed integral controllers; delay + best-response hopper = known Hopf-type instability in analogous coupled-oscillator settings. What's *missing* is the multi-chain coupled analysis with coordination and switching costs — no published theorem covers the coupled switched system.

## What would count as solving it?

1. **Lower bound / impossibility:** For any DAA in a natural class (windowed average of timestamps, bounded gain), exhibit a coordinated hashrate strategy $\alpha(t)$ causing sustained deviation of inter-block times by factor $\ge k$ from target (formalize $k$).
2. **Upper bound / stability:** Prove convergence (Lyapunov or ISS) of the coupled system for DAAs with gain below a threshold depending on max coordinated hash fraction $\bar\alpha$, delay $\tau$, and switching cost $c$; quantify the basin.
3. **Construction:** A modified retarget rule (or cross-chain signal) that is provably stable for $\bar\alpha < 1$ and $\tau$ up to one retarget period, with simulation support.
Any one of these is a publishable partial solution; all three is a full solve.

## How to start

1. Read Ilie et al. (2021) §3–5 for the single-chain DAA model; linearize their update as $D(t+1) - D^* = A(D(t)-D^*)$ with hashrate as exogenous input.
2. Model the hopper: simplest is proportional response $\alpha_i(t) \propto D_i(t-\tau)^{-1}$ (mine the easiest chain). The two-chain, symmetric, zero-cost version reduces to a 2D delayed linear system — apply classical delayed-feedback stability criteria ($\tau$ vs. gain product).
3. Main obstacle: nonlinearity and coordination (a pool is a single discrete agent, not a continuum), and timestamp manipulation making the controller's input itself attacker-controlled. Treat as switched/impulsive system.
4. Simulate Bitcoin-cw144 vs. ASERT on a two-chain fork simulator with a 20% coordinated pool to build intuition before proving anything.

## Key references

- Ilie, Matt, Stefan Wüster, Arthur Gervais (verify author list): *Unstable Throughput: When the Difficulty Algorithm Breaks*, arXiv:2006.03044 (2021). Analyzes how timestamp manipulation and hashrate variance drive Bitcoin-family DAAs into oscillation on a single chain. https://arxiv.org/abs/2006.03044
- Doger and Ulukus (2025), *Incentive Attacks in BTC*, arXiv:2511.11538 (verify — identifier not independently confirmed). Study miner incentive attacks exploiting retarget lag; the single-chain analog of coordinated hopping.
- Fuller & Varshavsky (verify), *An Analysis of Bitcoin Exchange Rate and Difficulty Retargeting* and the cw144/ASERT specification in the Bitcoin Cash upgrade documentation — practical DAA designs whose stability is the target of analysis.
- Background control theory: Liberzon, *Switching in Systems and Control* — the switched-systems toolkit for handling discrete hash-mass reallocations.

## Warnings

- The 2025 Doger–Ulukus citation and its arXiv ID come from the census record; verify existence before relying on it.
- Do not assume miners observe $D$ instantly — the delay is essential; without it, simple DAAs are often trivially stable and the problem collapses.
- Timestamps are self-reported by miners; any stability proof must handle falsified timestamps within consensus bounds (~2 hours in Bitcoin), or explicitly assume honest timestamps and note the gap.
- Two identical DAAs with symmetric chains can still oscillate via anti-phase hopping — don't "simplify away" the coupling.
- Real hopping has costs (orphan risk, pool reconfiguration); zero-cost models overstate instability.

---
*`BCGT-000244` · flagged for review*

## Acceptance guidance

1. **Lower bound / impossibility:** For any DAA in a natural class (windowed average of timestamps, bounded gain), exhibit a coordinated hashrate strategy $\alpha(t)$ causing sustained deviation of inter-block times by factor $\ge k$ from target (formalize $k$).
2. **Upper bound / stability:** Prove convergence (Lyapunov or ISS) of the coupled system for DAAs with gain below a threshold depending on max coordinated hash fraction $\bar\alpha$, delay $\tau$, and switching cost $c$; quantify the basin.
3. **Construction:** A modified retarget rule (or cross-chain signal) that is provably stable for $\bar\alpha < 1$ and $\tau$ up to one retarget period, with simulation support.
Any one of these is a publishable partial solution; all three is a full solve.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/31e5addf-3d1a-490d-a7b3-7c79ad7222d0?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000244.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000244)
