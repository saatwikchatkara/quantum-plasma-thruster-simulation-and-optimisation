# Quantum & Classical AI for Plasma Thruster Design 🚀

This project provides a comparative framework for optimizing the design of a simulated plasma thruster using two distinct methodologies: a **Quantum Approach** based on the Variational Quantum Eigensolver (VQE) and a **Classical AI Approach** using Bayesian Optimization with a Gaussian Process surrogate model.

The goal is to find the optimal set of thruster parameters (magnetic field, voltage, propellant flow, and channel material) that maximizes a performance score based on target values for thrust, specific impulse (Isp), efficiency, and material erosion rate.



## 📜 Table of Contents
* [Problem Domain: Plasma Thruster Optimization](#-problem-domain-plasma-thruster-optimization)
* [Methodologies Compared](#-methodologies-compared)
    * [1. Quantum Optimization (VQE)](#1-quantum-optimization-vqe-⚛️)
    * [2. Classical Surrogate Optimization (Bayesian)](#2-classical-surrogate-optimization-bayesian-🧠)
* [⚙️ Code Structure](#️-code-structure)
* [🚀 How to Run](#-how-to-run)
* [📊 Understanding the Output](#-understanding-the-output)
* [💡 Key Concepts Illustrated](#-key-concepts-illustrated)
* [📝 License](#-license)

***

## 🌌 Problem Domain: Plasma Thruster Optimization

A plasma thruster is a type of electric propulsion system for spacecraft that uses electromagnetic fields to accelerate a plasma propellant. Its performance is a complex interplay of several parameters. This simulation models that complexity.

The objective is to find a design (`b_field`, `voltage`, `flow`, `channel_material`) that minimizes the error between its calculated performance and a set of user-defined targets:
* **Thrust:** The force produced by the thruster.
* **Specific Impulse (Isp):** A measure of efficiency; how much thrust is produced per unit of propellant.
* **Efficiency:** The overall energy conversion efficiency.
* **Erosion Rate:** The rate at which the thruster's channel walls erode, affecting its lifespan.

The `ThrusterModel` class serves as our "real-world" physics oracle, calculating a final performance score for any given set of parameters.

***

## 🔬 Methodologies Compared

This code implements and visualizes two powerful optimization techniques to solve this complex, multi-parameter problem.

### 1. Quantum Optimization (VQE) ⚛️

The Variational Quantum Eigensolver (VQE) is a hybrid quantum-classical algorithm designed to find the minimum eigenvalue of a Hamiltonian. We map our engineering problem to this framework:

1.  **Encoding:** The discrete design parameters are encoded into the computational basis states of a multi-qubit system. For example, if the B-Field is represented by 3 qubits, it can take on $2^3 = 8$ discrete values.
2.  **Hamiltonian Construction:** A diagonal Hamiltonian is constructed where each diagonal entry corresponds to the **negative performance score** of a specific basis state (i.e., a specific thruster design). The ground state (lowest energy state) of this Hamiltonian therefore represents the thruster configuration with the **highest performance score**.
3.  **Variational Circuit:** A parameterized quantum circuit (the "ansatz," here `qml.StronglyEntanglingLayers`) is used to prepare a trial quantum state.
4.  **Optimization Loop:** A classical optimizer (e.g., `AdamOptimizer`) iteratively adjusts the parameters of the quantum circuit to minimize the expectation value of the Hamiltonian. This process "guides" the quantum state towards the ground state.
5.  **Solution:** The final optimized state has the highest probability of being measured in the basis state corresponding to the optimal thruster design.

This approach effectively uses a quantum algorithm to search the entire design space for the global optimum.

### 2. Classical Surrogate Optimization (Bayesian) 🧠

This method uses a classical machine learning technique called Bayesian Optimization, which is highly efficient for optimizing expensive-to-evaluate functions.

1.  **Surrogate Model:** It begins by sampling a few random points from the design space and evaluating their true performance using the `ThrusterModel`. It then trains a surrogate model—a **Gaussian Process Regressor** (`scikit-learn`)—to create an inexpensive, probabilistic approximation of the entire performance landscape.
2.  **Acquisition Function:** Instead of searching blindly, it uses an acquisition function (here, **Upper Confidence Bound - UCB**) to intelligently decide the next most promising point to evaluate. UCB balances **exploitation** (sampling in areas the surrogate predicts are good) and **exploration** (sampling in areas with high uncertainty).
3.  **Iterative Refinement:** The chosen point is evaluated with the true `ThrusterModel`, and the new data point is used to update and improve the surrogate model.
4.  **Solution:** This loop continues for a set number of iterations, progressively converging on the best design by minimizing the number of calls to the expensive physics model.

***

## ⚙️ Code Structure

The code is organized into a modular, class-based structure:

| Class                         | Role                                                                                              |
| ----------------------------- | ------------------------------------------------------------------------------------------------- |
| `ThrusterModel`               | The classical physics oracle. Simulates thruster performance based on input parameters.             |
| `QuantumOptimizer`            | Manages the entire VQE workflow, including Hamiltonian construction and the optimization loop.      |
| `SurrogateOptimizer`          | Manages the Bayesian Optimization workflow, including the surrogate model and acquisition logic.    |
| `QuantumResultVisualizer`     | A comprehensive plotting suite for all VQE results.                                               |
| `SurrogateResultVisualizer`   | A comprehensive plotting suite for all Bayesian optimization results.                             |
| **Main Execution Block** | Handles user input, orchestrates the hyperparameter search for VQE, and runs both workflows.      |

***

## 🚀 How to Run

### 1. Prerequisites
Ensure you have Python 3.8+ and the necessary libraries installed. You can install them using pip:

```bash
pip install pennylane matplotlib scikit-learn
