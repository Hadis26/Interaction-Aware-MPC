# Thesis Summary

## Problem

Autonomous vehicles operating in mixed traffic must plan safely despite uncertainty about how surrounding human-driven vehicles will react.

A purely worst-case planner can preserve safety but may behave too conservatively. Conversely, assuming cooperation can improve efficiency but may become unsafe when surrounding vehicles do not actually cooperate.

The thesis investigates how an autonomous vehicle can adapt its planning strategy according to the interaction behavior observed online.

---

## Proposed Approach

The developed framework combines three main ideas:

### Behavior-Dependent Reachability

Different reachable-set representations are used depending on the assumed behavior of the surrounding vehicle.

For non-cooperative behavior, conservative ellipsoidal occupancy over-approximations are used.

For cooperative behavior, graph-based reachability, centralized negotiation, and corridor extraction are used to construct mutually compatible future driving regions.

### Model Predictive Control

Each reachability representation is paired with an appropriate MPC formulation.

The conservative controller explicitly avoids predicted surrounding-vehicle occupancy.

The cooperative controller plans within negotiated driving corridors.

### Online Intent Inference

Because the surrounding vehicle's behavior is unknown, the framework maintains probabilities over competing behavioral hypotheses.

Observed motion is compared with model predictions and the belief is updated online.

The active planning mode is then selected from this belief.

---

## Evaluation Scenario

The approach is evaluated using a deterministic forced-merge highway simulation.

The ego vehicle travels in a lane blocked by a static obstacle and must merge into an adjacent lane occupied by another vehicle.

The scenario requires direct interaction because the vehicles' feasible future motions overlap around the merge location.

---

## Key Results

### Non-Cooperative Interaction

When the surrounding vehicle does not adapt, the conservative planner determines that merging ahead is infeasible under the considered dynamic and safety constraints.

The ego vehicle therefore slows down, allows the surrounding vehicle to pass, and merges afterward.

### Cooperative Interaction

When the surrounding vehicle adapts, negotiated corridors allow conflict resolution through mutual longitudinal adjustment.

The surrounding vehicle slightly reduces its velocity and creates a merging gap.

The ego vehicle can then complete the merge without aggressive braking.

### Unknown Behavior

When the behavior is not known beforehand, probabilistic inference selects between the two planning modes.

The resulting architecture retains conservative behavior when uncertainty is high but can exploit cooperative interaction when supported by observed motion.

---

## Main Contributions

1. **Reachability-compatible MPC formulations** for different behavioral assumptions.

2. **Trajectory-based probabilistic intent inference** for online behavioral estimation.

3. **A unified interaction-aware planning architecture** that combines prediction, safety verification, and trajectory optimization.

---

## Research Areas

The project combines concepts from:

- autonomous driving,
- optimal control,
- Model Predictive Control,
- reachability analysis,
- probabilistic inference,
- graph algorithms,
- numerical optimization,
- multi-agent interaction.
