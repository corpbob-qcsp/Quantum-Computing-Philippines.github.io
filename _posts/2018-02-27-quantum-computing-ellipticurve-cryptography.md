---
layout: post
title:  "Quantum Computing and Elliptic Curve Cryptography"
author: bobby
image: assets/images/elliptic_curve_2.jpeg
categories: Cryptography
comments: false
---

In the last <a href="https://wp.me/p1g6nH-La" target="_blank" rel="noopener">post</a>, we have learned what an elliptic curve is, how it is used in cryptography and we saw an example of how Alice can encrypt her message to Bob using Bob's public key and her own private key. We also saw how Bob is able to decrypt this message by multiplying Alice's public key with his private key and subtracting the result from the encrypted message to get the clear text message.

An eavesdropper, by the name of Eve, has seen the exchanges between Alice and Bob and she wants to be able to decrypt the message of Alice. However, she only sees the public keys of Alice and Bob, the encrypted message, the elliptic curve equation used and the modulus. How can she decrypt the message?
<h2>Discrete Logarithm Problem</h2>
Eve knows both the public key of Alice and Bob. If she can somehow compute the private key, she will be able to decrypt the message. The public key of Alice is

$$
\text{Alice Public Key} = P_a = e_a B
$$

Where $e_a$ is the private key of Alice and $B$ is the base point of the elliptic curve agreed before hand by Alice and Bob. Using a group theoretic notation, we can also write this as

$$
P_a = B^{e_a}
$$

where the group "multiplication" operation is defined at the addition of points defined in the last <a href="https://wp.me/p1g6nH-La" target="_blank" rel="noopener">blog post</a>, that is,

$$
B^n = \underbrace{B+B+B}_{\text{n times}}
$$

and $B^0 = O$, the identity element (point at infinity).

If $P_a = B^{e_a}$, then

$$
e_a = \log_{B} P_a
$$

Solving for $e_a$ is called the discrete logarithm problem.

<h2>Quantum Algorithm</h2>

If Eve has access to a quantum computer, she can use Shor's Algorithm to find the value of $e_a$. The trick to using Shor's algorithm is to create a periodic function $f$ such that the period of $f$ is $e_a$. To do this, define

$$
f(a,b) = B^{a-e_ab}
$$

If $(a_1,b_1)$ and $(a_2,b_2)$, notice that $f(a_2,b_2) = f(a_1, b_1) $ if $(a_2, b_2) = (a_1, b_1) + k(e_a,1)$. To see this,

$$
\begin{array}{rl}
f(a_2,b_2) &= B^{a_2-e_ab_2} = B^{(a_1+ke_a) - e_a(b_1+k)}\\
&= B^{a_1+ke_a - e_ab_1 - ke_a}\\
&= B^{a_1 - e_ab_1}\\
&= f(a_1,b_1)
\end{array}
$$

Eve will prepare 2 input registers and prepare the quantum state to be a superposition of all values of the basis vectors $\left\vert a\right\rangle\left\vert b\right\rangle$

$$
\displaystyle \frac{1}{p}\sum_{a=0}^{p-1}\sum_{b=0}^{p-1} \left|a\right\rangle\left|b\right\rangle\otimes\left|0\right\rangle
$$

She will then apply an operator $\mathbf{U}_f$ defined as

$$
\mathbf{U}_f \left|a\right\rangle \left|b\right\rangle\left|0\right\rangle = \left|a\right\rangle \left|b\right\rangle\left|f(a,b)\right\rangle
$$

to get

$$
\displaystyle \frac{1}{p} \sum_{a=0}^{p-1}\sum_{b=0}^{p-1} \left|a\right\rangle\left|b\right\rangle\left|f(a,b)\right\rangle
$$

where $f$ is the periodic function defined as above.

A measurement of the output register system will then yield a specific value $f$, which corresponds to $m$ values of $a$ and $b$ because of the periodic nature of the function:

$$
\displaystyle \frac{1}{\sqrt{m}} \sum_{k=0}^{m-1} \left|a_0 + ke_a\right\rangle\left|b_0+k\right\rangle
$$

