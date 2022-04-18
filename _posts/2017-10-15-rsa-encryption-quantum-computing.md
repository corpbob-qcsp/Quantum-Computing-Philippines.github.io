---
layout: post
title:  "RSA Encryption and Quantum Computing"
author: "Bobby Corpus"
image: assets/images/prime_numbers.jpeg
categories: Cryptography
comments: false
---

We are going to crack the RSA by finding the period of the ciphertext. Let m be the message, c the ciphertext, e the encoding number and N=pq such that

$$
c=m^e \mod N
$$

From <a href="https://bobbycorpus.wordpress.com/2017/10/15/period-finding-and-the-rsa/" target="_blank" rel="noopener">Period Finding and the RSA</a> post, the period of a ciphertext is defined as the smallest number r such that

$$
c^r \equiv 1 \mod N
$$

First we prepare $n=2n_0$ QBits at the state $\left\vert 0\right\rangle $ where $n_0 $ is the number of bits of $N=pq $. The number of bits can be computed using the formula:

$$
\lceil \log_2(N) \rceil
$$

We also prepare $n_0 $ QBits for the output register.

So initially we have the following setup:

$$
\underbrace{\left|00\ldots 0\right\rangle}_{n\text{ times}} \otimes \underbrace{\left|00\ldots0\right\rangle}_{n_0\text{ times}}
$$

The output register will contain the output of the operator:

$$
\mathbf{U}_f \left|x\right\rangle\left|0\right\rangle = \left|x\right\rangle\left|f(x)\right\rangle
$$

where

$$
f(x) = c^x \mod N
$$

We now apply the Hadarmard gate to the input register and apply the operator $\mathbf{U}_f$:

$$
\displaystyle \frac{1}{2^{n/2}} \sum_{x=0}^{2^n-1} \left|x\right\rangle\left|f(x)\right\rangle
$$

The Hadamard gates transform the state into a superposition of all possible values of the period while the operator $\mathbf{U}_f$ transforms the output QBits as superposition of all possible values of $c^x\mod N$.

Next we make a measurement of the output QBits. This will give us a number $f_0$ which corresponds to all states whose value of $\left\vert x\right\rangle $ is of the form $x_0+kr $ since

$$
\begin{array}{rl}
c^{x_0+kr} & \equiv c^{x_0}c^{kr} \mod N \\
& \equiv c^{x_0}(c^{r})^k \mod N \\
& \equiv c^{x_0} \mod N
\end{array}
$$

since $(c^{r})^k \equiv 1 \mod N $ by definition of a period.

Therefore, after the measurement, the input state is now

$$
\displaystyle \left|\Psi\right\rangle = \frac{1}{\sqrt{m}} \sum_{k=0}^{m-1} \left|x_0 + kr\right\rangle
$$

where m is the smallest integer such that $x_0 + mr \ge 2^{n}$.

We will then apply the quantum Fourier transform to state $\left\vert \Psi\right\rangle $. The Quantum Fourier Transform is defined by its action on an arbitrary $\left\vert x\right\rangle $

$$
\displaystyle \mathbf{U}_{\mathbf{FT}}\left|x\right\rangle = \frac{1}{2^{n/2}} \sum_{y=0}^{2^n-1} e^{2\pi ixy/2^n}\left|y\right\rangle
$$

Applying the Fourier Transform to the state $\left\vert \Psi\right\rangle $,

