# Hybrid Quantum Predictive World Model

> A business-oriented world model architecture that compresses asynchronous real-world information into a persistent predictive state, answers multiple future queries, and can use classical, tensor-network, or quantum backends depending on economic value.

## Motivation

The starting idea is simple:

1. Represent the current world as an internal information state.
2. Update that state whenever a new event or observation arrives.
3. Query the same state for multiple future quantities such as prices, risks, or business costs.
4. Convert forecasts into decisions and measure economic value.
5. Treat quantum computing as an optional compute backend rather than the objective itself.

The project therefore focuses on a **profitable predictive world model**, not on using quantum computing for its own sake.

## Core formulation

Let the observation history up to time `t` be `h_t`. We want a state `z_t` such that

```math
P(\text{relevant future}\mid h_t) \approx P(\text{relevant future}\mid z_t).
```

The system is decomposed into five major layers:

```text
Observation/Event
      ↓
WorldState
      ↓
Forecast
      ↓
Decision
      ↓
Outcome / Evaluation
```

A minimal abstract interface is:

```text
x_t = encode(o_t)

z_{t+1} = update(z_t, x_t, Δt)

forecast = forecast(z_t, query)

action = decide(forecast, objective, constraints)

metrics = evaluate(forecast, action, outcome)
```

The internal representation of `WorldState` is intentionally opaque. It may be:

- a dense classical latent vector,
- a Bayesian belief state,
- a Predictive State Representation (PSR),
- an Observable Operator Model (OOM),
- an MPS / tensor network,
- a density matrix,
- or a quantum state / quantum circuit.

The external interface should remain the same.

## Event semantics and coarse-graining

A newer hypothesis in the project is that observed events should not be treated as primitive objects of the physical world.

At an idealized microscopic level, the world follows a continuous high-dimensional trajectory. Human- or model-level events such as `rate_hike`, `bank_failure`, or `supply_shock` are **semantic coarse-grainings of trajectory segments**.

The key design problem becomes:

> What is the coarsest event representation that preserves predictive closure and economically relevant future information?

If the event representation is too fine, the model simply memorizes the original trajectory. If it is too coarse, discarded context can reappear as memory, path dependence, or effective non-commutativity between event operators.

The project therefore treats event granularity as a learnable rate-distortion problem rather than a fixed ontology.

## Hybrid quantum hypothesis

One of the most interesting implementation directions is to use a classical neural network as an interface between real-world information and structured quantum dynamics:

```text
Real-world event / context
          ↓
    Classical NN
          ↓
Hamiltonian / circuit parameters
          ↓
     Quantum circuit
          ↓
 Persistent world state
          ↓
 Observable / QAE readout
```

For example,

```math
\theta_t = G_\phi(e_t, z_t),
```

```math
H_t = \sum_k \alpha_k(e_t,z_t) P_k,
```

```math
U_t = e^{-iH_t\Delta t},
```

```math
|\psi_{t+1}\rangle = U_t |\psi_t\rangle.
```

This makes the neural network responsible for **identifying the operator that an event should induce**, rather than directly producing the final prediction.

A more general version replaces unitary dynamics with a learned quantum channel:

```math
\rho_{t+1}=\mathcal E_{e_t}(\rho_t).
```

## Multi-query world state

The same state should support multiple readouts:

```math
P(Y_q\mid z_t),
```

where a query can specify a target, horizon, and requested quantity.

Examples:

- WTI 1-day return distribution
- USD/JPY 5-day downside probability
- Gold tail risk
- expected procurement cost for a customer
- expected PnL of a strategy

The objective is therefore not a single-target predictor but a **query-independent predictive state**.

## Business-first principle

The system is evaluated at three levels:

1. **State quality** — does the state preserve useful predictive information?
2. **Forecast quality** — calibration, likelihood, error, tail prediction, etc.
3. **Decision value** — PnL, cost reduction, risk reduction, or other business utility.

The central principle is:

```math
\text{Forecast quality} \neq \text{Business value}.
```

Quantum computing is used only when it improves an end-to-end objective such as accuracy, latency, memory, scenario-evaluation cost, or economic utility.

## PoC philosophy

The recommended progression is:

```text
Business problem
      ↓
Strong classical baseline
      ↓
Quantum-compatible model
      ↓
Tensor-network benchmark
      ↓
Quantum simulator
      ↓
QPU
      ↓
End-to-end business evaluation
```

At every stage, the project may stop and keep the best classical solution. A quantum result is not required for the project to create value.

## Documentation

- [Concept and theoretical foundations](docs/01-concept-and-theory.md)
- [System architecture and interfaces](docs/02-system-architecture.md)
- [Hybrid neural–quantum design](docs/03-hybrid-quantum-design.md)
- [PoC and commercialization strategy](docs/04-poc-and-commercialization.md)
- [Prior art and related work](docs/05-prior-art.md)
- [Learning and design notes](docs/06-learning-and-design-notes.md)
- [Event semantics, coarse-graining, and non-commutativity](docs/07-event-semantics-and-coarse-graining.md)
- [Implementation roadmap](ROADMAP.md)

## Current status

This repository currently documents the architecture and hypotheses developed through exploratory discussion. The next engineering milestone is a small classical-first PoC with a pluggable `WorldState` backend, including a controlled experiment on learned event granularity, followed by a 4–8 qubit quantum-compatible implementation and tensor-network comparison.
