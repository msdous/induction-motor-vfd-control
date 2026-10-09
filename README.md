# induction-motor-vfd-control

# Three-Phase Induction Motor Control Using Variable Frequency Drives
### A Comparative Simulation Study of Scalar, Vector, and Intelligent Control Strategies

**Master's Thesis — Electrical Control**
Department of Electrical Engineering, University Mustapha Ben Boulaid — Batna 2, Algeria

**Messaoudi Abderrahmane**
Supervised by **Dr. Khamari Dalila** · Academic Year 2025/2026

---

## Overview

Induction motors account for the majority of electrical energy consumed in industrial drive systems, but they are inherently fixed-speed machines unless paired with a Variable Frequency Drive (VFD). The performance of that pairing depends entirely on the control strategy driving it. This thesis develops a full electromechanical model of a three-phase squirrel-cage induction motor and implements, simulates, and benchmarks **three distinct control architectures** — from simplest to most advanced — to provide clear, data-backed guidance on which strategy fits which application.

To keep every result fully traceable, the complete system — the nonlinear motor plant, a 5 kHz SPWM inverter stage, a dynamic DC-link, and all three controllers — was built from first principles using custom MATLAB Function blocks rather than pre-packaged toolbox components.

## Objectives

- Derive a high-fidelity fifth-order electromechanical state-space model of the induction motor using amplitude-invariant Clarke and Park transformations.
- Design and implement three control strategies of increasing complexity: **scalar V/f control**, **Indirect Field-Oriented Control (IFOC)**, and a **Mamdani-type Fuzzy Logic Controller (FLC)**.
- Evaluate all three under one identical, industrially representative load profile.
- Translate the simulation results into concrete controller-selection guidance for real drive applications.

## Methodology

**Plant:** 4 kW, 400 V, 50 Hz squirrel-cage induction motor, modeled in the synchronous (d,q) reference frame.

**Test profile (identical for all three controllers):**
1. No-load acceleration to a 1500 RPM reference
2. Rated load step of 25 N·m at t = 1 s
3. Overload step of 30 N·m (118% of rated torque) at t = 2 s

**Controllers implemented:**

| Controller | Approach |
|---|---|
| **Scalar V/f** | Open-loop constant volts-per-hertz law; no feedback |
| **IFOC** | Cascaded current (250 Hz) and speed (20 Hz) PI loops with slip-angle estimation for full flux/torque decoupling |
| **FLC** | Model-free 49-rule (7×7) Mamdani fuzzy inference system with centroid defuzzification, layered onto the IFOC current loop |

## Key Results

<p align="center">
  <img src="speed comparison.png" alt="Speed tracking performance comparison across V/f, IFOC, and FLC" width="720">
</p>

<p align="center">
  <img src="torque comparison.png" alt="Electromagnetic torque dynamics comparison across V/f, IFOC, and FLC" width="720">
</p>

| Metric | V/f | IFOC | FLC |
|---|---|---|---|
| Startup overshoot | ~100 RPM | ~80 RPM | 0 RPM |
| Peak startup current | ~55 A | ~30 A | ~38 A |
| Steady-state error @ 25 N·m | ~70 RPM | **0 RPM** | ~10 RPM |
| Steady-state error @ 30 N·m (118%) | ~90 RPM | **0 RPM** | ~12 RPM |
| Disturbance recovery time | — | ~0.10 s | ~0.05 s |
| Rotor flux orientation | — | Fully decoupled (ψ_qr ≈ 0) | Fully decoupled |

**Takeaways:**
- **Scalar V/f** is simple to implement but loses up to 90 RPM of steady-state accuracy under overload — unsuitable where speed holding matters.
- **IFOC** is the only strategy that reaches zero steady-state speed error at every load stage while maintaining clean torque response and strict flux orientation.
- **FLC** eliminates startup overshoot entirely and recovers from load disturbances fastest, at the cost of a small (10–12 RPM) residual error due to its lack of integral action.

## Conclusion & Recommendation

Based on a multi-criteria evaluation (steady-state accuracy, disturbance rejection, torque quality, startup behavior, and implementation complexity), **IFOC is recommended as the default control strategy** for applications requiring precise, high-bandwidth speed regulation (e.g., conveyors, compressors). **Scalar V/f** remains a reasonable choice for low-dynamic, cost-sensitive loads such as pumps and fans, while **FLC** is a strong candidate where startup overshoot must be fully suppressed or where parameter drift makes model-based control unreliable.

**Future work** identified in the thesis includes hardware-in-the-loop (HIL) validation on a DSP/FPGA platform, ANFIS-based auto-tuning of the fuzzy controller, and replacing SPWM with Space Vector PWM (SVPWM) to improve DC-bus utilization.

## Technical Scope

`MATLAB` · `Simulink` · `Control System Design` · `Field-Oriented Control` · `Fuzzy Logic (Mamdani FIS)` · `Power Electronics (SPWM Inverters)` · `Electric Machine Modeling` · `Comparative Performance Analysis`

---

*This is a condensed summary of a 111-page thesis manuscript. The full document, including complete derivations, all simulation architectures, and the extended multi-criteria evaluation, is available on request.*
