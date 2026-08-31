# Practical Tips for a Financial World Model

This document records practical modeling intuitions for the financial version of the World State.

These are **engineering priors and practitioner heuristics, not universal laws**. They should be tested out-of-sample and retained only when they improve prediction, decision quality, lead time, robustness, or economic value.

The goal is to avoid losing useful modeling instincts while the formal architecture evolves.

## 1. Human systems: attention is often upstream of action

Financial markets are embedded in human society. Human behavior is influenced not only by facts, but by what people notice, discuss, believe others are noticing, and eventually act on.

A useful working chain is:

```text
attention / topic salience
        ↓
expectations / beliefs
        ↓
action
        ↓
prices / capital allocation / demand / investment / policy
        ↓
real-world economic change
```

Therefore, for a financial World Model, it may be valuable to observe **what society is paying attention to before the downstream economic variables fully move**.

Examples include:

- a technology narrative becoming dominant before capex rises,
- commodity-supply fears spreading before procurement behavior changes,
- a policy narrative gaining attention before positioning shifts,
- a product or platform becoming socially salient before adoption becomes visible in lagging statistics.

This motivates separating semantic content from attention dynamics:

```math
e_t = \text{what happened / what is being said},
```

```math
a_t = \text{how strongly society is attending to it}.
```

A practical attention state may include:

```math
A_t =
\{
\text{volume},
\text{share of attention},
\text{velocity},
\text{acceleration},
\text{persistence},
\text{reach},
\text{source quality},
\text{network penetration}
\}.
```

The strongest predictive feature may not be "what is popular" but **what is rapidly becoming popular**.

This is especially relevant to finance because markets attempt to price future behavior before it is fully visible in real economic data.

### Practical rule

> In human systems, attention is often upstream of action.

Do not treat social attention as proof of fundamental value or causal impact. Treat it as a candidate leading state variable whose incremental economic value must be measured.

See also: [Social attention and reflexive dynamics](08-social-attention-and-reflexive-dynamics.md).

## 2. Dynamic systems: change can matter more than level

A World State should not only represent **where the world is**, but also **how the world is moving**.

For an observed variable `x_t`, distinguish:

```math
x_t
```

from its local change:

```math
\dot x_t \approx \frac{\Delta x_t}{\Delta t},
```

and its acceleration:

```math
\ddot x_t \approx \frac{\Delta^2 x_t}{\Delta t^2}.
```

Intuitively:

```text
level        = where the system is
velocity     = where it is heading
acceleration = whether that movement is strengthening or weakening
```

In finance, this distinction is common and often economically meaningful:

- price level vs return,
- inflation level vs change in inflation,
- policy-rate level vs hiking/cutting trajectory,
- search volume vs growth in search volume,
- news volume vs acceleration in coverage,
- installed base vs adoption growth,
- earnings level vs earnings revision momentum,
- inventory level vs inventory draw/build rate.

### Practical rule

> Do not only model what the world is. Model how the world is changing.

## 3. Keep level and derivative information together

The previous rule should **not** be interpreted as "discard levels and keep only differences."

The same derivative can mean very different things at different levels and in different regimes.

Therefore a useful representation may include:

```math
\phi(x_t)=
\left(
x_t,
\Delta x_t,
\Delta^2 x_t,
\frac{\Delta x_t}{|x_t|+\epsilon}
\right).
```

The relative change term is useful when scale matters.

For many variables, derivatives should also be computed over multiple horizons:

```math
\Delta_{1h}x_t,
\Delta_{1d}x_t,
\Delta_{1w}x_t,
\Delta_{1m}x_t.
```

This allows the World State to distinguish, for example, a short-lived spike from a persistent regime transition.

## 4. In finance, expectation error may matter more than raw change

Markets react not only to realized values but to deviations from what was already expected.

Define an innovation / surprise term:

```math
s_t
=
x_t
-
\mathbb E[x_t\mid I_{t^-}].
```

For scheduled macroeconomic releases, this is the familiar distinction between:

