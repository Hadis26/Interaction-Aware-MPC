# Limitations and Future Work

The project was designed primarily as a research and simulation framework rather than a production autonomous-driving system.

Several limitations remain.

## Single Interaction Scenario

Evaluation focuses on a forced-merge scenario involving one ego vehicle and one interacting surrounding vehicle.

More complex scenarios such as:

- intersections,
- roundabouts,
- dense highway traffic,
- multiple interacting surrounding vehicles

would require extensions of the current architecture.

---

## Short-Horizon Intent Inference

The intent estimator relies primarily on one-step positional prediction errors.

When cooperative and non-cooperative policies generate nearly identical short-term trajectories, their behavior becomes difficult to distinguish.

Behavioral identifiability is therefore strongly dependent on the current interaction geometry.

---

## Discrete Mode Switching

The current architecture selects between cooperative and conservative controllers using a probability threshold.

Because the two MPC formulations use different constraint structures, abrupt changes in the inferred behavior can result in sudden changes in the feasible region.

Possible extensions include:

- belief hysteresis,
- stronger temporal smoothing,
- continuous blending of control formulations,
- branch-based MPC.

---

## Simulation-Only Evaluation

The framework was evaluated in deterministic simulation.

No:

- real-vehicle experiments,
- hardware-in-the-loop evaluation,
- large-scale traffic experiments

were performed.

Real-time deployment would require additional computational optimization and validation.

---

## Safety Supervision

Strong braking in the conservative case is obtained through the admissible MPC acceleration and jerk limits.

A dedicated emergency-braking or safety-supervisor layer is not included.

Such a layer could provide an additional level of protection in extreme situations.

---

## Future Directions

Potential extensions include:

- multi-agent interaction,
- richer behavioral models,
- neural-network-based intent prediction,
- Branch MPC,
- continuous belief-aware controller blending,
- embedded real-time implementation,
- real-world or hardware-in-the-loop evaluation.
