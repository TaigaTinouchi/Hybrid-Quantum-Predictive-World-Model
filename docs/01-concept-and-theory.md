# Concept and Theoretical Foundations

## 1. What is the world model here?

In this project, a world model is not a complete simulation of reality. It is a **predictive information compressor**:

```math
P(\text{future}\mid\text{history}) \approx P(\text{future}\mid z_t).
```

The state `z_t` should preserve information that matters for future prediction while discarding irrelevant history.

This connects directly to:

- state-space models,
- Kalman filtering,
- Hidden Markov Models,
- Bayesian belief states,
- Predictive State Representations (PSRs),
- Observable Operator Models (OOMs),
- RKHS / kernel mean embeddings,
- Koopman operator methods,
- tensor-network state representations,
- quantum states and quantum channels.

## 2. State as compressed history

Let

```math
h_t=(o_1,\dots,o_t)
```

be the observation history. The ideal state satisfies conditional sufficiency:

```math
Y_{\text{future}} \perp h_t \mid z_t.
```

A useful information-theoretic view is to maximize predictive information while minimizing stored history:

```math
\max I(z_t;Y_{\text{future}}),
```

while controlling

```math
I(z_t;h_t).
```

This is closely related to the Information Bottleneck principle.

## 3. Belief states and uncertainty

A world state should not be only a point estimate.

Examples:

- Kalman filter: `(μ_t, P_t)`
- Bayesian filtering: `P(s_t | h_t)`
- Quantum formulation: density matrix `ρ_t`

The state should therefore encode both:

1. what the model currently believes about the world,
2. uncertainty about that belief.

## 4. OOM / PSR viewpoint

PSRs and OOMs replace hidden physical-state semantics with operational predictive state.

Instead of asking "what is the true hidden state?", the model asks "what information about future observable outcomes must be retained?"

This is especially appropriate here because latent financial and macroeconomic factors do not need to correspond one-to-one with human-interpretable variables.

A generic linear predictive-state update is

```math
s_{t+1}=T_e s_t.
```

A readout can be

```math
y=r^\top s_t.
```

The event ordering naturally matters when operators do not commute:

```math
T_A T_B \neq T_B T_A.
```

Non-commutativity itself is not uniquely quantum; classical matrices may also be non-commutative.

## 5. RKHS and kernel mean embeddings

A distribution `P` can be embedded into a Hilbert space as

```math
\mu_P=\mathbb E_{X\sim P}[\phi(X)].
```

Then suitable expectations become linear readouts:

```math
\mathbb E[f(X)] = \langle w,\mu_P\rangle.
```

Conditional mean embeddings introduce a linear operator view of probabilistic transition:

```math
\mu_{t+1}=T_e\mu_t.
```

This is an important bridge between probabilistic world models and operator-based dynamics.

## 6. Koopman perspective

For nonlinear dynamics

```math
x_{t+1}=F(x_t),
```

the Koopman operator acts linearly on observables:

```math
(\mathcal K g)(x)=g(F(x)).
```

The key distinction is between:

- a feature set that is sufficient for one-step prediction,
- and a feature set that is closed under dynamics.

A representation may predict the next step well without supporting stable long-horizon rollout.

For practical price prediction, this supports starting with one-step or short-horizon prediction before attempting long-horizon world simulation.

## 7. Tensor-network bridge

For `n` binary factors, a generic state has `2^n` coefficients. An MPS compresses these coefficients using a bond dimension `χ`.

The minimum exact bond dimension is tied to Schmidt rank across cuts. Entanglement entropy obeys

```math
S \le \log \chi.
```

The practical interpretation is that the bond carries cross-cut/shared information. If the relevant correlation structure is low-rank, a classical MPS may be sufficient.

An MPO represents an operator. The operator Schmidt rank across cuts governs required MPO bond dimension.

The key commercial question is therefore not whether a state is "quantum-like", but whether it is classically compressible.

## 8. Quantum-state viewpoint

A pure state is

```math
|\psi\rangle,
```

with density matrix

```math
\rho=|\psi\rangle\langle\psi|.
```

A mixed state is more general:

```math
\rho=\sum_i p_i |\psi_i\rangle\langle\psi_i|.
```

A classical probability vector can be embedded as a diagonal density matrix:

```math
\rho_{\text{classical}}=\mathrm{diag}(p_1,\dots,p_n).
```

Off-diagonal terms represent coherence. This gives the quantum state a richer geometry than a plain classical probability vector, although classical models can also represent correlations with other structures.

The value of a quantum representation is therefore not automatic expressive superiority; potential value lies in computational efficiency on structured high-dimensional spaces.

## 9. Quantum channels and event updates

General state evolution should not be limited to unitary dynamics.

A quantum channel is

```math
\rho' = \mathcal E(\rho)=\sum_k K_k\rho K_k^\dagger,
```

with

```math
\sum_k K_k^\dagger K_k=I.
```

A conditioned observation update is

```math
\rho'=
\frac{K_e\rho K_e^\dagger}
{\mathrm{Tr}(K_e\rho K_e^\dagger)}.
```

This has a direct analogy to Bayesian conditioning:

```text
prior × likelihood → normalize → posterior
```

The Stinespring dilation theorem ensures that a CPTP channel can be implemented as unitary evolution on a larger system plus discarding an environment:

```math
\mathcal E(\rho)=\mathrm{Tr}_E[U(\rho\otimes|0\rangle\langle0|)U^\dagger].
```

## 10. Readout

The generic quantum readout is

```math
\hat y_q=\mathrm{Tr}(\rho_t O_q).
```

This naturally supports multiple observables from the same state.

That leads to the central benchmark idea:

> A good world state should support many useful queries without being rebuilt for each target.

Examples include WTI, gold, FX, equity indices, procurement costs, tail risk, or strategy PnL.

## 11. QAE and expectation estimation

For a distribution encoded as

```math
|\psi\rangle=\sum_i\sqrt{p_i}|i\rangle,
```

an ancilla can encode a normalized function `f(i)` so that the probability of measuring `1` is

```math
P(1)=\sum_i p_i f(i)=\mathbb E[f].
```

Plain repeated measurement still has Monte Carlo scaling `O(1/ε²)`. Quantum Amplitude Estimation can ideally reduce oracle complexity to `O(1/ε)`.

But this is only useful if state preparation and readout/oracle construction are efficient. Therefore:

```math
C_{\text{total}}
=
C_{\text{state}}
+C_{\text{dynamics}}
+C_{\text{readout}}
+C_{\text{estimation}}.
```

End-to-end cost matters more than asymptotic QAE complexity alone.

## 12. Practical interpretation

The most useful high-level interpretation is:

> Build a persistent predictive state, update it from asynchronous real-world information, answer multiple future queries, and only use quantum computing where it improves end-to-end economic value.
