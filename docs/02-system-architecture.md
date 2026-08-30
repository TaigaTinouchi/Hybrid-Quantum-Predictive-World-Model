# System Architecture and Interfaces

## 1. Design principle

The project should keep the external interfaces stable while allowing the internal world-state representation to be swapped between classical, tensor-network, and quantum implementations.

The core pipeline is:

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

## 2. Observation/Event interface

Real-world inputs are asynchronous and heterogeneous. A normalized event should conceptually contain:

```text
WorldEvent
    event_id
    event_time
    observed_time
    type
    source
    entities[]
    raw_payload
    structured_payload
    numeric_features
    semantic_embedding
    confidence
    surprise
    metadata
```

Important distinctions:

- `event_time`: when the event occurred in the world,
- `observed_time`: when the system became able to use it.

This separation is necessary to avoid look-ahead bias in backtests.

Examples of event types:

- `PRICE_TICK`
- `MACRO_RELEASE`
- `NEWS`
- `EARNINGS`
- `POLICY_DECISION`
- `SUPPLY_SHOCK`
- `WEATHER`

The system should distinguish fact from interpretation. For example, "OPEC cuts production by 1M bpd" is an observation; "strong bullish pressure on crude" is an interpretation that belongs downstream.

## 3. Encoding

The encoder maps raw events to a common representation:

```math
x_t=E(e_t).
```

Different modalities may use different encoders:

```math
E_{\text{text}},\quad E_{\text{price}},\quad E_{\text{macro}},\quad E_{\text{graph}}.
```

The encoded event may retain multiple structured components rather than collapsing immediately to one vector:

```math
x_t=(h_t^{semantic},h_t^{entity},h_t^{numeric},h_t^{temporal}).
```

## 4. WorldState interface

`WorldState` is intentionally opaque.

Possible implementations include:

- `R^d` latent vector,
- probabilistic belief state,
- PSR / OOM state,
- particle representation,
- MPS,
- density matrix,
- quantum state or state-preparation circuit.

The main API is:

```text
update(state, encoded_event, delta_t) -> state
```

Mathematically:

```math
z_{t+1}=F(z_t,x_t,\Delta t).
```

The same interface can map to different backends:

```math
z_{t+1}=F_\theta(z_t,x_t)
```

or

```math
z_{t+1}=K(x_t)z_t
```

or

```math
\rho_{t+1}=\mathcal E_{x_t}(\rho_t).
```

## 5. Properties of a good WorldState

A good state should satisfy the following contracts:

1. **Predictive sufficiency**

```math
P(Y_{future}\mid h_t)\approx P(Y_{future}\mid z_t).
```

2. **Compression** — it should not simply copy all history.

3. **Multi-query usefulness** — the same state should answer many future queries.

4. **Incremental updateability** — new information should update the current state without replaying all history.

5. **Uncertainty representation** — the state should encode confidence/belief uncertainty, not only a point estimate.

6. **Queryable representation** — readout cost must stay manageable.

7. **Noise robustness** — small observational noise should not cause uncontrolled state changes.

8. **Regime sensitivity** — structurally important events should be able to trigger large state changes.

## 6. Forecast Query

The forecast interface should not bake a specific asset into the state representation.

A minimal query is:

```text
ForecastQuery
    target
    horizon
    quantity
    optional threshold
    optional conditioning
```

Examples:

```text
target   = WTI
horizon  = 1 day
quantity = return_distribution
```

```text
target    = USDJPY
horizon   = 5 days
quantity  = downside_probability
threshold = -3%
```

The interface is:

```text
forecast(world_state, query) -> PredictiveDistribution
```

or mathematically:

```math
P(Y_q\mid z_t)=R(z_t,q).
```

## 7. Predictive distribution

The primary output should be a predictive distribution when possible, not only a point estimate.

Possible implementations:

- Gaussian / parametric distribution,
- mixture models,
- quantile representation,
- sampled/generative distribution,
- implicit distribution object.

The interface can expose derived statistics such as:

```text
expected_value
variance
quantiles
confidence
calibration metadata
```

Aleatoric uncertainty and epistemic uncertainty should be kept conceptually separate.

## 8. Multi-target and joint forecasting

The system should eventually support joint queries:

```math
P(Y_1,Y_2,\dots,Y_k\mid z_t).
```

This is important because business decisions often depend on correlated risks, for example:

- fuel price,
- USD/JPY,
- demand,
- interest rates.

Marginal forecasts alone do not preserve cross-target dependence.

## 9. Decision interface

The world model should be separated from the business-specific decision rule.

The generic interface is:

```text
decide(forecast, objective, constraints) -> action
```

Mathematically:

```math
a^*=\arg\max_a\mathbb E_{Y\sim F_t}[U(a,Y)]
```

subject to constraints `a in C`.

Examples of actions:

- position size,
- hedge ratio,
- procurement amount,
- inventory target,
- reorder quantity,
- bid price.

This separation allows the same forecast to serve traders, airlines, manufacturers, or other users with different utility functions.

## 10. Evaluation interface

The system should evaluate both prediction quality and business value.

Prediction metrics may include:

- NLL,
- Brier score,
- MAE / RMSE,
- quantile loss,
- calibration,
- tail-event detection.

Decision metrics may include:

- PnL,
- Sharpe ratio,
- drawdown,
- CVaR,
- procurement cost reduction,
- risk reduction,
- realized utility.

The central distinction is:

```math
\text{forecast quality}\neq\text{decision value}.
```

## 11. Closed-loop operation

The complete loop is:

```text
Observe
  ↓
Encode
  ↓
Update WorldState
  ↓
Forecast
  ↓
Decide
  ↓
Act
  ↓
Observe outcome
  ↓
Evaluate and continue
```

The realized outcome is both an evaluation target and a new observation.