$$
\begin{array}{rl}
\displaystyle \mathbf{U}_{\mathbf{FT}} \frac{1}{\sqrt{m}} \sum_{k=0}^{m-1}\left|x_0 + kr\right\rangle &= \displaystyle \frac{1}{\sqrt{m}} \sum_{k=0}^{m-1}\mathbf{U}_{\mathbf{FT}} \left|x_0 + kr\right\rangle\\
&= \displaystyle \frac{1}{\sqrt{m}} \sum_{k=0}^{m-1} \frac{1}{2^{n/2}} \sum_{y=0}^{2^n-1} e^{2\pi i(x_0+kr)y/2^n}\left|y\right\rangle\\
&= \displaystyle \frac{1}{\sqrt{m}} \sum_{k=0}^{m-1} \frac{1}{2^{n/2}} \sum_{y=0}^{2^n-1} e^{2\pi ix_0y/2^n}e^{2\pi ikry/2^n}\left|y\right\rangle\\
&= \displaystyle \sum_{y=0}^{2^n-1} \underbrace{e^{2\pi ix_0y/2^n} \frac{1}{\sqrt{2^n m}} \sum_{k=0}^{m-1} e^{2\pi ikry/2^n}}_{\text{probability amplitude}}\left|y\right\rangle
\end{array}
$$

Now if we make a measurement of this new state, the probability of getting a value of y is the probability amplitude multiplied by it's complex conjugate:

$$
\displaystyle \Big[\underbrace{e^{2\pi ix_0y/2^n} } \frac{1}{\sqrt{2^n m}} \sum_{k=0}^{m-1} e^{2\pi ikry/2^n}\Big]\Big[\underbrace{e^{-2\pi ix_0y/2^n} } \frac{1}{\sqrt{2^n m}} \sum_{k=0}^{m-1} e^{-2\pi ikry/2^n}\Big]
$$

The factors in underbrace evaluates to 1 when multiplied and we're left with

$$
\displaystyle \frac{1}{2^n m}\Big| \underbrace{\sum_{k=0}^{m-1} e^{2\pi ikry/2^n}}\Big|^2
$$

Using the formula

$$
e^{i\theta} = \cos(\theta) + i\sin(\theta)
$$

we can write the summation in underbrace as

$$
\displaystyle \Big(\sum_{k=0}^{m-1} \cos(2\pi kry/2^n)\Big)^2 + \Big(\sum_{k=0}^{m-1} \sin(2\pi kry/2^n)\Big)^2
$$

We'd like to know at what value of y the expression above is maximum. We can use the following formula to simplify above summations:

$$
\displaystyle \sum_{k=0}^{m-1} \cos(2\pi fk) = \frac{\cos(\pi f(m-1)) \sin(\pi fm)}{\sin(\pi f)}\\
\displaystyle \sum_{k=0}^{m-1} \sin(2\pi fk) = \frac{\sin(\pi f(m-1)) \sin(\pi fm)}{\sin(\pi f)}
$$

If we let

$$
f = ry/2^n
$$

this reduces the summations to:

$$
\displaystyle \Big(\sum_{k=0}^{m-1} \cos(2\pi kry/2^n)\Big)^2 + \Big(\sum_{k=0}^{m-1} \sin(2\pi kry/2^n)\Big)^2
$$

$$
\begin{array}{rl}
&=\displaystyle \frac{\cos^2(\pi f(m-1)) \sin^2(\pi fm)}{\sin^2(\pi f)} + \frac{\sin^2(\pi f(m-1))\sin^2(\pi fm))}{\sin^2(\pi f)}\\
&= \displaystyle \frac{\sin^2(\pi fm)}{\sin^2(\pi f)}\Big[\underbrace{\cos^2(\pi f(m-1)) + \sin^2(\pi f(m-1))}_{\text{= 1}}\Big]\\
&= \displaystyle \frac{\sin^2(\pi fm)}{\sin^2(\pi f)}
\end{array}
$$

Therefore, the probability of getting a specific y is

$$
p(y) = \displaystyle \frac{1}{2^nm} \displaystyle \frac{\sin^2(\pi fm)}{\sin^2(\pi f)}
$$

Here is a plot of the function $\sin^2(\pi fm)/\sin^2(\pi f) $ for m=4:

<a href="https://bobbycorpus.files.wordpress.com/2017/10/graph_shors_algo1.png"><img class="aligncenter size-large wp-image-2635" src="https://bobbycorpus.files.wordpress.com/2017/10/graph_shors_algo1.png?w=660" alt="" width="660" height="320" /></a>

