# Event Semantics, Coarse-Graining, and Non-Commutativity

This document develops a hypothesis that emerged from the project discussion:

> The microscopic world may evolve under a single deterministic time flow, while the **semantic events** observed by an agent arise from coarse-graining trajectory segments. Non-commutative event dynamics can then appear at the coarse-grained level because relevant context has been discarded.

This is not a claim that semantic compression is the only source of non-commutativity. Rather, it is a concrete mechanism worth testing.

## 1. Microscopic state and Laplacian determinism

Ignoring quantum effects, suppose the complete physical state of a system with `N` particles is represented in phase space by

```math
x(t)=(q_1,\ldots,q_N,p_1,\ldots,p_N)\in\Gamma.
```

An idealized Laplace's-demon observer knows `x(t)` exactly and knows the dynamical law. Let

```math
U_\tau:\Gamma\to\Gamma
```

be the time-evolution map. For an autonomous deterministic system,

```math
U_{t+s}=U_tU_s=U_sU_t.
```

Therefore, **elapsed time itself does not create an ordering problem**:

```math
U_tU_s=U_sU_t.
```

The interesting ordering structure appears only after we stop representing the complete microscopic trajectory.

## 2. Events as semantic coarse-graining of trajectory segments

At the microscopic level, there need not be a primitive object called a "rate hike", "bank failure", "invasion", or "earnings surprise". There is only a high-dimensional trajectory.

An observer instead maps a trajectory segment into a compressed semantic representation:

```math
E_i = g_\phi\!\left(x_{[t_i,t_{i+1}]},\;o_{[t_i,t_{i+1}]}\right).
```

Examples are:

- `central_bank_rate_hike`,
- `unexpected_inflation_release`,
- `supply_shock`,
- `bank_failure`,
- `military_escalation`.

The working definition is therefore:

> **An event is a predictive semantic compression of a trajectory segment.**

The event representation should not be required to preserve all microscopic information. It should preserve information that matters for future prediction and decision making.

## 3. Coarse-graining can destroy closed time evolution

To make the intuition precise, consider an idealized linear projection `P` onto resolved variables and

```math
Q=I-P
```

onto unresolved variables.

Define the coarse effective propagator

```math
K_t = P U_t P.
```

Although the microscopic propagators commute,

```math
[U_t,U_s]=0,
```

the projected propagators need not commute:

```math
[K_t,K_s]
=
P U_s Q U_t P
-
P U_t Q U_s P.
```

The non-commuting part is entirely mediated by trajectories that leave the resolved subspace through `Q` and later return.

This gives a useful interpretation:

> Apparent order dependence at the semantic level can be produced by information that was removed by coarse-graining but later influences resolved variables.

The same projection also breaks the semigroup property:

```math
K_{t+s}-K_tK_s
=
P U_t Q U_s P.
```

So even when the full dynamics is Markovian and deterministic, the coarse dynamics can become history dependent.

This is closely related to the **Mori-Zwanzig projection formalism**, where unresolved degrees of freedom reappear as memory and fluctuating terms in the reduced dynamics.

## 4. Important caveat: projection is an idealized linear picture

For real-world semantic events, the encoder is unlikely to be a literal orthogonal projector.

A more realistic architecture is

```math
z_t = C_\phi(H_t),
```

where `C_φ` compresses an observation/history into a predictive state, and

```math
e_t=G_\psi(o_{t-k:t},z_t)
```

extracts an event representation.

The reduced update is then

```math
z_{t+1}=F_\theta(z_t,e_t,\Delta t).
```

The projection derivation above should therefore be treated as a mathematical toy model explaining **how information loss can induce memory and apparent order sensitivity**, not as the final implementation.

## 5. Event order sensitivity

For general nonlinear event updates `F_A` and `F_B`, define local order sensitivity at world state `z` as

```math
\Delta_{A,B}(z)
=
F_B(F_A(z))-F_A(F_B(z)).
```

