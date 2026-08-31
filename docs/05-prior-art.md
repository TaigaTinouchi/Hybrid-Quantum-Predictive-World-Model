# Prior Art and Related Work

This document summarizes the major research and commercial areas that overlap with the project. The goal is **not** to establish academic novelty. The goal is to identify validated mechanisms, reusable methods, and commercially useful building blocks.

A research area being mature is not a negative result for this project. If an established mechanism can improve prediction, decision quality, product strategy, or economic value, it should be reused aggressively.

## 1. Predictive state and stochastic modeling

Relevant families include:

- Hidden Markov Models (HMMs)
- Bayesian filtering
- Observable Operator Models (OOMs)
- Predictive State Representations (PSRs)
- Hilbert-space embeddings of predictive states
- Hidden Quantum Markov Models (HQMMs)
- quantum stochastic modeling

These areas support the core idea that a compact internal state can represent the predictive content of history.

## 2. Quantum stochastic modeling

A major relevant line of work studies quantum models of classical stochastic processes. Important themes include:

- quantum memory advantage over classical predictive models,
- reduced state dimension for equivalent predictive accuracy,
- memory–accuracy tradeoffs,
- unitary implementations of stochastic simulators,
- embedding stochastic simulators into open quantum dynamics.

These works provide theoretical support for investigating whether a quantum state can represent predictive information more efficiently than a constrained classical state.

## 3. Tensor networks

Matrix Product States (MPS) and Matrix Product Operators (MPO) are important both as classical baselines and as diagnostic tools.

They help answer:

> Is the candidate quantum state/operator actually hard to compress classically?

Relevant quantities include:

- Schmidt rank,
- entanglement entropy,
- bond dimension,
- operator Schmidt rank,
- bond-dimension growth under repeated updates.

Tensor networks should therefore be treated as a mandatory benchmark before claiming practical value from a QPU backend.

## 4. Koopman and operator learning

Koopman methods lift nonlinear dynamics into a linear operator acting on observables/features.

This is conceptually close to the project because both seek a state/feature representation where updates can be expressed through structured operators.

Deep Koopman approaches additionally learn the embedding and latent operator from data.

## 5. Quantum kernels

Quantum feature maps use

```math
x\mapsto |\phi(x)\rangle=U_\phi(x)|0\rangle,
```

with kernels such as

```math
K(x,x')=|\langle\phi(x)|\phi(x')\rangle|^2.
```

This project differs from a conventional static quantum-kernel setup by focusing on a persistent, event-updated predictive state rather than independently embedding each sample.

## 6. Quantum finance and amplitude estimation

Relevant work includes:

- derivative pricing with Quantum Amplitude Estimation (QAE),
- quantum Monte Carlo acceleration,
- path/stochastic-process encoding,
- qGAN-based pricing,
- quantum signal processing / payoff encoding.

These works support the use of quantum computation for expectation, payoff, and risk estimation once a useful state distribution is available.

The dominant practical caveat remains state preparation and readout/oracle construction cost.

## 7. Event-driven and multimodal finance

Classical financial ML already combines:

- price series,
- macroeconomic releases,
- news,
- event embeddings,
- knowledge graphs,
- causal features,
- LLM-derived signals.

Therefore the multimodal/event-driven side of the project should reuse strong classical methods rather than attempt to replace them with quantum preprocessing.

The hybrid hypothesis is to let classical models perform semantic extraction while structured operator dynamics manage the persistent predictive state.

## 8. Neural generation of quantum circuits

Several existing research directions overlap strongly with the neural-operator-generation idea:

- Quantum Architecture Search (QAS)
- differentiable circuit architecture search
- generative quantum eigensolvers
- conditional circuit generation
- hypernetworks that emit variational quantum-circuit parameters
- learned circuit synthesis

This means "use a neural network to design a quantum circuit" is not itself novel.

The useful project-specific formulation is instead:

> Generate event- and state-conditioned operators that recurrently update a persistent predictive world state.

## 9. Hybrid backend orchestration

Commercial and patent literature already contains many variants of:

- CPU/GPU/QPU task routing,
- cost/latency/fidelity-aware backend selection,
- circuit partitioning,
- tensor-network simulation before QPU execution,
- dynamic hybrid resource scheduling.

Therefore "select classical or quantum backend" should not be treated as a unique invention.

