# Quantum Search on an unstructured input

## Problem Statement
Design a quantum circuit that considers as input the following vector of integers numbers: \
[1,5,7,10] \
returns a quantum state which is a superposition of indices of the target solution, obtaining in the output the indices of the inputs where two adjacent bits will always have different values.

In this case the output should be: 1/sqrt(2) * (|01⟩ + |11⟩), as the correct indices are 1 and 3. \
1 = 0001 \
5 = 0101 \
7 = 0111 \
10 = 1010 \
The method to follow for this task is to start from an array of integers as input, pass them to a binary representation and you need to find those integers whose binary representation is such that two adjacent bits are different. Once you have found those integers, you must output a superposition of states where each state is a binary representation of the indices of those integers.

## Context
The key to this task is to use the superposition offered by quantum computing to load all the values of the input array on a single quantum state, and then locate the values that meet the target condition. So, how can we use a quantum computer to store multiple values? A possible solution is using the QRAM

As with classical computers, in the QRAM information is accessed using a set of bits indicating the address of the memory cell, and another set for the actual data stored in the array. 

## Bonus
Design a general circuit that accepts vectors with random values of size 2n with m bits in length for each element and finds the state(s) indicated above from an oracle.

## Idea
Given a list of 2^n numbers with size of the largest number being m-bits, we have to return the superposition of indices of 2 numbers (1010101...m-bits... and 0101010..m-bits...) if they exist. We can do this efficiently using an Grover Search.

I have attempted to design a circuit which searches for `only pure states` and since there are always two numbers that satisfy the given conditions for given n and m, I will actually use the circuit twice, and then calculate the superposed state.

Doing it this way is not a problem because the complexity (time and space) of the solution does not get affected. But more importantly, this method allows me to calculate the answer `even when there are repititions of the numbers` that satisfy the conditions. This method indeed finds the general solution.

The detailed procedure is described in the `Steps` below.