A scalar diagnostic is

```math
S_{A,B}(z)
=
d\!\left(F_B(F_A(z)),F_A(F_B(z))\right).
```

For linear operators,

```math
F_A(z)=A z,
\qquad
F_B(z)=B z,
```

this reduces to

```math
\Delta_{A,B}(z)
=
(BA-AB)z
=
[B,A]z.
```

This quantity should be distinguished from event impact:

```math
I_A(z)=d(F_A(z),z).
```

A large-impact event can commute with another event, while two individually small events can have strong order interaction.

Therefore:

```text
impact              ≠ order sensitivity
single-event effect ≠ interaction effect
```

## 6. Non-commutativity as a diagnostic, not a penalty

The project should **not** simply minimize

```math
\|[A,B]\|.
```

Real systems may genuinely contain path dependence and order effects. Penalizing all non-commutativity would erase useful structure.

Instead, commutators should initially be used as diagnostics:

```math
C_{ij}(z)=\|[A_i,A_j]z\|.
```

Potential interpretations include:

1. genuine path dependence,
2. interaction between event types,
3. missing context in the world state,
4. an event representation that is too coarse,
5. model error.

A major research question is to distinguish these causes.

## 7. Event granularity is an optimization problem

The central event-design question is:

> At what semantic granularity should the continuous world trajectory be segmented?

If events are too fine-grained, the model degenerates toward storing the original trajectory.

If events are too coarse-grained, predictive structure, ordering, and context are lost and the reduced dynamics requires long memory.

A useful objective is a **Predictive Event Bottleneck**:

```math
\mathcal L
=
\mathcal L_{\text{future}}
+
\beta\mathcal L_{\text{compression}}
+
\lambda\mathcal L_{\text{event-rate}}
+
\eta\mathcal L_{\text{closure}}
+
\gamma\mathcal L_{\text{dynamics}}.
```

### 7.1 Future-prediction distortion

For query distribution `p(q)`, use

```math
\mathcal L_{\text{future}}
=
\mathbb E_{q\sim p(q)}
\left[-\log p_\theta(Y_q\mid z_t,q)\right].
```

Using a distribution of queries rather than one target discourages event semantics that only work for a single asset or horizon.

### 7.2 Compression cost

An information-theoretic idealization is

```math
\mathcal L_{\text{compression}}
=
I(H_t;E_{\le t}).
```

This penalizes an event representation that retains unnecessary history.

In practice this can be replaced by a variational information-bottleneck bound, latent KL penalty, code-length estimate, or rate term.

### 7.3 Event-rate cost

Let `b_t` indicate an event boundary:

```math
b_t\in\{0,1\},
\qquad
N_E=\sum_t b_t.
```

Then

```math
\mathcal L_{\text{event-rate}}
=
\mathbb E\left[\frac{N_E}{T}\right].
```

This prevents a trivial solution in which every microscopic observation becomes its own event.

### 7.4 Closure / residual-memory cost

An ideal predictive state should make older history unnecessary:

```math
Y_{\text{future}}\perp H_t\mid z_t,E_{\le t}.
```

A corresponding cost is

```math
\mathcal L_{\text{closure}}
=
I(Y_{\text{future}};H_t\mid z_t,E_{\le t}).
```

This directly penalizes event/state representations that are too coarse to close the reduced dynamics.

A practical surrogate is to compare a history-aware teacher with the compressed-state predictor:

```math
\mathcal L_{\text{closure}}
\approx
D_{\mathrm{KL}}
\left(
 p_{\text{teacher}}(Y\mid H_t)
\;\|\;
 p_{\text{state}}(Y\mid z_t,E_{\le t})
\right).
```

### 7.5 Operator-consistency cost

If the project explicitly learns event operators,

```math
\hat z_{t+1}=A_{e_t,z_t}z_t,
```

then add