The commercial differentiation should instead come from:

- business-specific utility evaluation,
- predictive-value measurement,
- tensor-network compressibility diagnostics,
- QPU resource estimation,
- migration timing recommendations,
- accumulated cross-workload benchmark data.

## 10. Predictive compression, causal states, and semantic event discovery

The project's event-granularity problem has strong precedents in several research traditions.

### Predictive Information Bottleneck

Creutzig, Globerson, and Tishby, **Past-future information bottleneck in dynamical systems** (2009), formulate the trade-off between compressing the past and retaining information predictive of the future.

- DOI: https://doi.org/10.1103/PhysRevE.79.041925

This is a direct theoretical precedent for learning event representations that retain predictive information while discarding unnecessary historical detail.

### Computational Mechanics / Causal States

Shalizi and Crutchfield, **Computational Mechanics: Pattern and Prediction, Structure and Simplicity** (2001), define causal states as equivalence classes of histories that induce the same conditional future distribution.

- DOI: https://doi.org/10.1023/A:1010388907793
- arXiv: https://arxiv.org/abs/cond-mat/9907176

This supports the project's operational definition of semantic equivalence: two histories can be treated as the same state/event when doing so does not materially change the predictive distribution over relevant futures.

### Predictive Rate-Distortion

Marzen and Crutchfield, **Predictive Rate-Distortion for Infinite-Order Markov Processes** (2016), develop rate-distortion methods for lossy compression of predictive information.

- DOI: https://doi.org/10.1007/s10955-016-1520-1

This provides a natural foundation for an event-rate versus prediction-distortion curve.

### Mori-Zwanzig coarse-graining

The Mori-Zwanzig projection formalism shows that projecting a high-dimensional Markovian dynamical system onto resolved variables generally introduces memory and fluctuating terms.

A useful review/example is:

- Li et al., **Incorporation of memory effects in coarse-grained modeling via the Mori-Zwanzig formalism** (2015): https://pmc.ncbi.nlm.nih.gov/articles/PMC4644152/

This is highly relevant to the hypothesis that semantic coarse-graining can make effective event dynamics history dependent even when the underlying microscopic flow is deterministic.

### Event Segmentation Theory

Zacks et al., **Event Perception: A Mind-Brain Perspective** (2007), propose that continuous experience is segmented into events through predictive models and that increases in prediction error are associated with event boundaries.

- DOI: https://doi.org/10.1037/0033-2909.133.2.273
- Full text: https://pmc.ncbi.nlm.nih.gov/articles/PMC2852534/

This provides a conceptual precedent for learning event boundaries from surprise or predictive failure instead of fixing an event ontology in advance.

The project-specific synthesis is to combine predictive compression, learned event segmentation, reduced-state closure, and learned structured event operators in one pipeline.

## 11. Social attention, contagion, narratives, and adoption

The hypothesis that social attention can become self-reinforcing and economically consequential has extensive prior work.

This is commercially useful rather than disqualifying: it means the mechanism is sufficiently established to justify treating attention as a candidate state variable.

### Social influence and endogenous popularity

Salganik, Dodds, and Watts, **Experimental Study of Inequality and Unpredictability in an Artificial Cultural Market** (Science, 2006), showed experimentally that visible previous choices increase inequality and unpredictability of success.

- DOI: https://doi.org/10.1126/science.1121066

Muchnik, Aral, and Taylor, **Social Influence Bias: A Randomized Experiment** (Science, 2013), showed that randomly manipulated early positive ratings causally induce further positive herding.

- DOI: https://doi.org/10.1126/science.1240466

Aral and Walker, **Creating Social Contagion Through Viral Product Design** (Management Science, 2011), showed in a randomized Facebook field experiment that viral product features can causally increase peer adoption.

- DOI: https://doi.org/10.1287/mnsc.1110.1421

Commercial implication:

> Popularity and visibility can be inputs to future behavior, not merely passive measurements of intrinsic quality.

### Limited attention and viral diffusion

Weng et al., **Competition among memes in a world with limited attention** (Scientific Reports, 2012), showed that finite attention and network structure can generate large popularity differences without requiring large differences in intrinsic content value.

- DOI: https://doi.org/10.1038/srep00335

