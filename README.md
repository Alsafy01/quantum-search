# Quantum Search on an Unstructured Database

## Problem Statement
Design a quantum circuit that considers as input the following vector of integers numbers: \
[1,5,7,10] \
returns a quantum state which is a superposition of indices of the target solution, obtaining in the output the indices of the inputs where two adjacent bits will always have different values.

In this case the output should be: 1/sqrt(2) * (|01> + |11>), as the correct indices are 1 and 3. \
1 = 0001 \
5 = 0101 \
7 = 0111 \
10 = 1010 \
The method to follow for this task is to start from an array of integers as input, pass them to a binary representation and you need to find those integers whose binary representation is such that two adjacent bits are different. Once you have found those integers, you must output a superposition of states where each state is a binary representation of the indices of those integers.

## Context
If you’re struggling to find a proper way to solve this task, you can find some suggestions for a possible solution below. This is one way to approach the problem, but other solutions may be feasible as well, so feel free to also investigate different strategies if you see fit!

The key to this task is to use the superposition offered by quantum computing to load all the values of the input array on a single quantum state, and then locate the values that meet the target condition. So, how can we use a quantum computer to store multiple values? A possible solution is using the QRAM (some references: https://arxiv.org/pdf/0708.1879.pdf, https://github.com/qsharp-community/qram/blob/master/docs/primer.pdf).

As with classical computers, in the QRAM information is accessed using a set of bits indicating the address of the memory cell, and another set for the actual data stored in the array. 

For example, if you want to use a QRAM to store 2 numbers that have at most 3 bits, it can be achieved with 1 qubit of address and 3 qubits of data. \
Suppose you have the vector input_2 = [2,7].
In a properly constructed circuit, when the value of the address qubit is |0> the data qubits have value 010 (binary representation of 2) and when it is |1> in the data qubits have value 111 (binary representation of 7).

Given such a structure, you should be able to use Grover’s algorithm in order to obtain the solution to the task. \
You can assume that the input always contains at least two numbers that have alternating bitstrings. \

## Bonus
Design a general circuit that accepts vectors with random values of size 2n with m bits in length for each element and finds the state(s) indicated above from an oracle.

## Idea
Given a list of 2^n numbers with size of the largest number being m-bits, we have to return the superposition of indices of 2 numbers (1010101...m-bits... and 0101010..m-bits...) if they exist. We can do this efficiently using an Grover Search.

I have attempted to design a circuit which searches for `only pure states` and since there are always two numbers that satisfy the given conditions for given n and m, I will actually use the circuit twice, and then calculate the superposed state.

Doing it this way is not a problem because the complexity (time and space) of the solution does not get affected. But more importantly, this method allows me to calculate the answer `even when there are repititions of the numbers` that satisfy the conditions. This method indeed finds the general solution.

The detailed procedure is described in the `Steps` below.

## Full circuit obtained from Quirk simulator
![q_circ.jpg](https://photos.app.goo.gl/yEEEoUEoHiN46yoW8)

[**`link to the circuit`**](https://algassert.com/quirk#circuit={%22cols%22:[[%22H%22,%22H%22,%22H%22],[%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%97%A6%22,1,1,1,%22X%22],[%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%80%A2%22,1,%22X%22,1,%22X%22],[%22%E2%97%A6%22,%22%E2%80%A2%22,%22%E2%97%A6%22,1,%22X%22,%22X%22,%22X%22],[%22%E2%97%A6%22,%22%E2%80%A2%22,%22%E2%80%A2%22,%22X%22,1,%22X%22],[1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,1,1,1,%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%97%A6%22,%22X%22],[1,1,1,1,1,1,1,%22%E2%80%A2%22,%22%E2%80%A2%22,%22%E2%80%A2%22,%22%E2%80%A2%22,%22X%22],[1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%97%A6%22,1,1,1,%22X%22],[%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%80%A2%22,1,%22X%22,1,%22X%22],[%22%E2%97%A6%22,%22%E2%80%A2%22,%22%E2%97%A6%22,1,%22X%22,%22X%22,%22X%22],[%22%E2%97%A6%22,%22%E2%80%A2%22,%22%E2%80%A2%22,%22X%22,1,%22X%22],[%22H%22,%22H%22,%22H%22,1,1,1,1,1,1,1,1,%22H%22],[%22X%22,%22X%22,%22X%22],[%22%E2%80%A2%22,1,%22Z%22],[%22X%22,%22X%22,%22X%22],[%22H%22,%22H%22,%22H%22,1,1,1,1,%22X%22,1,%22X%22,1,%22X%22]],%22init%22:[0,0,0,0,0,0,0,1,0,1,0,%22-%22]})

## Steps
### Input Loading
   - We have the data in classical bits, but for the search algorithm we need the data into qubits
   - Mathematically input loading looks like: |000...000⟩|000...000⟩ -> |000..001⟩|d_1⟩ + |000..010⟩|d_2⟩ + ... |111..111⟩|d_N⟩
   - This also needs to be done efficiently (in less time and space)
   - There are multiple ways to do this
      - Classically
      - Quantumly
   
   > Designing unitary operation to load all information of vector into quantum registers of quantum CPU from classical memory is called quantum loading scheme (QLS). [5]
   - I have done a similar job. However, this requires the number of gates of the order O(N) and the number of qubits of the order O(log(N)).

   - For the purposes of my implementation of the search algorithm:
      - N = length of input vector
      - n = ⌈log2(N)⌉ \
         *(If N is not a power of 2 then it is set to 2^n and the input vector is padded with 0s in its end)* \
      - m = minimum length of bitstring of the largest number in the input \
      - M = 2^m
      - k = total number of values in the input vector that satify the given conditions (it will also be equal to the number of marked states)
   
### Oracle (for marking the necessary states)
   - We need to mark the states which satisfy the condition
   - First of all, we check if the values of the data qubits are either:
      - exaclty the same as the number we are searching for, or
      - the exact bitwise complement of the number we are searching for.
   - We then mark those states which satisfy one of the above conditions, by `bringing a -ve phase` into the qubits
   
### Diffuser
   - Apply the diffuser gate (this is where amplitude of only the required state is amplified)
   - Here the required states are actually marked (i.e. have a negative phase)

### Resetting Qubits
   - I have reset the qubits other than the address qubits to the |0⟩ state in order to `retrieve the address qubits exclusively`
   - **Please note here that this reset operation is actually a series of `unitary operations` (and hence reversible) unlike the reset operation for QuantumCircuits provided by Qiskit**

### The Final Result
   - Since we had taken an additional qubit to make sure that Grover's algorithm doesn't encounter more marked states than unmarked states, we are getting an additional qubit |0⟩ in the beginning in bloch sphere.
   - I have expressed my result in the form of a statevector so that all information about the result can be checked. The final statevector (`|psi⟩_idx`) gives the amplitudes of the basis states (as is expected). \
   For e.g. - `[0, 1/sqrt(2), 0, 1/sqrt(2), 0, 0, 0, 0]` in statevector form is same as `(|001⟩+|011⟩)/sqrt(2)`
   - The statevector of the whole circuit has dimensions 2^(n+2*m+1). But, I have obtained the final statevector of dimensions 2^n by considering the first 2^n entries. This won't give a wrong answer because I had deliberately reset all the qubits except the address register for this purpose only, though we shouldn't do this in general.

## Few Important Notes
   The oracle and the diffuser part need to be applied for approximately `sqrt(n/k)` iterations for large `n`s.
   
   #### Endiannes of Qiskit
   - Since Qiskit is little endian, if we want to initailize first three qubits of a circuit to '10', the initialization unitary will initialize qubit_0 to 0, qubit_1 to 0.
   - Similary if we want to see the Operator matrix of a gate or the statevector version of the result. The qubit order we see in Qiskit diagrams corresponds to the vectors and matrices in the opposite order of qubits
   - I wanted to show all the results as we perceive (i.e. Big Endian convention). So, wherever I have used the statevector representation I have used the [`Statevector.reverse_qargs()`](https://qiskit.org/documentation/stubs/qiskit.quantum_info.Statevector.reverse_qargs.html#qiskit-quantum-info-statevector-reverse-qargs) method.
   
   I think for the overall complexity of the search algorithm, we should only consider the `Oracle` and the `Diffuser` and not the `Input Loading` part.

### Refererences
   - [1] Michael A. Nielsen, Isaac L. Chuang - *Quantum Computation and Quantum Information*
   - [2] Olivia Di Matteo - *A primer on quantum RAM* - [https://github.com/qsharp-community/qram/blob/master/docs/primer.pdf]
   - [3] John A. Cortese, Timothy M. Braje - *Loading Classical Data into a Quantum Computer* - [https://arxiv.org/pdf/1803.01958.pdf]
   - [4] Vittorio Giovannetti, Seth Lloyd, Lorenzo Maccone - *Quantum random access memory* - [https://arxiv.org/pdf/0708.1879.pdf]
   - [5] Pang Chao-Yang - *Loading N-Dimensional Vector into Quantum Registers from Classical Memory with O(logN) Steps* - [https://arxiv.org/pdf/quant-ph/0612061.pdf]