# Learning and Design Notes

This file records the conceptual path that led to the current architecture and the major design conclusions reached along the way.

## 1. From linear dynamical systems to predictive state

The conceptual progression was:

```text
Linear dynamical systems
→ State-space models / Kalman filtering
→ HMM / Bayesian filtering
→ OOM
→ PSR
→ RKHS / kernel mean embedding
→ Hilbert-space predictive state
→ Koopman operator
→ Tensor networks (MPS/MPO)
→ Density matrices / quantum channels
→ Quantum kernels
→ QAE / expectation estimation
→ Hybrid neural–quantum world model
```

The recurring idea across all of these topics is:

```math
\text{history}\rightarrow\text{compressed state}\rightarrow\text{future prediction}.
```

## 2. State-space and filtering intuition

In a Kalman filter, the state estimate is not the observation itself. The pair

```math
(\mu_t,P_t)
```

represents the current best estimate and its uncertainty.

A Bayesian belief state generalizes this to

```math
P(z_t\mid x_{1:t}).
```

This established the principle that a useful world state should encode uncertainty, not only a point representation.

## 3. OOM / PSR lesson

The important shift from hidden-state modeling to predictive-state modeling is:

> The internal state does not need to correspond to a human-interpretable physical variable. It only needs to preserve information useful for predicting future observables.

This is especially relevant for markets, where latent causes are not cleanly identifiable.

## 4. Kernel mean embedding lesson

For

```math
\mu_P=\mathbb E[\phi(X)],
```

expectations can become linear readouts in an RKHS:

```math
\mathbb E[f(X)]=\langle w,\mu_P\rangle.
```

This reinforced the idea that a probability distribution can be represented as a vector-like object in a richer feature space.

## 5. Koopman lesson

A one-step predictive feature set is not necessarily closed under dynamics.

For example,

```math
x_{t+1}=x_t+y_t,
```

```math
y_{t+1}=y_t+x_t^2,
```

features `(x,y,x²)` may be sufficient to predict the next `(x,y)` but are not closed because

```math
x_{t+1}^2=x^2+2xy+y^2.
```

Practical conclusion:

> Start with one-step / short-horizon prediction before demanding long-horizon rollout consistency.

## 6. Tensor-network lesson

An `n`-bit state has `2^n` generic coefficients. MPS compresses this using bond dimension `χ`.

A product state can have `χ=1`. A GHZ-like state needs `χ=2`, even though the classical generating rule may be simple.

Therefore:

> MPS bond dimension measures complexity in that representation, not total classical computational complexity.

Schmidt decomposition provides the right intuition. Across a bipartition,

```math
|\psi\rangle=\sum_i\lambda_i|u_i\rangle_A|v_i\rangle_B.
```

The Schmidt spectrum determines compression quality. Good features should ideally make the relevant cross-factor information low-rank.

## 7. MPO lesson

MPO structure is not only compression; it is a structural hypothesis about how events couple latent factors.

A product operator

```math
T_A\otimes T_B
```

has minimal coupling complexity.

A conditionally coupled operator can require a larger operator Schmidt rank.

Practical interpretation:

> MPO bond dimension measures the non-local/conditional structure of an event transformation.

Repeated MPO application may increase MPS bond dimension. This creates an important benchmark against shallow quantum circuits.

## 8. Quantum-state lesson

A pure state

```math
|\psi\rangle
```

has density matrix

```math
\rho=|\psi\rangle\langle\psi|.
```

A classical mixture is

```math
\rho_{mix}=\sum_i p_i|i\rangle\langle i|.
```

The key distinction is that a superposition may have the same diagonal probabilities as a mixture while retaining off-diagonal coherence.

Observable readout is

```math
\langle O\rangle=\mathrm{Tr}(\rho O).
```

A diagonal observable cannot access off-diagonal coherence, which means a quantum representation is wasted if the readout cannot use the information it stores.

## 9. Quantum-channel lesson

Unitary-only dynamics are too restrictive for a real information system.

A general channel is

```math
\rho' = \sum_kK_k\rho K_k^\dagger.
```

An observed event/outcome produces a conditioned update

```math
\rho'=
\frac{K_e\rho K_e^\dagger}
{\mathrm{Tr}(K_e\rho K_e^\dagger)}.
```

This strongly resembles Bayesian filtering.

Practical design split:

- closed/natural evolution → unitary-like dynamics,
- noise/open-system change → channel,
- observed information update → instrument / conditioned channel.

## 10. Quantum-kernel lesson

For

```math
|\phi(x)\rangle=U_\phi(x)|0\rangle,
```

a common kernel is

```math
K(x,x')=|\langle\phi(x)|\phi(x')\rangle|^2.
```

Using density matrices,

```math
K(x,x')=\mathrm{Tr}(\rho_x\rho_{x'}).
```

The project extends this static-sample view into a dynamic state updated by event history.

## 11. QAE lesson

If

```math
|\psi\rangle=\sum_i\sqrt{p_i}|i\rangle,
```

and a function `f(i)` is encoded into an ancilla amplitude, then

```math
P(1)=\sum_i p_if(i)=\mathbb E[f].
```

Repeated measurement alone retains Monte Carlo scaling. QAE aims for improved error scaling, but only after state preparation and readout-oracle costs are included.

Important practical conclusion:

> QAE speedup is not end-to-end speedup.

## 12. Readout types

Two useful families emerged:

### Observable readout

```math
\hat y_q=\mathrm{Tr}(\rho O_q).
```

Useful for direct multi-target prediction.

### Expectation / payoff readout

```math
\mathbb E[f(X)]
```

Useful for:

- tail probabilities,
- expected PnL,
- option-like payoff,
- VaR/CVaR-related quantities,
- business utility.

The same world state may support both.

## 13. The key hybrid insight

The most interesting implementation idea that emerged is:

```text
real-world event
→ neural network
→ Hamiltonian / unitary / operator parameters
→ structured state update
→ predictive state
```

Instead of asking a neural network to directly predict the target, ask it to identify the operator that the event should induce.

This can be interpreted as a neural model identifying context-dependent latent dynamics.

The broader abstraction should be `Neural Operator Generator`; `Neural Unitary Generator` is a concrete first implementation.

## 14. Main strategic conclusions

1. Quantum advantage is not required.
2. Classical mathematical representations may be more flexible than physical quantum-state constraints.
3. Potential quantum value is computational, not automatically representational.
4. Strong classical and tensor-network baselines are mandatory.
5. One-step prediction is a better starting point than long-horizon rollout.
6. The world state should be query-independent and reusable across multiple targets.
7. Prediction quality and business value must be measured separately.
8. The product should be valuable before QPU execution becomes useful.
9. Neural operator generation is one of the most promising concrete technical directions.