Goel et al., **The Structural Virality of Online Diffusion** (Management Science, 2016), distinguish broadcast-driven popularity from multi-generation viral diffusion.

- DOI: https://doi.org/10.1287/mnsc.2015.2158

Commercial implication:

> Attention share, velocity, persistence, cascade structure, and cross-network penetration may be more useful features than raw mention count.

### Investor attention

Barber and Odean, **All That Glitters** (Review of Financial Studies, 2008), find that retail investors disproportionately buy attention-grabbing stocks.

- DOI: https://doi.org/10.1093/rfs/hhm079

Da, Engelberg, and Gao, **In Search of Attention** (Journal of Finance, 2011), use Google search frequency as a direct attention measure and find predictive relationships with subsequent asset prices and IPO behavior.

- DOI: https://doi.org/10.1111/j.1540-6261.2011.01679.x

Commercial implication:

> Attention can be a measurable leading variable for economically relevant outcomes.

### Narrative economics

Shiller, **Narrative Economics** (American Economic Review, 2017), argues that contagious narratives can influence spending, investment, and macroeconomic fluctuations.

- DOI: https://doi.org/10.1257/aer.107.4.967

Flynn and Sastry, **The Macroeconomics of Narratives** (NBER Working Paper 32602, 2024), model narratives as contagious beliefs and report that firms change behavior after adopting narratives even when those narratives do not predict future firm fundamentals.

- DOI: https://doi.org/10.3386/w32602

Commercial implication:

> In human economies, socially circulating beliefs can become part of the effective dynamics because agents act on them.

### Network externalities and de facto standards

Attention alone is not enough to create a standard. The next stage is adoption and coordination.

Relevant foundations include:

- Katz and Shapiro, **Technology Adoption in the Presence of Network Externalities** (1986), DOI: https://doi.org/10.1086/261409
- Farrell and Saloner, **Standardization, Compatibility, and Innovation** (1985), DOI: https://doi.org/10.2307/2555589
- Arthur, **Competing Technologies, Increasing Returns, and Lock-In by Historical Events** (1989), DOI: https://doi.org/10.2307/2234208

A useful project-level chain is:

```text
attention
→ social influence
→ adoption
→ installed base
→ network value / compatibility
→ further adoption
→ possible lock-in or de facto standard
```

The commercially interesting target is early detection of when this loop becomes self-sustaining.

See [Social attention and reflexive dynamics](08-social-attention-and-reflexive-dynamics.md) for the detailed project formulation and monetization paths.

## 12. Representative prior-art areas for deeper review

The following categories should be tracked continuously:

1. Quantum stochastic modeling and quantum memory advantage
2. HQMMs and quantum predictive-state models
3. PSR/OOM/RKHS predictive representations
4. Deep Koopman / operator learning
5. Tensor-network generative and sequential models
6. Conditional neural quantum-circuit generation
7. Hybrid quantum-classical orchestration
8. QAE / quantum Monte Carlo / quantum finance
9. Event-driven multimodal financial prediction
10. Quantum-readiness / business-value assessment frameworks
11. Predictive information bottleneck / predictive rate-distortion
12. Computational mechanics / causal states
13. Mori-Zwanzig coarse-graining and memory
14. Event segmentation and change-point discovery
15. Collective attention and limited-attention diffusion
16. Social influence and information cascades
17. Narrative economics
18. Network effects, adoption tipping, and de facto standards
19. Attention-based market and demand forecasting

## 13. Patent-search conclusion so far

A preliminary search suggests that broad claims around:

- classical-vs-quantum backend selection,
- cost/latency/accuracy-based routing,
- ML-based CPU/GPU/QPU allocation,
- TN simulation followed by QPU escalation,

are already crowded.

A potentially more differentiated commercial algorithm would combine:

```text
business decision utility
+ predictive improvement
+ TN compressibility
+ QPU resource estimate
+ migration/operational cost
```

into a single backend-adoption decision.

Patent protection is not required for the project. The immediate priority is implementation quality and monetizable prediction/decision value.

## 14. Practical positioning

The project should be described as an integration of established ideas rather than as a claim that every component is new.

A useful positioning statement is:

> A commercially oriented hybrid predictive world-model architecture that reuses validated methods from predictive-state modeling, social dynamics, classical AI, tensor networks, and quantum computing whenever they improve end-to-end economic value.