Notice that the graph attains its maximums when the values of f are integer values, that is when

$$
\displaystyle f = j= \frac{ry}{2^n} \text{ where } j= 1, 2, 3, 4, \ldots, r
$$

Since y is an integer value, this means that the value of y must be close to $j2^n/r$. We can use a theorem in Number theory which states that if $p/q$ is a rational number that approximates a real number x and

$$
\displaystyle \Big|\frac{p}{q} - x\Big| \le \frac{1}{2q}
$$

we can approximate x using continued fraction expansion of p/q. In fact, if we find a y that is within 1/2 of one of the $j2^n/r$ values, we have

$$
\displaystyle \left| y-j\frac{2^n}{r}\right| \le \displaystyle \frac{1}{2}\\
$$

and dividing both sides by $2^n$, we get

$$
\displaystyle \left| \frac{y}{2^n}-\frac{j}{r}\right| \le \displaystyle \frac{1}{2^{n+1}}
$$

which satisfies the theorem. We can therefore expand $y/2^n$ via continued fraction expansion and continue until we find a denominator $r$ that is less that $2^{n_0}$. We then test whether r could be our period if it satisfies

$$
\displaystyle c^r \equiv 1
$$

If it satisfies the above, then r is the period. It's just a matter of computing $d^\prime$, the inverse of $e$ modulo r satisfying

$$
\displaystyle ed^\prime \equiv 1 \mod r
$$

Having computed $d^\prime$, we can then decrypt the ciphertext using

$$
m = c^{d^\prime} \mod N
$$

In our example in <a href="https://bobbycorpus.wordpress.com/?p=2473&preview=true" target="_blank" rel="noopener">period finding</a>, let's suppose that the quantum computer gave us the following number

$$
y=1446311
$$

We can expand the fraction $y/2^n = 1446311/16777216$ as a continued fractions as follows:

$$
\displaystyle \frac{y}{2^n} = \frac{1446311}{16777216} = \frac{1}{11 + \displaystyle \frac{1}{1+\displaystyle\frac{1}{1+\displaystyle\frac{1}{1}}}} = \frac{5}{58}
$$

or

$$
\displaystyle \frac{y}{2^n} = \frac{1446311}{16777216} = \frac{1}{11 + \displaystyle \frac{1}{1+\displaystyle\frac{1}{1+\displaystyle\frac{1}{\displaystyle\frac{1}{6886}}}}} = \frac{34433}{399423}
$$

We can stop until $q=58$ since $q=58 &lt; 2^{n_0} = 2^{12} = 4096 $. So we use the first expansion where $q=58$ and testing it

$$
\begin{array}{rl}
\displaystyle c^{58} \mod 3127 &= \displaystyle 794^{58} \mod 3127\\
&= \displaystyle 1 \mod 3127
\end{array}
$$

which confirms that $q=58$ is a period. Using $r=58$ in the equation

$$
\displaystyle ed^\prime = m\times r + 1
$$

and solving for $d^\prime$ (which is the inverse of e modulo 58) we get

$$
\displaystyle d^\prime = 25
$$

We can now get the original message using

$$
\displaystyle 0794^{25} \mod 3127 = 1907
$$

which agrees with our original message.

How lucky do we need to get in order for the state to collapse to one of these y values close to $j2^n/r$? Let's evaluate the probability for

$$
y_j=\displaystyle j\frac{2^n}{r} \pm \delta_j
$$

where $\delta_j$ is the distance of the closest y to $j2^n/r$. Since $f = ry/2^n$, we have

