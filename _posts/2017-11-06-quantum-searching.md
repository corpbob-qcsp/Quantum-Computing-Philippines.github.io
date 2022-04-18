---
layout: post
title:  "Quantum Searching"
author: bobby
image: assets/images/quantum_searching.jpeg
categories: Searching
comments: false
---

Imagine a shuffled deck of 52 cards, and you are asked to find the ace of spades. The most natural thing to do is to take the top most card, see if it's the ace of spades. If not, put it aside, take the top most and repeat the process until you find the ace of spades. If you are lucky, the ace of spades is the top most then we're done. If you're not so lucky, the ace of spades is at the bottom and it will take you 52 peeks before you find the card. 

If we scatter the cards face down on the floor and randomly pick a card, then on the average, we will need $52/2$ peeks before we can find the ace of spades.

With quantum computing we can do even better!

For the sake of demonstration, let's say we have 8 cards as shown in the figure below. We want to find the ace of spades.

<a href="https://bobbycorpus.files.wordpress.com/2017/11/grover_cards.png"><img src="https://bobbycorpus.files.wordpress.com/2017/11/grover_cards.png" alt="" width="602" height="193" class="aligncenter size-full wp-image-2902" /></a>

Here are the steps:

1. Label each card from 0 to 7 in random order. The positions of the cards will represent the states of cards. The state $\left\vert 6\right\rangle$ will therefore represent the ace of spades.

2. Let $\left\vert\phi\right\rangle$ be the superposition of states:

$$
\displaystyle \left|\phi\right\rangle =  \frac{1}{2^{n/2}} \sum_{k=0}^{2^n-1} \left|x\right\rangle = \frac{1}{2^{n/2}} \Big( \left|0\right\rangle + \ldots + \left|7\right\rangle \Big) = \frac{1}{\sqrt{8}} \left[ \begin{array}{c}
1\\
1\\
1\\
1\\
1\\
1\\
1\\
1
\end{array} \right]
$$

3. Let $f$ be a function such that 

$$
f(x) = \begin{cases}
1 & x \text{ corresponds to ace of spades}\\
0 & \text{ otherwise}
\end{cases}
$$

Define the operator $\mathbf{V}$ 

$$
\mathbf{V} = \mathbf{I} - 2\left|a\right\rangle \left\langle a\right|
$$

where a is the unique value where 

$$
f(a) = 1
$$

In our example, the value of a is 6. Therefore, 

$$
\mathbf{V} = \begin{bmatrix}
  1.00 & 0.00 & 0.00 & 0.00 & 0.00 & 0.00 & 0.00 & 0.00 \\ 
  0.00 & 1.00 & 0.00 & 0.00 & 0.00 & 0.00 & 0.00 & 0.00 \\ 
  0.00 & 0.00 & 1.00 & 0.00 & 0.00 & 0.00 & 0.00 & 0.00 \\ 
  0.00 & 0.00 & 0.00 & 1.00 & 0.00 & 0.00 & 0.00 & 0.00 \\ 
  0.00 & 0.00 & 0.00 & 0.00 & 1.00 & 0.00 & 0.00 & 0.00 \\ 
  0.00 & 0.00 & 0.00 & 0.00 & 0.00 & 1.00 & 0.00 & 0.00 \\ 
  0.00 & 0.00 & 0.00 & 0.00 & 0.00 & 0.00 & -1.00 & 0.00 \\ 
  0.00 & 0.00 & 0.00 & 0.00 & 0.00 & 0.00 & 0.00 & 1.00 \\ 
  \end{bmatrix}
$$

Observe that the matrix element $\mathbf{V}_{6,6} = -1$

4. Define the operator W:

$$
\mathbf{W} = 2\left|\phi\right\rangle \left\langle\phi\right| - \mathbf{I} = \begin{bmatrix}
  -0.75 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 \\ 
  0.25 & -0.75 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 \\ 
  0.25 & 0.25 & -0.75 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 \\ 
  0.25 & 0.25 & 0.25 & -0.75 & 0.25 & 0.25 & 0.25 & 0.25 \\ 
  0.25 & 0.25 & 0.25 & 0.25 & -0.75 & 0.25 & 0.25 & 0.25 \\ 
  0.25 & 0.25 & 0.25 & 0.25 & 0.25 & -0.75 & 0.25 & 0.25 \\ 
  0.25 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 & -0.75 & 0.25 \\ 
  0.25 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 & -0.75 \\ 
  \end{bmatrix}
$$

5. Compute the operator $\mathbf{WV}$:

