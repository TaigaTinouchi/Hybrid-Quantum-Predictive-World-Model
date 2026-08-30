# Roadmap

## Goal

Build a commercially useful predictive world model whose internal backend can evolve from classical models to quantum-compatible and quantum implementations without changing the external product interface.

## Milestone 0 — Problem definition

- Select first target domain.
- Prefer one-step / short-horizon prediction.
- Define at least 3–5 readout targets.
- Define business-value metrics before model training.
- Lock data timestamp semantics to prevent leakage.

**Exit condition:** clear target, forecast query schema, evaluation protocol, and business metric.

## Milestone 1 — Classical baseline

Implement:

- event normalization,
- modality-specific encoders,
- opaque `WorldState`,
- `update`, `forecast`, `decide`, `evaluate` interfaces,
- strong probabilistic classical baseline.

Candidate transition models:

- recurrent/Transformer baseline,
- state-space model,
- operator/Koopman-inspired model.

**Exit condition:** reproducible backtest with calibrated forecasts and measurable business utility.

## Milestone 2 — Multi-query world state

Test whether one state can support multiple targets better than independent target-specific models.

Benchmark:

- single-target models,
- shared-state multi-head models,
- query-conditioned readout.

**Exit condition:** evidence that a reusable state is useful or a clear reason to abandon the shared-state hypothesis.

## Milestone 3 — Neural Operator Generator

Implement three matched transition families using the same encoder and readout:

1. nonlinear neural transition,
2. neural matrix generator,
3. neural unitary/Hamiltonian generator.

Suggested first unitary model:

```math
\theta_t=G_\phi(e_t,z_t),
```

```math
H_t=\sum_k\alpha_k(\theta_t)P_k,
```

```math
U_t=e^{-iH_t\Delta t}.
```

Use a small 4–8 qubit state-vector simulator first.

**Exit condition:** determine whether structured operator generation is competitive in predictive quality, stability, or representation efficiency.

## Milestone 4 — Tensor-network baseline

Translate the structured model to MPS/MPO where possible.

Measure:

- required bond dimension `χ`,
- approximation error vs `χ`,
- growth of `χ` over event sequences,
- wall-clock and memory cost.

Define:

```math
\chi(\epsilon)=\text{minimum bond dimension needed for target error }\epsilon.
```

**Exit condition:** determine whether classical TN simulation is already economically sufficient.

## Milestone 5 — Quantum-compatible readout

Implement observable-based readouts first:

```math
\hat y_q=\mathrm{Tr}(\rho O_q).
```

Then evaluate expectation-oriented readouts for:

- tail probability,
- expected strategy PnL,
- CVaR-like risk quantities,
- customer utility.

Only introduce QAE when its oracle/state-preparation assumptions are explicitly measured.

**Exit condition:** end-to-end resource model including state, dynamics, readout, and estimation cost.

## Milestone 6 — Noisy simulation and QPU

Evaluate:

- native gate compilation,
- circuit depth,
- shot count,
- noise sensitivity,
- error mitigation,
- total latency and monetary cost.

**Exit condition:** QPU execution provides enough evidence to compare business-adjusted cost against classical/TN alternatives.

## Milestone 7 — Productization

Possible product paths:

### Forecast / risk API

Return distributions, risk probabilities, and confidence.

### Decision SaaS

Translate world forecasts into customer-specific actions such as hedging, procurement, or inventory decisions.

### Quantum value assessment

Use accumulated benchmark data to recommend classical, TN, or QPU execution for customer workloads.

## Cross-cutting benchmark dimensions

Every backend should be evaluated on the same dimensions:

```text
prediction accuracy
calibration
multi-query performance
latency
memory
compute cost
engineering complexity
business utility
```

## Stop rules

The project intentionally supports stopping quantum escalation.

- If classical is best, ship classical.
- If TN is best, ship TN.
- If QPU is economically better, use QPU.

The product goal is decision value, not quantum usage.
