---
layout: post
title:  "Constructing the Quantum Fourier Circuit"
author: bobby
image: assets/images/fourier_transform.jpeg
categories: Cryptography
comments: false
---

From the previous <a href="https://bobbycorpus.wordpress.com/2017/10/15/rsa-encryption-and-quantum-computing/" target="_blank" rel="noopener">post</a>, we have seen the theory on how the Quantum Fourier Transform is used to crack the RSA. In order to utilize the QFT, we need to implement a circuit for it. In other words, <strong>we need to program it</strong>.

In this blog post, we will start with the basic definition of the Quantum Fourier Transform and implement a circuit for a 2 qubit system. We will then show the generalization to multiple qubits and implement a circuit for a 4-qubit system which we will use later to implement Shor's algorithm.

<h2>Definition</h2>

The Quantum Fourier Transform is defined on its action on the computational basis as

$$
\mathbf{U}_\mathrm{FT} |x\rangle = \displaystyle \frac{1}{\sqrt{2^n}}\sum_{y=0}^{2^n-1}e^{2\pi i xy/2^n}|y\rangle
$$

<h2>2-Qubit Example</h2>

To see how this looks like for a 2-qubit system, let's write out the terms:

$$
\begin{array}{rl}
\mathbf{U}_\mathrm{FT} |x_1x_0\rangle &= \displaystyle \frac{1}{\sqrt{2^2}}\sum_{y=0}^{2^2-1}e^{2\pi i xy/2^2}|y\rangle\\[10px]
&= \displaystyle \frac{1}{2}\left( e^{\pi i x\cdot 0/2}|0\rangle + e^{\pi i x\cdot 1/2}|1\rangle + e^{\pi i x\cdot 2/2}|2\rangle + e^{\pi i x\cdot 3/2}|3\rangle\right )\\[10px]
&= \displaystyle \frac{1}{2}\left( |00\rangle + e^{\pi i x\cdot 1/2}|01\rangle + e^{\pi i x\cdot 2/2}|10\rangle + e^{\pi i x\cdot 3/2}|11\rangle\right )\\[10px]
\end{array}
$$

In the last line, we used the binary representation of the integer $y$.

The multiplication, like $x\cdot 3$ is done in the following manner:

$$
(x_1\cdot 2^1 + x_0\cdot 2^0)(1\cdot 2^1 + 1\cdot 2^0) = 4x_1 + 2x_1 + 2x_0 + x_0
$$

For example,

$$
\begin{array}{rl}
\displaystyle e^{\pi i x\cdot 3/2} &= \displaystyle e^{\pi i \left( 4x_1 + 2x_1 + 2x_0 + x_0 \right)/2}\\[10px]
&= \displaystyle \exp\left[\pi i \left( 2x_1 + x_1 + x_0 + \frac{x_0}{2} \right)\right]\\[10px]
&= \displaystyle \exp\left[\pi i \left( x_1 + x_0 + \frac{x_0}{2} \right)\right]\\[10px]
\end{array}
$$

The last line above results because for any integer $m$

$$
\begin{array}{rl}
\exp(2\pi i mx) &= \cos(2\pi mx) + i\sin(2\pi mx)\\
&= 1
\end{array}
$$

Going back to the Fourier Transform and expanding the multiplications, we get

