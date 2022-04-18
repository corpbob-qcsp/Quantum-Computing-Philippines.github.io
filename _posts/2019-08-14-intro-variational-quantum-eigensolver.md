---
layout: post
title:  "Introducing the Variational Quantum Eigensolver"
author: bobby
image: assets/images/eigensolver.jpeg
categories: Optimization
comments: false
---

In the previous <a href="https://bobbycorpus.wordpress.com/category/quantum-computing/ising-model/" target="_blank" rel="noopener">blogs</a>, we have learned how to map the max-cut and the Traveling Salesman Problem to the Ising Model. We also solved the problems by computing the eigenvalues of the Hamiltonian matrix and finding the eigenstate of the corresponding minimum eigenvalue. We are able to do that because our problem size is small. For example, for the Traveling Salesman Problem, a problem size of $n=3$ cities needs $3^2 = 9$ qubits. This corresponds to a matrix size of $(2^9)\cdot (2^9)=512\times 512 = 262144$. If $n=4$, the matrix size becomes $4294967296$ elements! There is another way to tackle this complexity. It's a heuristic called the Variational Quantum Eigensolver.
<h2>VQE Algorithm</h2>
The Variational Quantum Eigensolver algorithm looks like this:

1. Using a set of parameters $\theta$, generate a trial state $\vert\psi\rangle$ using the quantum computer.
2. Compute the expectation value

$$
\langle\psi|H|\psi\rangle
$$

3. If the result above is less than the current minimum value, update the minimum value.
4. Use a classical optimizer to drive the generation of the $\theta$ parameters. For example, the LBFGS as the classic optimizer can be used.
5. Repeat the process until you converge to the minimum value.

Step 4 is standard, so we will just need to understand how qubits are generated and how the expectation value is computed. Finally, we will use the VQE to solve an instance of the TSP.
<h2>Generating Trial States</h2>
Given the qubit $\vert 0\rangle$, we can generate a superposition of $\vert 0\rangle$ and $\vert 1\rangle$ by performing a rotation along the Y axis. This Y rotation is defined by

$$
R_y(\alpha)=\exp(-i\alpha Y/2) = \left( \begin{array}{cc}
\cos(\frac{\alpha}{2}) & -\sin(\frac{\alpha}{2})\\
\sin(\frac{\alpha}{2}) & \cos(\frac{\alpha}{2})
\end{array}\right)
$$

where Y is the Pauli Y operator given by the matrix

$$
\sigma_y = \left(
\begin{array}{cc}
0 & -i\\
i & 0
\end{array}
\right)
$$

We can also entangle qubits using pairwise Control-Z gates. The resulting circuit looks like the below, with the one in the RED box applied $d$ times ($d$ is called the depth.) In QISKit, this is implemented via the <a href="https://qiskit.org/documentation/aqua/variational_forms.html#ry" target="_blank" rel="noopener">Ry variational form</a>.

<a href="https://bobbycorpus.files.wordpress.com/2019/08/generate_qubits2.png"><img class="aligncenter wp-image-3873 size-large" src="https://bobbycorpus.files.wordpress.com/2019/08/generate_qubits2.png?w=723" alt="" width="723" height="296" /></a>
<h2>Computing the Expectation Value</h2>
Let $\vert \psi\rangle$ a state vector and $H$ be a Hermitian Operator, then the expectation value of $H$ on the state $\vert \psi\rangle$ is given by

$$
\langle\psi|H|\psi\rangle
$$

Let $\{\lambda_0,\ldots,\lambda_{n-1}\}$ be the eigenvalues of $H$ and $\{\vert\phi_0\rangle,\ldots\vert\phi_{n-1}\rangle\}$ be the corresponding eigenvectors. Then we can write the above as

$$
\begin{array}{rl}
\displaystyle \langle\psi|H\psi\rangle &= \displaystyle \langle\psi|\sum_{i=0}^{n-1} \lambda_i |\phi_i\rangle\langle\phi_i|\psi\rangle\\
&= \displaystyle \sum_{i=0}^{n-1} \lambda_i \big|\langle\psi|\phi\rangle\big|^2
\end{array}
$$

The above expression is similar to the arithmetic mean where $\big\vert\langle\psi\vert\phi\rangle\big\vert^2$ is the probability of the eigenvalue $\lambda_i$ occurring.

Let's see how we can use this in computing the expectation value of $H$ in the Ising Model.

Suppose we have the following circuit,

