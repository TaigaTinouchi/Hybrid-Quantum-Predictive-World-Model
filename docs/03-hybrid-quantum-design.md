# Hybrid Neural–Quantum Design

## 1. Core idea

The most promising hybrid design is not "use a neural network and a quantum circuit together" in a generic sense.

The stronger formulation is:

> Use a classical neural network to infer the structured operator that a real-world event should induce, and use quantum-compatible dynamics to update the persistent predictive state.

The pipeline is:

```text
Real-world event / context
          ↓
    Classical NN
          ↓
Operator / Hamiltonian / circuit parameters
          ↓
 Structured state update
          ↓
 Persistent predictive state
          ↓
 Multi-query readout
```

## 2. Neural unitary generator

A direct parameter-generator design is:

```math
\theta_t=G_\phi(e_t,z_t),
```

```math
U_t=U(\theta_t),
```

```math
|\psi_{t+1}\rangle=U_t|\psi_t\rangle.
```

The circuit topology can be fixed while the neural network emits only gate parameters. This is attractive operationally because the same compiled topology can be reused while parameters change over time.

## 3. Neural Hamiltonian generator

A more structured option is to let the network emit Hamiltonian coefficients:

```math
H_t=\sum_k \alpha_k(e_t,z_t)P_k,
```

where `P_k` may be Pauli strings or another structured operator basis.

Then

```math
U_t=e^{-iH_t\Delta t}.
```

This automatically guarantees unitarity when `H_t` is Hermitian.

Example low-order structure:

```math
H_t
=
\sum_i a_i(t)Z_i
+
\sum_{i<j}b_{ij}(t)Z_iZ_j
+
\sum_i c_i(t)X_i.
```

The network can learn context-dependent interaction strengths instead of directly predicting the final target.

## 4. Interpretation

The model is not asserting that macroeconomics literally obeys a physical Hamiltonian.

Rather, the Hamiltonian is a **structured latent-dynamics parameterization**.

The conceptual benefit is that the network learns:

> "How should this event transform the current predictive state?"

instead of only:

> "What number should I predict after this event?"

This may improve reuse, stability, analysis, and portability across classical tensor-network and QPU backends.

## 5. Generalization beyond unitary dynamics

Unitary updates are reversible and preserve purity. Real information systems involve:

- forgetting,
- noisy observations,
- uncertainty growth,
- conditioning on measurements,
- unobserved variables.

Therefore the broader abstraction should be a **Neural Operator Generator**, with unitary generation as the first PoC.

General update:

```math
\rho_{t+1}=\mathcal E_{G_\phi(e_t,z_t)}(\rho_t).
```

Possible implementations include:

- unitary circuits,
- Stinespring dilations,
- Kraus-operator channels,
- measurement-conditioned instruments,
- MPO operators for classical tensor-network simulation.

## 6. Benchmark families

A useful ablation is to keep the same observation encoder and compare different transition classes.

### Generic nonlinear neural transition

```math
z_{t+1}=F_\theta(z_t,e_t).
```

### Neural matrix generator

```math
A_t=G_\theta(e_t,z_t),
```

```math
z_{t+1}=A_tz_t.
```

### Neural unitary generator

```math
U_t=G_\theta(e_t,z_t),
```

```math
z_{t+1}=U_tz_t.
```

### Neural quantum-channel generator

```math
\rho_{t+1}=\mathcal E_{\theta_t}(\rho_t).
```

This allows direct testing of whether structural constraints such as unitarity improve:

- generalization,
- numerical stability,
- long-sequence behavior,
- memory efficiency,
- multi-query reuse,
- quantum implementability.

## 7. Multi-observable readout

The same world state should support multiple observables:

```math
\hat y_a=\mathrm{Tr}(\rho_tO_a).
```

Examples:

```text
O_WTI
O_Gold
O_SP500
O_USDJPY
O_customer_procurement_cost
```

This produces a useful benchmark: does the representation encode a reusable world state, or is it merely hiding a target-specific predictor in the latent space?

## 8. Kernel connection

A static quantum feature map has the form:

```math
x\mapsto|\phi(x)\rangle=U_\phi(x)|0\rangle.
```

The usual quantum kernel is

```math
K(x,x')=|\langle\phi(x)|\phi(x')\rangle|^2.
```

Using density matrices,

```math
K(x,x')=\mathrm{Tr}(\rho_x\rho_{x'}).
```

The project generalizes the static-feature idea into a persistent event-updated state:

```math
\rho_t=\mathcal E_{e_t}\circ\cdots\circ\mathcal E_{e_1}(\rho_0).
```

A history kernel could then compare predictive states at different times.

## 9. Readout modes

Two major readout families are relevant.

### Observable readout

```math
y=\mathrm{Tr}(\rho O).
```

Useful for linear/sparse operator readouts and multi-task prediction.

### Expectation / amplitude readout

For a function `f(X)` encoded into an ancilla amplitude,

```math
P(1)=\mathbb E[f(X)].
```

This supports QAE-style estimation for payoff, risk, tail probabilities, or strategy utility.

The important practical constraint is that state preparation and readout construction must remain efficient.

## 10. Classical implementation path

The quantum-compatible design should be built first on classical infrastructure:

1. state-vector simulation for very small systems,
2. tensor-network simulation,
3. noisy circuit simulation,
4. hardware execution only after useful behavior is established.

This decouples product progress from near-term QPU limitations.

## 11. First PoC target

A practical first quantum-compatible PoC is:

- 4–8 latent qubits,
- fixed shallow ansatz,
- NN-generated circuit/Hamiltonian parameters,
- asynchronous event embeddings,
- persistent state update,
- multiple asset readouts,
- direct comparison against a classical latent transition and MPS/MPO simulation.

The purpose is not to demonstrate quantum advantage immediately. It is to determine whether this structured state-update architecture produces useful predictive behavior and whether its classical simulation complexity grows in an interesting way.