$$
\begin{array}{rl}
\mathbf{U}_\mathrm{FT} |x_1x_0\rangle
&=\displaystyle \frac{1}{2}\left(|00\rangle + \underbrace{\exp[\pi i(x_1 + \frac{x_0}{2})]}|01\rangle +\exp[\pi i(x_0)|10\rangle + \exp[\pi i(x_1+ \frac{x_0}{2} + x_0)|11\rangle\right)
\end{array}
$$

We can group the 2nd and 4th terms and factor out the term in the underbrace:

$$
\begin{array}{rl}
\mathbf{U}_\mathrm{FT} |x_1x_0\rangle &= \displaystyle \frac{1}{2}\left[|00\rangle + \exp[\pi i(x_0)|10\rangle + \exp[\pi i(x_1 + \frac{x_0}{2})]|01\rangle + \exp[\pi i(x_1+ \frac{x_0}{2} + x_0)|11\rangle\right]\\[10px]
&= \displaystyle \frac{1}{2}\left[\underbrace{\left(|0\rangle + \exp[\pi i(x_0)]|1\rangle\right)}\otimes|0\rangle + \underbrace{\left(|0\rangle + \exp[\pi i(x_0)]|1\rangle \right)}\otimes\exp[\pi i(x_1 + \frac{x_0}{2})]|1\rangle\right]
\end{array}
$$

Factor out the term in the underbrace to get

$$
\begin{array}{rl}
\mathbf{U}_\mathrm{FT} |x\rangle &= \displaystyle \frac{1}{2}\left[\underbrace{\left(|0\rangle + \exp[\pi i(x_0)]|1\rangle\right)}|0\rangle + \underbrace{\left(|0\rangle + \exp[\pi i(x_0)]|1\rangle \right)}\exp[\pi i(x_1 + \frac{x_0}{2})]|1\rangle\right]\\[10px]
&= \displaystyle \frac{1}{\sqrt{2}}\left[|0\rangle + \exp[\pi i(x_0)]|1\rangle\right)\otimes \frac{1}{\sqrt{2}}\left(|0\rangle + \exp[\pi i(x_1 + \frac{x_0}{2})]|1\rangle\right]
\end{array}
$$

<h2>Some Gates We Need For the Circuit</h2>

<h3>The Hadamard Gate</h3>
The first factor from the left is just the Hadamard gate acting on the first qubit since

$$
\begin{array}{rl}
\displaystyle \frac{1}{\sqrt{2}}\left(|0\rangle + \exp[\pi i(x_0)]|1\rangle\right)
&= \displaystyle \frac{|0\rangle + (-1)^{x_0} |1\rangle}{\sqrt{2}}\\[10px]
&= \mathbf{H}|x_0\rangle
\end{array}
$$

<h3>The Controlled-Phase Rotation</h3>

Let's define the following operator acting on two qubits:

$$
\begin{array}{rl}
\mathbf{V}_{ij}|x_{n-1}\cdots x_j\cdots x_i\cdots 0\rangle &= e^{\pi i x_ix_j/2^{|i-j|}}|x_{n-1}\cdots x_j\cdots x_i\cdots 0\rangle
\end{array}
$$

This is called the Controlled-Phase rotation operator. Notice that if either $x_i$ or $x_j$ is 0, the operator becomes the identity. Only when both are 1 will the operator multiply a phase $e^{\pi i/2^{|i-j|}}$.

Using these 2 operators, we can now write the gates for the Fourier Transformation.

<h2>Constructing the circuit for a 2-qubit System</h2>
Consider the following sequence of operations

$$
\begin{array}{rl}
\mathbf{H}_1\mathbf{V}_{01}\mathbf{H}_0 |x_0x_1\rangle &= \displaystyle \mathbf{H}_1\mathbf{V}_{01}|x_0\rangle\frac{1}{\sqrt{2}}(|0\rangle + \exp(\pi ix_1)|1\rangle)\\
&= \displaystyle \mathbf{H}_1 |x_0\rangle\frac{1}{\sqrt{2}}(|0\rangle + \exp(\pi i(x_1 + \frac{x_0}{2})|1\rangle)\\
&= \displaystyle \frac{1}{\sqrt{2}}(|0\rangle + \exp(\pi ix_0)|1\rangle\frac{1}{\sqrt{2}}\otimes(|0\rangle + \exp(\pi i(x_1 + \frac{x_0}{2})|1\rangle)\\
\end{array}
$$

If you compare this with the $\mathbf{U}_\mathrm{FT}\vert x_1x_0\rangle$, they are similar except for the order of the bits. Therefore, if you measure 

$$
\mathbf{H}_1\mathbf{V}_{01}\mathbf{H}_0 \vert x_0x_1\rangle
$$

and get a value $y$, you just need to reverse the bits of the binary representation of $y$ to get the corresponding state in $\mathbf{U}_\mathrm{FT}\vert x_1x_0\rangle$.
 
<h2>Visualizing the Circuit</h2>
This is the QISKit code to visualize the circuit

{% highlight python %}
# 2-qubit circuit
from qiskit import Aer

import math

from qiskit import QuantumCircuit, ClassicalRegister, QuantumRegister, execute
from qiskit.tools.visualization import circuit_drawer, plot_histogram
from qiskit import BasicAer, execute

input_register = QuantumRegister(2,name="qin")


c = ClassicalRegister(4)

qc = QuantumCircuit(input_register)

qc.h(input_register[0])
qc.cu1(math.pi/float(2),input_register[0],input_register[1])
qc.h(input_register[1])

qc.draw(output='mpl')
{% endhighlight %}

And here is the result

<a href="https://bobbycorpus.files.wordpress.com/2019/10/fourier_transform_circuit_2_qubits.png"><img class="aligncenter size-full wp-image-4413" src="https://bobbycorpus.files.wordpress.com/2019/10/fourier_transform_circuit_2_qubits.png" alt="" width="287" height="118" /></a>

<h2>General Fourier Transform Circuit</h2>


The Fourier Transform of an n-qubit system is generalized by its action on the computational basis

$$
\displaystyle \frac{1}{\sqrt{2^n}} (|0\rangle + e^{2\pi i0.x_0}|1\rangle)\otimes(|0\rangle + e^{2\pi i0.x_1x_0}|1\rangle)\otimes\cdots
$$

$$
\displaystyle \otimes(|0\rangle + e^{2\pi i 0.x_m\ldots x_2x_0}|1\rangle)
$$

where

$$
0.x_{m-1}x_{m-2}\ldots x_1x_0 = \displaystyle \frac{x_{m-1}}{2^1}+\frac{x_{m-2}}{2^2}+\cdots + \frac{x0}{2^m}
$$

<h2>Constructing the circuit for a 4-qubit System</h2>

Using this formula, the Fourier Transform of a 4-qubit system is

$$
\begin{array}{rl}
\mathbf{U}_\mathrm{FT} |x_3x_2x_1x_0\rangle &= (|0\rangle + e^{2\pi i0.x_0}|1\rangle)\otimes(|0\rangle + e^{2\pi i0.x_1x_0}|1\rangle)\otimes\\
& (|0\rangle + e^{2\pi i0.x_2x_1x_0}|1\rangle)\otimes(|0\rangle + e^{2\pi i0.x_3x_2x_1x_0}|1\rangle)
\end{array}
$$

Consider the following series of transformations:

$$
\mathbf{H}_3\mathbf{V}_{23}\mathbf{H}_2\mathbf{V}_{13}\mathbf{V}_{12}\mathbf{H}_1\mathbf{V}_{03}\mathbf{V}_{02}\mathbf{V}_{01}\mathbf{H}_0|x_3x_2x_1x_0\rangle
$$

This will give you

$$
(|0\rangle + e^{2\pi i0.x3}|1\rangle)\otimes(|0\rangle + e^{2\pi i0.x_2x_3}|1\rangle)
$$

$$
\otimes(|0\rangle + e^{2\pi i0.x_1x_2x_3}|1\rangle)\otimes(|0\rangle + e^{2\pi i0.x_0x_1x_2x_3}|1\rangle)
$$

If you compare this with $\mathbf{U}_\mathrm{TF}\vert x_3x_2x_1x_0\rangle$, they look similar except for the order of the bits. If you define the mapping

$$
\begin{array}{rl}
x_3 &\rightarrow x_0\\
x_2 &\rightarrow x_1\\
x_1 &\rightarrow x_2\\
x_0 &\rightarrow x_3
\end{array}
$$

and apply it to the transformation above, you'll get the $\mathbf{U}_\mathrm{FT}\vert x_3x_2x_1x_0\rangle$. 

Therefore, whatever $y$ you measure using 

$$
\mathbf{H}_3\mathbf{V}_{23}\mathbf{H}_2\mathbf{V}_{13}\mathbf{V}_{12}\mathbf{H}_1\mathbf{V}_{03}\mathbf{V}_{02}\mathbf{V}_{01}\mathbf{H}_0
$$

just reverse the bits to get the corresponding result using $\mathbf{U}_\mathrm{FT}\vert x_3x_2x_1x_0\rangle$.
<h2>Implementing the 4-Qubit Fourier Transform</h2>
The following code in QISKit implements the circuit

$$
\mathbf{H}_3\mathbf{V}_{23}\mathbf{H}_2\mathbf{V}_{13}\mathbf{V}_{12}\mathbf{H}_1\mathbf{V}_{03}\mathbf{V}_{02}\mathbf{V}_{01}\mathbf{H}_0|x_3x_2x_1x_0\rangle
$$

{% highlight python %}
# Fourier Transform
from qiskit import Aer
from qiskit import QuantumCircuit, ClassicalRegister, QuantumRegister, execute
from qiskit.tools.visualization import circuit_drawer, plot_histogram
from qiskit import BasicAer, execute
import math

input_register = QuantumRegister(4,name="qin")

c = ClassicalRegister(4)
qc = QuantumCircuit(input_register)

qc.h(input_register[0])
qc.cu1(math.pi/float(2),input_register[0],input_register[1])
qc.cu1(math.pi/float(2**2),input_register[0],input_register[2])
qc.cu1(math.pi/float(2**3),input_register[0],input_register[3])
qc.barrier()

qc.h(input_register[1])
qc.cu1(math.pi/float(2),input_register[1],input_register[2])
qc.cu1(math.pi/float(2**2),input_register[1],input_register[3])
qc.barrier()

qc.h(input_register[2])
qc.cu1(math.pi/float(2),input_register[2],input_register[3])
qc.h(input_register[3])
qc.barrier()

qc.draw(output='mpl')
{% endhighlight %}

Below is the circuit diagram

<a href="https://bobbycorpus.files.wordpress.com/2019/10/fourier_transform_circuit.png"><img class="aligncenter size-full wp-image-4178" src="https://bobbycorpus.files.wordpress.com/2019/10/fourier_transform_circuit.png" alt="" width="713" height="197" /></a>

Now that we have a circuit for a 4-qubit Fourier Transform, let's proceed to the next blog post and use it to <a href="https://bobbycorpus.wordpress.com/2019/10/28/programming-the-shors-algorithm-to-crack-the-rsa-cryptography/" rel="noopener" target="_blank">crack the RSA</a>!

Image Credit: "fourier" by fotodiagramas is marked with CC BY-NC 2.0. To view the terms, visit https://creativecommons.org/licenses/by-nc/2.0/?ref=openverse