<a href="https://bobbycorpus.files.wordpress.com/2019/08/expectation_value_1.png"><img class="aligncenter size-full wp-image-3820" src="https://bobbycorpus.files.wordpress.com/2019/08/expectation_value_1.png" alt="" width="696" height="391" /></a>

Using <a href="https://qiskit.org/" target="_blank" rel="noopener">QISKit</a>, we can simulate this circuit:

{% highlight python %}
q=QuantumRegister(4)
c=ClassicalRegister(4)
qc=QuantumCircuit(q,c)
angle=math.pi/4
qc.ry(angle,q[0])
qc.ry(angle,q[1])
qc.barrier()
qc.z(q[0])
qc.z(q[1])
qc.barrier()
qc.measure([0,1,2,3],[0,1,2,3])


backend_sim = Aer.get_backend('qasm_simulator')
shots = 1000
 
job_sim = execute(qc, backend=backend_sim, shots=shots)
result_sim = job_sim.result()
answer = result_sim.get_counts()
print(answer)

for i in answer:
    print(i,answer[i]/1000)
    
plot_histogram(answer)
{% endhighlight %}

<a href="https://bobbycorpus.files.wordpress.com/2019/08/expectation_value_2.png"><img class="aligncenter size-full wp-image-3824" src="https://bobbycorpus.files.wordpress.com/2019/08/expectation_value_2.png" alt="" width="452" height="331" /></a>

The simulation above gives us the probability of a given eigenstate to occur. To get the expectation value, we just need to multiply the eigenvalue with the probability of it to occur and sum it for all eigenvalues. In the Ising Model, we can easily read-off the eigenvalue of a given eigenstate. Since the eigenvalues/eigenvector pairs of $Z$ are given:

$$
Z |0\rangle = |0\rangle
$$

and

$$
Z|1\rangle = -|1\rangle
$$

Then eigenvalue of a state in the Ising Model is equal to

$$
(-1)^\text{Number of occurrences of 1}
$$

For example, for the state $1100$, we have $\lambda = -1^2 = 1$.

Using this formula, we can compute for the expectation value of the above circuit:

$$
\begin{array}{ccrr}
\mathrm{State} & \mathrm{Probability} & \mathrm{Eigenvalue} & \mathrm{Probability}\times\mathrm{Eigenvalue} \\\hline
0010 & 0.135 & -1 & -0.135 \\
0000 & 0.720 & 1 & 0.720 \\
0001 & 0.123 & -1 & -0.123 \\
0011 & 0.022 & 1 & 0.022 \\\hline
\mathrm{Expectation Value} & & & 0.484
\end{array}
$$

<h2>Applying to the Traveling Salesman Problem</h2>
Now that we understand how the VQE works, we can just apply this to the Traveling Salesman Problem. We assume you have read this <a href="https://bobbycorpus.wordpress.com/2019/07/30/traveling-pre-sales-and-quantum-computing/" target="_blank" rel="noopener">blog</a> to make sense of the code below (lifted directly from the QISKit tutorial):

{% highlight python %}
seed = 10598

spsa = SPSA(max_trials=300)
ry = RY(qubitOp.num_qubits, depth=5, entanglement='linear')
vqe = VQE(qubitOp, ry, spsa, 'matrix')

backend = Aer.get_backend('statevector_simulator')
quantum_instance = QuantumInstance(backend=backend, seed=seed)

result = vqe.run(quantum_instance)

print('energy:', result['energy'])
print('time:', result['eval_time'])
x = tsp.sample_most_likely(result['eigvecs'][0])
print('feasible:', tsp.tsp_feasible(x))
z = tsp.get_tsp_solution(x)
print('solution:', z)
print('solution objective:', tsp.tsp_value(z, tspdata.w))
draw_tsp_solution(G, z, colors, pos)
{% endhighlight %}

This gives the following result:

{% highlight python %}
energy: -597002.6480513655
time: 19.69760489463806
feasible: True
solution: [1, 2, 0]
solution objective: 223.0
{% endhighlight %}

<h2>Conclusion</h2>
This completes the series on how Quantum Computing can be used to tackle NP-Complete problems. More information can be found from <a href="https://arxiv.org/abs/1805.12037" target="_blank" rel="noopener">here</a> and <a href="https://arxiv.org/abs/1304.3061" target="_blank" rel="noopener">here</a>.

Image Credit: "Direction" by 23am.com is marked with CC BY 2.0. To view the terms, visit https://creativecommons.org/licenses/by/2.0/?ref=openverse
