# Quantum vs. Classical AI for Plasma Thruster Design
Teamname: collectivequbits
This project provides a comparative framework for optimizing the design of a simulated plasma thruster using two distinct methodologies: a quantum approach based on the Variational Quantum Eigensolver (VQE) and a classical AI approach using Bayesian Optimization with a Gaussian Process surrogate model.

The goal is to find the optimal set of thruster parameters (magnetic field, voltage, propellant flow, and channel material) that maximizes a performance score. This score is based on achieving target values for thrust, specific impulse (Isp), efficiency, and material erosion rate. The project serves as a demonstration of how different computational paradigms can tackle complex, multi-parameter optimization problems where function evaluations are computationally expensive.

---

## Table of Contents
- Problem Domain: Plasma Thruster Optimization
- Methodologies Compared
  - Quantum Optimization (VQE)
  - Classical Surrogate Optimization (Bayesian)
- Code Structure
- Installation
- How to Run
- Understanding the Output
- Key Concepts Illustrated
- Conclusion
- License

---

## Problem Domain: Plasma Thruster Optimization

A plasma thruster is a type of electric propulsion system for spacecraft that uses electromagnetic fields to accelerate a plasma propellant. Its performance is a complex interplay of several design parameters. This simulation models that complexity.

The objective is to find a design `(b_field, voltage, flow, channel_material)` that minimizes the error between its calculated performance and a set of user-defined targets:

- **Thrust:** The force produced by the thruster.  
- **Specific Impulse (Isp):** A measure of efficiency; how much thrust is produced per unit of propellant.  
- **Efficiency:** The overall energy conversion efficiency.  
- **Erosion Rate:** The rate at which the thruster's channel walls erode, affecting its lifespan.  

The `ThrusterModel` class serves as our "ground truth" physics oracle, calculating a final performance score for any given set of parameters. This function is treated as a computationally expensive black box that we aim to optimize.

---

## Methodologies Compared

This code implements and visualizes two powerful optimization techniques to solve this complex, multi-parameter problem.

### Quantum Optimization (VQE)

The **Variational Quantum Eigensolver (VQE)** is a hybrid quantum-classical algorithm designed to find the minimum eigenvalue of a Hamiltonian. We map our engineering problem to this framework:

- **Encoding:** The discrete design parameters are encoded into the computational basis states of a multi-qubit system. For example, if the B-Field is represented by 3 qubits, it can take on \(2^3 = 8\) discrete values.  
- **Hamiltonian Construction:** A diagonal Hamiltonian is constructed where each diagonal entry corresponds to the negative performance score of a specific basis state (i.e., a specific thruster design). The ground state (lowest energy state) of this Hamiltonian therefore represents the thruster configuration with the highest performance score.  
- **Variational Circuit:** A parameterized quantum circuit (the "ansatz," here `qml.StronglyEntanglingLayers`) is used to prepare a trial quantum state.  
- **Optimization Loop:** A classical optimizer (e.g., `AdamOptimizer`) iteratively adjusts the parameters of the quantum circuit to minimize the expectation value of the Hamiltonian.  
- **Solution:** The final optimized state has the highest probability of being measured in the basis state corresponding to the optimal thruster design.  

### Classical Surrogate Optimization (Bayesian)

This method uses **Bayesian Optimization**, a classical machine learning technique highly efficient for optimizing expensive-to-evaluate functions.

- **Surrogate Model:** It begins by sampling a few random points from the design space and evaluating their true performance using the `ThrusterModel`. It then trains a surrogate model — a Gaussian Process Regressor (`scikit-learn`) — to create an inexpensive, probabilistic approximation of the entire performance landscape.  
- **Acquisition Function:** Instead of searching blindly, it uses an acquisition function (here, **Upper Confidence Bound - UCB**) to intelligently decide the next most promising point to evaluate. UCB balances exploitation (sampling in areas the surrogate predicts are good) and exploration (sampling in areas with high uncertainty).  
- **Iterative Refinement:** The chosen point is evaluated with the true `ThrusterModel`, and the new data point is used to update and improve the surrogate model.  
- **Solution:** This loop continues for a set number of iterations, progressively converging on the best design by minimizing the number of calls to the expensive physics model.  

---

## Code Structure

The code is organized into a modular, class-based structure:

| Class                    | Role                                                                 |
|--------------------------|----------------------------------------------------------------------|
| **ThrusterModel**        | The classical physics oracle. Simulates thruster performance.         |
| **QuantumOptimizer**     | Manages the entire VQE workflow, including Hamiltonian construction.  |
| **SurrogateOptimizer**   | Manages the Bayesian Optimization workflow with surrogate logic.      |
| **QuantumResultVisualizer** | Provides plotting for VQE results.                               |
| **SurrogateResultVisualizer** | Provides plotting for Bayesian results.                        |
| **Main Execution Block** | Orchestrates hyperparameter search, runs both workflows.             |

---

## Understanding the Output

### VQE Results
- **VQE Convergence:** Expectation value of the Hamiltonian vs. iterations.  
- **Final State Probability:** Measurement probabilities for each design state.  
- **Search Path on Performance Landscape:** Visualizes optimization trajectory.  
- **Evolution of Physical Metrics:** Tracks thrust, Isp, efficiency, and erosion.  
- **Final Design Comparison:** Target vs. achieved performance.  
- **Results Summary:** Best hyperparameters and final design configuration.  

### Bayesian Optimization Results
- **Pareto Front:** Trade-off between Isp and efficiency.  
- **Surrogate Model Landscape:** GP prediction of performance surface.  
- **Convergence Plot:** Best performance score over iterations.  
- **Acquisition Function:** Shows exploration vs. exploitation balance.  

---

## Key Concepts Illustrated
- **Combinatorial Optimization:** Mapping discrete design problems to optimization frameworks.  
- **Hybrid Quantum-Classical Algorithms:** Demonstrating the VQE workflow.  
- **Surrogate Modeling:** Using Gaussian Processes to approximate expensive functions.  
- **Sample-Efficient Optimization:** Bayesian methods minimizing oracle calls.  
- **Exploration-Exploitation Trade-off:** Illustrated via UCB acquisition.  

---

## Conclusion
---<img width="1790" height="1144" alt="test2" src="https://github.com/user-attachments/assets/6bfa74d5-b0d6-49d8-a788-1521da42b559" />
<img width="2390" height="1144" alt="download" src="https://github.com/user-attachments/assets/48c3727b-9b8c-478f-8bee-05abbe260041" />


This project demonstrates two distinct computational paradigms for solving complex engineering optimization problems:

- **Bayesian Optimization (Classical):** Highly effective, practical, and scalable. Achieves high-quality solutions with fewer calls to the expensive physics model.  
- **VQE Optimization (Quantum):** Proof-of-concept highlighting current challenges in scalability but pointing to future potential as quantum hardware matures.  

While classical surrogate methods currently dominate in efficiency, the quantum approach provides an exciting foundation for research into problems intractable for classical computing.  




## License
This project is licensed under the **MIT License**.
### Prerequisites
- Python 3.8+  
- Recommended: Use a virtual environment  