```text
actual
expected
previous
revision
surprise = actual - expected
```

A strong financial observation representation may therefore retain four distinct components:

```text
Level
Velocity
Acceleration
Expectation Surprise / Innovation
```

For an attention variable, for example:

```math
A_t,
\dot A_t,
\ddot A_t,
A_t-\hat A_t.
```

"AI is highly discussed" and "AI discussion is unexpectedly accelerating" are different states and may carry very different predictive value.

## 5. Events should be interpreted in the local direction of the world

The same semantic event can have different effects depending on the current state and trajectory.

Instead of assuming:

```math
z_{t+1}=A_e z_t,
```

a richer transition can condition on state and local dynamics:

```math
z_{t+1}
=
A(e_t,z_t,\dot z_t,\Delta t)z_t,
```

or more generally:

```math
z_{t+1}=F(z_t,e_t,\dot z_t,\Delta t).
```

Example:

```text
"rate hike" while inflation is accelerating
```

and

```text
"rate hike" while inflation is rapidly decelerating
```

should not automatically induce the same latent transformation.

This reinforces the broader project idea that event operators should be **context dependent rather than fixed labels with fixed effects**.

## 6. Combine the two heuristics: detect acceleration of attention

The two main tips interact naturally.

If attention is upstream of human action, and change is often more predictive than level, then a particularly useful financial signal may be:

> **What is beginning to receive attention unusually quickly?**

A candidate chain is:

```text
acceleration in attention
        ↓
change in expectations
        ↓
change in positioning / behavior
        ↓
capital allocation / demand
        ↓
real-economy change
```

Examples worth testing include:

```text
AI attention acceleration
→ AI-equity flows
→ semiconductor orders
→ data-center capex
→ power-demand expectations
```

or

```text
shipping-risk attention acceleration
→ freight / insurance repricing
→ inventory and procurement behavior
→ delivered commodity costs
```

The objective is not to assume these chains are causal. The objective is to test whether upstream attention dynamics provide **earlier and more profitable forecasts** than lagging downstream variables alone.

## 7. Do not confuse derivative signals with stable information

Differencing amplifies noise. Higher-order differences amplify it further.

Therefore derivative features should be treated carefully:

- smooth or estimate local trends when raw sampling is noisy,
- preserve uncertainty in derivative estimates,
- use robust statistics for bursty social data,
- test multiple time scales,
- avoid look-ahead leakage when calculating rolling features,
- distinguish genuine acceleration from one-off shocks,
- normalize for changing platform/user population when using attention counts.

For latent dynamics, it may be preferable to **learn local velocity or trend states** rather than explicitly finite-difference every raw feature.

## 8. Evaluation principle

These tips are useful only if they create incremental out-of-sample value.

For any candidate feature family `g`, compare:

```math
\Delta V_g
=
V(M_{base+g})-V(M_{base}),
```

where `V` should include business-level outcomes when possible.

For example:

```text
forecast log-likelihood
calibration
lead time
turning-point detection
trading PnL after costs
hedging improvement
procurement savings
drawdown / tail-risk reduction
```

Useful ablations include:

```text
levels only
levels + first differences
levels + first + second differences
levels + expectation surprise
semantic events only
semantic events + attention levels
semantic events + attention derivatives
full model
```

The purpose of the tips is to provide **good hypotheses for feature and state design**, not to exempt those hypotheses from empirical testing.

## 9. Compact checklist

When adding a new financial/world variable, ask:

1. What is its current level?
2. In which direction is it moving?
3. Is that movement accelerating or decelerating?
4. Is the observed value surprising relative to prior expectations?
5. If humans mediate its effect, how much attention is it receiving?
6. Is that attention itself growing or shrinking?
7. Who is paying attention, and how broadly is it spreading?
8. Does adding these features improve out-of-sample economic value?

Two working reminders summarize the current intuition:

> **In human systems, attention is often upstream of action.**

> **Do not only model what the world is. Model how the world is changing.**
