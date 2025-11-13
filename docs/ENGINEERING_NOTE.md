# ENGINEERING NOTE — Nozzle Performance (Isentropic, Ideal)

**Project:** `nozzle-perf-estimator-demo`  
**Scope:** idealized rocket nozzle calculations using isentropic relations (no losses).  
**Version:** 1.0

---

## 1. Assumptions and model scope

- Ideal gas with constant \( \gamma \) and \( M_w \) (molar mass).
- Isentropic (adiabatic, reversible) expansion: no viscosity, no heat transfer, no boundary-layer losses, no shocks, no flow separation.
- Exit section is supersonic and defined by the specified area ratio \( \varepsilon = A_e/A_t \).
- No frozen/equilibrium chemistry modeling, no two-phase effects, no interstage effects.

This model is **teaching/indicative**, not for final design.

---

## 2. Notation (SI)

- \( P_c \) — chamber pressure, Pa  
- \( T_c \) — chamber temperature, K  
- \( P_a \) — ambient pressure, Pa  
- \( \gamma \) — ratio of specific heats  
- \( M_w \) — molar mass, kg/mol  
- \( R_u = 8.314462618\ \mathrm{J/(mol\cdot K)} \) — universal gas constant  
- \( R = R_u/M_w \) — gas constant for the mixture, J/(kg·K)  
- \( \varepsilon = A_e/A_t \) — exit-to-throat area ratio  
- \( M_e \) — exit Mach number  
- \( P_e, T_e, V_e \) — exit pressure, temperature, velocity  
- \( g_0 = 9.80665\ \mathrm{m/s^2} \) — standard gravity  
- \( I_{sp} \) — specific impulse, s  
- \( CF \) — thrust coefficient  
- \( F/A_t \) — thrust per throat area, N/m²

---

## 3. Key relations

### 3.1. Area–Mach (isentropic)
\[
\frac{A}{A^*} \;=\; \frac{1}{M}
\left(\frac{2}{\gamma+1}\left(1+\frac{\gamma-1}{2}M^2\right)\right)^{\frac{\gamma+1}{2(\gamma-1)}}
\]
For a given \( \varepsilon=A_e/A_t \), solve numerically for the **supersonic root** \( M_e>1 \).

### 3.2. Exit pressure and temperature
\[
\frac{P_e}{P_c} = \left(1+\frac{\gamma-1}{2}M_e^2\right)^{-\frac{\gamma}{\gamma-1}}, \qquad
\frac{T_e}{T_c} = \left(1+\frac{\gamma-1}{2}M_e^2\right)^{-1}
\]

### 3.3. Exit velocity
\[
V_e = M_e \sqrt{\gamma\,R\,T_e}
\]

### 3.4. Characteristic velocity (ideal estimate)
\[
c^* \approx \frac{\sqrt{R\,T_c}}{\gamma \left(\frac{2}{\gamma+1}\right)^{\frac{\gamma+1}{2(\gamma-1)}}}
\]

### 3.5. Thrust coefficient and specific impulse
Split into momentum and pressure terms:
\[
CF = \underbrace{\frac{V_e}{c^*}}_{\text{momentum}}
\;+\; \underbrace{\frac{P_e-P_a}{P_c}\,\varepsilon}_{\text{pressure}}, 
\qquad
\frac{F}{A_t} = CF \cdot P_c,
\qquad
I_{sp} \approx \frac{CF \cdot c^*}{g_0}.
\]

> Note: these formulas are aligned with `src/nozzle.py` and match the quantities reported by the project.

---

## 4. Typical numerical example

Inputs (illustrative):  
\( P_c=60\,\mathrm{bar}=6.0\cdot10^6\,\mathrm{Pa},\ T_c=3500\,\mathrm{K},\ P_a=101325\,\mathrm{Pa},\ \gamma=1.22,\ M_w=0.022\,\mathrm{kg/mol},\ \varepsilon=10 \)

Expected orders of magnitude:
- \( M_e \gtrsim 2 \)  
- \( P_e \ll P_c \) (tens–hundreds of kPa)  
- \( V_e \sim 2500\text{–}3500\ \mathrm{m/s} \)  
- \( CF \sim 1.5\text{–}2.0 \)  
- \( I_{sp} \sim 250\text{–}350\ \mathrm{s} \) (ideal estimates)

Exact values are reproducible via `src/run_sweep.py`.

---

## 5. Sanity checks

- **Monotonicity:** increasing \( \varepsilon \) increases the supersonic \( M_e \).  
- **Bounds:** \( 0 < P_e/P_c < 1 \), \( T_e/T_c < 1 \).  
- **Effect of \( \varepsilon \):** at fixed \( P_c,T_c,P_a \), larger \( \varepsilon \) typically increases \( M_e \) and \( CF \).  
- **Units:** inputs in SI; outputs have physically meaningful dimensions.

---

## 6. Limitations and next steps

Limitations:
- No **over/under-expansion** and separation modeling.
- No shocks, losses, frozen/equilibrium chemistry, multi-species effects, or two-phase flow.
- \( \gamma \) and \( M_w \) are constant (no \( T \)- or composition-dependence).

Potential extensions:
- Compare \( P_e \) vs \( P_a \) → under/over-expansion flag; a simple separation risk heuristic.
- Table-based gas properties (CEA grids) for \( \gamma(T), M_w(T) \).
- Loss models / nozzle efficiency.
- Plots of \( CF, I_{sp} \) vs \( \varepsilon \) and \( P_c \).

---

## 7. References

- Sutton & Biblarz, *Rocket Propulsion Elements*, Wiley.  
- Anderson, *Modern Compressible Flow*.  
- NASA SP-125 / classical notes on isentropic flow and nozzles.
