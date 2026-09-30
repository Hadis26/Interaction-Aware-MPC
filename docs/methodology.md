# Methodology

## 1. Vehicle Modeling

Two vehicle representations are used for different purposes.

### Trajectory Optimization

Trajectory planning uses a nonlinear kinematic bicycle model.

The state contains:

```text
x     global longitudinal position
y     global lateral position
ψ     heading angle
v     longitudinal velocity
a     longitudinal acceleration
δ     steering angle
```

The optimization inputs are:

```text
steering rate
jerk
```

Using steering rate and jerk instead of steering angle and acceleration directly helps generate smoother actuator-feasible trajectories.

---

## 2. Reachability Abstraction

For reachability computation, a simpler double-integrator model is used.

The abstract state represents longitudinal and lateral:

```text
position
velocity
```

with acceleration acting as the control input.

This abstraction is computationally cheaper than the nonlinear bicycle model and is used for safety-oriented reachable-set computation.

---

# 3. Non-Cooperative Reachability

When the surrounding vehicle is assumed to be non-cooperative, its future control is considered uncertain but bounded.

A learned control ellipsoid represents the admissible control set.

Observed vehicle motion is used to estimate new control samples.

The uncertainty set is updated online so that it remains consistent with the observed behavior.

These input uncertainties are propagated through the dynamic model to compute a sequence of reachable ellipsoids.

The position projection of these sets is enlarged by an additional safety margin and aligned with a nominal predicted trajectory.

The result is a sequence of environment-aware occupancy ellipsoids.

---

# 4. Graph-Based Reachability

For cooperative interaction, a discrete graph representation is used.

The continuous position domain is divided into grid cells.

Each layer of the graph corresponds to a future time step.

```text
time k

[ cell ] --> [ cell ]
     \------> [ cell ]

time k+1
```

An edge indicates that the vehicle can dynamically transition from one cell to another.

Obstacle filtering removes cells that intersect forbidden areas.

Forward and backward graph pruning preserve only dynamically consistent reachable regions.

---

# 5. Negotiation

Reachable regions of different agents can overlap.

These overlapping cells represent potential future conflicts.

Only the conflicting cells must be negotiated.

The negotiation assigns cells to agents while satisfying two main conditions:

1. the same spatial cell cannot belong to multiple agents at the same time,
2. the allocation should maximize the combined utility of the agents.

The utility function considers quantities such as:

- longitudinal progress,
- velocity,
- deviation from the reference path,
- loss of future reachable area.

After allocation, affected graph nodes are removed and the reachability graph is pruned again to restore dynamic consistency.

---

# 6. Corridor Extraction

The negotiated graph can still contain many possible reachable components.

A single executable corridor must therefore be selected.

At each time step:

1. active cells are grouped into connected components,
2. components become nodes in a component graph,
3. temporal reachability defines graph edges,
4. a shortest-path search identifies a dynamically consistent sequence of components.

The selected component sequence becomes the driving corridor.

The corridor is finally converted into time-indexed spatial bounds:

```text
[x_min(k), x_max(k)]
[y_min(k), y_max(k)]
```

which are imposed directly inside the nonlinear MPC.

---

# 7. Collision-Avoidance MPC

The conservative controller optimizes the EV trajectory while satisfying:

- vehicle dynamics,
- input constraints,
- lane boundaries,
- static-obstacle avoidance,
- surrounding-vehicle occupancy avoidance.

The surrounding vehicle occupancy is represented by ellipsoids.

EV footprint points must remain outside each relevant occupancy ellipsoid.

---

# 8. Cooperative MPC

The cooperative controller does not need explicit pairwise EV-SV collision constraints.

Instead, collision avoidance has already been resolved through negotiated, mutually disjoint corridors.

The MPC therefore solves trajectory optimization subject to:

- nonlinear vehicle dynamics,
- actuator limits,
- reference tracking,
- corridor constraints.

---

# 9. Probabilistic Intent Inference

The planner maintains two behavioral hypotheses:

```text
cooperative
non-cooperative
```

Each hypothesis predicts the surrounding vehicle's next motion.

The measured position is compared with both predictions.

The probability of each hypothesis is updated using a Gaussian likelihood and Bayesian belief update.

A rolling window of log-likelihood values reduces sensitivity to individual prediction errors.

---

# 10. Adaptive Planning

The complete planning loop is:

```text
observe vehicle states
        ↓
predict SV motion under each hypothesis
        ↓
update behavioral belief
        ↓
select planning mode
        ↓
construct appropriate reachable representation
        ↓
solve corresponding MPC
        ↓
apply first input
        ↓
repeat
```

The planner therefore adjusts the level of conservatism online instead of permanently assuming either worst-case or cooperative behavior.
