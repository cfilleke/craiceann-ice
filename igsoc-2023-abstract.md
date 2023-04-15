## Ice Shelf Calving: a graph theoretic approach with quantum computing

Calving is modeled as the joining of fracture networks when crevasses and other cracks in the ice intersect.
While surface cracks can be minimally joined in 2D with standard flow algorithms (the graphs they form being planar),
crevasses and basal cracks can join to calve in 3D, and the graphs thus formed are not necessarily planar.
These may be modeled [1] and characterized from data [2] in 3D as non-planar graphs, the calving prediction formulated
as a weighted cut problem, which can run polynomial time on quantum computers [3].
The physics of fracture is incorporated using an Hamiltonian formulation [4]. While the cross-over to
quantum advantage will require hundreds of qubits representing hundreds of fractures, IBM systems will
have over a thousand this year and nearly five thousand by 2025 [5]. There are some problems that can be
examined with smaller-scale formulations in the mean time, such as constraining the distribution of
fractures and cavities not visible at the surface to generate a proof-of-concept calving model.

[1] Sanderson, D. J., D.C.P. Peacock, C.W. Nixon, A. Rotevatn, Graph theory and the analysis of fracture networks, Journal of Structural Geology, vol. 125, pp 155-165, 2019, https://doi.org/10.1016/j.jsg.2018.04.011.

[2] Wang, S, P. Alexander, Q. Wu, M. Tedesco, S. Shu, Characterization of ice shelf fracture features using ICESat-2 – A case study over the Amery Ice Shelf, Remote Sensing of Environment, vol. 255, 2021, https://doi.org/10.1016/j.rse.2020.112266.

[3] Guerreschi, G.G., Matsuura, A.Y. QAOA for Max-Cut requires hundreds of qubits for quantum speed-up. Nature Sci Rep 9, 6903 (2019). https://doi.org/10.1038/s41598-019-43176-9

[4] Reccho, N., Hamiltonian Formalisms Applied to Continuum Mechanics: Potential use for Fracture Mechanics, DOI:10.1007/978-3-642-22700-4_2

[5] Choi, C.Q, IBM's Quantum Leap: The Company Will Take Quantum Tech Past the 1,000-Qubit Mark in 2023, in IEEE Spectrum, vol. 60, no. 1, pp. 46-47, 2023, doi: 10.1109/MSPEC.2023.10006669.

