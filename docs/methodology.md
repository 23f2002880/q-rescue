# Q-RESCUE Methodology

## 1. Payload

The benchmark uses an 8-qubit Clifford payload with depth 14 and 70 gates.

The Clifford restriction is deliberate: it provides a controlled setting for the Paulice spacetime-check workflow used in this study.

## 2. Paulice check sweep

For every tested noise level, the experiment evaluates 0–4 actual check qubits using:

- `add_pauli_checks`
- `method="windowed"`
- `cost="gamma"`

The notebook explicitly validates that the returned circuits contain the expected number of check qubits.

## 3. Noise model

Qiskit Aer supplies controlled noisy execution:

- single-qubit depolarizing error: `p1 = noise_level`;
- two-qubit depolarizing error: `p2 = min(0.999, 5*p1)`;
- readout error: `min(0.25, 0.02 + noise_level)`.

The simulator model is not presented as a hardware characterization.

## 4. Yield

For a check configuration:

\[
\eta = rac{N_{accepted}}{N_{total}}
\]

and sampling overhead is:

\[
O_{sampling} = rac{1}{\eta}.
\]

## 5. Fidelity proxy

The Clifford state contains 255 non-identity stabilizers. The experiment samples 64 stabilizers without replacement.

For each stabilizer, the notebook measures the stabilizer expectation before and after real Paulice post-selection.

The reported value is therefore a stabilizer-expectation fidelity proxy rather than exact full-state fidelity.

## 6. Resource burden

The measured resource burden is:

\[
B =
	ext{depth ratio}	imes	ext{qubit ratio}.
\]

It is a circuit-resource metric. It is not claimed to represent hardware energy, wall-clock cost, or financial cost.

## 7. Pareto analysis

For each noise level, a configuration is retained as Pareto-optimal when no other measured configuration simultaneously provides:

- equal or better post-selected fidelity;
- equal or better acceptance;
- equal or lower resource burden;

with at least one strict improvement.

## 8. Policies

### Yield-Saver

Maximizes acceptance while preferring lower resource burden.

### Maximum Protection

Maximizes measured post-selected fidelity, with acceptance and resource burden as tie-breakers.

### Balanced Resilience

Restricts to Pareto candidates and scores:

\[
S =
(F_{norm}	imes \eta 	imes E)^{1/3},
\]

where:

- \(F_{norm}\) is fidelity normalized within the Pareto candidate set;
- \(\eta\) is acceptance;
- \(E\) is inverse-normalized resource efficiency.

## 9. Interactive game

The interactive game exposes the same measured configurations.

A player selects:

- one of the seven tested noise levels;
- an available check budget from 0 to 4.

Only measured configurations satisfying `check_budget <= budget` are eligible.

The game then applies the same balanced multi-objective scoring philosophy and reports the selected configuration.

No new quantum simulation is triggered by the interaction.

## 10. Scope

The current benchmark establishes simulator results for an 8-qubit Clifford payload. It does not establish hardware performance, fault tolerance, quantum advantage, full-state fidelity, or general non-Clifford/T-doped Paulice support.