$$
\mathbf{WV} = \begin{bmatrix}
  -0.75 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 & -0.25 & 0.25 \\ 
  0.25 & -0.75 & 0.25 & 0.25 & 0.25 & 0.25 & -0.25 & 0.25 \\ 
  0.25 & 0.25 & -0.75 & 0.25 & 0.25 & 0.25 & -0.25 & 0.25 \\ 
  0.25 & 0.25 & 0.25 & -0.75 & 0.25 & 0.25 & -0.25 & 0.25 \\ 
  0.25 & 0.25 & 0.25 & 0.25 & -0.75 & 0.25 & -0.25 & 0.25 \\ 
  0.25 & 0.25 & 0.25 & 0.25 & 0.25 & -0.75 & -0.25 & 0.25 \\ 
  0.25 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 & 0.75 & 0.25 \\ 
  0.25 & 0.25 & 0.25 & 0.25 & 0.25 & 0.25 & -0.25 & -0.75 \\ 
  \end{bmatrix}
$$

6. Multiply $\mathbf{WV}$ to $\left\vert\phi\right\rangle$ a number of times, about $\pi/4 \cdot 2^{n/2} = \pi/4 \cdot \sqrt{8} = 2.2 \approx 2$:

$$
\mathbf{WV}\left|\phi\right\rangle = \begin{bmatrix}
  0.18 \\ 
  0.18 \\ 
  0.18 \\ 
  0.18 \\ 
  0.18 \\ 
  0.18 \\ 
  0.88 \\ 
  0.18 \\ 
  \end{bmatrix}, \mathbf{(WV)^2}\left|\phi\right\rangle = \begin{bmatrix}
  -0.09 \\ 
  -0.09 \\ 
  -0.09 \\ 
  -0.09 \\ 
  -0.09 \\ 
  -0.09 \\ 
  0.97 \\ 
  -0.09 \\ 
  \end{bmatrix}
$$

As you can see, the probability of getting the state $\left\vert 6\right\rangle$ becomes very close to 1.

<a href="https://bobbycorpus.files.wordpress.com/2017/11/grover_probability_distribution.png"><img src="https://bobbycorpus.files.wordpress.com/2017/11/grover_probability_distribution.png?w=660" alt="" width="660" height="622" class="aligncenter size-large wp-image-2811" /></a>

Making a measurement of the input register at this point will give us the state $\left\vert 6\right\rangle$ with a probability very close to 1.

We have just demonstrated the quantum search algorithm!

<h2>Why it works</h2>

Let $f$ be a function such that 

$$
f(x) = \begin{cases}
1 & x \text{ the item we are looking for}\\
0 & \text{ otherwise}
\end{cases}
$$

Define the operator $\mathbf{U}_f$ whose action on an n qubit register and 1 qubit output register is

$$
\mathbf{U}_f(\left|x\right\rangle \otimes \left|y\right\rangle) = \left|x\right\rangle\otimes\left|y\oplus f(x)\right\rangle
$$

Prepare the n qubit input register and 1 qubit output register in the following state:

$$
\underbrace{\left|0\right\rangle\ldots \left|0\right\rangle}_{\text{n times}} \otimes \left|1\right\rangle
$$

Applying the Hadamard operator on the input and output qubits gives us a superposition of $N=2^n$ states:

$$
\begin{array}{rl}
\displaystyle \mathbf{H}^{\otimes n}\otimes\mathbf{H}(\left|0\ldots 0\right\rangle\otimes \left|1\right\rangle )&= \mathbf{H}^{\otimes n}\left|0\ldots 0\right\rangle\otimes \mathbf{H}\left|1\right\rangle\\
&= \displaystyle \frac{1}{2^{n/2}}\sum_{x=0}^{2^n-1} \left|x\right\rangle \otimes \frac{1}{\sqrt{2}} \left(\left|0\right\rangle - \left|1\right\rangle\right)
\end{array}
$$

Next apply the operator $\mathbf{U}_f$ to get