To solve for $e_a$, we apply the Quantum Fourier Transform as defined <a href="https://wp.me/p1g6nH-Ef" rel="noopener" target="_blank">here</a>:

$$
\begin{array}{lr}
\displaystyle \mathbf{U}_{\mathbf{FT}}\otimes\mathbf{U}_{\mathbf{FT}}\frac{1}{\sqrt{m}} \sum_{k=0}^{m-1} \left|a_0 + ke_a\right\rangle\left|b_0+k\right\rangle\\
= \displaystyle \frac{1}{\sqrt{m}} \sum_{k=0}^{m-1} \mathbf{U}_{\mathbf{FT}}\left|a_0 + ke_a\right\rangle\otimes \mathbf{U}_{\mathbf{FT}}\left|b_0+k\right\rangle\\
= \displaystyle \frac{1}{(p-1)\sqrt{m}} \sum_{k=0}^{m-1} \sum_{x=0}^{p-2}\sum_{y=0}^{p-2}e^{2\pi i (a_0+ke_a)x/(p-1)} e^{2\pi i (b_0+k)y/(p-1)}\left|x\right\rangle\left|y\right\rangle\\
= \displaystyle \frac{1}{(p-1)\sqrt{m}} \sum_{k=0}^{m-1} \sum_{x=0}^{p-2}\sum_{y=0}^{p-2}e^{2\pi i/(p-1) [(a_0x + b_0y) +k(e_ax +y)]}\left|x\right\rangle\left|y\right\rangle\\
= \displaystyle \frac{1}{(p-1)\sqrt{m}} \sum_{x=0}^{p-2}\sum_{y=0}^{p-2}e^{2\pi i (a_0x + b_0y)/(p-1)} \sum_{k=0}^{m-1} e^{2\pi i k(e_ax +y)/(p-1)}\left|x\right\rangle\left|y\right\rangle\\
\end{array}
$$

The probability of getting a specify $x$ and $y$ is given by the square of the amplitudes:

$$
\displaystyle \frac{1}{(p-1)^2m}\underbrace{\left|\sum_{k=0}^{m-1} e^{2\pi i k(e_ax +y)/(p-1)}\right|}
$$

If we let 

$$
\displaystyle f = \frac{e_ax +y}{p-1}
$$

we can write the quantity in underbrace as

$$
\displaystyle \frac{\sin^2(\pi fm)}{\sin^2(\pi f)}
$$

which is maximum when $f$ is an integer, that is,

$$
e_ax + y = 0 \mod p-1
$$

After a measurement of the input registers, we get a specific value of $x$ and $y$. We can then compute the private key using:

$$
e_a = -y x^{-1} \mod p-1
$$

where $x^{-1}$ is the inverse of $x \mod p-1$.

<h2>Example</h2>

Suppose after we measure the input registers, we get the values x=51, y=44. We can compute for the private key as follows:

$$
\begin{array}{rl}
e_a &= -y x^{-1} \mod p-1\\
&= -44(51)^{-1} \mod 70\\
&= -44(11) \mod 70\\
&= 6
\end{array}
$$

<h2>Decrypting the Message</h2>

Suppose we are given the following parameters: The elliptic curve equation is $y^2 = x^3 - 3x +3 \mod 71 $, the base point is $(0,28)$, Alice's public key is $(61,58)$, Bob's public key is $(30,2)$ and the encrypted message is $(23,12)$. 

Eve uses the Quantum Computer to compute Alice private key to get $e_a = 6$. She can then multiply Bob's public key with Alice's private key to get $6\times (30,2) = (10,60)$. Subtracting this from the encrypted text, we get $(23,12) + (10,-60) = (17,54)$, which is the decrypted message.

Image Credit: "Print Gallery solved (2003) - H.W. Lenstra (1949)" by pedrosimoes7 is marked with CC BY 2.0. To view the terms, visit https://creativecommons.org/licenses/by/2.0/?ref=openverse