$$
\begin{array}{rl}
p(y_j) = \displaystyle \frac{1}{2^nm}\frac{\sin^2(\pi fm)}{\sin^2(\pi f)} &= \displaystyle \frac{1}{2^nm} \frac{\sin^2(\pi mr\displaystyle\frac{y}{2^n})}{\sin^2(\pi r\displaystyle\frac{y}{2^n}) }\\
&= \displaystyle \frac{1}{2^nm} \frac{\sin^2\Big[\displaystyle\frac{\pi mr}{2^n}(j\frac{2^n}{r} \pm \delta_j)\Big]}{\sin^2\Big[\displaystyle\frac{\pi r}{2^n}(j\frac{2^n}{r} \pm \delta_j)\Big] }\\
&= \displaystyle \frac{1}{2^nm} \frac{\sin^2\Big[\displaystyle j\pi m \pm \frac{\pi m r\delta_j}{2^n})\Big]}{\sin^2\Big[\displaystyle j\pi \pm \frac{\pi r\delta_j}{2^n}\Big] }\\
&= \displaystyle \frac{1}{2^nm} \frac{\sin^2\Big[\displaystyle \frac{\pi m r\delta_j}{2^n})\Big]}{\sin^2\Big[\displaystyle \frac{\pi r\delta_j}{2^n}\Big] }
\end{array}
$$

We can approximate the denominator using the small angle approximation of sine:

$$
\sin(x) \approx x
$$

to get

$$
\displaystyle p(y_j) = \displaystyle \frac{1}{2^nm} \frac{\sin^2\Big[\displaystyle \frac{\pi m r\delta_j}{2^n})\Big]}{\left[\displaystyle \frac{\pi r\delta_j}{2^n}\right]^2}
$$

Since m is the smallest integer such that $x_0 + mr \ge 2^{n}$, then

$$
m = \displaystyle \left\lceil\frac{2^n}{r}\right\rceil
$$

We can use this to approximate

$$
\displaystyle m\frac{r}{2^n} \approx 1
$$

to get

$$
p(y_j) = \displaystyle \frac{1}{2^nm} \frac{\sin^2(\displaystyle \pi\delta_j)}{\left(\displaystyle \frac{\pi\delta_j}{m}\right)^2}
$$

Since $0 \le \delta_j \le 1/2$, we can see from the graph below that the line joining (0,0) and $(\pi/2,1)$, whose equation is $2x/\pi$ is less that the value of the sine function at that interval, that is,

$$
\displaystyle \frac{2x}{\pi} \le \sin(x)
$$

<a href="https://bobbycorpus.files.wordpress.com/2017/10/graph_shors_algo_2.png"><img class="aligncenter size-large wp-image-2638" src="https://bobbycorpus.files.wordpress.com/2017/10/graph_shors_algo_2.png?w=660" alt="" width="660" height="260" /></a>

Using this inequality, we can get a lower bound of the probability of getting a specific y value to be

$$
\begin{array}{rl}
p(y_j) = \displaystyle \frac{1}{2^nm} \frac{\sin^2(\displaystyle \pi\delta_j)}{\left(\displaystyle \frac{\pi \delta_j}{m}\right)^2} &\ge \displaystyle \frac{1}{2^nm} \left(\frac{\displaystyle \frac{2\pi\delta_j}{\pi}}{\displaystyle \frac{\pi \delta_j}{m}}\right)^2\\
&= \displaystyle \frac{1}{2^nm} \left( \frac{2m}{\pi}\right)\\
&= \displaystyle \frac{m}{2^n}\frac{4}{\pi^2}\\
&= \displaystyle \frac{1}{r}\frac{4}{\pi^2}
\end{array}
$$

Since there are r of these $y_j$'s, the probability of getting any one of these $y_j$'s is therefore:

$$
p(y) = \displaystyle \frac{4}{\pi^2} \approx 0.405
$$

which means that there is 40% probability of the state to collapse to a value of y that will give us the correct period.

We have just seen how a quantum computer can be used to crack the RSA. We have exploited the fact that the measurement will give us a value of y that is near $j2^n/r$ with high probability. We then computed for the period using the continued fraction expansion of $y/2^n$. With the period computed, it's becomes straightforward to decrypt the ciphertext.

Image Credit: "Prime Numbers" by chrisinplymouth is marked with CC BY-NC-SA 2.0. To view the terms, visit https://creativecommons.org/licenses/by-nc-sa/2.0/?ref=openverse
