# Quadratic Speedup
## Task
Design a quantum circuit that considers as input the following vector of integers numbers: \
[1,5,7,10] \
returns a quantum state which is a superposition of indices of the target solution, obtaining in the output the indices of the inputs where two adjacent bits will always have different values.

In this case the output should be: 1/sqrt(2) * (|01> + |11>), as the correct indices are 1 and 3. \
1 = 0001 \
5 = 0101 \
7 = 0111 \
10 = 1010 \
The method to follow for this task is to start from an array of integers as input, pass them to a binary representation and you need to find those integers whose binary representation is such that two adjacent bits are different. Once you have found those integers, you must output a superposition of states where each state is a binary representation of the indices of those integers.

Example 1\
Consider the vector [1,5,4,2]. \
Pass the integer values to binary numbers that is [001,101,100,010] \
Identify the values whose binary representation is such that two adjacent bits are different, we can see that are 2 - 101 and 010, [001,101,100,010].
Return the linear combination of the indices in which the values satisfying the criterion are found. \
Indices: \
   0    1   2   3\
   |    |    |   |\
[001, 101, 100, 010] \
Indices are converted to binary states\
|00> |01> |10> |11>\
|     |     |    |\
[001,101,100,010]\
 The answer would be the superposition of the states |01> and |11> or 1/sqrt(2) * (|01> + |11>)


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
Given a list of numbers with size of the largest number being n-bits, we have to return the superposition of indices of 2 numbers (1010101..n-bits... and 0101010..n-bits...) if they exist. We can do this efficiently using an Grover Search. For that we need to follow some steps.

## Steps
### Input Loading
   - We have the data in classical bits, but for the search algorithm we need the data into qubits
   - This needs to be done efficiently (in less time and space)
   - There are multiple ways to do this
      - Classically
      - Quantumly
### Mark
   - We need to mark the states which satisfy the condition
   - 
   
### Diffuser
   - Apply the diffuser gate (this is where amplitude of only the required state is amplified)
   - 