# **Fracture Pipeline – Assumptions, Simplifications, and Derivation Notes**

This document summarises all assumptions made in **Steps 1–3** of the fracture-graph pipeline.  
Hopefully we can go through this to see if anything is wrong.

---

# **1. Modelling Idea**

The pipeline follows a structured progression:

1. **Local modelling** – implement a minimal Recho crack-tip Hamiltonian.
2. **Introduce graph structure** – treat crack tips as nodes and encode energy along edges.
3. **Introduce physical fracture mechanics** – add stress intensity factors (SIFs) and energy release rates.
4. **Prepare for generalisation** – design everything so it can be upgraded to handle multiple cracks, V-notches, anisotropy, and full elastic interaction.

Each step builds on the one before.

---

# **2. Material Model Assumptions**

## **2.1 Isotropy**
Steps 1–3 assume **isotropic linear elasticity**, defined by Young’s modulus $E$ and Poisson’s ratio $\nu$.

The **plane-strain** Voigt stiffness matrix is

$$
C =
\begin{pmatrix}
\lambda + 2\mu & \lambda          & 0 \\
\lambda        & \lambda + 2\mu   & 0 \\
0              & 0               & \mu
\end{pmatrix},
$$

where

$$
\lambda = \frac{E\nu}{(1+\nu)(1-2\nu)}, \qquad
\mu = \frac{E}{2(1+\nu)}.
$$

## **2.2 Plane Strain**
We use **plane strain**, so the effective modulus is

$$
E' = \frac{E}{1-\nu^2}.
$$

This is standard for thick plates and is closer to typical ice-shelf behaviour. (apparently)

## **2.3 Anisotropy**
Not included yet for step 3, but the mapping from 6×6 Voigt → Recho’s reduced matrix has been done and can be enabled later.

---

# **3. Hamiltonian (Recho) Assumptions**

## **3.1 Purpose of the Hamiltonian**
The Recho Hamiltonian encodes **near-tip singular fields** and their angular dependence.  
Our implementation keeps:

- the Hamiltonian matrix $H$,
- its eigenvalues,
- a singular exponent $\lambda$.

## **3.2 Mapping from Voigt stiffness to Recho 2×2 form**
Recho requires a reduced stiffness block:

$$
\begin{pmatrix}
C_{22} & C_{24} \\
C_{42} & C_{44}
\end{pmatrix}.
$$

We mapped these entries from the Voigt matrix consistently:

- $C_{22}$ → $\sigma_{yy}$–$\varepsilon_{yy}$ stiffness,  
- $C_{44}$ → shear stiffness,  
- $C_{24} = C_{42}$ → coupling terms.

This mapping is physically correct for isotropic elasticity and generalises to anisotropic materials.

## **3.3 Hamiltonian Approximation**
The current Hamiltonian is a simplified version:

- A 2×2 block extracted from the 3×3 isotropic stiffness.
- Orientation enters through a toy factor  
  $$f(\theta) = 1 + 0.1 \cos(2\theta).$$

This preserves the interface while allowing later replacement with a fully derived Hamiltonian from the Recho ODE system.

## **3.4 Lambda-based Relative Energy**
For node $i$:

$$
s_i = \sum_j |\mu_{ij}|,
$$

and the surrogate edge energy is

$$
G_{\lambda,\text{edge}} =
\frac{s_i^2 + s_j^2}{2E'}.
$$

This is **not** a physical SIF-based energy, but it reflects differences in Hamiltonian spectra(sensitive to stiffness, orientation and geometry).

---

# **4. Linear Elastic Fracture Mechanics(LEFM) + SIF Energy (Step 3)**

## **4.1 Geometry**
Three tips:

- $(0,0)$  
- $(1,0)$  
- $(3,0)$

Two cracks:

- Segment 0–1: length = 1 → $a_1 = 0.5$
- Segment 1–2: length = 2 → $a_2 = 1.0$

## **4.2 Loading**
Infinite plate under far-field loading:

- Mode I tension: $\sigma_\infty$
- Mode II shear: $\tau_\infty$ (0 by default)

## **4.3 SIF Formulas (Independent-Crack Approximation)**

For an isolated straight crack of half-length $a$ in an infinite plate:

$$
K_I = \sigma_\infty \sqrt{\pi a}, \qquad
K_{II} = \tau_\infty \sqrt{\pi a}.
$$

## **4.4 Energy Release Rate**
Under plane strain:

$$
G = \frac{K_I^2 + K_{II}^2}{E'}.
$$

This correctly predicts that **longer cracks release more energy** (e.g., $G \propto a$).

## **4.5 Edge Weight**
Each graph edge stores:

- $G_{\text{edge}}$ (SIF-based energy),
- `weight` = $G_{\text{edge}}$ (for algorithms),
- $G_{\lambda,\text{edge}}$ (Hamiltonian-based surrogate energy).

## **4.6 Limitations**
- No crack–crack interaction.
- No shielding or amplification.
- No finite boundary effects.
- Hamiltonian not tied to loading.
- $\lambda$ is a placeholder, not solved.

This is intentional for Step 3.

---

# **5. Toward Full Physics (Future Upgrades)**

## **5.1 Multi-Crack Interaction (Erdogan–Gupta Framework)**
The next step is solving:

$$
\int_{\Gamma_j} K(x,\xi)\,\phi_j(\xi)\, d\xi = f(x),
$$

where $\phi_j$ is the displacement discontinuity on crack $j$.

This will introduce:

- shielding,
- amplification,
- full crack–crack coupling,
- load redistribution.

## **5.2 Generalising to V-Notches**
The solver is being designed to support **arbitrary straight segments**:

- Each crack or notch arm = one segment
- Node 1 in V-notch = junction of two arms at different angles
- SIFs at each arm-tip follow from the same integral-equation machinery

Thus no code needs to be rewritten—only the geometry changes.

## **5.3 Integration With the Hamiltonian**
Later, the Recho Hamiltonian can be:

- fully solved for $\lambda$,
- used to weight or modify SIF-based energies,
- used to build continuum → discrete fracture energy maps.

---

# **6. File-by-File Summary**

## **`Step-1.ipynb`**
- Isotropic, plane strain.
- Extracted 2×2 Hamiltonian.
- Placeholder $\lambda$.
- No loads, no SIFs.

## **`Step-2.ipynb`**
- Two nodes representing crack tips.
- Relative λ-based energy:

  $$G_\lambda \sim \frac{s_i^2 + s_j^2}{2E'}.$$

- No crack geometry or loading.
- Tried out an anisotropic version as well. 

## **`Step-3.ipynb`**
- Three tips, two collinear cracks.
- Independent-crack SIFs:

  $$K_I = \sigma_\infty\sqrt{\pi a}, \quad
  K_{II} = \tau_\infty\sqrt{\pi a}.$$

- Plane strain energy release:

  $$G = \frac{K_I^2 + K_{II}^2}{E'}.$$

- Graph edges weighted by physical $G$.
- Hamiltonian still stored for future integration.

---

# **7. Known Gaps (Intentional for Now)**

- True $\lambda$ extraction from Recho’s ODE system.
- Full multi-crack interaction.
- Finite geometry.
- Anisotropy.
- Coupled Hamiltonian–SIF energy.
- Time evolution and propagation laws.

---

# **8. Summary**

We have built a pipeline that now connects:

$$
\text{Geometry}
\;\Longrightarrow\;
\text{Hamiltonian}
\;\Longrightarrow\;
\text{SIFs + LEFM}
\;\Longrightarrow\;
\text{Graph Weights}.
$$
