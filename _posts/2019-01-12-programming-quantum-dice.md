---
layout: post
title:  "Quantum Computing: Programming the Quantum Dice"
author: bobby 
image: assets/images/quantum_dice.jpeg
categories: Programming
comments: false
tags: [featured]
---

The power of quantum computing comes from the superposition of states which allows us to do computation in parallel. From an input of 1 qubit, we can do parallel computation of 2 integers (0 and 1). From an input of 2 qubits, we can do parallel computation of 4 numbers (0, 1, 2, 3) and so on. In general, for an $n$ number, we can do parallel computation of $2^n$;

A quantum computer allows us to simulate easily the roll of a dice. Imagine, for the sake of simplicity, say we have a 4-faced honest die with faces 0, 1, 2 ,3. Since there are 4 faces, the probability of getting any face is 1/4.

<a href="https://bobbycorpus.files.wordpress.com/2019/01/dice_photo.png"><img class="aligncenter wp-image-3301 size-medium" src="https://bobbycorpus.files.wordpress.com/2019/01/dice_photo.png?w=300" alt="" width="300" height="208" /></a>
Using the quantum circuit below, we can show the probabilities using a quantum computer.

<a href="https://bobbycorpus.files.wordpress.com/2019/01/4-faced-die-circuit.png"><img class="aligncenter size-full wp-image-3275" src="https://bobbycorpus.files.wordpress.com/2019/01/4-faced-die-circuit.png" alt="" width="287" height="209" /></a>

The circuit is constructed very easily and in my opinion is the hello work of quantum computing. We start with 2 qubits. Each qubit is initialized to 0. Then using a Hadamard gate, we can create a superposition of all states from 0 to 3:

The hadamard gates action on the qubits gives you this state:

$$
\displaystyle \mathbf{H}^{\otimes n} |0\rangle = \displaystyle \frac{1}{\sqrt{2}^n} \sum_{x=0}^{2^n-1} |x\rangle
$$

Then doing a measurement on the circuits 1000 times we get a probability distribution. The graph below shows that the probability of getting any state is more of less the same.

<a href="https://bobbycorpus.files.wordpress.com/2019/01/1_dice_probabilities.png"><img class="aligncenter size-full wp-image-3298" src="https://bobbycorpus.files.wordpress.com/2019/01/1_dice_probabilities.png" alt="" width="398" height="261" /></a>
<h2>2-Dice Game</h2>
What if we toss 2 dice and get the sum of the faces that show up? The following numbers are the possible outcomes: 0, 2, 3, 3, 4, 5, 6. What number are you going to bet on to maximize your winnings?
If we plot a table of possible outcomes, we would end up with the following table:

$$
\begin{array}{|c|cccc|}
\hline
& 0 & 1 & 2 & 3\\\hline
0 & 0 & 1 & 2 & 3\\
1 & 1 & 2 & 3 & 4\\
2 & 2 & 3 & 4 & 5\\
3 & 3 & 4 & 5 & 6\\\hline
\end{array}
$$

We can see that the sum = 3 has a much greater probability of coming up compared to any other number. In fact, the sum 3 comes up 4 times out of 16, which means that the probability of getting a sum of 3 is 4/16 or 0.25.

We can also show the same probability distribution using quantum computing.


<h2>2-Qubit Addition Circuit</h2>

First we need to create a circuit for adding two 2-qubit numbers. Do do this, we can use of the full-adder circuit for adding 1 bit numbers:

<a href="https://bobbycorpus.files.wordpress.com/2019/01/Full_Adder.png"><img class="aligncenter size-large wp-image-3273" src="https://bobbycorpus.files.wordpress.com/2019/01/Full_Adder.png?w=660" alt="" width="660" height="331" /></a>

The $a_0$ and $b_0$ refer to the input qubits, the $C_{\mathrm{i}0}$ refer to the input carry bit from the addition of the lesser significant bit. The $s_0$ refer to the sum of the 2 bits. The $C_{\mathrm{o}0}$ refer to the carry bit of the current addition operation and will be input to the addition of the next significant bit. For more information about binary adders refer to this <a href="https://en.wikipedia.org/wiki/Adder_(electronics)" target="_blank" rel="noopener">wikipedia article</a>.

Since there are two bits we need to add, we need two of these circuits stringed together as shown below:

[caption id="attachment_3280" align="aligncenter" width="660"]<a href="https://bobbycorpus.files.wordpress.com/2019/01/2-Full_Adders.png"><img class="wp-image-3280 size-large" src="https://bobbycorpus.files.wordpress.com/2019/01/2-Full_Adders.png?w=660" alt="" width="660" height="748" /></a> Figure 3: Two Full Adders to add 2-Qubit Numbers[/caption]
<h2>Translating to a Quantum Circuit</h2>
Using the circuit diagram above, we can translate it to a quantum circuit by using the following rules:

1. For each input bit in the XOR gate, create a CNOT gate such that the input of the CNOT gate is the input bit and the target is the output of the XOR gate:

<a href="https://bobbycorpus.files.wordpress.com/2019/01/xor_to_cnot.png"><img class="aligncenter size-large wp-image-3283" src="https://bobbycorpus.files.wordpress.com/2019/01/xor_to_cnot.png?w=660" alt="" width="660" height="182" /></a>

2. For each AND gate, create a Control-Control-NOT gate (also known as Toffoli gate) such that the input bits of the Control-Control-NOT gate is the input bits of the AND gate and the target bit is the output bit of the AND gate.

<a href="https://bobbycorpus.files.wordpress.com/2019/01/and_to_ccnot.png"><img class="aligncenter size-large wp-image-3285" src="https://bobbycorpus.files.wordpress.com/2019/01/and_to_ccnot.png?w=660" alt="" width="660" height="153" /></a>

Using these 2 rules, we can translate Figure 3 to the following quantum circuit:

<a href="https://bobbycorpus.files.wordpress.com/2019/01/2-full-adder-quantum-circuit.png"><img class="aligncenter size-large wp-image-3289" src="https://bobbycorpus.files.wordpress.com/2019/01/2-full-adder-quantum-circuit.png?w=660" alt="" width="660" height="562" /></a>
<h2>Simulating the 2-dice Betting Game</h2>
Now that we have created the adder circuit, we can now use quantum computing to simulate the betting game. Thanks to the Hadamard gate, it's very easy to create a superposition of states from the input qubits. We only have to apply hadamards to each input qubit. The full quantum circuit is now shown below:

<a href="https://bobbycorpus.files.wordpress.com/2019/01/2_dice_circuit.png"><img class="aligncenter size-large wp-image-3271" src="https://bobbycorpus.files.wordpress.com/2019/01/2_dice_circuit.png?w=660" alt="" width="660" height="551" /></a>

Here is the QISKit code to do this:

{% highlight python %}
# Import the Qiskit SDK
from qiskit import QuantumCircuit, ClassicalRegister, QuantumRegister
from qiskit import execute, Aer
from qiskit.tools.visualization import circuit_drawer, plot_histogram

# Create an input Quantum Registers
qa = QuantumRegister(2, name="a")
qb = QuantumRegister(2, name="b")

# Create the intermediate registers
qci = QuantumRegister(1, name="ci")
qco = QuantumRegister(1, name="co")
qd0 = QuantumRegister(3, name="d0")
qd1 = QuantumRegister(3, name="d1")

# Create the output registers

qs = QuantumRegister(3, name="s")

# Create a Classical Register with 3 bits.
c = ClassicalRegister(3, name="cl")
# Create a Quantum Circuit
qc = QuantumCircuit(qa, qb, qci, qco, qd0, qd1, qs, c)



# Flip the output qubit and apply Hadamard gate.
qc.h(qa[0])
qc.h(qa[1])
qc.h(qb[0])
qc.h(qb[1])

qc.barrier()
# The addition circuit

qc.cx(qa[0],qd0[0])
qc.cx(qb[0],qd0[0])
qc.cx(qd0[0],qs[0])
qc.cx(qci[0],qs[0])


qc.ccx(qci[0],qd0[0],qd0[1])
qc.ccx(qa[0],qb[0],qd0[2])
qc.cx(qd0[1],qco[0])
qc.cx(qd0[2],qco[0])

#### 2nd bit
qc.cx(qa[1],qd1[0])
qc.cx(qb[1],qd1[0])
qc.cx(qd1[0],qs[1])
qc.cx(qco[0],qs[1])

qc.ccx(qco[0],qd1[0],qd1[1])
qc.ccx(qa[1],qb[1],qd1[2])
qc.cx(qd1[1],qs[2])
qc.cx(qd1[2],qs[2])

qc.barrier()
qc.measure(qs, c)