```math
\mathcal L_{\text{dynamics}}
=
d(z_{t+1},A_{e_t,z_t}z_t).
```

For the neural-unitary implementation,

```math
A_{e_t,z_t}
=
U_{\phi(e_t,z_t)}
=
e^{-iH_{\phi(e_t,z_t)}\Delta t}.
```

This encourages semantic event classes to correspond to reproducible effective transformations.

## 8. Operational definition of semantic equivalence

The model can define two microscopic trajectory segments as semantically equivalent when they have approximately the same predictive consequences.

A strong version is

```math
h_1\sim h_2
\iff
P(Y_{\text{future}}\mid h_1)
\approx
P(Y_{\text{future}}\mid h_2).
```

This is closely related to **causal states** in computational mechanics, which group histories according to their conditional future distributions and form minimal predictive sufficient statistics.

For this project, an operator-aware refinement is possible:

```math
h_1\sim h_2
```

when they induce both

1. similar future distributions, and
2. similar effective state transformations.

Informally:

> Two events have the same meaning when treating them as the same event does not materially damage future prediction or downstream decision value.

## 9. Event boundaries can be prediction-driven

Event Segmentation Theory in cognitive science proposes that observers maintain predictive event models and detect event boundaries when prediction error increases sharply.

That suggests a practical event-discovery mechanism:

```text
continuous observations
        ↓
short-horizon predictor
        ↓
prediction-error / surprise signal
        ↓
boundary proposal
        ↓
semantic event encoder
```

The project need not adopt the cognitive model literally, but the principle is useful:

> A meaningful boundary may be a point where the currently active predictive model stops being locally adequate.

Potential boundary signals include:

- predictive negative log-likelihood,
- Bayesian surprise,
- change-point probability,
- latent-state innovation,
- forecast-distribution shift,
- operator-regime shift.

## 10. Event granularity should be hierarchical

There is unlikely to be one universally correct event vocabulary.

For example:

```text
25 bp policy-rate increase
    ↓
monetary tightening
    ↓
liquidity contraction
    ↓
macro-financial regime shift
```

may all be valid representations at different predictive horizons.

Therefore the event encoder should eventually support a hierarchy

```math
E_t^{(1)},E_t^{(2)},\ldots,E_t^{(L)}.
```

The correct level may depend on

```math
\text{granularity}=f(z_t,q,\text{horizon},\text{available compute}).
```

However, the persistent `WorldState` should still aim to be broadly query-independent. A practical compromise is to learn event semantics against a distribution of forecast queries rather than a single query.

## 11. Rate-distortion curves as an empirical tool

Instead of selecting one value of `β` or `λ`, sweep them and measure

```text
event rate
vs
predictive distortion
vs
residual memory
vs
business utility.
```

This produces an **event rate-distortion curve**.

Interesting operating points are places where a small reduction in event complexity causes a sharp increase in predictive distortion or residual memory.

These transitions may reveal naturally useful semantic scales.

## 12. Counterfactual limitation

Order sensitivity poses an identifiability problem.

If history only contains

```text
A → B,
```

then the alternative

```text
B → A
```

is counterfactual.

Therefore

```math
F_B(F_A(z))-F_A(F_B(z))
```

cannot generally be estimated from one observed trajectory without additional assumptions, repeated environments, natural experiments, structural causal models, or a learned simulator.

Commutator-based event analysis must therefore report epistemic uncertainty rather than treating counterfactual order effects as directly observed quantities.

## 13. Relation to prior work

This direction is strongly connected to established research rather than being developed in isolation.

### Predictive Information Bottleneck

Creutzig, Globerson, and Tishby, **Past-future information bottleneck in dynamical systems** (2009), explicitly optimize the trade-off between compressing the past and retaining information predictive of the future.

- DOI: https://doi.org/10.1103/PhysRevE.79.041925

This is the closest theoretical foundation for choosing semantic event granularity by predictive value rather than human intuition alone.