$$
\begin{array}{rl}
\displaystyle \mathbf{U}_f \left[\frac{1}{2^{n/2}}\sum_{x=0}^{2^n-1} \left|x\right\rangle \otimes \frac{1}{\sqrt{2}} \left(\left|0\right\rangle - \left|1\right\rangle\right)\right] &= \displaystyle \frac{1}{\sqrt{2}} \frac{1}{2^{n/2}}\left[\sum_{x=0}^{2^n-1} \mathbf{U}_f \left|x\right\rangle \otimes  \left|0\right\rangle - \sum_{x=0}^{2^n-1} \mathbf{U}_f \left|x\right\rangle \otimes \left|1\right\rangle\right]
\\
&= \displaystyle \frac{1}{\sqrt{2}} \frac{1}{2^{n/2}}\left[\underbrace{\sum_{x=0}^{2^n-1} \left|x\right\rangle \otimes  \left|0\oplus f(x) \right\rangle }_{\text{first underbrace}}
- \underbrace{\sum_{x=0}^{2^n-1} \left|x\right\rangle \otimes \left|1\oplus f(x) \right\rangle}_{\text{second underbrace}}\right]
\end{array}
$$

When $f(x) = 1$ for x=a, the first underbrace will expand to

$$
\displaystyle \sum_{x=0}^{2^n-1} \left|x\right\rangle \otimes  \left|0\oplus f(x) \right\rangle = \left|0\right\rangle\otimes\left|0\right\rangle + \ldots + \underbrace{\left|a\right\rangle\otimes\left|1\right\rangle} + \ldots + \left|2^n-1\right\rangle \otimes \left|0\right\rangle
$$

The second underbrace will expand to

$$
\displaystyle \sum_{x=0}^{2^n-1} \left|x\right\rangle \otimes  \left|1\oplus f(x) \right\rangle = \left|0\right\rangle\otimes\left|1\right\rangle + \ldots + \underbrace{\left|a\right\rangle\otimes\left|0\right\rangle} + \ldots + \left|2^n-1\right\rangle \otimes \left|1\right\rangle\\
$$

The underbraces in the above summations can be swapped so that we get

$$
\displaystyle \frac{1}{\sqrt{2}} \frac{1}{2^{n/2}} \left[ \sum_{x=0}^{2^n-1} (-1)^{f(x)} \left|x\right\rangle \otimes  \left|0\right\rangle - \sum_{x=0}^{2^n-1} (-1)^{f(x)} \left|x\right\rangle \otimes \left|1\right\rangle\right]
$$

$$
\begin{array}{rl}
&= \displaystyle \frac{1}{2^{n/2}} \sum_{x=0}^{2^n-1} (-1)^{f(x)} \left|x\right\rangle \otimes \frac{1}{\sqrt{2}} \left( \left|0\right\rangle - \left|1\right\rangle\right)\\
&= \displaystyle \frac{1}{2^{n/2}} \sum_{x=0}^{2^n-1} (-1)^{f(x)} \left|x\right\rangle \otimes \mathbf{H}\left|1\right\rangle
\end{array}
$$

This means that the action of $\mathbf{U}_f$ leaves the output qubit unentangled with the input qubit. We can just ignore this moving forward and focus our attention to the input qubits.

We view the current state of the input qubit as the result of some operator $\mathbf{V}$ defined by 

$$
\mathbf{V} \left|\phi\right\rangle = \displaystyle \frac{1}{2^{n/2}} \sum_{x=0}^{2^n-1} (-1)^{f(x)} \left|x\right\rangle
$$

where 

$$
\left|\phi\right\rangle = \displaystyle \frac{1}{2^{n/2}} \sum_{x=0}^{2^n-1} \left|x\right\rangle
$$

We can derive the expression of $\mathbf{V}$  by expanding $\mathbf{V} \left\vert\phi\right\rangle$, noting that at x=a, $f(x) = 1$:

$$
\begin{array}{rl}
\mathbf{V} \left|\phi\right\rangle &= \displaystyle \frac{1}{2^{n/2}} \sum_{x=0}^{2^n-1} (-1)^{f(x)} \left|x\right\rangle\\
&= \displaystyle \frac{1}{2^{n/2}} \left[\left|0\right\rangle + \ldots - \left|a\right\rangle +\ldots + \left|2^n-1\right\rangle\right]\\
&= \displaystyle \frac{1}{2^{n/2}} \left[\left|0\right\rangle + \ldots + \left|a\right\rangle +\ldots + \left|2^n-1\right\rangle - 2\left|a\right\rangle\right]\\
&= \displaystyle \frac{1}{2^{n/2}} \sum_{x=0}^{2^n-1} \left|x\right\rangle - \frac{2}{2^{n/2}} \left|a\right\rangle\\
&= \left|\phi\right\rangle - 2\left\langle a|\phi\right\rangle \left|a\right\rangle\\
&= \Big[\mathbf{I} - 2\left|a\right\rangle \left\langle a\right|\Big] \left|\phi\right\rangle\\
\end{array}
$$

which means

$$
\mathbf{V} = \mathbf{I} - 2\left|a\right\rangle \left\langle a\right|
$$

