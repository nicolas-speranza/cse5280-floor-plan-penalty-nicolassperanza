## Penalty Functions to Implement and Compare

In this assignment, you will implement and compare **multiple wall penalty functions**.
All penalty functions must be based on the same geometric primitive:

> **Distance from a point to a wall segment**

Walls are defined as line segments  

$$
s_i = [\mathbf{a}_i, \mathbf{b}_i],
$$

and the distance from a point $\mathbf{x} \in \mathbb{R}^2$ to a wall segment is

$$
d_i(\mathbf{x}) =
\min_{t \in [0,1]}
\left\|
\mathbf{x} -
\left(\mathbf{a}_i + t(\mathbf{b}_i - \mathbf{a}_i)\right)
\right\|.
$$

All wall costs follow the same structure:

$$
C_{\text{walls}}(\mathbf{x}) =
\sum_i w_i \, \phi\!\left(d_i(\mathbf{x})\right),
$$

where \(\phi(d)\) is a scalar penalty function.

Your task is to **experiment with different choices of $\phi(d)$** and analyze how they
change the resulting motion.

You must implement and compare **at least three** of the penalty functions listed below.



### Candidate Wall Penalty Functions

Below are several approved penalty function families. All are valid; none is “the correct one.”
Your analysis should focus on how their qualitative behavior differs.



## Penalty Function Families to Try

### 1. Truncated Log Barrier (Strong Repulsion)

$$
\phi(d) =
\begin{cases}
\log\!\left(\dfrac{R}{d+\varepsilon}\right), & d \le R, \\
0, & d > R.
\end{cases}
$$

**Behavior**

- Very strong repulsion near the wall
- Effectively “forbidden” region close to walls

**Discussion points**

- Sensitivity to $\varepsilon$
- Behavior near corners
- Numerical stiffness

------

### 2. Truncated Inverse-Distance Repulsion

$$
\phi(d) =
\begin{cases}
\dfrac{1}{(d+\varepsilon)^p}, & d \le R, \\
0, & d > R,
\end{cases}
\quad p \in \{1,2\}.
$$

**Behavior**

- Smooth but increasingly aggressive near the wall
- Adjustable sharpness via $p$

**Discussion points**

- Comparison with log barrier
- Gradient magnitude near walls
- Stability vs safety

------

### 3. Quadratic Band (Soft Wall)

$$
\phi(d) =
\begin{cases}
\frac{1}{2}(R-d)^2, & d \le R, \\
0, & d > R.
\end{cases}
$$

**Behavior**

- Smooth gradients
- Creates a “buffer zone” around walls

**Discussion points**

- Why trajectories may “graze” walls
- Trade-off between smoothness and safety

------

### 4. Quartic Band (Sharper Soft Wall)

$$
\phi(d) =
\begin{cases}
(R-d)^4, & d \le R, \\
0, & d > R.
\end{cases}
$$

**Behavior**

- Stronger repulsion than quadratic
- Still fully smooth and bounded

**Discussion points**

- Why higher-order penalties change path curvature
- Comparison with inverse-distance penalties

------

### 5. Exponential / Gaussian Repulsion

$$
\phi(d) =
\exp\!\left(-\frac{d^2}{2\sigma^2}\right),
$$

optionally truncated for $d>R$.

**Behavior**

- Extremely smooth everywhere
- Long-range but weak influence

**Discussion points**

- Why this may require larger weights
- Why gradients never truly vanish without truncation

------

### 6. Signed-Distance (Capsule) Penetration Penalty

*(Walls have thickness)*

Define a signed distance

$$
\operatorname{sdf}_i(\mathbf{x}) = d_i(\mathbf{x}) - t,
$$

where $t$ is the wall half-thickness.

Penalty:

$$
\phi(\operatorname{sdf}) =
\left[\max(0,-\operatorname{sdf})\right]^2.
$$

**Behavior**

- No penalty unless the wall is penetrated
- Clean physical interpretation

**Discussion points**

- Difference between “repulsion” and “constraint violation”
- Comparison with buffer-zone penalties

------

### 7. Corridor-Friendly Penalty (Parallel Walls)

For two parallel walls forming a corridor:

$$
C(\mathbf{x}) =
C_{\text{goal}}(\mathbf{x})
+ w\sum_i \phi(d_i(\mathbf{x}))
+ \lambda \left(\operatorname{sdf}_1(\mathbf{x}) -
               \operatorname{sdf}_2(\mathbf{x})\right)^2.

$$
**Behavior**

- Prevents oscillation between walls
- Encourages motion along corridor center

**Discussion points**

- Why naive wall repulsion fails in narrow passages
- How adding structure changes local minima

------

## How You Can Ask Students to Compare Them

You can explicitly ask students to evaluate penalties along these axes:

- Path smoothness
- Clearance from walls
- Convergence speed
- Sensitivity to parameters
- Presence of local minima
- Numerical stability

Example prompt:

> Compare at least three penalty functions and discuss how their qualitative behavior differs for the same floor plan.

------

## Recommended Starting Defaults (Tell Students This)

- Start with **Quadratic Band**
- If walls are crossed → try **Log Barrier**
- If motion is too stiff → try **Quartic** or **Gaussian**

Typical parameters:

- $R$: 5–20% of map width
- $\varepsilon$: $10^{-3}$–$10^{-2}$
- $w$: 10–100 relative to goal cost






