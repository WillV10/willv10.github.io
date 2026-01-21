---
layout: project
title: "Robust Pitch-Rate Control for a Fixed-Wing UAV"
description: "Mixed-sensitivity H∞ controller design, simulation, and flight validation using system-identified dynamics."
hero: /assets/images/projects/h_infinity_hero.png
order: 1
tags: [Robust Control, H∞, UAV, Flight Test, System Identification]
---

## Application Description

This project applies mixed-sensitivity **H∞ robust control** techniques to the **pitch-rate inner-loop** of a small fixed-wing unmanned aerial vehicle (UAV). The aircraft uses a **Pixhawk 6X** flight controller, and the **PX4 Hardware Support Package** within MATLAB/Simulink enables controller development, simulation, and autocoding prior to flight testing.

The vehicle weighs approximately **5 lb**, with a **4 ft wingspan**, **3.7 ft² wing area**, and a **~12% static margin**. The objective was to design a pitch-rate controller that achieves:

- Strong disturbance rejection  
- Sensor noise attenuation  
- Robust performance across modeled parametric uncertainty  

The pitch-rate controller regulates rotation about the body-y axis using elevator deflection and serves as the primary **stability augmentation system**, damping atmospheric turbulence while enabling command tracking and acceptable handling qualities.

![RC Aircraft Used](/assets/images/projects/rc-aircraft.jpg)

---

## Mathematical Model

A **state-space model** describing the longitudinal dynamics was obtained via **frequency-domain system identification** at several airspeeds. Logarithmic chirp signals were injected at the elevator command, and the resulting pitch-rate responses were processed using **CIFER** (Comprehensive Identification from Frequency Responses).

The identified model structure follows standard longitudinal dynamics formulations and was subsequently simplified to a **SISO transfer function** from elevator command to pitch rate, isolating the **short-period mode**.

![State-Space Longitudinal Dynamics](/assets/images/projects/longitudinal-state-space.png)

![Simplified Short-Period Transfer Function](/assets/images/projects/short-period-tf.png)

To validate model fidelity, a **doublet maneuver**—distinct from the identification chirp—was performed. The measured aircraft response closely matched the model time history, indicating acceptable agreement.

![System ID Model Verification](/assets/images/projects/system-id-verification.png)

---

## Identified Parameters

System identification was performed at multiple airspeeds. The extracted aerodynamic parameters are summarized below.

### Identified Parameters by Airspeed

| Airspeed (ft/s) | M<sub>δe</sub> | Z<sub>w</sub> | ω<sub>n,sp</sub> | ζ<sub>sp</sub> | τ |
|-----------------|---------------|---------------|------------------|----------------|----|
| 73              | -105.65       | 8.28          | 10.11            | 0.85           | 0.85 |
| 76              | -115.69       | 8.94          | 10.83            | 0.86           | 0.86 |
| 78              | -121.67       | 9.41          | 11.32            | 0.98           | 0.98 |
| 83              | -137.43       | 10.33         | 11.28            | 0.99           | 0.99 |

### Parameter Ranges Used for Robust Analysis

| Parameter | Min | Nominal | Max |
|---------|-----|---------|-----|
| M<sub>δe</sub> | -137.43 | -120.10 | -105.65 |
| Z<sub>w</sub> | 8.28 | 9.24 | 10.33 |
| ω<sub>n,sp</sub> | 10.11 | 10.90 | 11.32 |
| ζ<sub>sp</sub> | 0.85 | 0.92 | 0.99 |
| τ (s) | 0.050 | 0.0575 | 0.060 |

A pure time delay τ representing combined actuator and sensor lag was identified and modeled using a **first-order Padé approximation**.

---

## Uncertainty Modeling

Parametric uncertainty was introduced using **min–nominal–max grids** of the identified aerodynamic parameters. The resulting plant variations exhibited significant relative error growth near **30 rad/s**, primarily due to time-delay effects.

A **multiplicative uncertainty model** was constructed using a frequency-shaped weighting function **W<sub>i</sub>**, bounding all uncertain plant responses while accounting for unmodeled high-frequency dynamics not captured by the second-order short-period approximation.

---

## Assumptions

Key assumptions in the design include:

- Linear time-invariant behavior near the trim condition  
- Decoupled longitudinal and lateral/directional dynamics  
- Actuator saturation handled indirectly through control-effort weighting  
- Sensor noise dominance above 20–30 rad/s  

---

## Controller Design

The mixed-sensitivity problem was solved using MATLAB’s `mixsyn` function.

- **W<sub>p</sub>**: Sensitivity shaping  
  - Peak sensitivity: 2 (6 dB)  
  - Bandwidth: 3 rad/s  
  - Steady-state error: 0.001  

- **W<sub>u</sub>**: Control-effort penalty  
  - DC gain: 5 (14 dB)  

- **W<sub>t</sub>**: Noise and high-frequency robustness  
  - Attenuation above 20–30 rad/s  

The resulting controller was a **4-state H∞ controller** with a performance level **γ = 1.14**.

---

## Simulation & Flight Results

All weighted sensitivity channels satisfied their constraints, confirming appropriate loop shaping.

**Nominal time-domain performance:**
- Rise time: **0.24 s**
- Settling time: **0.5 s**
- Overshoot: **3%**

Disturbance rejection tests showed recovery to within **20%** of the initial pitch rate within **0.25 s**.

Monte Carlo sampling confirmed **robust stability and performance**, with a robust stability margin of approximately **115%**. Control effort remained below **10% of available elevator deflection** for a unit step input.

The controller was discretized and deployed on the Pixhawk flight controller. Flight-test results closely matched simulation predictions, including in-flight frequency response measurements consistent with the designed open-loop characteristics.

---

## Pros and Cons

### Advantages
- Explicit robustness guarantees  
- Integrated handling of uncertainty, noise, and actuator usage  
- Excellent agreement between simulation and flight behavior  

### Limitations
- Higher controller order than PID or LQR  
- Requires careful weight selection  
- Performance tied to linearized trim conditions  

---

## References

1. Tischler, M., Remple, R., *Aircraft and Rotorcraft System Identification*, AIAA, 2006.  
2. Matt, J. J. et al., “Frequency Domain System Identification of a Small Flying-Wing UAS,” *AIAA SCITECH*, 2022.


<iframe
  src="/assets/pdfs/pitch_controller_design.pdf"
  width="100%"
  height="1000px"
  style="border:1px solid rgba(255,255,255,0.2); border-radius:12px;">
</iframe>