The two vectors $\left\vert\phi\right\rangle$ and $\left\vert a\right\rangle$ determine a plane P. Let $\left\vert a_{\perp}\right\rangle$ be the vector in the plane perpendicular to $\left\vert a\right\rangle$. We can write $\left\vert \phi\right\rangle$ as

$$
\left|\phi\right\rangle = \phi_1 \left|a_\perp\right\rangle + \phi_2\left|a\right\rangle
$$


<a href="https://bobbycorpus.files.wordpress.com/2017/11/grover1.png"><img src="https://bobbycorpus.files.wordpress.com/2017/11/grover1.png?w=660" alt="" width="660" height="447" class="aligncenter size-large wp-image-2738" /></a>

Applying $\mathbf{V}$ to $\left\vert \phi\right\rangle$ gives us:

$$
\begin{array}{rl}
\mathbf{V}\left|\phi\right\rangle &= \left(\mathbf{I} - 2\left|a\right\rangle \left\langle a\right| \right) \left(  \phi_1 \left|a_\perp\right\rangle + \phi_2\left|a\right\rangle \right)\\
&= \phi_1 \left|a_\perp\right\rangle + \phi_2\left|a\right\rangle - 2\phi_2\left|a\right\rangle\\
&= \phi_1 \left|a_\perp\right\rangle - \phi_2\left|a\right\rangle
\end{array}
$$

The effect of the operator $\mathbf{V}$ is therefore to reflect the vector $\left\vert \phi\right\rangle$ with respect to the vector $\left\vert a_\perp\right\rangle$.

<a href="https://bobbycorpus.files.wordpress.com/2017/11/fullsizerender-3.jpg"><img src="https://bobbycorpus.files.wordpress.com/2017/11/fullsizerender-3.jpg?w=660" alt="" width="660" height="463" class="aligncenter size-large wp-image-2756" /></a>

Now, we want to reflect this vector $\mathbf{V}\left\vert \phi\right\rangle$ with respect to $\left\vert \phi\right\rangle$. To accomplish this, we define the operator $\mathbf{W}$ by

$$
\mathbf{W} = 2\left|\phi\right\rangle \left\langle\phi\right| - \mathbf{I}
$$

Apply this operator to $\mathbf{V}\left\vert\phi\right\rangle$:

$$
\begin{array}{rl}
\mathbf{WV}\left|\phi\right\rangle &= \Big[ 2\left|\phi\right\rangle \left\langle\phi\right| - \mathbf{I} \Big]\mathbf{V}\left|\phi\right\rangle\\
&= 2\left|\phi\right\rangle \left\langle\phi\right|\mathbf{V}\left|\phi\right\rangle -\mathbf{V}\left|\phi\right\rangle
\end{array}
$$

If we express $\mathbf{V}\left\vert \phi\right\rangle$ as a linear combination of $\left\vert\phi\right\rangle$ and a vector $\left\vert\phi_\perp\right\rangle$ in the plane P, 

$$
\mathbf{V}\left|\phi\right\rangle = \alpha \left|\phi\right\rangle + \beta \left|\phi_\perp\right\rangle
$$

We have,

$$
\begin{array}{rl}
\mathbf{WV}\left|\phi\right\rangle &= \Big[ 2\left|\phi\right\rangle \left\langle\phi\right| - \mathbf{I} \Big]\mathbf{V}\left|\phi\right\rangle\\
&= 2\left|\phi\right\rangle \left\langle\phi\right|\mathbf{V}\left|\phi\right\rangle -\mathbf{V}\left|\phi\right\rangle\\
&= 2\alpha\left|\phi\right\rangle  - \alpha \left|\phi\right\rangle - \beta \left|\phi_\perp\right\rangle\\
&= \alpha\left|\phi\right\rangle  - \beta \left|\phi_\perp\right\rangle
\end{array}
$$

which demonstrates that $\mathbf{W}$ reflects $\mathbf{V}\left\vert \phi\right\rangle$ with respect to $\left\vert \phi\right\rangle$.

<a href="https://bobbycorpus.files.wordpress.com/2017/11/fullsizerender-2.jpg"><img src="https://bobbycorpus.files.wordpress.com/2017/11/fullsizerender-2.jpg?w=660" alt="" width="660" height="523" class="aligncenter size-large wp-image-2757" /></a>

Therefore, the effect of $\mathbf{WV}$ on $\left\vert \phi\right\rangle $ is to rotate it by an angle $\gamma $ counter-clockwise.

