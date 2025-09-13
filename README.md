Quantum vs. Classical AI for Plasma Thruster Design
This project provides a comparative framework for optimizing the design of a simulated plasma thruster using two distinct methodologies: a quantum approach based on the Variational Quantum Eigensolver (VQE) and a classical AI approach using Bayesian Optimization with a Gaussian Process surrogate model.

The goal is to find the optimal set of thruster parameters (magnetic field, voltage, propellant flow, and channel material) that maximizes a performance score. This score is based on achieving target values for thrust, specific impulse (Isp), efficiency, and material erosion rate. The project serves as a demonstration of how different computational paradigms can tackle complex, multi-parameter optimization problems where function evaluations are computationally expensive.

Table of Contents
Problem Domain: Plasma Thruster Optimization

Methodologies Compared

1. Quantum Optimization (VQE)

2. Classical Surrogate Optimization (Bayesian)

Code Structure

Installation

How to Run

Understanding the Output

Key Concepts Illustrated

Conclusion

License

Problem Domain: Plasma Thruster Optimization
A plasma thruster is a type of electric propulsion system for spacecraft that uses electromagnetic fields to accelerate a plasma propellant. Its performance is a complex interplay of several design parameters. This simulation models that complexity.

The objective is to find a design (b_field, voltage, flow, channel_material) that minimizes the error between its calculated performance and a set of user-defined targets:

Thrust: The force produced by the thruster.

Specific Impulse (Isp): A measure of efficiency; how much thrust is produced per unit of propellant.

Efficiency: The overall energy conversion efficiency.

Erosion Rate: The rate at which the thruster's channel walls erode, affecting its lifespan.

The ThrusterModel class serves as our "ground truth" physics oracle, calculating a final performance score for any given set of parameters. This function is treated as a computationally expensive black box that we aim to optimize.

Methodologies Compared
This code implements and visualizes two powerful optimization techniques to solve this complex, multi-parameter problem.

1. Quantum Optimization (VQE)
The Variational Quantum Eigensolver (VQE) is a hybrid quantum-classical algorithm designed to find the minimum eigenvalue of a Hamiltonian. We map our engineering problem to this framework:

Encoding: The discrete design parameters are encoded into the computational basis states of a multi-qubit system. For example, if the B-Field is represented by 3 qubits, it can take on 2 
3
 =8 discrete values.

Hamiltonian Construction: A diagonal Hamiltonian is constructed where each diagonal entry corresponds to the negative performance score of a specific basis state (i.e., a specific thruster design). The ground state (lowest energy state) of this Hamiltonian therefore represents the thruster configuration with the highest performance score. Note that this encoding method is exhaustive and scales exponentially with the number of qubits, making it suitable only for problems with a small search space.

Variational Circuit: A parameterized quantum circuit (the "ansatz," here qml.StronglyEntanglingLayers) is used to prepare a trial quantum state.

Optimization Loop: A classical optimizer (e.g., AdamOptimizer) iteratively adjusts the parameters of the quantum circuit to minimize the expectation value of the Hamiltonian. This process guides the quantum state towards the ground state.

Solution: The final optimized state has the highest probability of being measured in the basis state corresponding to the optimal thruster design.

2. Classical Surrogate Optimization (Bayesian)
This method uses a classical machine learning technique called Bayesian Optimization, which is highly efficient for optimizing expensive-to-evaluate functions.

Surrogate Model: It begins by sampling a few random points from the design space and evaluating their true performance using the ThrusterModel. It then trains a surrogate model—a Gaussian Process Regressor (scikit-learn)—to create an inexpensive, probabilistic approximation of the entire performance landscape.

Acquisition Function: Instead of searching blindly, it uses an acquisition function (here, Upper Confidence Bound - UCB) to intelligently decide the next most promising point to evaluate. UCB balances exploitation (sampling in areas the surrogate predicts are good) and exploration (sampling in areas with high uncertainty).

Iterative Refinement: The chosen point is evaluated with the true ThrusterModel, and the new data point is used to update and improve the surrogate model.

Solution: This loop continues for a set number of iterations, progressively converging on the best design by minimizing the number of calls to the expensive physics model.

Code Structure
The code is organized into a modular, class-based structure:

Class

Role

ThrusterModel

The classical physics oracle. Simulates thruster performance based on input parameters.