### Computational Mechanics / Causal States

Shalizi and Crutchfield, **Computational Mechanics: Pattern and Prediction, Structure and Simplicity** (2001), formalize causal states as equivalence classes of histories that induce the same future distribution and show their predictive minimality properties.

- DOI: https://doi.org/10.1023/A:1010388907793
- arXiv: https://arxiv.org/abs/cond-mat/9907176

This directly motivates defining meaning through predictive equivalence.

### Predictive Rate-Distortion

Marzen and Crutchfield, **Predictive Rate-Distortion for Infinite-Order Markov Processes** (2016), connect predictive rate-distortion to causal-state structure.

- DOI: https://doi.org/10.1007/s10955-016-1520-1

Neural variational approaches have also been proposed for estimating predictive rate-distortion curves from data, which makes this direction implementable with modern neural models.

### Mori-Zwanzig coarse-graining

Mori-Zwanzig projection methods show that coarse-graining a high-dimensional Markovian dynamical system generally introduces memory and fluctuating terms into the resolved dynamics.

A useful review/example is:

- Li et al., **Incorporation of memory effects in coarse-grained modeling via the Mori-Zwanzig formalism** (2015): https://pmc.ncbi.nlm.nih.gov/articles/PMC4644152/

This is the main physical precedent for the hypothesis that discarded variables can reappear as apparent history dependence in a reduced world model.

### Event Segmentation Theory

Zacks et al., **Event Perception: A Mind-Brain Perspective** (2007), propose a prediction-driven account of how continuous experience is segmented into events, with transient prediction-error increases associated with event boundaries.

- DOI: https://doi.org/10.1037/0033-2909.133.2.273
- Full text: https://pmc.ncbi.nlm.nih.gov/articles/PMC2852534/

This provides a useful conceptual precedent for learning event boundaries rather than defining all event categories manually.

## 14. Concrete PoC proposal

Before attempting a quantum implementation, test the event-semantics hypothesis classically.

### Dataset

Use a controlled synthetic dynamical system first, where the microscopic state and full trajectory are known.

Possible systems:

- coupled oscillators,
- Lorenz-like dynamics with interventions,
- agent-based market toy model,
- synthetic macro-financial state machine.

### Models

Compare:

1. fixed-time-window events,
2. hand-defined semantic events,
3. learned segmentation + event embedding,
4. learned segmentation + neural operator generator.

### Metrics

Measure:

- future log-likelihood,
- event rate,
- latent information/code length,
- residual-memory gain from adding history,
- operator consistency,
- event-order sensitivity,
- downstream decision utility.

### Main experiment

Sweep event-compression strength and test whether an intermediate semantic scale minimizes

```math
\mathcal L
=
\mathcal L_{\text{future}}
+
\beta\mathcal L_{\text{compression}}
+
\lambda\mathcal L_{\text{event-rate}}
+
\eta\mathcal L_{\text{closure}}
+
\gamma\mathcal L_{\text{dynamics}}.
```

Then inspect whether the learned event operators develop structured commutators and whether high order-sensitivity pairs correspond to genuinely path-dependent dynamics or to missing state information.

## 15. Working hypothesis

The current hypothesis can be summarized as:

> The microscopic world follows a continuous high-dimensional trajectory. An observer compresses that trajectory into a predictive state and a sequence of semantic events. If the compression discards dynamically relevant context, the reduced event dynamics acquire memory and may become order-sensitive or non-commutative. The optimal event vocabulary is therefore the coarsest representation that preserves predictive closure and economically relevant future information.

This hypothesis provides a bridge between:

```text
microscopic dynamics
→ coarse-graining
→ semantic event discovery
→ predictive state
→ event operators
→ non-commutativity diagnostics
→ neural operator / unitary generation
```

and gives a concrete classical-first path for testing the idea before introducing tensor-network or quantum backends.
