## 1. Objective
To establish a verifiable software bridge between the classical fracture graph data and the quantum hardware simulation. This module accepts weighted graph data and converts it into an executable QAOA circuit, ensuring that the classical optimization loop does not fail due to numerical instability.

## 2. Methodology

### A. Data Ingestion & Normalization
* **Input:** List of edges `(u, v, weight)` derived from Jack's physics engine.
* **Problem:** Physics-derived Energy Release Rates ($G$) can range from $10^{-10}$ (micro-cracks) to $10^{10}$ (major rifts). This dynamic range causes classical optimizers (COBYLA/SPSA) to suffer from vanishing or exploding gradients.
* **Solution:** Implementation of a **Min-Max Normalization Layer**. All weights are mapped to the dimensionless range $[0.1, 1.0]$ before Hamiltonian construction.
    $$w_{norm} = 0.1 + (w_{raw} - w_{min}) \times \frac{0.9}{w_{max} - w_{min}}$$

### B. Hamiltonian Mapping (Ising)
* The Fracture Graph $G(V,E)$ is mapped to the cost Hamiltonian $H_C$ for the Max-Cut problem.
* **Formulation:**
    $$H_C = \sum_{(i,j) \in E} w_{ij} Z_i Z_j$$
* **Qiskit Implementation:**  `SparsePauliOp` strings

### C. Hardware Simulation
* **Target Backend:** `FakeTorino` (133-qubit Heron)
* **Topology:** Heavy-Hex Lattice
* **Transpilation:** Using Qiskit's `generate_preset_pass_manager` to assess topological fit and SWAP gate overhead

## 3. Current Limitations & Risks
1.  **Topology Bias:** 3-node linear graph ($0-1-2$). This fits natively onto the heavy-hex lattice with **0 SWAPs**. This is a "best-case scenario" and does not reflect the complexity of a real interacting fracture network
2.  **Physics Placeholder:** weights used are dummy values from non-interacting model.
circuit depth will likely remain stable, but the result has no physical meaning until e.g. Erdogan-Gupta solver is implemented for interacting cracks