QuantumOptimizer

Manages the entire VQE workflow, including Hamiltonian construction and the optimization loop.

SurrogateOptimizer

Manages the Bayesian Optimization workflow, including the surrogate model and acquisition logic.

QuantumResultVisualizer

A comprehensive plotting suite for all VQE results.

SurrogateResultVisualizer

A comprehensive plotting suite for all Bayesian optimization results.

Main Execution Block

Handles user input, orchestrates the hyperparameter search for VQE, and runs both optimization workflows.

Installation
1. Prerequisites
Ensure you have Python 3.8+ and the necessary libraries installed. It is highly recommended to use a virtual environment.

2. Setup
First, clone the repository to your local machine.

git clone [https://github.com/your-username/your-repository-name.git](https://github.com/your-username/your-repository-name.git)
cd your-repository-name

Create and activate a virtual environment:

On macOS and Linux:

python3 -m venv venv
source venv/bin/activate

On Windows:

python -m venv venv
.\venv\Scripts\activate

3. Install Dependencies
Install the required Python packages using pip:

pip install pennylane matplotlib scikit-learn

How to Run
Execute the main script from your terminal:

python your_script_name.py

The script will first prompt you to enter the target performance metrics and the number of qubits to allocate for each thruster parameter. After you provide the inputs, it will:

Initialize the ThrusterModel with your targets.

Perform a hyperparameter search for the QuantumOptimizer to find the best VQE configuration.

Display the results of the best VQE run.

Run the SurrogateOptimizer using Bayesian Optimization.

Display the results of the Bayesian optimization run.

Understanding the Output
The script will generate two sets of comprehensive plots for each methodology:

VQE Results
VQE Convergence: Shows the Hamiltonian expectation value over iterations. A downward trend indicates successful optimization.

Final State Probability: A bar chart of measurement probabilities for each basis state. The highest bar corresponds to the optimal design found.

VQE Path on Performance Landscape: A 2D contour plot showing the optimizer's search path over the B-Field and Voltage parameters.

Evolution of Physical Metrics: Tracks the normalized values of thrust, Isp, efficiency, and erosion during the optimization.

Final Design: Target vs. Achieved: A bar chart comparing the target metrics with the performance of the final design.

Final Results Summary: A text summary of the best hyperparameters and the optimal design parameters found.

Bayesian Optimization Results
Multi-Objective Pareto Front: A scatter plot illustrating the trade-off between specific impulse and efficiency for the points sampled.

Surrogate Model Landscape: A 2D contour plot showing the Gaussian Process's prediction of the performance landscape.

Bayesian Optimization Convergence: Tracks the best performance score found at each iteration.

Acquisition Function: A 1D slice showing the UCB values, indicating where the optimizer is most likely to sample next.

Key Concepts Illustrated
Combinatorial Optimization: Mapping a discrete engineering design problem to computational frameworks.

Hybrid Quantum-Classical Algorithms: Demonstrating the VQE workflow where a classical computer optimizes a quantum circuit.

Surrogate Modeling: Using a cheap, data-driven model (Gaussian Process) to approximate an expensive function.

Sample-Efficient Optimization: Showing how Bayesian Optimization minimizes calls to the expensive model.

The Exploration-Exploitation Trade-off: A fundamental concept in machine learning, demonstrated by the UCB acquisition function.

Conclusion
This project successfully demonstrates two distinct paradigms for tackling a complex, real-world engineering optimization problem.

Classical Bayesian Optimization proves to be highly effective and sample-efficient. Its ability to build a surrogate model of the expensive physics simulation allows it to converge on a high-quality solution with a minimal number of function calls. This approach is practical, scalable, and represents the current state-of-the-art for this class of problems.

Quantum VQE Optimization, while functional, highlights the current state of quantum algorithms for such tasks. The exhaustive encoding required for the Hamiltonian scales exponentially, limiting its application to small-scale problems. However, it serves as a valuable proof-of-concept for how optimization problems can be mapped to quantum hardware. As quantum hardware matures and more advanced encoding techniques become viable, this approach may offer new avenues for solving problems intractable for classical machines.

Ultimately, the framework provides a clear educational tool and a sandbox for exploring the trade-offs between established classical methods and emerging quantum techniques.

License
This project is licensed under the MIT License.
