# PoC and Commercialization Strategy

## 1. Business-first principle

The project is not optimized for academic novelty. The primary goal is to create economic value from better prediction and decision making.

The central rule is:

```text
If classical works better, ship classical.
If tensor networks are sufficient, use tensor networks.
If a QPU improves the end-to-end economics, use the QPU.
```

Quantum computing is therefore a backend option, not a requirement.

## 2. Large-scale roadmap

The recommended development sequence is:

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

### Phase 1 — Define the business target

Start with a narrow forecasting problem, ideally one-step or short-horizon prediction:

```math
P(r_{t+1}\mid I_t).
```

Use multiple readouts where possible, for example:

- WTI,
- gold,
- S&P 500,
- USD/JPY,
- customer-specific procurement cost.

### Phase 2 — Build a strong classical baseline

Possible baselines:

- Transformer / temporal Transformer,
- state-space models,
- deep state-space models,
- PSR/OOM-inspired models,
- Koopman latent dynamics,
- modern probabilistic forecasting models.

The purpose is to establish whether the problem is commercially predictable at all.

### Phase 3 — Introduce a quantum-compatible latent model

Keep the observation encoder and downstream evaluation fixed, but replace the transition model with a structured operator model.

Examples:

```math
z_{t+1}=A_tz_t,
```

```math
|\psi_{t+1}\rangle=U_t|\psi_t\rangle,
```

or

```math
\rho_{t+1}=\mathcal E_t(\rho_t).
```

### Phase 4 — Tensor-network benchmark

Implement an equivalent MPS/MPO representation and measure the bond dimension required to preserve target accuracy.

A key quantity is

```math
\chi(\epsilon),
```

the bond dimension required to approximate the model within error tolerance `ε`.

If `χ` stays small, classical TN execution is likely sufficient.

### Phase 5 — Quantum simulation

Evaluate:

- circuit depth,
- gate count,
- qubit count,
- state-preparation cost,
- readout cost,
- sensitivity to noise.

### Phase 6 — QPU execution

Only after simulator evidence is positive, evaluate real hardware including:

- shots,
- noise/error mitigation,
- queue time,
- latency,
- monetary cost,
- total wall-clock time.

### Phase 7 — Business evaluation

The final question is not "was the quantum circuit faster?" but:

```math
\Delta V
=
V_{\text{model}}
-
V_{\text{baseline}}.
```

`V` may be:

- PnL,
- hedging cost reduction,
- procurement savings,
- inventory savings,
- risk reduction,
- expected utility.

## 3. Stage-based product forms

### Stage A — Classical product

```text
Data
 ↓
Multimodal NN
 ↓
Predictive latent state
 ↓
Forecast / decision
```

This should be commercially viable even if no quantum backend is used.

### Stage B — Quantum-inspired product

```text
Data
 ↓
NN
 ↓
Structured operator parameters
 ↓
Tensor Network / quantum simulator
 ↓
Forecast / decision
```

This may already provide engineering or modeling benefits.

### Stage C — Hybrid quantum product

```text
Data
 ↓
Classical NN
 ↓
Circuit / Hamiltonian parameters
 ↓
QPU
 ↓
Observable / QAE readout
 ↓
Decision
```

## 4. Gate-based PoC management

A useful decision process is:

```text
Gate 0: Is there meaningful business value?
Gate 1: Does a strong classical model work?
Gate 2: Does a quantum-compatible representation work?
Gate 3: Is a classical tensor-network implementation already sufficient?
Gate 4: Is the quantum resource estimate realistic?
Gate 5: Does QPU end-to-end economics beat alternatives?
```

At any gate, the project can stop quantum escalation and keep the best classical solution.

## 5. Quantum value routing

A possible commercial assessment layer is to compare backends using a common utility function.

For backend `b`:

```math
S_b
=
V_b
-C_b,
```

where `V_b` is realized/expected business value and `C_b` includes compute, latency, engineering, and operational cost.

A more detailed assessment might use:

```math
A=
f(
\Delta U_{prediction},
C_{classical},
\chi_{TN}(\epsilon),
R_{QPU}(\epsilon),
L_{latency},
C_{migration}
).
```

This can support a recommendation such as:

```text
Use GPU now.
Re-evaluate tensor-network/QPU migration when workload size or hardware capability crosses threshold X.
```

The commercial value is in selecting the economically correct backend, not in maximizing quantum usage.

## 6. Monetization paths

### A. Internal trading / risk engine

Use forecasts to create positions or hedge rules.

Pros:

- direct link from prediction quality to profit,
- fast experimental feedback.

Cons:

- capital constrained,
- strong market-risk and backtesting requirements.

### B. Forecast / risk API

Example output:

```text
WTI
Expected 1d return: +0.42%
P(drop > 3%): 7.3%
P(rise > 3%): 15.1%
Risk regime: 0.64
Confidence: 0.71
```

The customer pays for prediction quality rather than the underlying quantum technology.

### C. Decision SaaS

Potential customers include organizations exposed to:

- crude oil,
- LNG,
- electricity prices,
- FX,
- shipping costs,
- raw materials,
- inventory risk.

The model can predict a joint set of world variables and optimize a customer-specific objective.

For a customer `c`, the most valuable query may be

```math
P(C_{c,t+h}\mid z_t),
```

where `C` is procurement or operating cost rather than an asset price.

## 7. Moat

Patent protection is optional. A stronger practical moat may be accumulated private operational knowledge:

- standardized PoC templates,
- cross-backend benchmark data,
- workload-to-backend decision history,
- customer-specific utility models,
- known failure modes,
- mappings from problem structure to classical compressibility,
- hardware-specific execution data.

Repeated commercial deployments can therefore create a proprietary dataset about when quantum computation is actually economically useful.

## 8. Initial commercialization target

The near-term product should be valuable with a classical backend.

Quantum support should be designed as an upgrade path rather than a dependency.

This reduces technical risk and makes the architecture commercially relevant before large-scale fault-tolerant quantum hardware is available.