We can compute this $\gamma$ by getting the inner product of $\mathbf{WV}\left\vert\phi\right\rangle$ and $\left\vert\phi\right\rangle$.  First, let's find the expression of $\mathbf{WV}\left\vert \phi\right\rangle$:

$$
\begin{array}{rl}
\mathbf{WV}\left|\phi\right\rangle &= \mathbf{W} \left( \mathbf{I} - 2\left|a\right\rangle \langle a |\right) \left|\phi\right\rangle\\
&= \mathbf{W} \left(\left|\phi\right\rangle - 2 \underbrace{\langle a| \phi \rangle} \left|a\right\rangle \right)
\end{array}
$$

The quantity $\langle a\vert \phi \rangle$ is

$$
\langle a| \phi \rangle = \displaystyle \langle a| \left( \frac{1}{2^{n/2}} \sum_{k=0}^{2^n-1} \left|x\right\rangle \right) = \frac{1}{2^{n/2}} = \cos \theta
$$

where $\theta$ is the angle between $\left\vert a\right\rangle$ and $\left\vert \phi\right\rangle$.

<a href="https://bobbycorpus.files.wordpress.com/2017/11/grover_angle_between_phi_a_perp1.jpg"><img src="https://bobbycorpus.files.wordpress.com/2017/11/grover_angle_between_phi_a_perp1.jpg?w=660" alt="" width="660" height="582" class="aligncenter size-large wp-image-2909" /></a>

The complementary angle, $\rho = 90-\theta$ is the angle between $\left\vert\phi\right\rangle$ and $\left\vert a_\perp\right\rangle$. Using a well-known trigonometric identity, we can compute for the angle of $\rho$:

$$
\cos \theta = \sin \rho = \displaystyle \frac{1}{2^{n/2}}
$$

Since $\displaystyle \frac{1}{2^{n/2}}$ is very small if n is large,

$\sin \rho = \displaystyle \frac{1}{2^{n/2}} \approx \rho $

Continuing,

$$
\begin{array}{rl}
\mathbf{WV}\left|\phi\right\rangle &= \mathbf{W} \left( \mathbf{I} - 2\left|a\right\rangle \langle a |\right) \left|\phi\right\rangle\\
&= \mathbf{W} \left(\left|\phi\right\rangle - 2 \cos\rho \left|a\right\rangle \right)\\
&= (2\left|\phi\right\rangle \langle \phi| - \mathbf{I}) \left(\left|\phi\right\rangle - 2 \cos\rho \left|a\right\rangle \right)\\
&= 2\left|\phi\right\rangle - \left|\phi\right\rangle - 2\cos\rho \left|\phi\right\rangle \langle \phi |a\rangle + 2 \cos\rho \left|a\right\rangle\\
&= \left|\phi\right\rangle - 4\cos^2\rho \left|\phi\right\rangle + 2 \cos\rho \left|a\right\rangle\\
&= (1- 4\cos^2\rho) \left|\phi\right\rangle + 2 \cos\rho \left|a\right\rangle
\end{array}
$$

The inner product of $\mathbf{WV}\left\vert\phi\right\rangle$ and $\left\vert\phi\right\rangle$ is given by

$$
\begin{array}{rl}
\langle\phi|\mathbf{WV}\left|\phi\right\rangle &= \langle\phi|\left[  (1- 4\cos^2\rho) \left|\phi\right\rangle + 2 \cos\rho \left|a\right\rangle\right]\\
&= (1- 4\cos^2\rho) + 2\cos^2\rho\\
&= 1- 2\cos^2\rho\\
&= \cos 2\rho
\end{array}
$$

Therefore, the angle between these two vectors is $2\rho = \displaystyle \frac{1}{2^{n/2}}$. How many times do we have to apply the operator $\mathbf{WV}$ to get to $\pi/2$? 

$$
\begin{array}{rl}
m\cdot 2\rho &= \displaystyle \frac{\pi}{2}\\
\displaystyle m \frac{2}{2^{n/2}}&= \displaystyle \frac{\pi}{2}\\
m &=\displaystyle \frac{\pi\cdot 2^{n/2}}{4}
\end{array}
$$

Therefore, we need to apply the operator $\mathbf{WV}$ $O(\sqrt{N})$ times to find our value (where $N=2^n$).

Image Credit: "Binoculars at Duomo roof /Prismaticos en el tejado del Duomo, Milan" by albertopveiga is marked with CC BY-NC-SA 2.0. To view the terms, visit https://creativecommons.org/licenses/by-nc-sa/2.0/?ref=openverse