## Full circuit obtained from Quirk simulator
![q_circ.jpg](https://raw.githubusercontent.com/AsishMandoi/qudratic-speedup-using-quantum/main/cir.jpg)

[**`link to the circuit`**](https://algassert.com/quirk#circuit={%22cols%22:[[%22H%22,%22H%22,%22H%22],[%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%97%A6%22,1,1,1,%22X%22],[%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%80%A2%22,1,%22X%22,1,%22X%22],[%22%E2%97%A6%22,%22%E2%80%A2%22,%22%E2%97%A6%22,1,%22X%22,%22X%22,%22X%22],[%22%E2%97%A6%22,%22%E2%80%A2%22,%22%E2%80%A2%22,%22X%22,1,%22X%22],[1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,1,1,1,%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%97%A6%22,%22X%22],[1,1,1,1,1,1,1,%22%E2%80%A2%22,%22%E2%80%A2%22,%22%E2%80%A2%22,%22%E2%80%A2%22,%22X%22],[1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[1,1,1,1,1,1,%22%E2%80%A2%22,1,1,1,%22X%22],[%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%97%A6%22,1,1,1,%22X%22],[%22%E2%97%A6%22,%22%E2%97%A6%22,%22%E2%80%A2%22,1,%22X%22,1,%22X%22],[%22%E2%97%A6%22,%22%E2%80%A2%22,%22%E2%97%A6%22,1,%22X%22,%22X%22,%22X%22],[%22%E2%97%A6%22,%22%E2%80%A2%22,%22%E2%80%A2%22,%22X%22,1,%22X%22],[%22H%22,%22H%22,%22H%22,1,1,1,1,1,1,1,1,%22H%22],[%22X%22,%22X%22,%22X%22],[%22%E2%80%A2%22,1,%22Z%22],[%22X%22,%22X%22,%22X%22],[%22H%22,%22H%22,%22H%22,1,1,1,1,%22X%22,1,%22X%22,1,%22X%22]],%22init%22:[0,0,0,0,0,0,0,1,0,1,0,%22-%22]})

## Steps
### Input Loading
   - We have the data in classical bits, but for the search algorithm we need the data into qubits
   - Mathematically input loading looks like: |000...000⟩|000...000⟩ -> |000..001⟩|d_1⟩ + |000..010⟩|d_2⟩ + ... |111..111⟩|d_N⟩
   - This also needs to be done efficiently (in less time and space)
   - There are multiple ways to do this
      - Classical Loading Scheme
      - Quantum Loading Scheme
   
   > Designing unitary operation to load all information of vector into quantum registers of quantum CPU from classical memory is called quantum loading scheme (QLS). [5]
   
   I have done a similar job. However, this requires the number of gates of the order O(N) and the number of qubits of the order O(log(N)).

   - For the purposes of my implementation of the search algorithm:
      - N = length of input vector
      - n = ⌈log2(N)⌉ \
         *(If N is not a power of 2 then it is set to 2^n and the input vector is padded with 0s in its end)* \
      - m = minimum length of bitstring of the largest number in the input \
      - M = 2^m
      - k = total number of values in the input vector that satify the given conditions (it will also be equal to the number of marked states)
   
   <details>
   <summary><b>Explanation of the Input Loading procedure <i>[click to expand]</i></b></summary>
   </br>
   Input loading consists of exactly <code>2^n multi-control multi-not gates</code>.
   The multi-control part acts on <code>n address qubits</code> and the multi-NOTs part on <code>m data qubits</code>.

   The application of the multi-controls is defined as follows:
   - The control points of the ith gate from left corresponds to the ith index.
   - The control points on the jth qubit from the top (in the diagram) corresponds to the jth bit in the binary representation of the ith index.
   - A <code>hole</code> or <code>anti-control</code> corresponds to a <code>0</code> and a <code>dot</code> or control corresponds to a <code>1</code>.
   - For e.g.:
      - If the index is 5 in a 4-qubit control structure, the control points will look like <code>○-■-○-■</code> corresponding to <code>0101</code>.
      - Similary, if the index is 11, in an 4-qubit control structure, the control points will look like <code>○-■-■-■</code> corresponding to <code>1011</code>.

   The application of the multi-<code>NOT</code>s or multi-<code>X</code>s are defined as follows:
   - The X in ith gate corresponds to the data of ith index.
   - The X in jth data qubit corresponds to the jth bit in the binary representation of data in ith index.
   - An <code>I</code> (or no X gate) corresponds to a <code>0</code> in the data, and an <code>X</code> corresponds to a <code>1</code>
   - For e.g.:
      - If the data to be stored is 16 in a 5-qubit data, the gates will look like <code>X-I-I-I-I</code> corresponding to <code>10000</code>.
      - Similarly, if the data to be stored is 3 in a 5-qubit data, the gates will look like <code>I-I-I-X-X</code> corresponding to <code>00011</code>.
   
   </details>

### Oracle (for marking the necessary states)
   - We need to mark the states which satisfy the condition
   - First of all, we check if the values of the data qubits are either:
      - exaclty the same as the number we are searching for, or
      - the exact bitwise complement of the number we are searching for.
   - We then mark those states which satisfy one of the above conditions, by `bringing a -ve phase` into the qubits
   
   <details>
   <summary><b>Working of the Oracle <i>[click to expand]</i></b></summary>
   </br>
   The CNOT gates compare the individual |s⟩ (search state) qubits and the individual |d⟩ (data) qubits.
   
   The gate will return a |0⟩ if the two states are same and a |1⟩ if they are complementary to each other.
   The <code>multi-anti-control-X</code> gate will act on the ancilla only if all the |s⟩ states and the |d⟩ states are equal.
   Similarly, the <code>multi-control-X</code> gate will act on the ancilla only if all the |s⟩ states and the |d⟩ states are complimentary.

   When one of the gates acts on the ancilla, *(It easy to see that at most one of the gate can act as the |s⟩ states and the |d⟩ states can't be equal as well as complimentary at the same time.)* there is a bit-flip. (|0⟩-|1⟩)/sqrt(2) changes to (|1⟩-|0⟩)/sqrt(2) which is same as -(|0⟩-|1⟩)/sqrt(2).

   </details>
   
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
   The oracle and the diffuser part need to be applied for approximately `sqrt(n/k)` iterations.

   I have `tested this algorithm for a few test random cases including repitition of elements` (present in the code), and it seems to work fine, apart from very small errors in some cases. It should work almost always for any general input.
   
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

I and a friend, Arya Bhatta who is also applying as one of my team members for the QOSF Mentorship program, worked collaboratively throughout the task. We did a lot of research together for the overall idea as well as for small optimizations, and after a lot of hours of discussing and arguing we finally managed to implement this beautiful working algorithm.