# Compile and run the Quantum circuit on a simulator backend
backend_sim = Aer.get_backend('qasm_simulator')
shots = 100

job_sim = execute(qc, backend=backend_sim, shots=shots)
result_sim = job_sim.result()
answer = result_sim.get_counts()
plot_histogram(answer)

# Test
# 0+0 = 0 check
# 0+1 = 1 check
# 0+2 = 2 check
# 0+3 = 3 check
# 1+0 = 1 check
# 1+1 = 2 check
# 1+2 = 3 check
# 1+3 = 4 check
# 2+0 = 2 check
# 2+1 = 3 check
# 2+2 = 4 check
# 2+3 = 5 check
# 3+0 = 3 check
# 3+1 = 4 check
# 3+2 = 5 check
# 3+3 = 6 check

{% endhighlight %}

As usual, the code for drawing the circuit is as follows:

{% highlight python %}
from qiskit.tools.visualization import matplotlib_circuit_drawer as drawer, qx_color_scheme
my_scheme=qx_color_scheme()
my_scheme['plotbarrier'] = False
drawer(qc, style=my_scheme)
{% endhighlight %}

And here's the resulting probability distribution generated for 50 simulation runs.

<a href="https://bobbycorpus.files.wordpress.com/2019/01/2_dice_probabilities.png"><img class="aligncenter size-full wp-image-3293" src="https://bobbycorpus.files.wordpress.com/2019/01/2_dice_probabilities.png" alt="" width="398" height="268" /></a>

<h2>Why it Works</h2>

Suppose we have 2 n-qubit numbers. We apply superposition of states to both numbers and an Identity to the output qubit:

$$
\mathbf{H}^{\otimes n}\otimes \mathbf{H}^{\otimes n}\otimes \mathbf{I} |0\rangle |0\rangle |0\rangle 
$$

to get

$$
\displaystyle \Big(\displaystyle \frac{1}{\sqrt{2}^n} \sum_{x=0}^{2^{n}-1} |x\rangle\Big)\otimes  \Big( \displaystyle \frac{1}{\sqrt{2}^n} \sum_{y=0}^{2^{n}-1} |y\rangle\Big)\otimes |0\rangle
$$

Next, we apply the addition operator defined as

$$
\mathrm{U}_{f} |xy\rangle |0\rangle = |xy\rangle |x+y\rangle
$$

where 

$$
|xy\rangle = |x\rangle \otimes |y\rangle
$$

to give

$$
\displaystyle \frac{1}{2^n}\sum_{x=0}^{2^{n}-1} \sum_{y=0}^{2^{n}-1} \mathrm{U}_f |xy\rangle \otimes |0\rangle = \frac{1}{2^n}\sum_{x=0}^{2^{n}-1} \sum_{y=0}^{2^{n}-1} |xy\rangle \otimes |x+y\rangle
$$

The right hand side can be written as:

$$
\displaystyle \frac{1}{2^n}\sum_{i=0}^{2\cdot(2^n-1)}\Big(\sum_{j=0}^i|j\rangle |i-j\rangle\Big) |i\rangle
$$

If we measure the output qubit, it will collapse to the state

$$
\Psi = \displaystyle \left(\frac{1}{2}\right)^n \sum_{j=0}^i|j\rangle |i-j\rangle\Big) |i\rangle
$$

with probability

$$
\Psi^*\Psi = \displaystyle \left(\frac{1}{2}\right)^{2n} \sum_{j=0}^i 1 = (i+1) \left(\frac{1}{2}\right)^{2n}
$$

In our example, we have $n=2$, the probability of getting a sum of i=3 is:

$$
\displaystyle (i+1)\cdot \left(\frac{1}{2}\right)^{2n} = (3+1)\left(\frac{1}{2}\right)^{4} = 4\cdot \left(\frac{1}{16}\right) = \frac{1}{4} = 0.25
$$

<h2>Conclusion</h2>

We have shown the power of quantum computing by allowing us to represent inputs as the superposition of all possible inputs to be computed in parallel. We have shown that this can be done using a Hadamard gate. Using an adder circuit we were able to modify the probabilities so that they are not evenly distributed but follow some distribution which favors some outcomes rather than others. You can just imagine what other possibilities we can use quantum computing for!

Image Credit: "Dice five" by @Doug88888 is marked with CC BY-NC-SA 2.0. To view the terms, visit https://creativecommons.org/licenses/by-nc-sa/2.0/?ref=openverse
